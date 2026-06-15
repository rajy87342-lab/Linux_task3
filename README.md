# ðŸ§ Linux Task 03: Process Management, System Monitoring & Basic Shell Scripting

**Date:** June 14, 2026
**Intern:** Soham Sharma
**Program:** Cybersecurity Internship

---

## ðŸ“Œ Task Objectives

- Understand how Linux manages and monitors processes
- Use system monitoring commands to track resource usage
- Learn to manage services using systemctl
- Write and execute a basic shell script
- Research security monitoring commands used by SOC Analysts

---

## âœ… Part A: Process Monitoring

### Commands Used

```bash
ps
ps aux
top
htop
```

### Q&A

**What is a Process?**
A process is a running instance of a program in Linux. When you execute any command or open any application, the operating system creates a process for it. Every process has its own memory space, CPU time, and unique identifier.

**What is a PID?**
PID stands for Process ID. It is a unique number assigned by the Linux kernel to every running process. The very first process started at boot (systemd or init) always gets PID 1. PIDs are used to identify, monitor, and control processes.

**Which process is consuming the most CPU?**
Based on the `top` output: `xfce4-terminal` (PID 512) was consuming the most CPU at **2.3%**.

**Which process is consuming the most Memory?**
Based on the `top` output: `xfce4-terminal` (PID 512) was also consuming the most memory at **2.2% (89320 KB RSS)**.

> âœ… Screenshots included in `/screenshots/part_a/`

---

## âœ… Part B: Process Management

### Steps Performed

```bash
# Step 1: Start a background sleep process
sleep 300 &
# Output: [1] 2043

# Step 2: Find the process
ps aux | grep sleep

# Step 3: Kill using PID
kill 2043
# Output: [1]+ Terminated    sleep 300

# Step 4: Start another and force-kill with kill -9
sleep 300 &
# Output: [1] 2061
kill -9 2061
# Output: [1]+ Killed         sleep 300
```

### Documentation

| Field | Value |
|-------|-------|
| PID Found | 2043 (first), 2061 (second) |
| Command Used | `ps aux \| grep sleep` |
| Kill Command | `kill 2043` and `kill -9 2061` |
| Result | Both processes terminated successfully |

**Difference between `kill` and `kill -9`:**
- `kill PID` sends SIGTERM (signal 15) â€” asks the process to terminate gracefully
- `kill -9 PID` sends SIGKILL (signal 9) â€” forces immediate termination with no cleanup

> âœ… Screenshots included in `/screenshots/part_b/`

---

## âœ… Part C: System Monitoring

### Commands Run

```bash
free -h
df -h
uptime
uname -a
```

### System Summary Report

| Property | Value |
|----------|-------|
| Total RAM | 3.8 GiB |
| Used RAM | 847 MiB |
| Available RAM | 2.8 GiB |
| Swap Total | 2.0 GiB |
| Swap Used | 0 B |
| Disk Total | 49G |
| Disk Used | 8.2G (18%) |
| Disk Free | 39G |
| Kernel Version | 6.6.9-amd64 |
| System Uptime | 1 hour, 25 minutes |
| Load Average | 0.18, 0.21, 0.14 |

> âœ… Screenshots included in `/screenshots/part_c/`

---

## âœ… Part D: Service Monitoring

### Commands Run

```bash
systemctl status ssh
systemctl status NetworkManager
```

### Results

| Service | Status | PID | Port |
|---------|--------|-----|------|
| ssh | âœ… active (running) | 188 | 22 |
| NetworkManager | âœ… active (running) | 312 | â€” |

### Q&A

**What is a Service?**
A service (also called a daemon) is a background process that runs continuously to provide specific functionality to the system or network. Services are managed by `systemd` in modern Linux distributions. Examples include SSH (for remote access), NetworkManager (for network connectivity), and Apache (for web hosting).

**Why are services important?**
Services are the backbone of a Linux system. They provide essential functions like networking, authentication, logging, and remote access. Without properly running services, many system functions would be unavailable. For security professionals, monitoring services is critical to detecting unauthorized or suspicious background activity.

