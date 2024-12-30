# Born2beRoot

This repository contains my implementation of the **Born2beRoot** project from 42 School. The project focuses on setting up a secure server environment using virtualization while adhering to strict system administration guidelines.

```
## Project Overview

- **Virtualization**: Use **VirtualBox** or **UTM** to create a virtual machine.
- **Operating System**: Choose between the latest stable versions of **Debian** (recommended) or **Rocky Linux**.
- **Security**: Implement robust password policies, firewalls, and user management.
- **Monitoring**: Develop a script to display system metrics periodically.

---

## Setup Instructions

### 1. Prerequisites
- Install **VirtualBox**:
  ```bash
  sudo apt install virtualbox
  ```
- Download the latest stable version of [Debian](https://www.debian.org/distrib/) or [Rocky Linux](https://rockylinux.org/download).

---

### 2. Virtual Machine Configuration
- Allocate at least **1024 MB RAM** and **10 GB storage**.
- Use **LVM Encrypted Partitions**:
  - `/boot`: 512 MB
  - `/`: 8 GB
  - `swap`: 1 GB

---

### 3. System Configuration

#### Hostname
Set your hostname to `<your-login>42`:
```bash
sudo hostnamectl set-hostname yourlogin42
```

#### User and Group Management
1. Add a new user and configure groups:
   ```bash
   sudo adduser yourlogin
   sudo usermod -aG sudo yourlogin
   sudo groupadd user42
   sudo usermod -aG user42 yourlogin
   ```

2. Enforce a strong password policy by editing `/etc/security/pwquality.conf`:
   ```bash
   minlen = 10
   minclass = 3
   maxrepeat = 3
   ```

#### Sudo Configuration
1. Limit password attempts and add custom messages:
   - Edit `/etc/sudoers.d/custom`:
     ```bash
     Defaults passwd_tries=3
     Defaults badpass_message="Access Denied: Incorrect Password"
     Defaults logfile="/var/log/sudo/sudo.log"
     Defaults requiretty
     Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
     ```

---

### 4. Firewall Configuration
1. Install and enable UFW (for Debian):
   ```bash
   sudo apt install ufw
   sudo ufw allow 4242
   sudo ufw enable
   ```

2. For Rocky Linux, use Firewalld:
   ```bash
   sudo yum install firewalld
   sudo firewall-cmd --permanent --add-port=4242/tcp
   sudo firewall-cmd --reload
   ```

---

### 5. Monitoring Script
1. Create a `monitoring.sh` script:
   ```bash
   nano monitoring.sh
   ```
2. Example content:
   ```bash
   #!/bin/bash
   echo "#Architecture: $(uname -a)"
   echo "#CPU physical: $(lscpu | grep 'Socket' | awk '{print $2}')"
   echo "#vCPU: $(nproc)"
   echo "#Memory Usage: $(free -m | awk '/Mem:/ {print $3}')MB / $(free -m | awk '/Mem:/ {print $2}')MB"
   echo "#Disk Usage: $(df -h / | awk 'NR==2 {print $3}') / $(df -h / | awk 'NR==2 {print $2}')"
   echo "#Last boot: $(who -b | awk '{print $3, $4}')"
   echo "#LVM use: $(lsblk | grep lvm | wc -l)"
   ```
3. Make the script executable:
   ```bash
   chmod +x monitoring.sh
   ```

4. Schedule it to run every 10 minutes:
   ```bash
   sudo crontab -e
   # Add the following line:
   */10 * * * * wall $(/path/to/monitoring.sh)
   ```

---

## Common Commands
- **Check Partition Configuration**:
  ```bash
  lsblk
  ```
- **List Open Ports**:
  ```bash
  sudo netstat -tuln
  ```
- **Verify SSH Configuration**:
  ```bash
  sudo systemctl status ssh
  ```
- **Get Machine Signature**:
  ```bash
  sha1sum /path/to/your.vdi
  ```

---

## Bonus Features
1. Set up a WordPress website using:
   - **lighttpd**, **MariaDB**, and **PHP**.
2. Add a custom service of your choice.
3. Customize firewall rules to support additional services.

---

## License
This repository is intended for educational purposes as part of the 42 School curriculum. Contributions and suggestions are welcome!
```
