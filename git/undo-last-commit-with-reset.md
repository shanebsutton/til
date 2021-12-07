# Undo Last Commit With Reset

Occasionally you need to undo your last local commit before pushing to a remote.

This can be done with `git reset` and one of several modes.  Technically `git reset` will "reset current HEAD to the specified state".  `HEAD~1` is used to designate one commit before the current `HEAD`.

## Retain changes

`--soft` will remove the last commit and leave any changes in place.

The index and working directory are not touched.  Changes will be staged (or "Changes to be committed" from `git status`).

```bash
git reset --soft HEAD~1
```

## Discard changes

`--hard` will remove the last commit AND discard all changes in the index and working directory.

It "resets the index and working tree".

```bash
git reset --hard HEAD~1
```

## Retain changes in working directory but not index

`--mixed` will remove the last commit, keep the changes in the working directory, but not the index.

`git status` will not show "Changes to be committed", but rather "Changes not staged for commit" or "Untracked files".

```bash
git reset --mixed HEAD~1
```

## References

- [git reset](https://git-scm.com/docs/git-reset)
- [devconnected blog](https://devconnected.com/how-to-undo-last-git-commit/)
