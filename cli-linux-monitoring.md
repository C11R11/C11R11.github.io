```sh
# ==============================================================================
# CROSS-DISTRIBUTION LINUX LOG MONITORING CHEATSHEET
# ==============================================================================

# 1. REAL-TIME LOG STREAMING (FOLLOWING)
# ------------------------------------------------------------------------------
# Follow a specific text log file dynamically in real-time

sudo tail -f /var/log/syslog          # Debian/Ubuntu style global log
sudo tail -f /var/log/messages        # RHEL/CentOS/Fedora style global log


# Follow by filename (robust against log rotation/truncation)
sudo tail -F /var/log/secure          # Monitor authentication logs on RHEL
sudo tail -F /var/log/auth.log        # Monitor authentication logs on Debian

# Stream live binary logs for the entire system via systemd
sudo journalctl -f

# Stream live systemd logs for a specific background service unit
sudo journalctl -f -u sshd


# 2. READING AND NAVIGATING LOG FILES
# ------------------------------------------------------------------------------
# Open a log file in a scrollable viewer (press 'q' to exit, '/' to search)
sudo less /var/log/messages

# Print only the final 50 lines of a specific log file
sudo tail -n 50 /var/log/syslog

# Print only the very first 20 lines of a log file
sudo head -n 20 /var/log/auth.log


# 3. FILTERING AND SEARCHING LOG DATA
# ------------------------------------------------------------------------------
# Search a log file for a specific case-insensitive string (e.g., "error")
sudo grep -i "error" /var/log/messages

# Live-stream a log file while filtering out and showing only matching terms
sudo tail -f /var/log/syslog | grep "denied"

# Search through compressed/archived historical log files (.gz format)
sudo zgrep -i "failed" /var/log/auth.log.*.gz


# 4. SYSTEMD JOURNALCTL FILTERING (UNIVERSAL MODERN LINUX)
# ------------------------------------------------------------------------------
# View all logs generated since the current system boot sequence
sudo journalctl -b

# Filter system logs to display errors, critical alerts, and panics only
sudo journalctl -p err..emerg

# Show all logs generated within a specific custom window of time
sudo journalctl --since "2026-07-10 00:00:00" --until "2026-07-10 12:00:00"
sudo journalctl --since "1 hour ago"
```