# Prune Local Branches

After a branch has been merged and deleted from the remote, the local copy can be removed.

`git remote prune <name>` will delete stale remote-tracking branches under `<name>`. Use `--dry-run` to see what would be pruned.

Afterwards, you can remove local branches with `git branch -d <branch_name>`.

```bash
PS C:\git\DBA\checkdb> git branch -a
  local-ssd
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/actifio-test
  remotes/origin/local-ssd
  remotes/origin/main
PS C:\git\DBA\checkdb> git remote prune origin --dry-run
Pruning origin
URL: git@gitlab.com:xxxx/application/dba/checkdb.git
 * [would prune] origin/local-ssd
PS C:\git\DBA\checkdb> git remote prune origin
Pruning origin
URL: git@gitlab.com:xxxx/application/dba/checkdb.git
 * [pruned] origin/local-ssd
 PS C:\git\DBA\checkdb> git branch -d local-ssd
Deleted branch local-ssd (was 68f2d8a).
PS C:\git\DBA\checkdb> git branch -a
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/actifio-test
  remotes/origin/main
```

## References

- [git remote prune](https://git-scm.com/docs/git-remote#Documentation/git-remote.txt-empruneem)
- [git branch -d](https://git-scm.com/docs/git-branch#Documentation/git-branch.txt--d)
