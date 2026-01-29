# Linux

# Linux Command Reference

| Category | Command | Description |
| :--- | :--- | :--- |
| **System** | `uname -a` | Display kernel version and system info |
| **System** | `uptime` | See how long the system has been running |
| **Disk** | `df -h` | Show free disk space in human-readable format |
| **Disk** | `du -sh *` | Show size of files/folders in current directory |
| **Network** | `ip addr` | Show all network interfaces and IP addresses |
| **Network** | `ss -tulpn` | View all listening ports and services |
| **Process** | `top` or `htop` | Interactive process viewer (Task Manager) |
| **Process** | `ps aux \| grep <name>` | Find a specific running process |
| **File Permissions** | `chmod 755 <file>` | Set read/write/execute permissions |
| **File Permissions** | `chown user:group <file>` | Change owner and group of a file |
| **Logs** | `journalctl -xe` | View system logs for troubleshooting |
| **Logs** | `tail -f /var/log/syslog` | Watch logs in real-time |