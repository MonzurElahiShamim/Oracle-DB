---
updated_at: 2024-10-21T15:57:41.440+06:00
edited_seconds: 10
---
# Pre-configure system

### 1. Automatic (Should run in Terminal):  

```bash
yum install oracle-database-server-12cR2-preinstall -y
yum update -y
```

PS: this automatic step will make the server ready to install but for confirmation, use the manual steps. 

For deep knowledge of this script please refer to [https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ladbi/installing-the-oracle-preinstallation-rpm-from-unbreakable-linux-network.html](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ladbi/installing-the-oracle-preinstallation-rpm-from-unbreakable-linux-network.html) 

### 2. Manual:   

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

2. Creating Oracle directory:
```bash
vi /etc/oraInst.loc
```
Add following lines to oraInst.loc file
```
inventory_loc=/u01/oracle/oraInventory
inst_group=oinstall
```

1. Update configurations in the below files:
  - ! Kernel parameters should be configured automatically by preinstall script. 
  - To configure kernel parameters:
```bash
	vi /etc/sysctl.conf
```
(If not configured automatically) Please see this file for configuration info: [sysctl.conf](https://drive.google.com/file/d/1q5u8ycfwvfZzMzU9HJoCOBqGaMdZ7EZW/view?usp=drive_link)
  - Run `sysctl -p`

4. Prepare the Groups, Users and File System (if not created automatically):
```bash
groupadd oinstall
groupadd dba
adduser oracle
passwd oracle
usermod -g oinstall -G dba oracle
```

```bash 
 vi /etc/sysconfig/network
```
 
```
NOZEROCONF=yes
HOSTNAME=<Hostname>.<Domain>.com
```

5. Oracle DB Home Directory: 
```bash
chown -R oracle:oinstall /u01
chmod -R 775 /u01
```
