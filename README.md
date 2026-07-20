# SOC Home Lab - BEC Detection Pipeline 


**A production-grade simulation of Business Email Compromise (BEC) attack detection using a single Kali Linux host.**

---

## Project Description

This project demonstrates the end-to-end detection of **Business Email Compromise (BEC)** attacks — one of the most financially damaging threats faced by organizations today.

As part of my **SOC Analyst Home Lab Journey**, I built a realistic detection pipeline on a **resource-constrained single Kali Linux machine** (no additional VMs due to storage limitations).

### Key Objectives Achieved
- Simulate realistic BEC attack scenarios (CEO fraud, urgent wire transfer, invoice manipulation)
- Deploy and configure a lightweight mail server (Postfix)
- Ingest and parse mail logs into Splunk Enterprise
- Develop high-fidelity detection rules using SPL
- Create professional documentation and architecture diagram

---<img width="1408" height="768" alt="Gemini_Generated_Image_txfd4atxfd4atxfd" src="https://github.com/user-attachments/assets/949404d0-020e-4bb6-bcd7-0365d0b3d361" />


## Lab Architecture


**Components:**
- **Attack Layer**: Kali Linux + Swaks (Spoofed Email Simulation)
- **Mail Server**: Postfix (SMTP) with local delivery
- **Log Source**: `/var/log/mail.log`
- **SIEM**: Splunk Enterprise 10.4.1
- **Detection**: Custom `mail:postfix` sourcetype + SPL-based alerts

---

## Tools & Technologies

| Category          | Technology                  | Purpose                          |
|-------------------|-----------------------------|----------------------------------|
| OS                | Kali Linux                  | Lab Environment                  |
| Mail Server       | Postfix + Mailutils         | Email Simulation                 |
| Attack Tool       | Swaks                       | BEC Email Spoofing               |
| SIEM              | Splunk Enterprise           | Log Ingestion & Detection        |
| Documentation     | Markdown + Architecture     | Professional Portfolio           |

---

## Implementation Highlights

### 1. Mail Server Setup
- Installed and configured Postfix for local domain (`lab.local`)
- Enabled proper logging for security monitoring

### 2. Attack Simulation
Used `swaks` to simulate sophisticated BEC scenarios:
- Executive impersonation
- Urgent financial requests
- Spoofed sender domains

### 3. Splunk Integration
- Created custom sourcetype `mail:postfix`
- Added file monitor for `/var/log/mail.log`
- Built real-time data ingestion pipeline

### 4. Detection Engineering

**High Priority BEC Alert:**
```spl
index=main sourcetype="mail:postfix" 
( "wire transfer" OR "urgent payment" OR "new account" OR "invoice" OR "ceo@" OR "executive" )
| stats count by from, to, subject, _time
| where count >= 1
