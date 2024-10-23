---
updated_at: 2024-10-20T17:10:01.407+06:00
edited_seconds: 350
---
## Prerequisites
- Oracle 12c database should be up and running and it should be applied with latest patches
- Download latest 19c software's from oracle website.
- Also download latest Oracle Patches for 19c
- Start Installation of 19c software on separate mount point and apply latest patches which was downloaded earlier.
	- `./runInstaller -applyRU /u01/Q4_PATCH/32226239`
- At this stages two separate database software will be ready one is 12cR2 and other will be 19c on two different mount points
- Now will proceed further ...

---
---

## Executing the pre-upgrade jar tool

#### 1. **Pre-Upgrade Information Tool**
Oracle provides a **Pre-Upgrade Information Tool** to assess your database before upgrading. It checks compatibility, resource requirements, deprecated features, and necessary adjustments, generating scripts that must be executed before the actual upgrade.

The **pre-upgrade tool** is found in the new version's Oracle home (`ORACLE_HOME/rdbms/admin/preupgrade.jar`) and must be executed from the new Oracle 19c home.

#### 2. **Create Directory for Output**
Before running the tool, you need to create a directory where the output of the pre-upgrade process will be stored:

```bash
mkdir -p /u01/preupgrade
```

This command creates the directory `/u01/preupgrade`, where the output will be saved.

#### 3. **Run the Pre-Upgrade Tool**
To run the pre-upgrade tool, use the `java -jar` command to execute the jar file from the Oracle 19c home:

```bash
/u01/oracle/product/12.2.0/dbhome_1/jdk/bin/java -jar /u01/oracle/product/19c/dbhome_1/rdbms/admin/preupgrade.jar DIR /u01/preupgrade
```

- **Java Path**: You use the JDK from the existing Oracle 12c home to run the pre-upgrade jar file.
- **Pre-upgrade Tool Path**: The path of the tool in the Oracle 19c home directory.
- **DIR Option**: The `DIR` option specifies that the pre-upgrade output will be stored in the `/u01/preupgrade` directory.

#### 4. **Database Check (Optional)**
You can check the current state of the database using the following query:

```sql
SELECT name, open_mode, log_mode FROM v$database;
```

This gives you the name, open mode (READ WRITE or MOUNT), and log mode (ARCHIVELOG or NOARCHIVELOG) of the current database.

#### 5. **Run the Pre-Upgrade Fix-Up Script**
After running the pre-upgrade tool, it generates a SQL script, typically called `preupgrade_fixups.sql`, which contains actions necessary to prepare your database for the upgrade. These include adjustments to initialization parameters, fixing deprecated features, etc.

To apply these pre-upgrade fixes,
- Go to preupgrade directory : `cd /u01/preupgrade/`
- Login to database: `sqlplus / as sysdba`
- Execute the generated script:
```sql
@preupgrade_fixups.sql
```

- This script ensures that your database is ready for the upgrade by fixing issues identified by the pre-upgrade tool.


