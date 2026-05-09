
**FORENSIC EXAMINATION REPORT**

**Date:** May 9, 2026
** Digital Invistigators :** Toqa Ayman & Basmalla Atta

---

### **Introduction**
A forensic examination was conducted on a hybrid network environment following a simulated multi-stage cyber attack. The purpose of this investigation is to identify the origin, methods, and impact of unauthorized access attempts targeting a Linux-based web server. This report documents the collection, preservation, and analysis of host-based and network-based evidence to reconstruct the adversary’s timeline.

### **Executive Summary**
The examination confirmed a coordinated attack originating from IP **192.168.56.103**. The investigation identified two primary attack vectors: a manual SQL Injection (SQLi) targeting the web application’s database and an automated brute-force attack targeting both the web login interface and the system’s Secure Shell (SSH) service. Forensic artifacts from Apache, MySQL, and Linux system logs were successfully correlated with network packet captures (PCAP) to confirm the scope of the incident.

### **Evidence Summary**
Evidence was collected from a virtualized Ubuntu environment and network interfaces. Tools used for analysis include **Wireshark** for packet inspection, **Grep/Bash** for log parsing, and **Burp Suite** for attack replication.

**Case Item # 001**
*   **Description of Device:** Ubuntu Linux Server (Lab VM) hosting Apache2, MySQL, and DVWA.
*   **Owners of Device:** Internal Research Lab
*   **Received/Permission From:** Lab Administrator
*   **Device Manufacturer:** Virtual Machine (VMware/VirtualBox)
*   **Operating System/Version #:** Ubuntu 20.04 LTS / Linux Kernel 5.x
*   **Artifacts Recovered:** 
    *   Web Logs: `/var/log/apache2/access.log`
    *   Database Logs: `/var/lib/mysql/labvm.log` (General Query Log)
    *   System Logs: `/var/log/auth.log`

**Case Item # 002**
*   **Description of Device:** Network Traffic Captures (PCAP files)
*   **Location:** Captured from the Host-Only Network Adapter 1
*   **Storage Capacity:** ~50MB (Raw Packet Data)
*   **Artifacts Recovered:** `graduation_week1.pcap`, `full_forensic_capture.pcap`

---

### **Forensic Methodology and Analysis**

**1. Acquisition and Integrity:**
Evidence acquisition was performed using standard Linux command-line utilities to preserve the original state of the logs. Artifacts were copied from their protected system directories into a dedicated forensic repository (`~/DEPI-Digital-Forensics-Final-Project/`). Integrity was maintained by ensuring a read-only analysis approach on the copied data.

**2. Web and Database Correlation:**
The investigator analyzed the Apache `access.log`, identifying a series of suspicious GET and POST requests from IP **192.168.56.103**. Specifically, URI strings contained encoded SQL payloads such as `' or 1=1 --` and `' union select user(), database() --`. To verify if these attempts were successful, the investigator cross-referenced these timestamps with the MySQL `labvm.log`. The database logs confirmed that the SQL engine executed these injected queries, proving a successful exploitation of the SQL Injection vulnerability.

**3. Brute Force and Network Validation:**
Analysis of the `auth.log` revealed a high-volume "noise" pattern, characterized by hundreds of failed login attempts for the user "devasc" within a very narrow time window. This matched the pattern found in the web logs, where automated POST requests were sent to `/DVWA/login.php`. Using **Wireshark**, the network traffic confirmed these attempts, showing HTTP 302 redirect responses following the failed logins. The source IP remained consistent across all forensic sources, linking the web, system, and network activity to a single adversary persona.



### **Conclusion**
The data examined indicates that the target server was subjected to a multi-stage attack involving reconnaissance, database exploitation, and credential harvesting. 

**Key Findings:**
*   **Attacker Origin:** 192.168.56.103.
*   **Attack Vectors:** SQL Injection (Manual) and Brute Force (Automated).
*   **Impact:** The attacker successfully queried database metadata via SQLi and generated significant authentication failure logs.

The forensic mission was successful in filling the missing data pieces. By correlating the HTTP requests in the web logs with the query execution in the database logs and the packet-level detail in the PCAP files, we have reconstructed a complete timeline of the adversary's actions. The evidence suggests the need for immediate implementation of input validation and account lockout policies (e.g., Fail2Ban) to mitigate future incidents.

