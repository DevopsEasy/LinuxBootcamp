
---

### **1. Your application server is running slowly. How do you troubleshoot performance issues in Linux?**

**Solution:**
A step-by-step investigation approach:

1. **Check CPU usage:**

   ```bash
   top -o %CPU
   ```
2. **Check memory usage:**

   ```bash
   free -h
   vmstat 1 5
   ```
3. **Check load average:**

   ```bash
   uptime
   ```
4. **Check disk I/O performance:**

   ```bash
   iostat -xz 1
   ```
5. **Check swap usage:**

   ```bash
   swapon --show
   ```
6. **Check network bottlenecks:**

   ```bash
   sar -n DEV 1 5
   ```
7. **Identify top resource-consuming processes:**

   ```bash
   ps aux --sort=-%mem | head -10
   ```

✅ **Real-Life Use Case:** This is a common issue when deploying apps on EC2 or VM — you must find whether the bottleneck is CPU, memory, disk, or network.

---

### **2. How do you analyze and resolve high load average on a Linux server?**

**Solution:**

1. **Check load average:**

   ```bash
   uptime
   ```

   If it’s consistently higher than the number of CPU cores → overload.
2. **Check for zombie/defunct processes:**

   ```bash
   ps aux | grep Z
   ```
3. **Check I/O wait (if high):**

   ```bash
   iostat -xz 1
   ```
4. **Check for blocked processes:**

   ```bash
   vmstat 1 5
   ```
5. **Kill or optimize high-usage processes:**

   ```bash
   kill -9 <PID>
   ```

✅ **Real-Life Use Case:** Used when a production server suddenly spikes in load due to memory leaks, heavy queries, or disk I/O bottlenecks.

---

### **3. How would you troubleshoot a service that fails to start at boot time?**

**Solution:**

1. **Check service status:**

   ```bash
   systemctl status myservice
   ```
2. **View boot logs:**

   ```bash
   journalctl -b
   ```
3. **Check dependency issues:**

   ```bash
   systemctl list-dependencies myservice
   ```
4. **Enable and restart:**

   ```bash
   systemctl enable myservice
   systemctl restart myservice
   ```

✅ **Real-Life Use Case:** Services like `docker`, `jenkins`, or `nginx` might fail due to dependency order, missing mounts, or permission issues.

---

### **4. How do you investigate a sudden increase in disk usage?**

**Solution:**

1. **Check disk space:**

   ```bash
   df -h
   ```
2. **Find large files:**

   ```bash
   du -ah / | sort -rh | head -20
   ```
3. **Check logs growing rapidly:**

   ```bash
   find /var/log -type f -size +100M
   ```
4. **Check deleted files still held by processes:**

   ```bash
   lsof | grep deleted
   ```

✅ **Real-Life Use Case:** Useful when log files or application caches suddenly fill up storage and bring down services.

---

### **5. A server is not responding to SSH. How do you troubleshoot it remotely?**

**Solution:**

* **Ping the server:**

  ```bash
  ping <IP>
  ```
* **Check port connectivity:**

  ```bash
  nc -zv <IP> 22
  ```
* **Use console access (cloud/web):** Check firewall rules.
* **Restart SSH service:**

  ```bash
  systemctl restart sshd
  ```
* **Check `sshd_config`:**

  ```bash
  cat /etc/ssh/sshd_config
  ```

✅ **Real-Life Use Case:** Happens when SSH daemon crashes or firewall blocks the port.

---

### **6. How do you recover a Linux system that is stuck in emergency mode?**

**Solution:**

1. **Check `/etc/fstab` errors:** Incorrect mount entries are common causes.
2. **Boot into single-user mode and fix fstab:**

   ```bash
   mount -o remount,rw /
   vi /etc/fstab
   ```
3. **Reboot the system:**

   ```bash
   reboot
   ```

✅ **Real-Life Use Case:** Happens when an NFS share is not available during boot.

---

### **7. How to detect and stop a DDoS attack on a Linux server?**

**Solution:**

1. **Monitor active connections:**

   ```bash
   netstat -ant | awk '{print $6}' | sort | uniq -c | sort -n
   ```
2. **Find offending IPs:**

   ```bash
   netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr | head
   ```
3. **Block them using `iptables`:**

   ```bash
   iptables -A INPUT -s <IP> -j DROP
   ```

