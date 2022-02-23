# Verify SQL Server Management Studio Installation

To check for a current installation of SSMS, you can query the registry.  This specific path applies to v18.xx.

Using PowerShell:

```powershell
> $path = "Registry::HKLM\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Management Studio"
> Test-Path -Path $path
True

> $path = "Registry::HKLM\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server Management Studio\18"
> Test-Path $path
True

> Get-ItemProperty -Path $path | Select-Object -ExpandProperty 'Version'
15.0.18390.0
```

## References

- [Found the registry info on a semi-related GitHub issue...](https://github.com/MicrosoftDocs/sql-docs/issues/2026)
