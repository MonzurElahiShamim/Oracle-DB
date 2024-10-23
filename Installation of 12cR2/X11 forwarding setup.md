---
updated_at: 2024-10-22T22:55:28.246+06:00
edited_seconds: 80
---
# Setting Up X11 Forwarding on Oracle Linux

1. **Check if `DISPLAY` is set**:
   ```bash
   echo $DISPLAY
   ```

2. **If `DISPLAY` is not set**, configure it:
   - Get your local machine's IP address:
     ```bash
     echo $SSH_CLIENT
     ```
     Example output:
     ```
     192.168.1.100 12345 22
     ```
     - Use the first IP (`192.168.1.100` in this case) as your local machine’s IP.

   - Set the `DISPLAY` variable on the **remote machine**:
     ```bash
     export DISPLAY=192.168.1.100:0.0
     echo 'export DISPLAY=192.168.1.100:0.0' >> ~/.bash_profile
     source ~/.bash_profile
     ```
     Replace `192.168.1.100` with your actual IP.

3. **Test X11 forwarding** by running an X application:
   ```bash
   xclock
   ```

   - If `xclock` is missing, install it with:
     ```bash
     sudo yum install xorg-x11-apps -y
     ```