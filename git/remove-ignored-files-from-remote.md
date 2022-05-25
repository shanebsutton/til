# Remove File in .gitignore from Remote Repository

While doing some work with Terraform, I accidentally tracked and committed the config files that were generated from `terraform init`. These files were then pushed up to the remote repo.

I have since added a .gitignore file to exclude these going forward, but I need to remove them from the repo. I need to unstage them, create a new commit, and then push to the remote.

## Unstage the files

"Remove files from the working tree and from the index" with `git rm -r --cached` ( [docs](https://git-scm.com/docs/git-rm) )

```bash
PS C:\git\terraform\demo-iac> git rm -r --cached .terraform*
rm '.terraform.lock.hcl'
rm '.terraform/providers/registry.terraform.io/hashicorp/google/4.10.0/windows_amd64/terraform-provider-google_v4.10.0_x5.exe'
rm '.terraform/providers/registry.terraform.io/hashicorp/random/3.1.0/windows_amd64/terraform-provider-random_v3.1.0_x5.exe'
```

`-r` - recursive removal  
`--cached` - Only update the index. Working tree files will be left alone.

## Commit the changes locally

```bash
git commit -m 'Remove ignored files'
```

## Push changes to remote

```bash
git push
```

## References

- [git rm](https://git-scm.com/docs/git-rm)
- [Remove git files, directories in .gitignore from a remote repository](https://computingforgeeks.com/how-to-remove-git-files-directories-in-gitignore-from-a-remote-repository/)
