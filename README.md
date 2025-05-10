---

# 📂 Transferring Files Between Virtual Machines (VMs) on OpenNebula

---

## 1️⃣ Introduction

**OpenNebula** is a powerful open-source platform for managing distributed data center infrastructures and private clouds. It allows you to deploy and manage virtual machines (VMs) easily.

One common task is **transferring files between VMs**, which is essential for:

* Sharing data between applications.
* Migrating files during scaling or backup.
* Synchronizing environments (e.g., code, configurations).

**Example Use Cases:**

* Moving logs from a web server VM to a storage VM.
* Syncing configuration files across development and production environments.
* Backing up critical data from one VM to another.

---

## 2️⃣ Prerequisites

Before you begin, ensure the following:

### ✔ Requirements:

* Two running VMs on **OpenNebula** (source & destination).
* Both VMs are **accessible via SSH**.

### 🛠 Required Tools:

* `scp` (Secure Copy Protocol) – typically pre-installed with SSH.
* (Optional) `rsync` – for more advanced synchronization.
* (Optional) NFS (Network File System) – for persistent mounts.

### 🔑 Access & Permissions:

* SSH access to **both VMs** (username, IP).
* SSH keys **set up** for password-less authentication (*recommended*).

**Tip:**
If SSH keys aren't set up yet, you can generate and copy them like this:

```bash
ssh-keygen -t rsa
ssh-copy-id user@destination-ip
```

---

## 3️⃣ Setup

### 🖥 Preparing the Source VM

* **Check if `scp` is available:**

```bash
which scp
```

* **If missing, install via:**

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install openssh-client

# RHEL/CentOS
sudo yum install openssh-clients
```

### 🖥 Preparing the Destination VM

* **Ensure SSH is running:**

```bash
sudo systemctl status ssh
```

* **Ensure the destination VM can accept SSH connections.**

### 🔍 Check the IP Addresses

Run on both VMs:

```bash
ip a
```

Sample output:

```bash
eth0: inet 192.168.1.100
```

**Note:**
Save both **source VM** and **destination VM** IP addresses for use in the next steps.

---

## 4️⃣ Step-by-Step Guide

### 🔄 Transfer Files Using `scp`

#### ✅ Single File Example:

```bash
scp /path/to/local/file user@destination-ip:/path/to/remote/directory
```

**Example:**

```bash
scp /home/user/data.txt user@192.168.1.101:/home/user/
```

* `/home/user/data.txt`: file to send.
* `user`: SSH username.
* `192.168.1.101`: destination IP.
* `/home/user/`: destination directory.

#### ✅ Entire Directory Example:

```bash
scp -r /path/to/local/directory user@destination-ip:/path/to/remote/
```

### 🔄 Alternative: Using `rsync`

```bash
rsync -avz /path/to/local/file user@destination-ip:/path/to/remote/
```

**Why use `rsync`?**

* Supports **resuming** interrupted transfers.
* More **efficient** for large or incremental transfers.

### 🔄 Optional: Using NFS Mount

* **Install NFS tools:**

```bash
sudo apt install nfs-common   # Debian/Ubuntu
sudo yum install nfs-utils    # RHEL/CentOS
```

* **Mount the NFS share:**

```bash
sudo mount destination-ip:/shared-directory /mnt/local-mount
```

### 🚀 Tips for Large Transfers

* **Run in Background:**

```bash
scp /path/to/file user@destination-ip:/path & disown
```

* **Use Compression:**

```bash
scp -C /path/to/file user@destination-ip:/path
```

---

## 5️⃣ Automation Tip (Optional)

You can automate transfers using a **shell script**:

```bash
#!/bin/bash
scp /home/user/data.txt user@192.168.1.101:/home/user/
```

And set a **cron job** (example: daily at midnight):

```bash
crontab -e
```

Add:

```bash
0 0 * * * /home/user/transfer_script.sh
```

---

## 6️⃣ Troubleshooting

| **Issue**                       | **Possible Fix**                                                             |
| ------------------------------- | ---------------------------------------------------------------------------- |
| `Permission denied (publickey)` | Check SSH keys and permissions. Use `ssh -v` for debugging.                  |
| `Network unreachable`           | Check VM network settings in OpenNebula. Ensure VMs are on the same network. |
| Transfer hangs                  | Check firewall settings. Temporarily disable `ufw`/`firewalld` for testing.  |
| `No such file or directory`     | Verify source and destination paths.                                         |

**Tip:**
Always test **SSH login** manually first:

```bash
ssh user@destination-ip
```

---

## 7️⃣ Security Best Practices

* Use **SSH keys** instead of passwords.
* Restrict SSH access to trusted IPs (via firewall).
* **Remove sensitive files** after transfer if no longer needed.
* Use `rsync --remove-source-files` if you want to delete files after transfer.

**Warning:**
Avoid using `scp` over **public networks** without proper encryption/tunneling.

---

## 8️⃣ Screenshots

| **Description**                             | **Embedded Image**                                                                                |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| OpenNebula login page          | ![OpenNebula Dashboard](https://github.com/GiribharathiV/cloud-computing-lab/blob/aa1a5b589e89f50668cfa8e2ab583195f836eaec/WhatsApp%20Image%202025-05-10%20at%2010.29.54_30960f37.jpg)                |
| VM creation or VM list view                 | ![VM Creation](https://docs.opennebula.io/6.10/_images/sunstone_create_vrouter.png)                  |
| File transfer in progress using       | ![SCP Transfer](https://linbit.com/wp-content/uploads/2023/09/linstor-opennebula-how-to-setup_01-1536x509.png)                        |
| Transfer success confirmation               | ![Transfer Success](https://canada1.discourse-cdn.com/flex031/uploads/opennebula/original/2X/c/c11566edd35b12b29b0ccd4fcfa7465c337b4518.png)                                          |

---

## 9️⃣ Conclusion

We’ve covered:

* Preparing VMs on **OpenNebula**.
* Transferring files via `scp` and alternatives like `rsync`.
* Automating and troubleshooting transfers.
* Security tips for safe operations.

**Further Reading:**

* [OpenNebula Documentation](https://docs.opennebula.io/)
* [scp Command Manual](https://linux.die.net/man/1/scp)
* [rsync Documentation](https://download.samba.org/pub/rsync/rsync.html)

---

## 🔗 Code Snippets (Quick Copy)

```bash
# Transfer a file
scp /path/to/local/file user@destination-ip:/path/to/remote/directory

# Transfer a directory
scp -r /path/to/local/directory user@destination-ip:/path/to/remote/

# rsync with compression
rsync -avz /path/to/local/file user@destination-ip:/path/to/remote/

# Generate SSH key
ssh-keygen -t rsa

# Copy SSH key
ssh-copy-id user@destination-ip

# Check VM IP
ip a
```

---

## 🏷 Suggested Tags/Keywords

`OpenNebula` `VM` `File Transfer` `scp` `rsync` `Linux` `SSH` `Automation` `Cloud` `Virtualization`

---
