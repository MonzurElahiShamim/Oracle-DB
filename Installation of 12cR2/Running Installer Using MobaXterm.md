---
updated_at: 2024-10-21T18:52:38.719+06:00
edited_seconds: 320
---
# Running Installer Using MobaXterm:

1. **Open MobaXterm** and log in as the `oracle` user.

2. **Check if the `DISPLAY` environment variable is set**:
   ```bash
   echo $DISPLAY
   ```

3. **If `DISPLAY` is set**, go to step 4

4. **If `DISPLAY` is not set**, manually set the `DISPLAY` variable:
   - First, get your local machine’s IP address:
     ```bash
     echo $SSH_CLIENT
     ```
     The output will look like this:
     ```
     192.168.1.100 12345 22
     ```
     - The first IP address (`192.168.1.100` in this example) is your local machine’s IP.
   
   - Set the `DISPLAY` variable on your **remote machine** (Oracle Linux 7.9):
     ```bash
     export DISPLAY=192.168.1.100:0.0
     echo 'export DISPLAY=192.168.1.100:0.0' >> ~/.bash_profile
     source ~/.bash_profile
     ```
     - Replace `192.168.1.1f00` with your actual IP address.

5. **Test if X11 forwarding is working** by running a simple X application:
   ```bash
   xclock
   ```
   - If `xclock` is not installed, install it as root or a sudo user:
     ```bash
     sudo yum install xorg-x11-apps -y
     ```

6. **Navigate to the Oracle installation directory**:
   ```bash
   cd /dump/database
   ```

7. **Run the Oracle Universal Installer**:
   ```bash
   ./runInstaller
   ```

---
---
> [!note] Next
> Go to next step >> [[Installation of Oracle DB 12cR2 Enterprise Edition#Running the Installation Wizard|Running the Installation Wizard]]
