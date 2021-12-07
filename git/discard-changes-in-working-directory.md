# Discard Changes in Working Directory

`git restore` discards unstaged changes in working directory files.

This is suggested by `git status`.

```bash
PS C:\git\TestRepo> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   file1.txt

no changes added to commit (use "git add" and/or "git commit -a")
PS C:\git\TestRepo> git restore file1.txt
PS C:\git\TestRepo> git status
On branch master
nothing to commit, working tree clean
```

## References

- [git restore](https://git-scm.com/docs/git-restore)