**How can a stopped service affect a system?**
If a critical service stops unexpectedly, it can have serious consequences. For example, if the SSH service stops, all remote administrators lose access to the server. If NetworkManager stops, the system loses internet/network connectivity. In a production environment, a stopped service can mean downtime, data loss, or a security gap that attackers can exploit.

> âœ… Screenshots included in `/screenshots/part_d/`

---

## âœ… Part E: Shell Scripting

### Script: `system_report.sh`

```bash
#!/bin/bash
# system_report.sh â€” System Information Report Script
# Author: Soham Sharma
# Date: 14-06-2026

echo "============================================"
echo "        System Information Report"
echo "============================================"
echo ""
echo "User             : $(whoami)"
echo "Hostname         : $(hostname)"
echo "Date & Time      : $(date '+%d-%m-%Y %H:%M:%S')"
echo "Current Directory: $(pwd)"
echo "Kernel Version   : $(uname -r)"
echo "System Uptime    : $(uptime -p)"
echo ""
echo "--- Memory Usage ---"
free -h
echo ""
echo "--- Disk Usage ---"
df -h
echo ""
echo "============================================"
```

### Execution

```bash
# Make executable
chmod +x system_report.sh

# Run the script
./system_report.sh
```

### Output

```
============================================
        System Information Report
============================================

User             : kali
Hostname         : kali
Date & Time      : 14-06-2026 09:58:44
Current Directory: /home/kali
Kernel Version   : 6.6.9-amd64
System Uptime    : up 1 hour, 26 minutes

--- Memory Usage ---
              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       847Mi       2.3Gi        18Mi       728Mi       2.8Gi
Swap:         2.0Gi          0B       2.0Gi

--- Disk Usage ---
Filesystem      Size  Used Avail Use% Mounted on
udev            1.9G     0  1.9G   0% /dev
/dev/sda1        49G  8.2G   39G  18% /
tmpfs           2.0G     0  2.0G   0% /dev/shm

============================================
```

> âœ… Screenshots included in `/screenshots/part_e/`

---

## âœ… Part F: Security Monitoring Commands

### 1. `netstat`

**Purpose:** Displays active network connections, listening ports, routing tables, and network interface statistics.

**Example Output:**
```
Proto Recv-Q Send-Q Local Address     Foreign Address    State
tcp        0      0 0.0.0.0:22        0.0.0.0:*          LISTEN
tcp6       0      0 :::22             :::*               LISTEN
udp        0      0 127.0.0.53:53     0.0.0.0:*
```

**Security Use Case:** Used by SOC analysts to detect unauthorized open ports or suspicious outbound connections that may indicate malware, backdoors, or data exfiltration.

---

### 2. `ss`

**Purpose:** A modern, faster replacement for `netstat`. Displays socket statistics and active connections.

**Example Output:**
```
Netid  State   Recv-Q  Send-Q  Local Address:Port   Peer Address:Port
tcp    LISTEN       0     128         0.0.0.0:22          0.0.0.0:*
tcp    LISTEN       0     128            [::]:22             [::]:*
```

**Security Use Case:** Quickly identify which processes are listening on which ports. Useful for detecting unusual services running on unexpected ports.

---

### 3. `who`

**Purpose:** Shows which users are currently logged into the system, along with their terminal and login time.

**Example Output:**
```
kali     tty7         2026-06-14 08:32 (:0)
kali     pts/0        2026-06-14 09:32 (:0)
```

**Security Use Case:** Helps detect unauthorized or unexpected user sessions on a system â€” a key indicator of a compromised account or insider threat.

---

### 4. `w`

**Purpose:** An extended version of `who` that also shows what each logged-in user is currently doing, along with system load information.

**Example Output:**
```
 09:59:02 up 1:26,  2 users,  load average: 0.18, 0.21, 0.14
USER     TTY      FROM         LOGIN@   IDLE   JCPU   PCPU WHAT
kali     tty7     :0           08:32    1:26m  4.81s  0.06s /usr/bin/xfce4-session
kali     pts/0    :0           09:32    0.00s  0.05s  0.01s w
```

