Born2beRoot
This repository contains my implementation of the Born2beRoot project from 42 School. The project is designed to introduce virtualization and system administration while ensuring a secure server setup. Follow the steps below to replicate the setup and understand the key configurations.

Project Overview
Virtualization: Set up a virtual machine using VirtualBox or UTM.
Operating System: Use the latest stable version of Debian (recommended for beginners) or Rocky Linux.
Security: Implement strict firewall, password, and user management policies.
Monitoring: Deploy a monitoring.sh script to display essential system statistics.
Setup Instructions
1. Prerequisites
Install VirtualBox or UTM.
bash
Copy code
sudo apt install virtualbox
Download the latest stable version of Debian or Rocky Linux.
Debian ISO
Rocky Linux ISO
2. Virtual Machine Configuration
Create a virtual machine with the following settings:

RAM: At least 1024 MB
Disk: Minimum 10 GB
Network: NAT with port forwarding (Port 4242 → Port 22 on the VM)
Partitioning Example:

/boot: 512 MB
LVM Encrypted Partitions:
/: 8 GB
swap: 1 GB
3. System Setup
Hostname Configuration
Set your hostname to <your-login>42:
bash
Copy code
sudo hostnamectl set-hostname yourlogin42
User and Group Management
Create a new user and assign groups:

bash
Copy code
sudo adduser yourlogin
sudo usermod -aG sudo yourlogin
sudo groupadd user42
sudo usermod -aG user42 yourlogin
Enforce a strong password policy by editing /etc/security/pwquality.conf:

bash
Copy code
minlen = 10
minclass = 3
maxrepeat = 3
Sudo Configuration
Limit password attempts and add custom messages:
Edit /etc/sudoers.d/custom:
bash
Copy code
Defaults passwd_tries=3
Defaults badpass_message="Access Denied: Incorrect Password"
Defaults logfile="/var/log/sudo/sudo.log"
Defaults requiretty
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
4. Firewall Configuration
Install and enable UFW (for Debian):

bash
Copy code
sudo apt install ufw
sudo ufw allow 4242
sudo ufw enable
For Rocky Linux, use Firewalld:

bash
Copy code
sudo yum install firewalld
sudo firewall-cmd --permanent --add-port=4242/tcp
sudo firewall-cmd --reload
5. Monitoring Script
Create a monitoring.sh script:

bash
Copy code
nano monitoring.sh
Example content:

bash
Copy code
#!/bin/bash
echo "#Architecture: $(uname -a)"
echo "#CPU physical: $(lscpu | grep 'Socket' | awk '{print $2}')"
echo "#vCPU: $(nproc)"
echo "#Memory Usage: $(free -m | awk '/Mem:/ {print $3}')MB / $(free -m | awk '/Mem:/ {print $2}')MB"
echo "#Disk Usage: $(df -h / | awk 'NR==2 {print $3}') / $(df -h / | awk 'NR==2 {print $2}')"
echo "#Last boot: $(who -b | awk '{print $3, $4}')"
echo "#LVM use: $(lsblk | grep lvm | wc -l)"
Make the script executable:

bash
Copy code
chmod +x monitoring.sh
Schedule it to run every 10 minutes:

bash
Copy code
sudo crontab -e
# Add the following line:
*/10 * * * * wall $(/path/to/monitoring.sh)
Common Commands
Check Partition Configuration:
bash
Copy code
lsblk
List Open Ports:
bash
Copy code
sudo netstat -tuln
Verify SSH Configuration:
bash
Copy code
sudo systemctl status ssh
Get Machine Signature:
bash
Copy code
sha1sum /path/to/your.vdi
Bonus Features
Set up a WordPress website using:
lighttpd, MariaDB, and PHP.
Add a custom service of your choice.
Customize firewall rules to support additional services.
License
This repository is intended for educational purposes as part of the 42 School curriculum. Contributions and suggestions are welcome!

This README combines commands, configuration examples, and clear instructions to make the setup easier to follow. Let me know if you'd like further adjustments!
