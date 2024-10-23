---
updated_at: 2024-10-22T23:03:43.739+06:00
edited_seconds: 1320
---

# Installation of 12cR2 

```bash
sudo yum update -y
```

## Install the preinstall utility

```
sudo yum install oracle-database-server-12cR2-preinstall -y
```

This should configure all the necessary pre-configurations for software installation.

## Configure System Limits

1. Configure system limits (on terminal):  
```bash 
    vi /etc/security/limits.conf
```
add the following lines at the end of `limits.conf` file:
```text

oracle   soft   nofile    1024
oracle   hard   nofile    65536
oracle   soft   nproc    16384
oracle   hard   nproc    16384
oracle   soft   stack    10240
oracle   hard   stack    32768
oracle   hard   memlock    134217728
oracle   soft   memlock    134217728
```

## Creating Oracle directory:

Create directory
```bash
sudo mkdir -p /u01/app/oracle/product/12.2.0.1/dbhome_1
sudo mkdir -p /u01/app/oracle/oraInventory
```

Change Ownership
```bash
sudo chown -R oracle:oinstall /u01
```

Change Permission:
```bash
sudo chmod -R 775 /u01
```

Editing the Oracle Inventory Configuration File
```bash
sudo vi /etc/oraInst.loc
```
Add following lines to `oraInst.loc` file
```
inventory_loc=/u01/app/oracle/oraInventory
inst_group=oinstall
```

## Set Password for `oracle` user

```bash
sudo passwd oracle
```

## Set Environment Variables

Switch to oracle user:
```bash
su - oracle
```

Open `.bash_profile` in `vi` editor:
```bash
vi ~/.bash_profile
```

Add following lines at the end of the `.bash_profile` file:
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

```bash
source ~/.bash_profile
```

## Download Oracle Software

Refer to the note: [[Download 12cR2 Software]].

## Preparing for Installation (Extracting Files)

1. Create the `/dump` directory:
   ```bash
   mkdir /dump
   ```
2. Navigate to directory where downloaded software kept
	```bash
	cd /path/to/software/download dirctory
	```
3. Extract the downloaded ZIP files into `/dump` on your Oracle Linux VM as the root user:
   ```bash
   sudo unzip V839960-01.zip -d /dump
   ```
4. Change ownership to the `oracle` user:
   ```bash
   sudo chown -R oracle:oinstall /dump/database/
   ```

## Running the Oracle Universal Installer

Refer to the note: [[Running Installer Using MobaXterm]].

---
1. **Open MobaXterm** and log in as the `oracle` user.

2. **Navigate to the Oracle installation directory**:
   ```bash
   cd /dump/database
   ```

3. **Run the Oracle Universal Installer**:
   ```bash
   ./runInstaller
   ```

> [!warning] 
> ##### If GUI Installation wizard does not launch, you may need to configure X11 forwarding manually. You can find instructions [[X11 forwarding setup|here]].

## Running the Installation Wizard:

1. **Configure Security Updates**: Uncheck the box and click "Next". If a warning appears, select “Yes”.
2. **Installation Option**: Choose "Install database software only" and click "Next".
3. **Database Installation Option**: Select “Single instance database installation” and click "Next".
4. **Database Edition**: Select the desired edition (e.g., Enterprise Edition) and click "Next".
5. **Installation Location**: Use `/u01/oracle` as the Oracle base.
6. **Operating System Group**: Click "Next".
7. **Prerequisite Check**: Address any warnings, especially related to soft checks or missing libraries.
8. **Install Product**: During this step, you will be prompted to run scripts as the `root` user.
![[Oracle_DB_installation_root_script.png]]

## Database Creation

To create a single instance database, refer to the note: [[Database Creation (single instance)]].


