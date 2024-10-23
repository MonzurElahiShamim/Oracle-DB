---
updated_at: 2024-10-22T20:27:31.649+06:00
edited_seconds: 4440
---

# Oracle 12c to 19c manual upgrade steps

To check current database and version:
```bash
sqlplus / as sysdba
```

```SQL
SELECT INSTANCE_NUMBER, INSTANCE_NAME, VERSION, HOST_NAME
FROM v$instance;
```

```sql
SQL> !ps -ef | grep pmon
oracle   14387     1  0 13:23 ?        00:00:00 ora_pmon_prod
oracle   31997 17316  0 15:00 pts/3    00:00:00 /bin/bash -c ps -ef | grep pmon
oracle   31999 31997  0 15:00 pts/3    00:00:00 grep pmon
```

![[environment details.png]]

## Prerequisites 

- Oracle 12c database should be up and running and it should be applied with latest patchtes.
- Oracle 19c should be Installed also it should be applied with latest patches.

Create db.env (for 19c) file in oracle user home directory with following variables:
```
export ORACLE_BASE=/u01/app/oracle
export DB_HOME=$ORACLE_BASE/product/19c/dbhome_1
export ORACLE_SID=prod
export ORACLE_HOME=/u01/app/oracle/product/19c/dbhome_1
export ORACLE_TERM=xterm
export BASE_PATH=/usr/sbin:$PATH
export PATH=$ORACLE_HOME/bin:$BASE_PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
```

## Install 19c with latest patches

> Follow this note: [[Oracle 19c Installation Guide]]

- After installation confirm software version : `sqlplus -v`
- Confirm 12c is up and running: `ps -ef | grep pmon`

---
---

## Executing the pre-upgrade jar tool

 > Follow this note:  [[Executing the pre-upgrade jar tool]]
 
---
---
## Backing up the database / Create a guaranteed restore point

> Follow this note: [[Backing up the database (Create a guaranteed restore point)]]

---
---
# Upgrading Database

```bash
sqlplus / as sysdba
```

```sql
shutdown immediate;
exit
```

Stop listener running on 12c home and start it from 19c home
```bash
lsnrctl stop
```

Copy the spfile and password file from old ORACLE_HOME to new 19c ORACLE_HOME
```bash
cd $ORACLE_HOME/dbs

ls -lrt

cp spfileprod.ora orapwprod /u01/oracle/product/19c/dbhome_1/dbs

ls /u01/oracle/product/19c/dbhome_1/dbs

# init.ora  orapwprod  spfileprod.ora
```

Copy listener file from old ORACLE_HOME to new 19c ORACLE_HOME
```bash
cd ../network/admin/ 
# or use: 
# cd $ORACLE_HOME/network/admin/

ls
# listener.ora  samples  shrept.lst

cp listener.ora /u01/oracle/product/19c/dbhome_1/network/admin/
```

Set environment variables for 19c:
```bash
cd
. db.env
```

Start the database from 19c ORACLE_HOME and start the upgrade.
```bash
sqlplus / as sysdba
```

```sql
startup upgrade
```

```SQL
SELECT INSTANCE_NUMBER, INSTANCE_NAME, STATUS, NAME AS DB_NAME, VERSION, OPEN_MODE, LOG_MODE FROM V$INSTANCE, V$DATABASE;

exit
```

```bash
cd $ORACLE_HOME/bin

./dbupgrade -n 8 # with 8 parallel process
```

It may take several minutes depending upon hardware configuration to complete the upgradation.

---
## Post upgrade configurations

After Completing the upgrade,

#### 1. Startup database manually:
```bash
sqlplus / as sysdba
```

```sql
STARTUP 

SELECT INSTANCE_NUMBER, INSTANCE_NAME, STATUS, NAME AS DB_NAME, VERSION, OPEN_MODE, LOG_MODE FROM V$INSTANCE, V$DATABASE;
```

 
#### 2. Execute Post-Upgrade Status Tool, utlusts.sql
```sql
@$ORACLE_HOME/rdbms/admin/utlusts.sql TEXT
```

#### 3. Recompile the INVALID Objects using utlrp.sql
```sql
@$ORACLE_HOME/rdbms/admin/utlrp.sql
```

#### 4. Re-execute Post-Upgrade Status Tool, utlusts.sql
```sql
@$ORACLE_HOME/rdbms/admin/utlusts.sql TEXT
```

#### 5. Run post upgrade fixups:
```sql
@/u01/preupgrade/postupgrade_fixups.sql
```

#### 6. Upgrade the timezone file version

```sql
SELECT version FROM v$timezone_file;

@$ORACLE_HOME/rdbms/admin/utltz_countstats.sql;

@$ORACLE_HOME/rdbms/admin/utltz_countstar.sql;

@$ORACLE_HOME/rdbms/admin/utltz_upg_check.sql;

@$ORACLE_HOME/rdbms/admin/utltz_upg_apply.sql;

SELECT version FROM v$timezone_file;
```

#### 7. Performing the post-upgrade actions

Gather statistics on fixed object
```sql
EXECUTE DBMS_STATS.GATHER_FIXED_OBJECTS_STATS;
```

Now re-run the postupgrade_fixups.sql
```sql
@/u01/preupgrade/postupgrade_fixups.sql
```

#### 8. Drop the restore point

Check the restore point
```sql
select name from v$restore_point;
```

Drop the restore point
```sql
drop restore point BEFORE_UPGRADE_19C;
```

Confirm the drop operation
```sql
select name from v$restore_point;
```

#### 9. Modify `compatible` parameter

Check `compatible` parameter status:
```sql
show parameter compatible
```

Update `compatible` parameter:
```sql
alter system set compatible='19.0.0' scope=spfile;
```

Confirm update:
```sql
shutdown immediate

STARTUP

show parameter compatible
```

---
---
```sql
set lines 200 pages 200
col COMMENTS for a30
col ACTION for a15
select * from dba_registry_history;
```

