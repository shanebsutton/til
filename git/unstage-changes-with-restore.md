# Unstage Changes With Restore

`git restore --staged` unstages changes from the index.

This is suggested by `git status`.

```bash
*After updating a file*
PS C:\git\TestRepo> git add .
PS C:\git\TestRepo> git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file1.txt

PS C:\git\TestRepo> git restore --staged .
PS C:\git\TestRepo> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file1.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

## References

- [git restore](https://git-scm.com/docs/git-restore)
