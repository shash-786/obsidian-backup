### Git Commit-tree Algorithm

So till now we have created an in-memory *correct* (in the sense that it is updated) view of the entire working tree using the `write_tree` routine.

All that is left is to create a commit, and for that we use this `commit_tree` function.

`git commit-tree <tree> [(-p <parent>)..]`

We supply 2 things to this
1) Parent Commit (if any)
2) SHA1 hash of the in-memory tree we just created
3) The commit message (**optional**)

We begin by loading the git config env's. (Please go and understand the [Git Config Setup](https://github.com/shash-786/obsidian-backup/blob/main/VCS/Git%20Config%20Setup.md)) 


Now that it is out of the way here's what an commit object would look like

```bash
tree b16fc36da1fd7dd47d74948958bcd3e180ce1956
parent 8fd8e89d842011fa1d736723de45a86edafbb446
author anon786 <ssalian60@yahoo.com> 1762086353 +0530
committer anon786 <ssalian60@yahoo.com> 1762086353 +0530

init // <-- commit message
```

In the `commit_tree` we will be building this line by line

1) First 2 lines are supplied by the caller itself so no need to worry about those
2) The next two lines which are about **Author** and **Committer** are taken from the git credentials, which are filled in by the config setup (basically fetched from the config file).
3) Finally the commit message which is piped in from `stdin`

### Code walk-through

The commit builder basically is a buffer where you keep adding each line from the commit object in the buffer and then it is ready.

```c
init_buffer(&buffer, &size);
add_buffer(&buffer, &size, "tree %s\n", sha1_to_hex(tree_sha1));

for (i = 0; i < parents; i++)
		add_buffer(&buffer, &size, "parent %s\n", sha1_to_hex(parent_sha1[i]));
		
add_buffer(&buffer, &size, "author %s\n", git_author_info(1));
add_buffer(&buffer, &size, "committer %s\n", git_committer_info(1));

add_buffer(&buffer, &size, "\n");

/* And add the comment */
while (fgets(comment, sizeof(comment), stdin) != NULL)
	add_buffer(&buffer, &size, "%s", comment);
buffer[size] = '\0';

// Then write the SHA1 File to the index
```

The `init_buffer` is pretty straightforward we allocate a buffer of arbitrary size to the variable `buffer` and allocate the `size` variable to `0` since nothing is consumed yet.

The `add_buffer` on the other hand...

```c
static void add_buffer(char **bufp, unsigned int *sizep, const char *fmt, ...)
{
	char one_line[2048];
	va_list args;
	int len;
	unsigned long alloc, size, newsize;
	char *buf;

	va_start(args, fmt);
	len = vsnprintf(one_line, sizeof(one_line), fmt, args);
	va_end(args);
	size = *sizep;
	newsize = size + len + 1;
	alloc = (size + 32767) & ~32767; // <-- ? ? ? What is this 
```

This above line `(size + 32767) & 32767`. This is a fast, bitwise trick to round the current size up to the nearest 32KB boundary. It calculates the buffer's current allocated capacity. 

Let's understand with a simple example

```c
	buf = *bufp;
	if (newsize > alloc) {
		alloc = (newsize + 32767) & ~32767;
		buf = xrealloc(buf, alloc);
		*bufp = buf;
	}
	*sizep = newsize - 1;
	memcpy(buf + size, one_line, len);
}
```
