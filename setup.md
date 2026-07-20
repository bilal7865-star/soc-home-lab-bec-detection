# Setup Guide - BEC Detection Lab

This document provides step-by-step instructions to set up the Business Email Compromise (BEC) detection lab on Kali Linux.

## Prerequisites

- Kali Linux (Updated)
- At least 5-10 GB free disk space
- Splunk Enterprise installed and running

## 1. Install and Configure Postfix

```bash
sudo apt update
sudo apt install postfix mailutils swaks -y
```

During installation, select:

Internet Site
System mail name: lab.local

## 2. Configure Postfix
Edit the main configuration:
```
bash
sudo nano /etc/postfix/main.cf
Add/Update these lines:
Bashmyhostname = mail.lab.local
mydomain = lab.local
myorigin = $mydomain
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
home_mailbox = Maildir/
inet_interfaces = all
inet_protocols = all
```

Restart Postfix:
Bash
```
sudo systemctl restart postfix
sudo systemctl status postfix
```

## 3. Test Email Simulation
Bash
```Simple test
echo "Test body" | mail -s "Test Email" $(whoami)@lab.local

# BEC Simulation (Spoofed)
swaks --to $(whoami)@lab.local \
      --from ceo@company.com \
      --server 127.0.0.1 \
      --header "Subject: Urgent Wire Transfer Request" \
      --body "Please process this payment immediately."
```

## 4. Splunk Configuration
Add Data Input

Go to Settings → Add Data → Monitor
Path: /var/log/mail.log
Source Type: Create new → mail:postfix
Index: main

Test Search
splindex=main sourcetype="mail:postfix" | tail 20

## 5. Detection Rules
See README.md for the main BEC detection queries and alerts.
