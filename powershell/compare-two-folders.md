# Compare Two Folders with PowerShell

This is fairly simple, but not something I do too often. End up searching every time...

Borrowed directly from: https://devblogs.microsoft.com/scripting/easily-compare-two-folders-by-using-powershell/

```powershell
$ref = Get-ChildItem -Recurse -path C:\reference
$dif = Get-ChildItem -Recurse -path C:\difference
Compare-Object -ReferenceObject $ref -DifferenceObject $dif
```

## References

- [Ed Wilson blog post](https://devblogs.microsoft.com/scripting/easily-compare-two-folders-by-using-powershell/)
- [Compare-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/compare-object?view=powershell-7.3)
