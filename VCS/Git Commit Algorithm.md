### Git Commit Algorithm

##### High level view of the commit algorithm in the git

1) First go and understand [Git Write-Tree Algorithm](https://github.com/shash-786/obsidian-backup/blob/main/VCS/Git%20Write-Tree%20Algorithm.md)
2) Now the bulk of the porcelain command `commit` is contained in a shell script --> `commit.sh`
3) This script is responsible for validation of few things to see if we can commit rn.
4) First We use the `write-tree` function to build a active-cache-tree now using the index file.
5) Then we use the `commit-tree` command (*plumbing*) to create a commit object and pass the Tree SHA1 from the previous step and also the Parent commit (if any) along with the commit message.
6) Now go understand the [Git Commit-Tree Algorithm](https://github.com/shash-786/obsidian-backup/blob/main/VCS/Git%20Commit-Tree%20Algorithm.md) :)
 