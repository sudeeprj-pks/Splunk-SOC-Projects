# Install and Configure Splunk on Ubuntu

This repository provides a **step-by-step guide** to install, configure, and access **Splunk Enterprise 9.3.0** on an Ubuntu system (physical machine or virtual machine).

The guide includes **commands with explanations**, verification steps, and **troubleshooting tips** for common issues such as firewall blocks and VM networking problems.

---

## 📘 Overview

This guide covers:

* Downloading the Splunk `.deb` package
* Installing Splunk Enterprise on Ubuntu
* Accepting the license and enabling Splunk at boot
* Starting and verifying the Splunk service
* Accessing the Splunk Web UI
* Troubleshooting firewall, networking, and service issues

✅ **Tested with:** Splunk Enterprise **9.3.0**
(Adjust filenames and URLs if using a different version)

---

## 🧾 Prerequisites

* Ubuntu Desktop or Server
* `sudo` privileges
* Active internet connection
* Basic Linux command-line knowledge
* If using a VM, knowledge of **NAT vs Bridged networking**

---

## ⚡ Quick Installation Commands

```bash
# 1. Update system and install required utilities
sudo apt update
sudo apt install -y curl net-tools

# 2. Create a working directory (optional)
mkdir -p ~/downloads/splunk
cd ~/downloads/splunk

# 3. Download Splunk Enterprise 9.3.0
wget -O splunk-9.3.0-51ccf43db5bd-linux-2.6-amd64.deb \
"https://download.splunk.com/products/splunk/releases/9.3.0/linux/splunk-9.3.0-51ccf43db5bd-linux-2.6-amd64.deb"

# 4. Install Splunk
sudo dpkg -i splunk-9.3.0-51ccf43db5bd-linux-2.6-amd64.deb

# If dependency errors occur
sudo apt --fix-broken install -y
# Re-run dpkg if needed
sudo dpkg -i splunk-9.3.0-51ccf43db5bd-linux-2.6-amd64.deb

# 5. Enable Splunk to start at boot and accept license
sudo /opt/splunk/bin/splunk enable boot-start --accept-license

# 6. Start Splunk (first run prompts for admin credentials)
sudo /opt/splunk/bin/splunk start

# 7. Verify Splunk status
sudo /opt/splunk/bin/splunk status

# 8. Verify Splunk Web locally
curl -I http://localhost:8000
# Expected: HTTP/1.1 303 See Other

# 9. Allow Splunk Web through firewall (UFW)
sudo ufw allow OpenSSH
sudo ufw allow 8000/tcp
sudo ufw reload
sudo ufw status

# 10. Get system IP address
hostname -I
```

---

## 🌐 Accessing Splunk Web UI

Open a browser and navigate to:

```
http://<SERVER-IP>:8000
```

Example:

```
http://192.168.1.100:8000
```

Login using the **admin username and password** created during first startup.

---

## 🖥️ VM Networking Notes

### NAT Mode

* Splunk is accessible **inside the VM only**
* Use **SSH port forwarding** to access from host

```bash
ssh -L 8000:localhost:8000 user@VM-IP
```

Then open:

```
http://localhost:8000
```

### Bridged Mode

* VM gets an IP on the same network as host
* Access Splunk directly using VM IP

---

## 🔧 Troubleshooting

### Splunk Not Running

```bash
sudo /opt/splunk/bin/splunk status
```

### Port 8000 Not Listening

```bash
sudo netstat -tulnp | grep 8000
```

### Firewall Blocking Access

```bash
sudo ufw status
sudo ufw allow 8000/tcp
```

### Forgot Admin Password

```bash
sudo /opt/splunk/bin/splunk stop
sudo /opt/splunk/bin/splunk edit user admin -password NEWPASS -role admin -auth admin:OLDPASS
sudo /opt/splunk/bin/splunk start
```

---

## 📂 Default Splunk Paths

* Installation: `/opt/splunk/`
* Binaries: `/opt/splunk/bin/`
* Logs: `/opt/splunk/var/log/splunk/`
* Configuration: `/opt/splunk/etc/`

---

## 🧠 Learning Outcomes

* Installing and managing Splunk on Linux
* Understanding Splunk services and ports
* Troubleshooting Linux services and firewalls
* Working with VM networking (NAT vs Bridged)

---

## ⚠️ Disclaimer

This guide is intended for **educational and lab environments**. Secure Splunk properly before deploying in production.

---

## 📄 License

This documentation is provided for **learning and academic use**.

---

**Project:** Install and Configure Splunk on Ubuntu
**Tool:** Splunk Enterprise 9.3.0
**Platform:** Ubuntu Linux
**Author:** Sudeep