**Security Use Case:** Allows a security analyst to see in real time what commands a logged-in user is running â€” useful during incident response to monitor suspicious user activity.

---

### 5. `last`

**Purpose:** Displays a list of the last logged-in users by reading the `/var/log/wtmp` file. Shows login time, logout time, and duration.

**Example Output:**
```
kali     pts/0    :0           Sat Jun 14 09:32   still logged in
kali     tty7     :0           Sat Jun 14 08:32   still logged in
reboot   system boot 6.6.9-amd64  Sat Jun 14 08:31   still running
kali     pts/0    :0           Fri Jun 13 21:14   21:58   (00:44)
```

**Security Use Case:** Used during forensic investigations to trace who logged in, from where, and when. Helps identify unauthorized access patterns or accounts that logged in at unusual hours.

---

## âœ… Part G: Mini SOC Activity

*Scenario: You are a Security Analyst. The system is running slowly.*

### 1. How would you identify resource-heavy processes?

I would start by running `top` or `htop` to get a real-time overview of CPU and memory usage. The processes at the top of the list sorted by CPU% or MEM% are the most resource-intensive. I would also run `ps aux --sort=-%cpu | head -10` to quickly list the top 10 CPU-consuming processes, and `ps aux --sort=-%mem | head -10` for memory. The `uptime` command would also tell me the load average â€” if it is significantly higher than the number of CPU cores, the system is under stress.

### 2. How would you determine whether a process is suspicious?

After identifying the heavy process, I would check several things. First, I would note the process name, PID, and the user running it. A legitimate process running as root that I do not recognize is a red flag. I would then use `ls -l /proc/PID/exe` to find the actual executable file on disk, and check its location â€” processes running from `/tmp`, `/dev/shm`, or random home directories are highly suspicious. I would also check network activity using `ss -tulnp | grep PID` to see if the process is making unexpected network connections. Finally, I would search the process name online to verify if it is a known system process or potential malware.

### 3. What information would you collect before terminating a process?

Before killing any process, I would collect the following information for documentation and forensic purposes. The PID and full process name, the user account running it, the full command line using `cat /proc/PID/cmdline`, the open files and network connections using `lsof -p PID`, the parent process (PPID) to understand how it was launched, and a timestamp of when it was first seen. I would also take a screenshot of `top` or `htop` showing the process, and note the system load at that time. Only after collecting all this evidence would I use `kill -9 PID` to terminate it, and then check if it respawns â€” which would indicate persistence and a deeper compromise.

---

## ðŸ“ Repository Structure

```
Linux_Task_03_SohamSharma/
â”‚
â”œâ”€â”€ README.md                        â† This file
â”œâ”€â”€ system_report.sh                 â† Shell script from Part E
â””â”€â”€ screenshots/
    â”œâ”€â”€ part_a/                      â† ps, ps aux, top, htop screenshots
    â”œâ”€â”€ part_b/                      â† sleep, grep, kill screenshots
    â”œâ”€â”€ part_c/                      â† free -h, df -h, uptime, uname screenshots
    â”œâ”€â”€ part_d/                      â† systemctl status screenshots
    â”œâ”€â”€ part_e/                      â† nano editor + script execution screenshots
    â””â”€â”€ part_f/                      â† netstat, ss, who, w, last screenshots
```

---

## ðŸ“ Notes

- All commands were executed on **Kali Linux 2024.2** running on **VirtualBox 7.0**
- VM Configuration: 2 CPU cores, 4GB RAM, 50GB disk
- Shell used: **zsh** (default in Kali Linux)

---

## ðŸ”— Submission

Submitted via the **Sunday Submission Form** as part of Week 3 of the Cybersecurity Internship Program.

> ðŸ’¡ **Key Takeaway:** Understanding processes, services, and system resources is the foundation of Linux administration and SOC analysis. The ability to quickly identify what is running on a system â€” and whether it is legitimate â€” is one of the most critical skills in cybersecurity.
