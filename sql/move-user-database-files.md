# Move User Database Files

We occasionally need to move the physical database files to a new location, when adding a disk for example.  This is a straightforward process, but it does require setting the database offline.

```sql
-- Get logical name of database files
SELECT type_desc, name, physical_name, state_desc
FROM sys.master_files
WHERE database_id = DB_ID('database_name')

-- Set database offline
ALTER DATABASE database_name SET OFFLINE;
```

Move the file(s) to the new location.

```sql
-- Update the database metadata for each moved file
ALTER DATABASE database_name MODIFY FILE (NAME = 'logical_name', FILENAME = 'new_path\os_file_name')

-- Set database online
ALTER DATABASE database_name SET ONLINE;

-- Verify the change
SELECT type_desc, name, physical_name, state_desc
FROM sys.master_files
WHERE database_id = DB_ID('database_name')
```

## References

- [SQL Docs - Move User Databases](https://docs.microsoft.com/en-us/sql/relational-databases/databases/move-user-databases)
