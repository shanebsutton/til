# Remove Untracked Files With Clean

`git clean` simply removes untracked files from the working directory.

Depending on the Git config variable `clean.requireForce`, you will likely have to add the `--force` or `-f` option.

```bash
git clean -f
```

If there is any concern about what will be removed, use the `--dry-run` or `-n` option to preview the changes.
You can also use the `--interactive` or `-i` option to preview the changes and then decide how to proceed.

```bash
git clean -n
git clean -i
```

> **NOTE** `git clean` will not remove ignored files by default.  Use the `-x` option to remove these.

## In Action

```bash
PS C:\git\TestRepo> git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file2.txt

PS C:\git\TestRepo> git clean --dry-run
Would remove file2.txt
PS C:\git\TestRepo> git clean -i
Would remove the following item:
  file2.txt
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers
    4: ask each             5: quit                 6: help
What now> 1
Removing file2.txt
PS C:\git\TestRepo> git status
On branch master
nothing to commit, working tree clean
```

## References

- [git clean](https://git-scm.com/docs/git-clean)
