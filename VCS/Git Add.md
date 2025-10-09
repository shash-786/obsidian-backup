Every command should go through a few checks 
For example: For `init` there needs to be no repository present in the current directory

Similarly for `add` we will perform a few checks

#### 1) `RUN_SETUP`

This will start searching upwards from the current directory and keep going until it's constructed a path from the current working directory to the directory where `.git` is present. 

**Also this will be responsible for creating a prefix (This took a day to understand ;) )** 

Prefix finding process
* Get the current working directory `/home/shash/project/api`
* Start iterating from the end 
* Check if `.../api` is a git directory
* If not go one level above and check if `.../project` is
* If it is return a pointer after just after project for prefix
* So the prefix would be `/api` 

```c
	offset = len = strlen(cwd);
	for (;;) {
		if (is_git_directory(".git"))
			break;
		chdir("..");
		do {
			if (!offset) {
				if (is_git_directory(cwd)) {
					if (chdir(cwd))
						die("Cannot come back to cwd");
					setenv(GIT_DIR_ENVIRONMENT, cwd, 1);
					inside_git_dir = 1;
					return NULL;
				}
				if (nongit_ok) {
					if (chdir(cwd))
						die("Cannot come back to cwd");
					*nongit_ok = 1;
					return NULL;
				}
				die("Not a git repository");
			}
		} while (cwd[--offset] != '/');
	}
	if (offset == len)
		return NULL;
	/* Make "offset" point to past the '/', and add a '/' at the end */
	offset++;
	cwd[len++] = '/';
	cwd[len] = 0;
	inside_git_dir = !strncmp(cwd + offset, ".git/", 5);
	return cwd + offset;
```
