# Working with Remote Repositories

I don't yet do this often enough to remember every time...

## Add a remote

From within the local repo...

```bash
git remote add origin git@github.com:shanebsutton/til.git
```

## List remotes

```bash
git remote -v
```

## Pull and push

From within local repo...

```bash
git pull origin master
```

```bash
git push origin master
```

## Set upstream (tracking) reference

Do this for up to date branches, so that you do not have to specify a remote every time.

```bash
git branch --set-upstream-to=origin master
```

Or...

```bash
git push --set-upstream origin master
```

Now, this should work...

```bash
git pull
```

## References

- [git remote](https://git-scm.com/docs/git-remote)
- [git push](https://git-scm.com/docs/git-push)
- [git branch](https://git-scm.com/docs/git-branch)
