# Week 2 Report: Attack Simulation & Evidence Acquisition
**Project:** Hybrid Network & Web Forensic Investigation  
**Focus:** Generating Malicious Artifacts and Multi-Source Log Collection  
**Investigator:** [Toqa Ayman & Basmalla Atta]  
**Date:** April 28, 2026

---

## 1. Objective
The goal of this week was to simulate a multi-stage cyber attack to generate realistic host-based and network-based evidence. This phase ensures that the forensic investigator has a complete set of logs from the operating system, web server, and database to correlate in the next phase.

---

## 2. Attack Simulation Details

### Attack A: Advanced SQL Injection (SQLi)
*   **Target:** DVWA SQL Injection Module (Security: Low).
*   **Adversary Method:** Manual injection via the browser on the Windows Host.
*   **Payloads Executed:**
    1.  `' or 1=1 --`: Authentication bypass to dump the user database.
    2.  `' union select user(), database() --`: Information gathering to identify the database user and name.
    3.  `' and sleep(5) --`: Time-based injection to test for blind vulnerabilities.
*   **Simulation Goal:** To trigger entries in the Apache `access.log` and the MySQL `general_log`.

### Attack B: Automated Brute Force
*   **Target:** DVWA Login Interface.
*   **Adversary Method:** Automated dictionary attack using **Burp Suite Intruder**.
*   **Configuration:**
    *   **Payload 1 (Usernames):** A list of 20 common administrative usernames (admin, root, etc.).
    *   **Payload 2 (Passwords):** A list of 20 common weak passwords (123456, p@ssw0rd, etc.).
    *   **Total Requests:** 400 attempts launched at high velocity.
    *   **Adversary Persona:** Updated the **User-Agent** to a modern Chrome string to mimic legitimate user traffic.
*   **Simulation Goal:** To generate a high-volume "noise" pattern in the `access.log` and multiple failure records in the system `auth.log`.

---

## 3. Evidence Collection Procedure (Linux Commands)

After the simulation, the following forensic acquisition process was executed on the Ubuntu server to collect all necessary artifacts.

### A. Web Server Artifacts (Apache2)
These logs capture the "entry point" of the web attacks.
```bash
# Capture every HTTP request and error
sudo bash -c 'cp /var/log/apache2/access.log* ~/DEPI-Digital-Forensics-Final-Project/week2/apache2_logs/'
sudo bash -c 'cp /var/log/apache2/error.log* ~/DEPI-Digital-Forensics-Final-Project/week2/apache2_logs/'
```

### B. Database Artifacts (MySQL/MariaDB)
These logs prove that the SQLi payloads successfully reached the database engine.
```bash
# Capture the execution of injected SQL queries
sudo bash -c 'cp /var/lib/mysql/*.log ~/DEPI-Digital-Forensics-Final-Project/week2/mysql_logs/'
```

### C. OS Authentication Artifacts (System)
These logs record the failed authentication attempts from the Brute Force attack.
```bash
# Capture login failures and SSH attempts
sudo cp /var/log/auth.log* ~/DEPI-Digital-Forensics-Final-Project/week2/system_auth_logs/
```

### D. Network Artifacts (PCAP)
Raw packet data was collected using `tcpdump` to provide the network layer perspective.
```bash
# Move all PCAP captures to the project repository
cp ~/graduation_week1.pcap ~/DEPI-Digital-Forensics-Final-Project/week2/captures/
cp ~/full_forensic_capture.pcap ~/DEPI-Digital-Forensics-Final-Project/week2/captures/
```

---

## 4. Evidence Inventory Summary

| Category | Artifact Type | Quantity/Source | Forensic Importance |
| :--- | :--- | :--- | :--- |
| **Network** | `.pcap` | 2 Files (tcpdump) | Packet-level proof of the attack payload. |
| **Web** | `access.log` | Full Rotation (Apache) | Identifies Attacker IP and malicious URI strings. |
| **Database** | `labvm.log` | General Query Log | Confirms successful execution of SQLi. |
| **System** | `auth.log` | Auth Logs (Linux) | Records the volume and results of Brute Force. |

---

## 5. Challenges and Troubleshooting
*   **Issue:** Network isolation when using Host-Only adapter.
    *   **Solution:** Temporarily enabled a NAT adapter (Adapter 2) to allow `git` operations while maintaining a private attack network on Adapter 1.
*   **Issue:** Git Password Deprecation.
    *   **Solution:** Generated a **Personal Access Token (PAT)** on GitHub and updated the local Git remote URL to authenticate the evidence upload.

---

## 6. Conclusion
The simulation and collection phase of Week 2 is complete. All relevant logs have been successfully acquired, verified, and pushed to the GitHub repository. The environment and the evidence are now fully prepared for **Week 3: Detailed Forensic Analysis and Timeline Reconstruction.**

