Git is a simple ***key-value*** data store, meaning insert anything get a key then use that key to retrieve item later. 

Where are these objects stored?
--> In the `.git/objects` directory (object storage)

## 1) Blob

Most **basic** git object. Basically anything other than a directory

***Blob Representation***
`blob [CONTENT_LENGTH]\0xx[CONTENT]`

*When you do this you are just storing the raw object data, what about the FILENAME ??*

## 2) Tree

 Solves the problem of storing the filename and also allows you to store a group of files together

***Tree Representation***
`[MODE] [FILENAME/DIRNAME]\0xx[HASH]`

***MODE***
- `100644` for a normal, non-executable file (a blob).    
- `100755` for an executable file (a blob).
- `040000` for a sub-directory (another tree).
## 3) Commit 

Information about who saved the snapshots, when they were saved, or why they were saved. This is the basic information that the commit object stores for you.
```bash
$ git cat-file -p fdf4fc3
tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
author Scott Chacon <schacon@gmail.com> 1243040974 -0700
committer Scott Chacon <schacon@gmail.com> 1243040974 -0700

First commit
```

The format for a commit object is simple: it specifies the top-level tree for the snapshot of the project at that point; the parent commits if any, the author/committer information (which uses your `user.name` and `user.email` configuration settings and a timestamp); a blank line, and then the commit message