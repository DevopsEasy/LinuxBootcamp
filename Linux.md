

### ✅ **1. Server Installation & Decommissioning**

**Q:** *Can you explain the steps you follow when installing a new Linux server (either physical or VM) in production?*

**Real-Time Answer:**

```text
1. Check hardware compatibility (for physical) or resource allocation (for VM).
2. Mount ISO and boot into Linux installer (RHEL/CentOS/Ubuntu).
3. Configure disk partitioning (use LVM if needed).
4. Set hostname, IP, DNS, and network interface.
5. Install base packages and harden the OS (disable unused services, SSH hardening).
6. Apply latest updates and security patches.
7. Register monitoring agents (e.g., Nagios, Zabbix).
8. Add server to central configuration tools like Puppet/Ansible.
```

**Decommissioning Steps:**

```text
1. Inform stakeholders and schedule downtime.
2. Backup critical data.
3. Remove from DNS/load balancer/monitoring.
4. Delete or archive logs and configs.
5. De-register from CM tools.
6. Secure wipe disks (for physical servers).
7. Update documentation and CMDB.
```

---

### ✅ **2. Troubleshooting High CPU / Server Down**

**Q:** *A Linux server goes down suddenly. How would you handle the situation?*

**Real-Time Solution:**

```text
1. Try pinging the server.
2. Use remote management tools (iLO/IPMI/VM console).
3. Check if it's reachable via SSH.
4. If accessible, check logs: `journalctl -xe`, `/var/log/messages`, `dmesg`.
5. Run `top` or `htop` to find CPU/memory hogs.
6. Check disk space: `df -h`, `du -sh /var/*`.
7. Restart critical services if needed: `systemctl restart xyz`.
8. Engage infra/network team if hardware or hypervisor is suspect.
```

---

### ✅ **3. LVM Management**

**Q:** *A partition is running out of space. How do you extend it using LVM in real time?*

**Real-Time Solution (Example with ext4):**

```bash
# Check space availability
vgs
# Extend logical volume by 5G
lvextend -L +5G /dev/mapper/vg0-lv_root
# Resize filesystem
resize2fs /dev/mapper/vg0-lv_root
```

**If using XFS:**

```bash
xfs_growfs /mount/point
```

---

### ✅ **4. Hypervisor Operations (XEN/VMware)**

**Q:** *How do you troubleshoot a VM that's not booting on a VMware/XEN hypervisor?*

**Real-Time Steps:**

```text
1. Access the hypervisor console (vSphere for VMware, xl console for Xen).
2. Check VM power status.
3. View boot logs or kernel panic screen.
4. Inspect VM disk configuration (corruption/missing).
5. Mount rescue ISO if needed and perform chroot repair.
6. Restore snapshot if necessary.
7. Cross-check host CPU/RAM usage to ensure it's not overcommitted.
```

---

### ✅ **5. DNS Issues**

**Q:** *Users report they cannot reach the application by hostname. How do you troubleshoot DNS issues on the server?*

**Real-Time Solution:**

```bash
# Check hostname resolution
nslookup app.example.com
dig app.example.com

# Check /etc/resolv.conf for correct DNS entries

# Test name server
dig @<dns-server-ip> app.example.com

# Check if DNS service is running on internal DNS servers (if applicable)

# If using /etc/hosts, validate entries
cat /etc/hosts

# Flush DNS cache (if any local service like systemd-resolved is used)
systemd-resolve --flush-caches
```

---

### ✅ **6. LDAP Configuration**

**Q:** *How do you configure a Linux server to authenticate users via LDAP?*

**Real-Time Solution:**

```bash
# Install required packages
yum install openldap-clients nss-pam-ldapd

# Configure /etc/nslcd.conf or /etc/ldap.conf
# Example:
uri ldap://ldap.example.com/
base dc=example,dc=com
binddn cn=admin,dc=example,dc=com
bindpw secret

# Update /etc/nsswitch.conf
passwd:     files ldap
shadow:     files ldap
group:      files ldap

# Restart services
systemctl restart nslcd
getent passwd <ldap_user>  # Test user fetch
```

---

### ✅ **7. IP Tables Troubleshooting**

**Q:** *You cannot access a service port (e.g., 8080) on your Linux server. How do you check if IP Tables is blocking it?*

**Real-Time Solution:**

```bash
# List all iptables rules
iptables -L -n

# Add temporary rule to allow port 8080
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT

# Save rule permanently
service iptables save

# Or use firewalld (for RHEL7+)
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload
```

---

### ✅ **8. Physical Server Management**

**Q:** *How do you remotely manage a physical server that’s not reachable via SSH?*

**Real-Time Answer:**

```text
1. Use IPMI/iLO/DRAC interface to access server console remotely.
2. Check power status, remote reboot if needed.
3. Mount ISO for OS rescue.
4. Check hardware logs (e.g., memory errors).
5. Use PXE boot environment for diagnostics.
```

---

---

### ✅ **1. Linux Server Provisioning Steps (Physical/VM)**

```bash
# General steps:
1. ISO boot → Partitioning (LVM preferred)
2. Set hostname, static IP, gateway, DNS
3. Install base OS packages
4. Configure SSH, disable root login
5. Apply updates: yum update / apt update
6. Add to monitoring (Zabbix/Nagios)
7. Register in CM tool (Ansible/Puppet)
```

---

### 🛠️ **2. Server Down – Real-Time Troubleshooting**

```bash
ping <IP>
ssh <user>@<IP>         # If SSH fails, use iLO/IPMI
journalctl -xe
dmesg
top / htop              # CPU/Memory usage
df -h                   # Disk space
systemctl status xyz    # Check service status
```

---

### 📦 **3. LVM Commands**

```bash
vgs                     # Check free space in VG
lvextend -L +5G /dev/vg0/lv_root
resize2fs /dev/vg0/lv_root        # ext4
xfs_growfs /mnt/data              # XFS
```

---

### 🌐 **4. DNS Troubleshooting**

```bash
dig <hostname>
nslookup <hostname>
cat /etc/resolv.conf
ping <hostname>
systemd-resolve --flush-caches
```

---

### 🛡️ **5. IP Tables / Firewall**

```bash
iptables -L -n
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
service iptables save

firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload
```

---

### 👥 **6. LDAP Authentication**

```bash
yum install nss-pam-ldapd
vim /etc/nslcd.conf              # Set URI, base, binddn, bindpw
vim /etc/nsswitch.conf           # passwd, group, shadow → ldap
systemctl restart nslcd
getent passwd <ldap_user>        # Test
```

---

### ☁️ **7. Hypervisor VM Issues (XEN / VMware)**

```text
- Use console via vSphere or XEN (`xl console <vm>`)
- Check VM state: powered on? tools installed?
- Check hypervisor resource usage
- Attach rescue ISO for recovery if needed
```

---

### 🔌 **8. Physical Server Management**

```text
- Access via iLO / IPMI / DRAC
- Power status, reboot options
- Mount remote ISO via virtual media
- Check logs: hardware failures (RAID, RAM)
```

---

### 🔄 **9. Decommissioning a Server**

```text
1. Notify stakeholders & schedule
2. Backup all data
3. Remove from monitoring, DNS, CMDB
4. Revoke access (SSH keys, LDAP)
5. Wipe disks securely (e.g., `shred`, `dd`)
6. Update documentation
```

---

### 🧠 **Bonus Commands**

```bash
who -b                 # Last system boot time
uptime
netstat -tuln         # Listening ports
ss -ltn               # Modern alternative to netstat
lsblk                 # List block devices
```

---




Would you like a **one-page summary cheat sheet** of commands and steps you can revise just before your interview?
