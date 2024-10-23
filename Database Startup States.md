---
updated_at: 2024-10-23T13:13:16.078+06:00
edited_seconds: 30
---
# Oracle Database Startup States

Oracle Database goes through several distinct states during the startup process, each with specific use cases and purposes. Understanding these states is crucial for database administrators, especially when performing tasks like backups, recovery, or maintenance. Below is a detailed explanation of each startup state, its use cases, commands, and how they relate to different operations.

---

### 1. **SHUTDOWN**
   **Description**: 
   - In this state, the Oracle instance is not running. The memory structures (SGA), background processes, and database connections are all terminated.
   - The data files, control files, and redo log files are closed, and users cannot access the database.

   **Use Cases**:
   - **Database Maintenance**: Before hardware upgrades, operating system patches, or Oracle software upgrades.
   - **Cold Backup**: Required when performing a cold backup (a backup taken when the database is fully shut down).
   - **Error Recovery**: Used to resolve certain critical errors by restarting the database.

   **Shutdown Commands**:
   - **SHUTDOWN NORMAL**: Waits for all connected users to disconnect before shutting down.
   - **SHUTDOWN IMMEDIATE**: Immediately disconnects users and shuts down without waiting for user activity to finish.
   - **SHUTDOWN ABORT**: Forces an immediate shutdown, without gracefully stopping the database.

   **Example**:
   ```sql
   SHUTDOWN IMMEDIATE;
   ```

---

### 2. **NOMOUNT**
   **Description**:
   - In this state, the Oracle instance starts, which includes initializing the System Global Area (SGA) and starting background processes (PMON, SMON, DBWn, etc.). However, the control file is not yet opened, and the database is not accessible.
   - **NOMOUNT** is typically used for specific administrative tasks that do not require database access.

   **Use Cases**:
   - **Database Creation**: When creating a new database, Oracle starts in NOMOUNT mode to configure the initial settings before the control file is created.
   - **Control File Recreation**: Used when re-creating or recovering the control file during a recovery process.
   - **Backup and Restore Operations**: Required by RMAN for specific operations like restoring control files.

   **Command**:
   ```sql
   STARTUP NOMOUNT;
   ```

---

### 3. **MOUNT**
   **Description**:
   - In the MOUNT state, Oracle reads the control file to locate and identify data files and redo log files, but the database is not yet open for access.
   - The MOUNT state is used for various maintenance and recovery tasks that do not require full database access.

   **Use Cases**:
   - **Database Recovery**: Required for recovering a database from backup or performing media recovery.
   - **Renaming or Moving Data Files**: You can rename or move data files, redo log files, and control files in the MOUNT state.
   - **Changing Database Modes**: Needed when changing the database from ARCHIVELOG to NOARCHIVELOG mode (or vice versa).
   - **Backup Operations**: Sometimes used in offline or partial backups.

   **Command**:
   ```sql
   STARTUP MOUNT;
   ```

---

### 4. **OPEN**
   **Description**:
   - In this state, the database is fully operational. Oracle opens all data files, redo log files, and allows user connections. The database is available for both read and write operations.
   - This is the normal state for a database in use by applications and users.

   **Use Cases**:
   - **Normal Operations**: General use for day-to-day transactions, querying, and administrative tasks.
   - **Database Read/Write Access**: Users and applications connect and perform read and write operations in this state.
   - **Administrative Tasks**: Most administrative tasks such as user management, schema changes, or tuning can be done when the database is open.

   **Command**:
   ```sql
   STARTUP;
   ```

   - If the database was in the MOUNT state, it can be opened with:
     ```sql
     ALTER DATABASE OPEN;
     ```

---

### 5. **RESTRICTED**
   **Description**:
   - The database is open, but only users with special administrative privileges can connect. Normal users are restricted from accessing the database.
   - **RESTRICTED** mode is commonly used for administrative tasks where the DBA needs exclusive access to the database without user interference.

   **Use Cases**:
   - **Database Maintenance**: For operations like upgrading, data migration, or running diagnostics where only DBAs should be allowed access.
   - **Schema Changes**: Useful for performing critical schema modifications or data cleanup without user access.
   - **Performance Tuning**: Can be used to perform tuning or load testing without external interference.

   **Command**:
   ```sql
   ALTER SYSTEM ENABLE RESTRICTED SESSION;
   ```
   - To disable restricted access:
     ```sql
     ALTER SYSTEM DISABLE RESTRICTED SESSION;
     ```

---

### 6. **ARCHIVELOG vs. NOARCHIVELOG Modes**
   While not strictly a startup state, these modes dictate how the Oracle database handles redo log files and are often configured while the database is in the MOUNT state.

   - **ARCHIVELOG Mode**: Oracle archives redo log files, allowing recovery to any point in time.
   - **NOARCHIVELOG Mode**: Redo log files are overwritten, limiting recovery options.

   **Use Case for Changing Modes**:
   - Switching from `NOARCHIVELOG` to `ARCHIVELOG` mode is usually done for production databases that need point-in-time recovery.

   **Command** (from the MOUNT state):
   ```sql
   ALTER DATABASE ARCHIVELOG;
   ALTER DATABASE OPEN;
   ```

---

### General Startup Commands Overview

- **Starting the Database with Default Settings**:
   ```sql
   STARTUP;
   ```

- **Starting the Database in NOMOUNT Mode**:
   ```sql
   STARTUP NOMOUNT;
   ```

- **Starting the Database in MOUNT Mode**:
   ```sql
   STARTUP MOUNT;
   ```

- **Opening a Mounted Database**:
   ```sql
   ALTER DATABASE OPEN;
   ```

---

### Typical Startup Sequence
1. **NOMOUNT**: The Oracle instance is started, but no database is accessed.
   ```sql
   STARTUP NOMOUNT;
   ```

2. **MOUNT**: The control files are read, but the database is not open for user access.
   ```sql
   STARTUP MOUNT;
   ```

3. **OPEN**: The database is fully opened for users to connect and perform transactions.
   ```sql
   ALTER DATABASE OPEN;
   ```

---

### Conclusion

Oracle Database states are essential for various database operations, from routine tasks to complex recovery operations. Understanding these startup states helps DBAs efficiently manage the database and perform maintenance, recovery, or upgrades without causing unnecessary downtime or disruption.

