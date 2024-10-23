---
updated_at: 2024-10-23T20:10:51.664+06:00
edited_seconds: 230
---
## Executing the pre-upgrade jar tool

The Pre-upgrade Information Tool is available in the new release Oracle home, in path ORACLE_HOME/rdbms/admin/preupgrade.jar
- `mkdir -p /u01/preupgrade`
- make sure database is up and running in read-write mode
	- `sqlplus / as sysdba`
	- `STARTUP`
	- `SELECT open_mode FROM v$database;`


Run the pre-upgrade tool
```
/u01/oracle/product/12.2.0/dbhome_1/jdk/bin/java -jar /u01/oracle/product/19c/dbhome_1/rdbms/admin/preupgrade.jar DIR /u01/preupgrade
```

Navigate to `/u01/preupgrade` directory and run sqlplus:
```bash
cd /u01/preupgrade
sqlplus / as sysdba
```

```sql
select name,open_mode,log_mode from v$database;
```

```sql
@preupgrade_fixups.sql
```


> [!warning] Result 1
> 
> | **Preup Action Number** | **Preupgrade Check Name** | **Issue Is Remedied** | **Further DBA Action**                          |
> | ----------------------- | ------------------------- | --------------------- | ----------------------------------------------- |
> | 1                       | parameter_min_val         | NO                    | Manual fixup recommended.                       |
> | 2                       | dictionary_stats          | YES                   | None.                                           |
> | 3                       | pre_fixed_objects         | YES                   | None.                                           |
> | 4                       | tablespaces_info          | NO                    | Informational only. Further action is optional. |
> | 5                       | rman_recovery_version     | NO                    | Informational only. Further action is optional. |


See the `preupgrade.log` file to get the detailed log:

```sql
SQL> !cat preupgrade.log
```

---

![[recommended_preupgrade_actions.png]]

To [Update memory_target parameter](Update%20memory_target%20parameter.md) follow this link.

---

![[required_tablespace_allocations.png]]

```sql
SQL> select file_name from dba_data_files;

FILE_NAME
----------------------------------------------------------------------------
/u01/app/oradata/PROD/datafile/o1_mf_system_mk997wkq_.dbf
/u01/app/oradata/PROD/datafile/o1_mf_sysaux_mk999btq_.dbf
/u01/app/oradata/PROD/datafile/o1_mf_users_mk99b7x6_.dbf
/u01/app/oradata/PROD/datafile/o1_mf_undotbs1_mk99b4k8_.dbf
```

Resize SYSTEM datafile to 1GB
```sql
alter database datafile '/u01/app/oradata/PROD/datafile/o1_mf_system_mk997wkq_.dbf' resize 1g;
```

Resize SYSAUX datafile to 600MB
```sql
alter database datafile '/u01/app/oradata/PROD/datafile/o1_mf_sysaux_mk999btq_.dbf' resize 600m;
```

Resize UNDOTBS1 to 500MB
```sql
alter database datafile '/u01/app/oradata/PROD/datafile/o1_mf_undotbs1_mk99b4k8_.dbf' resize 500m;
```

```sql
select file_name from dba_temp_files;

FILE_NAME
---------------------------------------------------------------------------
/u01/app/oradata/PROD/datafile/o1_mf_temp_mk99fs7z_.tmp
```

Resize TEMP datafile to 200MB
```sql
alter database tempfile '/u01/app/oradata/PROD/datafile/o1_mf_temp_mk99fs7z_.tmp' resize 200m;
```

