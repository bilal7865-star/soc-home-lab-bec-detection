# Setup Guide - BEC Detection Pipeline

This document provides complete step-by-step instructions to replicate the lab.

## Prerequisites

- Kali Linux (updated)
- Minimum 10-15 GB free disk space
- Splunk Enterprise installed and running
- Root/Sudo access

---

## 1. Postfix Mail Server Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Postfix and tools
sudo apt install postfix mailutils swaks -y

During Postfix installation:

Choose "Internet Site"
Set System mail name to: lab.local

Configure Postfix
Bashsudo nano /etc/postfix/main.cf
Update/add these lines:
confmyhostname = mail.lab.local
mydomain = lab.local
myorigin = $mydomain
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
home_mailbox = Maildir/
inet_interfaces = all
inet_protocols = all

Apply changes:
Bashsudo systemctl restart postfix
sudo systemctl enable postfix
sudo systemctl status postfix

2. Test Email Simulation (BEC Attacks)
Bash# Simple test
echo "This is a BEC simulation test" | mail -s "Test Email" $(whoami)@lab.local

# Realistic BEC spoofing using Swaks
swaks --to $(whoami)@lab.local \
      --from ceo@company.com \
      --server 127.0.0.1 \
      --header "Subject: Urgent: Wire Transfer Request" \
      --body "Please process this payment immediately to the new account details."

3. Splunk Configuration
Add Data Input

Go to Settings → Add Data → Monitor
Select Files and Directories
Path: /var/log/mail.log
Source type: Create new → mail:postfix
Index: main
Apply

Test Search
splindex=main sourcetype="mail:postfix" | tail 20

4. Detection Rules
See README.md for full SPL queries and alert configuration.

Troubleshooting

Postfix not starting: Check sudo journalctl -u postfix
No logs: Run sudo tail -f /var/log/mail.log
Splunk not seeing data: Verify monitor path and permissions
Repository issues: sudo apt update --fix-missing
