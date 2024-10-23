---
updated_at: 2024-10-22T10:07:14.641+06:00
edited_seconds: 960
---

## System Configuration

| Infrastructure Type             | VMware ESXi           |
| -------------------------------- | --------------------- |
| Domain Name                      | waddaya.com           |
| Host IP Address                  | 10.12.12.210          |
| Host FQDN                        | oracleprd.waddaya.com |
| Operating System                 | Oracle Linux 7.9 (x86-64) |
| CPU, RAM, and Storage            | ? vCPU, ?GB RAM, ?GB Storage |

## 1. Directory Creation

- **Base Directory**: `/u01/app/oracle` --> (`$ORACLE_BASE`)
- **Oracle Home Directory**: `/u01/app/oracle/product/12.2.0/dbhome_1` --> (`$ORACLE_HOME`)
- **Oracle Inventory Location**: `/u01/app/oracle/oraInventory`

Create the necessary directories:
```bash
mkdir -p /u01/app/oracle/product/12.2.0/dbhome_1
mkdir -p /u01/app/oracle/oraInventory
```  

## 2. System Pre-Configuration

Refer to the note: [[Pre-configure system]].

```
export ORACLE_HOSTNAME=primary
export ORACLE_UNQNAME=ORCLPR
export ORACLE_BASE=/u01/app/oracle
export DB_HOME=$ORACLE_BASE/product/12.2.0.1/db_1
export ORACLE_HOME=$DB_HOME
export ORACLE_SID=prod
export ORACLE_TERM=xterm
export BASE_PATH=/usr/sbin:$PATH
export PATH=$ORACLE_HOME/bin:$BASE_PATH

export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/JRE:$0RACLE_HOME/jlib:$0RACLE_HOME/rdbms/jlib
```

> [!tip] Environment variables
> Add the following lines at the end of `.bash_profile` file of `oracle` user
> ```
> export ORACLE_BASE=/u01/app/oracle
> export ORACLE_HOME=$ORACLE_BASE/product/12.2.0.1/db_1
> export ORACLE_SID=prod
> export ORACLE_TERM=xterm
> export BASE_PATH=/usr/sbin:$PATH
> export PATH=$ORACLE_HOME/bin:$BASE_PATH
> 
> export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
> export CLASSPATH=$ORACLE_HOME/JRE:$0RACLE_HOME/jlib:$0RACLE_HOME/rdbms/jlib
> ``` 
> 
> ```bash
> source ~/.bash_profile
> ```


## 3. Download Oracle Software

Refer to the note: [[Download 12cR2 Software]].

## 4. Preparing for Installation (Extracting Files)

1. Create the `/dump` directory:
   ```bash
   mkdir /dump
   ```
2. Extract the downloaded ZIP files into `/dump` on your Oracle Linux VM as the root user:
   ```bash
   sudo unzip V839960-01.zip -d /dump
   ```
3. Change ownership to the `oracle` user:
   ```bash
   chown -R oracle:oinstall /dump/database/
   ```

## 5. Running the Oracle Universal Installer

### Using MobaXterm:
Refer to the note: [[Running Installer Using MobaXterm]].

### Using VNC Server:

For VNC server setup, refer to: [[VNC Server Setup]]. 

After setting up **VNC server**:
1. Open **VNC Viewer** and connect to the configured VNC server.
2. Open a terminal within the VNC Viewer.
3. Navigate to the database directory:
   ```bash
   cd /dump/database
   ```
4. Start the Oracle Universal Installer:
   ```bash
   ./runInstaller
   ```

## 6. Running the Installation Wizard:

1. **Configure Security Updates**: Uncheck the box and click "Next". If a warning appears, select “Yes”.
2. **Installation Option**: Choose "Install database software only" and click "Next".
3. **Database Installation Option**: Select “Single instance database installation” and click "Next".
4. **Database Edition**: Select the desired edition (e.g., Enterprise Edition) and click "Next".
5. **Installation Location**: Use `/u01/oracle` as the Oracle base.
6. **Operating System Group**: Click "Next".
7. **Prerequisite Check**: Address any warnings, especially related to soft checks or missing libraries.
8. **Install Product**: During this step, you will be prompted to run scripts as the `root` user.

## 7. Database Creation

To create a single instance database, refer to the note: [[Database Creation (single instance)]].

---
---
