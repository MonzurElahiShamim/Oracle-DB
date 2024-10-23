---
updated_at: 2024-10-23T20:07:52.830+06:00
edited_seconds: 1200
---

# Oracle 19c Installation Guide

- open mobaXterm
- login as `root` user
- install 19c preinstall utility: `sudo yum install -y oracle-database-preinstall-19c`
- create groups `oinstall` (for software ownership) and `dba` (for database ownership).
	- `groupadd oinstall`
	- `groupadd dba`
- add one OS user and set password
	- `useradd -g oinstall -G dba oracle `
	- `passwd oracle`
- Create necessary directories
	- `mkdir -p /u01/app/oracle` stores configurations, logfiles and diagnostics info.
	- `mkdir -p /u01/app/oralce/product/19.3.0/dbhome_1` stores actual software.
	- `mkdir -p /u01/app/oraInventory` store software installation path not the actual software.
- Unzip the downloaded software inside the `ORACLE_HOME` directory
	- `unzip /path/to/downloaded/software.zip -d /u01/app/oracle/product/19.3.0/dbhome_1`
- Change the ownership
	- `chown -R oralce:oinstall /u01/app/oracle`
	- `chown -R oralce:oinstall /u01/app/oraInventory`
- Change permissions 
	- `chmod -R 775 /u01/app/oracle`
	- `chmod -R 775 /u01/app/oraInventory`
- Login as oracle user using mobaXterm
	- `ssh oracle@your_ip`
- Run the Installer
	- `/u01/app/oracle/product/19.3.0/dbhome_1/runInstaller`
	- ! To install with latest patches
		- `./runInstaller -applyRU /u01/Q4_PATCH/32226239`
- Follow the GUI Installer instructions to install the software
- 
