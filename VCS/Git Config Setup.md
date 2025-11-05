### Git Config Setup

How your default config file looks like?
```
[init]
        defaultBranch = main
[user]
        email = anon@yahoo.com
        name = anon786
```

This file is located in the home directory so we will first load and read from this file. Also there can be in practice project specific config located in individual `.git` folder which we'll ignore for now and only focus on the global `~/.gitconfig`

The config reader and initialization logic lies in the `git_config` function which takes in a function pointer. For simplicity reasons consider this `f_ptr` a hash function which will basically take an entry `[user] email = anon@yahoo.com` parsed by helper functions to key `user.email` and value `anon@yahoo.com` and make it available to the global config default email and also we'll assume that there will no comments in the config file.

```c
typedef int (*config_fn_t)(const char *, const char *);

int git_default_config(const char *var, const char *value) {
	...
	
	if (!strcmp(var, "user.email")) {
		strlcpy(git_default_email, value, sizeof(git_default_email));
		return 0;
	}
	...
}
```


```c
int git_config(config_fn_t fn)
{
	int ret = 0;
	char *repo_config = NULL;
	const char *home = NULL, *filename;

	/* $GIT_CONFIG makes git read _only_ the given config file,
	 * $GIT_CONFIG_LOCAL will make it process it in addition to the
	 * global config file, the same way it would the per-repository
	 * config file otherwise. 
	filename = getenv(CONFIG_ENVIRONMENT);
	if (!filename) {
		home = getenv("HOME");
		filename = getenv(CONFIG_LOCAL_ENVIRONMENT);
		if (!filename)
			filename = repo_config = xstrdup(git_path("config"));
	}
	*/	
	home = getenv("HOME");

	if (home) {
		char *user_config = xstrdup(mkpath("%s/.gitconfig", home)); // <-- duplicate string "/home/user/.gitconfig" to user_config, Also mkpath just returns a static buffer with the arg formatted with the variable onto to buffer
		if (!access(user_config, R_OK))
			ret = git_config_from_file(fn, user_config);
		free(user_config);
	}
	// ret += git_config_from_file(fn, filename);
	//free(repo_config);
	return ret;
}
```

### git_parse_file

So `git_config_from_file` will just open the `~/.gitconfig` file and then call this main parser function which will do most of the heavy lifting. Base variables like the ones that appear in `[]` can be can be multiple words as well for our use case we'll only assume it's one word like 'user' or 'core'.

```c
static int git_parse_file(config_fn_t fn)
{
	int comment = 0;
	int baselen = 0;
	static char var[MAXNAME];

	for (;;) {
		int c = get_next_char();
		if (c == '\n') {
			/* EOF? */
			if (!config_file)
				return 0;
			comment = 0;
			continue;
		}
		if (comment || isspace(c))
			continue;
		if (c == '#' || c == ';') {
			comment = 1;
			continue;
		}
		if (c == '[') {
			baselen = get_base_var(var);
			if (baselen <= 0)
				break;
			var[baselen++] = '.';
			var[baselen] = 0; // <- null terminating so that if we break we
			// have a printable string
			continue;
			/*
				Will extract out what ever is in the []
				after this loop the var will be 
				var = "user."
			*/
		}
		if (!isalpha(c))
			break;
		var[baselen] = tolower(c); // now here our var state would be like 
		// var = "user.n"
		if (get_value(fn, var, baselen+1) < 0) // <-- this is where var will be filled and also a
		// we will get the value corresponding to the key 'user.name'
			break;
	}
	die("bad config file line %d in %s", config_linenr, config_file_name);
}
```

Suppose we have config like this

```
[core]
	filemode = true
	bare = false
```

```
then in git_parse_file
var = "core.\0" baselen = 5

then var = "core.f"
first call to `get_value` will be ("core.f" , 5 + 1) 

then IMPORTANT we return to same state 
var = "core.f"

second call to `get_value` will be ("core.b" , 5 + 1)

and finally when we encounter new '[' we reset baselen back to 0
```