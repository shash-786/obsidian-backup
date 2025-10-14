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