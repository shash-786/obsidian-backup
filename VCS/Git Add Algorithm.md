### Git Add Algorithm

##### High level view of the ADD algorithm in the git

```c 
int cmd_add(int argc, const char **argv, const char *prefix)
```

Suppose you're project structure is something like this
```
|
|
|_ src
|_ api
	 |
	 |_ foo.py
	 |- bar.py
```

You type the command `git add foo.py bar.py`

1) The pre-flight check is done and the prefix is extracted which in our case is `api/`

2) The `argv` array contains `{"foo.py", "bar.py"}`

3) We use the `pathspec` function to prepend and resolve the path in that if the path contains `.` or `..` we will take care of that and also prefix is pre appended

4) So now the array would be like `{"api/foo.py", "api/bar.py"}`

5) Now we will 'fill_directory' what this will do is fill the `dir_struct` with all the entries with excludes patterns like `*.log` or `src/`

6) In the `read_directory` function we also recursively loop through the files/directories and then we check for the exclude rules for each item in the directory if excluded we completely skip it. If it is not excluded we add it to the the `dir->entry` list.  

7) Then read the index file if present or if not create it in memory first and then write it to the actual index file (**actual git uses lockfile mechanism where in the contents are first written onto the lockfile and the merged with the actual one**)

8) The Entries in the index file are sorted in the alphabetical order. 

```c
#define EXC_CMDL 0 // <-- COMMAND LINE IGNORE (NOT IMPORTANT)
#define EXC_DIRS 1 // <-- .gitignore FILES IN EVERY DIRECTORY (** IMPORTANT **)
#define EXC_FILE 2 // <-- info/exclude FILE IGNORES (NOT IMPORTANT)

struct dir_entry {
	unsigned int ignored : 1;
	unsigned int ignored_dir : 1;
	unsigned int len : 30;
	char name[FLEX_ARRAY]; /* more */
};

struct exclude_list {
	int nr;
	int alloc;
	struct exclude {
		const char *pattern;
		const char *base;
		int baselen;
	} **excludes;
};

struct dir_struct {
	int nr, alloc;
	unsigned int show_ignored:1,
		     show_other_directories:1, 
		     hide_empty_directories:1; 
	struct dir_entry **entries;
	/* Exclude info */
	const char *exclude_per_dir;
	struct exclude_list exclude_list[3]; // only one list required for me :)
};
```