✅ **Real-Life Use Case:** Production web servers often face brute-force or DDoS attacks.

---

### **8. How do you troubleshoot high network latency?**

**Solution:**

1. **Ping and traceroute:**

   ```bash
   ping 8.8.8.8
   traceroute 8.8.8.8
   ```
2. **Check packet drops:**

   ```bash
   netstat -i
   ```
3. **Monitor live traffic:**

   ```bash
   iftop
   ```
4. **Check DNS resolution delays:**

   ```bash
   dig google.com
   ```

✅ **Real-Life Use Case:** Helps debug slow deployments or application responses.

---

### **9. How do you monitor real-time file changes in a directory?**

**Solution:**

```bash
inotifywait -m /var/log
```

✅ **Real-Life Use Case:** Used in CI/CD to monitor configuration or code changes.

---

### **10. How do you track down zombie processes?**

**Solution:**

1. **Find zombies:**

   ```bash
   ps aux | grep Z
   ```
2. **Find parent process and restart:**

   ```bash
   ps -o ppid= -p <PID>
   kill -HUP <PPID>
   ```

✅ **Real-Life Use Case:** Zombie processes often indicate bugs in services.

---

### **11. How do you debug SELinux access denials?**

**Solution:**

1. **Check audit logs:**

   ```bash
   ausearch -m avc -ts recent
   ```
2. **Allow the denied action:**

   ```bash
   setsebool -P httpd_can_network_connect 1
   ```

✅ **Real-Life Use Case:** SELinux often blocks Nginx, Apache, or Jenkins ports.

---

### **12. How do you find all open network ports and the associated processes?**

**Solution:**

```bash
netstat -tulnp
ss -tulnp
lsof -i -P -n
```

✅ **Real-Life Use Case:** Essential for **security auditing and troubleshooting port conflicts**.

---

### **13. How do you analyze and reduce swap usage?**

**Solution:**

* **Check swap usage:**

  ```bash
  swapon --show
  free -h
  ```
* **Identify memory hogs:**

  ```bash
  ps aux --sort=-%mem | head -10
  ```
* **Increase RAM or tune `vm.swappiness`:**

  ```bash
  sysctl vm.swappiness=10
  ```

✅ **Real-Life Use Case:** High swap usage slows down applications.

---

### **14. How do you backup and restore critical configuration files?**

**Solution:**

```bash
tar -czvf /backup/etc_backup_$(date +%F).tar.gz /etc
tar -xzvf /backup/etc_backup_2025-09-24.tar.gz -C /
```

✅ **Real-Life Use Case:** Essential for **disaster recovery**.

---

### **15. How do you find which cron jobs ran recently?**

**Solution:**

```bash
grep CRON /var/log/syslog
journalctl -u cron
```

✅ **Real-Life Use Case:** Troubleshooting **failed automation jobs**.

---

### **16. How do you safely reboot a production Linux server?**

**Solution:**

1. **Notify users:**

   ```bash
   wall "Server will reboot in 5 minutes"
   ```
2. **Sync disks:**

   ```bash
   sync
   ```
3. **Reboot safely:**

   ```bash
   shutdown -r +5 "Scheduled reboot"
   ```

✅ **Real-Life Use Case:** Prevents data corruption and avoids downtime surprises.

---

### **17. How do you recover a forgotten root password?**

**Solution:**

1. Reboot and edit GRUB → add `init=/bin/bash`
2. Remount filesystem:

   ```bash
   mount -o remount,rw /
   ```
3. Reset password:

   ```bash
   passwd root
   ```

✅ **Real-Life Use Case:** Useful for **emergency access to critical servers**.

---

### **18. How do you check for failed login attempts?**

**Solution:**

```bash
grep "Failed password" /var/log/auth.log
```

✅ **Real-Life Use Case:** Security auditing.

---

### **19. How do you kill a process listening on a specific port?**

**Solution:**

```bash
lsof -i :8080
kill -9 <PID>
```

✅ **Real-Life Use Case:** Required when deploying microservices on busy ports.

---

### **20. How do you verify and fix DNS resolution issues?**

**Solution:**

```bash
dig google.com
nslookup google.com
cat /etc/resolv.conf
```

✅ **Real-Life Use Case:** DNS issues often cause deployment or API failures.

---


