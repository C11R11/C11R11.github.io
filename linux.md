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
uptime                   # See how long the system has been running
who                      # Show who is logged in
hostname                 # Show or set hostname
free                     # Display memory usage

# --- Disk Usage ---
df -h                    # Show free disk space in human-readable format
du -sh *                 # Show size of files/folders in current directory
free -h                  # Show memory usage

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

## Files

### Search for files
```sh

### Look for all the files within a certain folder
# alt 1: with ls (shows only name)
ls -R | grep "<file.ext>"
# alt 2: with find (shows files with path)
find . -type f -name "<file.ext>"

### look for all the files with a certain extension
find . -type f -name "<*.ext>"
```

### look into files
```sh

# Find files and show only the errors 
# -> find will lookup for files
# -> -> then xargs will use that output to read the files with command cat
# -> -> -> again the results get filtered with grep
# -> -> -> -> gets sorted in the 4th colum (assuming it's a text with an space as delimeter)
# -> -> -> -> -> and filter only uniques values from column 3 and after with uniq 
# -> -> -> -> -> -> and redirect to save the output as a log file
find . -type f -name "<file.ext>" | xargs cat | grep "Error" | sort -k4 | uniq -f3 > errors.log

# Compare two files and show them
diff -y file1.txt file2.txt

# Compare at byte level
cmp file1.bin file2.bin
```

### Backup files

> starting from the find command to see which files we need to backup

```sh
# -> find will lookup for files
# -> (the exec flag will tell to execute someting with each find result)
# -> -> rsync will synchronize the the files from find to a destination path
find . -type f -name "<file.ext>" -exec rsync -R <destination_path> \;
```

## How's and where's

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


