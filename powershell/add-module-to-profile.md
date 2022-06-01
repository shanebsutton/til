# Add Module to PowerShell Profile

These are the steps taken to add a module to my PowerShell profile.

A simple custom module had been created by a co-worker to interact with the Puppet Enterprise API. The code was saved in our company GitLab repo.

To use, I needed to download the code and save it in a PowerShell module path. These can be found in the `$env:PSModulePath` environment variable.  
I chose the CurrentUser scope location that corresponeded to my `$PROFILE`.

Since we use OneDrive, the location was: `C:\Users\ssutton\OneDrive - Aptos\Documents\WindowsPowerShell\Modules`

The `.psm1` script was in a larger repo, so I created the module folder and downloaded the script to this folder.

I then needed to create a profile:

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

This will create a `Microsoft.PowerShell_profile.ps1` file. Edit this file to import common modules, set the startup working directory, etc.

To import this specific module, add the following to the profile file:

```powershell
Import-Module PEServer
```

Since this file was downloaded from gitlab.com I needed to unblock it. This was a one time, manual step for this script.

```powershell
Get-ChildItem -Path 'C:\Users\ssutton\OneDrive - Aptos\Documents\WindowsPowerShell\Modules\PEServer' -Recurse | Unblock-File
```

I'm sure there are ways to take care of this going forward, perhaps including the unblock command in the profile file or changing the execution policy, but time is short at the moment...

## References

- [about_Profiles](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.2)
- [about_PSModulePath](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_psmodulepath?view=powershell-7.2)
- [Unblock Files in Folder](https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-unblock-files-in-folder/)
