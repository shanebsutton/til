# Get Database File Size

Get file sizes of all databases:

```sql
SELECT database_id, DB_NAME(database_id) AS database_name,
    file_id, name, type_desc, physical_name, size/128 AS FileSizeMB
FROM sys.master_files
```

Get file sizes of the current database:

```sql
SELECT file_id, name, type_desc, physical_name, size/128 AS FileSizeMB
FROM sys.database_files;
```

Note that the `size` in both views is defined as the size of the file in 8 KB pages.

Therefore, size / 128 == size * 8 / 1024

## References

- [SQL Docs - sys.master_files](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql)
- [SQL Docs - sys.database_files](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
