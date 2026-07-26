[🏡 Home](index.md)

# Linux

## Shortcuts

```sh
# Clear screen
ctrl + l # or command + l
```

## Linux Command Reference

```sh
# --- System Info ---
uname -a                 # Display kernel version and system info
id                       # Display all users ids
uptime                   # See how long the system has been running
who                      # Show who is logged in
hostname                 # Show or set hostname
free                     # Display memory usage

# --- Disk Usage ---
df -h                           # Show free disk space in human-readable format
du -sh *                        # Show size of files/folders in current directory
free -h                         # Show memory usage
du -h -d 1 | sort -hr  # Show lsit of directories sorted by size

# --- Network ---
ip addr                  # Show all network interfaces and IP addresses
ss -tulpn                # View all listening ports and services

# --- Process Management ---
top                      # Interactive process viewer
htop                     # Interactive process viewer (enhanced)
ps aux | grep <name>     # Find a specific running process

# --- Root & User Access ---
sudo <command>           # Run command as root
sudo su                  # Become superuser
passwd                   # Change password

# --- File Permissions ---
chmod 755 <file>         # Set read/write/execute permissions
chown user:group <file>  # Change owner and group of a file

# --- Logs & Troubleshooting ---
journalctl -xe           # View system logs for troubleshooting
tail -f /var/log/syslog  # Watch logs in real-time
```

## Alias

```sh

nano ~/.bashrc
alias alias_name="cmd"
source ~/.bashrc

```

## Special for chained commands

```sh
grep    # for use regular expressions 
xargs   # for taking the output of and execute something with that output as input
sort    # order an output like a text file
uniq    # for report or remove adjacent repeated lines from a file or piped input. Used often after sort
>       # Redirect a command into a file (overwrite)
>>      # ensures that the new ouput is added to the ond of that file
rsync   # used for efficiently copying and synchronizing files and directories between source and a destination
2>      # Redirect errors
```

## Networking

```sh
# --- System Info ---
curl -I https://example.com    # Checks if a website is up without downloading the entire page content. -I tells curl to send a HEAD request instead of the default GET reques
netstat                        # Displays network statistics
# -t shows TCP connections
# -u shows UDP connections
# -l displays only listening sockets (servers)
# -n shows numerical addresses instead of resolving hostnames and port numbers
netstat -tuln 
```

### ssh

```sh
# Where's the ssh folder:
~/.ssh

# How to create a new key
ssh-keygen 

# Copy SSH key
ssh-copy-id user@host

# Copy file
scp file user@host:path

# How to connect
ssh user@hostname
```


