# Search for files
```sh

### Look for all the files within a certain folder
# alt 1: with ls (shows only name)
ls -R | grep "<file.ext>"
# alt 2: with find (shows files with path)
find . -type f -name "<file.ext>"

### look for all the files with a certain extension
find . -type f -name "<*.ext>"

tree                  # List current directory contents in a tree structure
tree /path/to/dir     # List a specific directory path
tree -a               # Show all files, including hidden files (starting with .)
tree -d               # List directories only (no files)
tree -f               # Print the full path prefix for each file

# --- LEVEL CONTROL ---
tree -L 2             # Limit the display to a maximum depth of 2 levels
tree -L 3 -d          # Limit depth to 3 levels, directories only

# --- FILE FILTERING ---
tree -P "*.txt"       # Only list files that match the pattern (*.txt)
tree -I "node_modules|temp" # Exclude files/directories matching a pattern
tree --prune          # Remove empty directories from the output

# --- FILE DETAILS & PERMISSIONS ---
tree -p               # Print file type and permissions (like ls -l)
tree -u               # Display the file owner or UID number
tree -g               # Display the group name or GID number
tree -s               # Print the size of each file in bytes
tree -h               # Print file sizes in a human-readable format (e.g., 4K, 2M)
tree --si             # Like -h, but use powers of 1000 instead of 1024

# --- SORTING OPTIONS ---
tree -r               # Sort the output in reverse order (Z to A)
tree -t               # Sort output by last modification time (newest first)
tree -c               # Sort output by last status change time (ctime)
tree -U               # Do not sort (leave unsorted in directory order)

# --- GRAPHICS & OUTPUT ---
tree -C               # Turn on colorization of files/folders
tree -A               # Use ASCII line graphics (instead of UTF-8)
tree -X               # Print output in XML format
tree -J               # Print output in JSON format
tree -o output.txt    # Write the output directly into a text file

```

## Filter

```sh
# List and filter without extension
ls -1 | sed -e 's/\.mp3$//
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

# Generate ssh keys and connect 

```sh
# ==========================================
# 1. ON YOUR LOCAL LINUX PC
# ==========================================

# Step A: Generate an SSH key pair (if you don't have one yet)
# Press Enter to accept defaults; don't overwrite if you already have keys!
ssh-keygen -t ed25519 -C "local-pc"

# Step B: Securely copy your public key to the server
# This automatically adds your PC to the server's '~/.ssh/authorized_keys'
# (Replace 'username' and 'server_ip' with your actual server details)
ssh-copy-id username@server_ip

# Step C: Manually add the server to your local 'known_hosts' ahead of time
# This prevents the "authenticity of host can't be established" warning on your first connection
ssh-keyscan -H server_ip >> ~/.ssh/known_hosts

#Create the config gile
mkdir -p ~/.ssh && touch ~/.ssh/config && chmod 600 ~/.ssh/config && nano ~/.ssh/config

# Use this config

# ==========================================
# SSH CONFIGURATION FOR MY SERVER
# ==========================================

Host myserver
    HostName 192.168.1.50
    User ubuntu
    # Optional settings (uncomment by removing '#' if needed):
    # Port 22
    # IdentityFile ~/.ssh/id_ed25519

# Connect to the server
ssh myserver

```

# Mounting disks

```sh
# Look at the size of the disk to identify the correct one.
lsblk

# Create a folder where the USB contents will appear (change 'usb-drive' to whatever name you want)
sudo mkdir -p /media/usb-drive

# Mount the specific partition (replace '/dev/sdb1' with your actual USB partition from step 1)
sudo mount /dev/sdb1 /media/usb-drive

# Verify it successfully mounted by checking available space
df -h | grep /media/usb-drive

# mount ntfs
sudo mount -t ntfs-3g -o uid=1000,gid=1000,rw /dev/sda2 /media/cristian/usb_fix/

```

## Persist the volume mount

```sh
# Step A: Find the exact UUID for sda2 (Look for the UUID="..." string in the output)
sudo blkid /dev/sdax

# Step B: Open your system mount configuration file
sudo nano /etc/fstab

# Insert the block id and the mounting point
UUID=YOUR-ACTUAL-UUID-HERE /media/xxxxxxx ext4 defaults,nofail 0 2
# For ntfs
UUID=YOUR-ACTUAL-UUID-HERE /media/xxxxxxx ntfs-3g defaults,nofail,uid=1000,gid=1000,dmask=022,fmask=133 0 0

# 3. Test the fstab entry immediately without rebooting
sudo mount -a

# 4. Confirm it mounted and shows up as fuseblk/ntfs-3g (df is for diskfree)
df -T /media/usb-media

/dev/sda2: LABEL="SWITCH" BLOCK_SIZE="512" UUID="EA24613124610245" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="b8e5a15d-0cb1-4d20-a8b2-124c13f1e184"


UUID=EA24613124610245 /media/usb-media ext4 defaults,nofail 0 2

```
## Maintain system volumes

```bash
# This will watch the disk free space 
watch -n 1 df -h

# This will list the most large files 
du -h -d 1 | sort -hr | head -n 20

# Finding for some file name
du -h -d 1 | sort -hr | grep "xyz"

# Full folder structure
tree

#This is to copy files and see progress (mv command doesn't have a progress option, only verbose)
rsync -a --info=progress2 --remove-source-files source_file.mp4 /path/to/destination/
rsync -av --info=progress2 --remove-source-files /path/to/source/ /path/to/destination/
```
