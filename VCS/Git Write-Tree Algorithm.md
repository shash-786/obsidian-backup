### Git write-tree Algorithm

### `write-tree`

Pseudo Algorithm

1) read the index file
2) we check if we have an active-cache-tree (optimization not important for now)
3) If not we build a cache tree
4) Then we `cache_tree_update` **update** (in the case of using the cached tree) or create **from** scratch the cache tree
5) Then write the tree back to the index (OPTIMIZATION)
6) Return the top node hash


### `cache_tree_update`

1) `cache_tree_update` calls 2 functions 
	1) `verify_cache` - Basically does a validation on the index file entries (not important be responsible while inserting in the index file). This is for cases to check whether tree is merged or not and for conflicting index entries like `src/foo` and `src/foo/bar.c` in a sorted list
	2) `update_one` - essentially the tree builder.


### `update_one`

```c
static int update_one(struct cache_tree *it,
		      struct cache_entry **cache,
		      int entries,
		      const char *base,
		      int baselen,
		      int missing_ok,
		      int dryrun)
{
	unsigned long size, offset;
	char *buffer;
	int i;

	if (0 <= it->entry_count && has_sha1_file(it->sha1))
		return it->entry_count;
	
	/*
	 * We first scan for subtrees and update them; we start by
	 * marking existing subtrees -- the ones that are unmarked
	 * should not be in the result.
	 */
	for (i = 0; i < it->subtree_nr; i++)
		it->down[i]->used = 0;
```
If a tree exists Mark all the sub-directories as deleted and then after below executes if still unused then **DELETE**

```c
	/*
	 * Find the subtrees and update them.
	 */
	for (i = 0; i < entries; i++) {
		struct cache_entry *ce = cache[i];
		struct cache_tree_sub *sub;
		const char *path, *slash;
		int pathlen, sublen, subcnt;

		path = ce->name;
		pathlen = ce_namelen(ce);
		if (pathlen <= baselen || memcmp(base, path, baselen))
			break; /* at the end of this level */

		slash = strchr(path + baselen, '/');
		if (!slash)
			continue;
		/*
		 * a/bbb/c (base = a/, slash = /c)
		 * ==>
		 * path+baselen = bbb/c, sublen = 3
		 */
		sublen = slash - (path + baselen);
		sub = find_subtree(it, path + baselen, sublen, 1);
		if (!sub->cache_tree)
			sub->cache_tree = cache_tree();
		subcnt = update_one(sub->cache_tree,
				    cache + i, entries - i,
				    path,
				    baselen + sublen + 1,
				    missing_ok,
				    dryrun);
		if (subcnt < 0)
			return subcnt;
		i += subcnt - 1;
		sub->used = 1;
	}
```

Let's assume in the beginning the `base=""` and `baselen=0` and the index file is something like this
```
0 foo.cc
1 src/api/bar.c
2 src/utils/baz.c
```
1) `foo.c` will be skipped we will be focusing on sub-directories first
2) for `src/api/bar.c` slash will be `pos=4` so sub-directory Len would be `4-(0-0)` i.e `4`, we'll find a sub-tree for `src/` (which does not exist) so we'll create one
3) Now we'll recursively call `update_one` with the `1 src/api/bar.c` index in cache (*benefits of sorted list*)

```c
	discard_unused_subtrees(it);
```
Discard all the unused sub-dirs we can now confidently say that they were deleted.


```c
	/*
	 * Then write out the tree object for this level.
	 */
	size = 8192;
	buffer = xmalloc(size);
	offset = 0;

	for (i = 0; i < entries; i++) {
		struct cache_entry *ce = cache[i];
		struct cache_tree_sub *sub;
		const char *path, *slash;
		int pathlen, entlen;
		const unsigned char *sha1;
		unsigned mode;

		path = ce->name;
		pathlen = ce_namelen(ce);
		if (pathlen <= baselen || memcmp(base, path, baselen))
			break; /* at the end of this level */

		slash = strchr(path + baselen, '/');
		if (slash) {
			entlen = slash - (path + baselen);
			sub = find_subtree(it, path + baselen, entlen, 0);
			if (!sub)
				die("cache-tree.c: '%.*s' in '%s' not found",
				    entlen, path + baselen, path);
			i += sub->cache_tree->entry_count - 1;
			sha1 = sub->cache_tree->sha1;
			mode = S_IFDIR;
		}
		else {
			sha1 = ce->sha1;
			mode = ntohl(ce->ce_mode);
			entlen = pathlen - baselen;
		}
		if (!missing_ok && !has_sha1_file(sha1))
			return error("invalid object %s", sha1_to_hex(sha1));

		if (!ce->ce_mode)
			continue; /* entry being removed */

		if (size < offset + entlen + 100) {
			size = alloc_nr(offset + entlen + 100);
			buffer = xrealloc(buffer, size);
		}
		offset += sprintf(buffer + offset,
				  "%o %.*s", mode, entlen, path + baselen);
		buffer[offset++] = 0;
		hashcpy((unsigned char*)buffer + offset, sha1);
		offset += 20;
```
We append a whole tree-entry now like
`040000 api \0 [api-tree-hash]` 
`100644 main.c \0 [main.c-blob-hash]`

```c

#if DEBUG
		fprintf(stderr, "cache-tree update-one %o %.*s\n",
			mode, entlen, path + baselen);
#endif
	}

	if (dryrun)
		hash_sha1_file(buffer, offset, tree_type, it->sha1);
	else	
		write_sha1_file(buffer, offset, tree_type, it->sha1);
	
	free(buffer);
	it->entry_count = i;
#if DEBUG
	fprintf(stderr, "cache-tree update-one (%d ent, %d subtree) %s\n",
		it->entry_count, it->subtree_nr,
		sha1_to_hex(it->sha1));
#endif
	return i;
}
```