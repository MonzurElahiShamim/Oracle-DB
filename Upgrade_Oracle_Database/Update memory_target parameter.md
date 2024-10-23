---
updated_at: 2024-10-20T19:15:07.591+06:00
edited_seconds: 310
---
# Resolving the memory_target Issue for Oracle Upgrade

To address the `memory_target` issue based on the pre-upgrade recommendations, follow these steps to update the parameter to meet the minimum requirement for Oracle 19c.

### Steps to Update memory_target Parameter:

1. **Connect to the Database as SYSDBA:**
   Open SQL*Plus and connect to your database as SYSDBA:
   ```bash
   sqlplus / as sysdba
   ```

2. **Check Current Value of memory_target:**
   Query the current value of the `memory_target` parameter:
   ```sql
   SHOW PARAMETER memory_target;
   ```

3. **Update the memory_target Parameter:**
   Since the recommended minimum value for `memory_target` is 1203765248 (around 1.2 GB), update it using the `ALTER SYSTEM` command with the `SCOPE=SPFILE` option so the change persists after a restart:
   ```sql
   ALTER SYSTEM SET memory_target = 1203765248 SCOPE=SPFILE;
   ```

4. **Restart the Database:**
   For the changes to take effect, restart the Oracle instance:
   ```sql
   SHUTDOWN IMMEDIATE;
   STARTUP;
   ```

While STARTUP you may face following Error:

### Resolving ORA-00845: MEMORY_TARGET not supported on this system

If you encounter the error `ORA-00845: MEMORY_TARGET not supported on this system`, it indicates that your system does not have enough shared memory (`/dev/shm`) allocated to support the `memory_target` value you set. 

#### Steps to Resolve the Issue:

1. **Check Current /dev/shm Size:**
   On Linux systems, `/dev/shm` is the shared memory filesystem. Check its size by running:
   ```bash
   df -h /dev/shm
   ```

1. **Increase /dev/shm Size:**
   To increase the size of `/dev/shm`: 
	- Edit the `/etc/fstab` file to modify the shared memory size. 
	```bash
	 sudo nano /etc/fstab
	```
	- Add or modify the following line:
	```text
	tmpfs /dev/shm tmpfs defaults,size=2G 0 0
	```
	This sets the shared memory size to 2 GB. Adjust the size as needed (it should be larger than the `memory_target` value).

3. **Remount /dev/shm to Apply the New Size:**
   Remount `/dev/shm` to apply the new size:
   ```bash
   sudo mount -o remount /dev/shm
   ```

4. **Verify the New /dev/shm Size:**
   Check the size again to confirm the change:
   ```bash
   df -h /dev/shm
   ```

1. **Restart the Oracle Database:**
   Now try starting the database again:
```bash
	sqlplus / as sysdba
```

   ```sql
   STARTUP;
   ```

6. **Verify the Change:**
   After restarting, verify that the parameter has been updated to the required value:
   ```sql
   SHOW PARAMETER memory_target;
   ```
