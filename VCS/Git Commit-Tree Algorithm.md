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
	
/*

Also why is the address of the original pointer buffer passed?
so what happens is if you pass just the pointer you are essentially 
passing a copy of the pointer 

meaning let's say 
in the caller function

buffer is at 0x123 and it points to 0xFFF
if you pass by value then func_buffer will be at 0x456 and it will still be pointer to 0XFFF

In the add_buffer `realloc` might move the entire block of memory from `0xFFF` to a new, larger location, say `0xABC`. If you only passes `char *buf`, the function would update its local copy to point to `0xABC`. The caller's `buffer` would still be pointing at `0xFFF`, which is now invalid (freed) memory

That's why we pass the buffer as reference so that any changes to the size of the func_buffer should be reflected in the original buffer 

*/
```

This above line `(size + 32767) & 32767`. This is a fast, bit-wise trick to round the current size up to the nearest 32KB boundary. It calculates the buffer's current allocated capacity.

Let's understand with a simple example. Let's assume instead of 32kb boundary we have only 32 bytes.

so expression will be `alloc = (size + 31) & ~31`
31  = 0001 1111
~31 = 1110 0000


```
### Suppose our size is 40

alloc = (40 + 31)  & 1110 0000
alloc = 71 & 1110 0000
alloc = 0100 0111 & 1110 0000

0100 0111
1110 0000
---------
0100 0000

alloc = 0100 0000 (64)

So we have successfully rounded up (it can be done using the division operator as well but this is faster)

### Suppose our size is 32

alloc = (32 + 31) & 1110 0000
alloc = 63 & 1110 0000
alloc = 0011 1111 & 1110 0000
alloc = 0010 0000 (32)

No rounding required perfect fit

```



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
