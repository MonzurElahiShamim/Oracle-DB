---
updated_at: 2024-10-20T21:48:18.384+06:00
edited_seconds: 10
---
# Backing up the database / Create a guaranteed restore point

Check whether any existing restore point available:
```SQL
SELECT NAME,GUARANTEE_FLASHBACK_DATABASE,TIME FROM V$RESTORE_POINT;
```

Check whether recovery parameter is set properly:
```sql
SQL> show parameter recovery

NAME                                 TYPE        VALUE
------------------------------------ ----------- ---------------------------
db_recovery_file_dest                string      /u01/oracle/FRA
db_recovery_file_dest_size           big integer 8016M
recovery_parallelism                 integer     0
remote_recovery_file_dest            string
```

Shutdown the database:
```sql
shut immediate
```

Startup and mount:
```sql
STARTUP MOUNT
```

Enable Archivelog mode:
```sql
alter database archivelog;
```

Create restore point:
```sql
create restore point before_upgrade_19c guarantee flashback database;
```

Verify restore point:
```sql
select NAME,GUARANTEE_FLASHBACK_DATABASE,TIME FROM V$RESTORE_POINT;

NAME
---------------------------------------------------------------------------
GUA TIME
--- -----------------------------------------------------------------------
BEFORE_UPGRADE_19C
YES 20-OCT-24 08.42.59.000000000 PM
```
