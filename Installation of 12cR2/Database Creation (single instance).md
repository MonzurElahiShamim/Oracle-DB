---
updated_at: 2024-10-21T21:05:09.521+06:00
edited_seconds: 360
---
# Database Creation and Configuration Steps (Single Instance)

Before launching `dbca` create the following directories:
- **Fast Recovery Area**: `/u01/app/oracle/FRA`
- **Database Files Location**: `/u01/app/oradata`
```bash
mkdir -p /u01/app/oracle/FRA
mkdir -p /u01/app/oradata
```
#### 1. **Launch Database Configuration Assistant (DBCA)**

- Log in as the `oracle` user in **MobaXterm** ***OR*** open a terminal in the **VNC viewer** app.
- Run the following command to start the Database Configuration Assistant (DBCA):
  ```bash
  dbca
  ```
- If you see a "command not found" error, restart the database server and try running the command again also check `Oracle Environment Variables` are set properly (especially `$PATH`).

#### 2. **Database Creation:**
   DBCA will guide you through 14 steps for database creation. Follow the instructions below:

   - **Step 1: Database Operation**  
     Select "Create a database" and click **Next**.

   - **Step 2: Creation Mode**  
     Select "Advanced Configuration" and click **Next**.  
     - **Deployment Type**: Choose "Oracle Single Instance database."  
     - **Database Type**: Select "General Purpose or Transaction Processing."

   - **Step 3: Database Identification**  
     - **Global Database Name**: Enter `prod`.  
     - **SID**: Enter `prod`.  
     - Uncheck "Create a Container database with one or more PDBs" and click **Next**.

   - **Step 4: Storage Option**  
     - Select "Use the following for the database storage attributes."  
     - **Database files storage type**: Choose "File System."  
     - **Database files location**: Enter `/u01/app/oradata` for the database file location.  
     - Check "Use Oracle-Managed Files (OMF)" and click **Next**.

   - **Step 5: Fast Recovery Option**  
     - Select "Specify Fast Recovery Area (FRA)."  
     - **FRA Location**: Enter `/u01/oracle/FRA`.  
     - Click **Next**.

   - **Step 6: Network Configuration**  
     This part configures the listener, which you will set up later. Skip for now by clicking **Next**.

   - **Step 7: Data Vault Option**  
     Skip this step and click **Next**.

   - **Step 8: Memory Configuration (SGA & PGA)**  
     Configure the memory options:  
     - **SGA Size**: ~3GB.  
     - **PGA Size**: <1GB.  
     - Note: The PGA is proportional to the SGA, so setting the PGA will automatically adjust the SGA. Click **Next**.

   - **Step 9: Management Options**  
     Uncheck "Configure Enterprise Manager (EM) database express" and click **Next**.

   - **Step 10: User Credentials**  
     Select "Use the same administrator password for all accounts." Set a strong password for the SYS account and click **Next**.

   - **Step 11: Creation Option**  
     Choose "Create database" and click **Next**.

   - **Step 12: Summary**  
     Review the settings you've configured so far. If everything is correct, click **Finish** to start the database creation process.

   - **Step 13: Progress Page**  
     Monitor the progress of the database creation. You'll see DBCA logs and alert logs listed at the bottom of the page.  
     - **DBCA Log Location**: `/u01/oracle/cfgtoollogs/dbca/prod/trace.<timestamp>`  
     - **Alert Log Location**: `/u01/oracle/diag/rdbms/prod/prod/trace/alert<timestamp>`

#### 3. **Check Database Services**

After the database is created, verify the database services are running:

```bash
ps -ef | grep pmon
```

You should see output like `ora_pmon_prod`, indicating the process monitor for your database is active.

---

### Listener Configuration (Local)
After creating the database, configure the listener:
#### 1. **Run Network Manager (netmgr)**

In the terminal, run:
```bash
netmgr
```
A graphical utility will open.

#### 2. **Configure Listener**
1. In the **Oracle Net Configuration** window, go to **Local** → **Listeners**.
2. Click the **+** button to add a new listener.
3. **Enter Listener Name**: `Listener`.
4. **Listening Locations**: Add an address if needed.
5. **Database Services**:
   - **Global Database Name**: `prod`.
   - **Oracle Home Directory**: `/u01/app/oracle/product/12.2.0/dbhome_1`.
   - **SID**: `prod`.
6. **Save Configuration**: Go to the top left corner and click **File** → **Save Network Configuration**.
7. Close the GUI.

#### 3. **Start the Listener**
After saving the configuration, start the listener:
```bash
lsnrctl start
```

---

### Validation Steps

#### 1. **Verify Database Access**

To verify the database is running properly, connect using SQL*Plus:

```bash
sqlplus / as sysdba
```

Once inside SQLPlus, you can run queries to ensure the database is functioning as expected.

You can run the following query:
```sql
select instance_number, instance_name, name as db_name, version from v$instance, v$database;
```
