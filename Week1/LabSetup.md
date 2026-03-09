# Project Documentation: Hybrid Network & Web Forensic Investigation

## 1. Environment Setup (Infrastructure)
The project environment consists of a three-tier architecture to simulate a real-world cyber attack and forensic investigation.

*   **Target Server (Victim):** Ubuntu Linux (VirtualBox VM) running Apache, MySQL (MariaDB), and PHP (LAMP Stack).
*   **Attacker:** Windows (Host Machine) using Burp Suite and Browser.
*   **Forensic/Monitoring Station:** Kali Linux (VirtualBox VM) for traffic analysis (Wireshark/Zeek).

### Network Configuration:
*   **Initial State:** NAT (Network Address Translation). 
*   **Problem:** The Windows Host could not reach the Ubuntu VM directly.
*   **Solution:** Switched the Ubuntu VM network adapter to **"Host-Only Adapter."**
*   **Result:** The Ubuntu server obtained a static-reachable IP: `192.168.56.101`.

---

## 2. Application Setup (DVWA Deployment)
We deployed **Damn Vulnerable Web Application (DVWA)** as the target for forensic analysis.

1.  **Software Installation:** 
    Installed Apache2, MariaDB-server, PHP, and Git.
    `sudo apt install apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php git -y`
2.  **Deployment:** 
    Cloned the DVWA repository into the web root: `/var/www/html/DVWA`.
3.  **Permissions:** 
    Adjusted ownership to the web user: 
    `sudo chown -R www-data:www-data /var/www/html/DVWA`

---

## 3. Problems Faced & Technical Solutions

### Problem 1: Commands "Not Found"
*   **Issue:** `mysql` and `apache2` commands were missing on the initial Ubuntu VM.
*   **Cause:** The VM was a minimal build without a pre-installed LAMP stack.
*   **Solution:** Executed a full repository update and manual installation of the LAMP components.

### Problem 2: Database Access Denied (Error #1698)
*   **Issue:** DVWA could not connect to the database, showing "Access denied for user 'dvwa'@'localhost'".
*   **Cause:** MariaDB uses `unix_socket` for the root user by default, and the 'dvwa' user didn't exist with the correct permissions.
*   **Solution:** Manually logged into MariaDB and created the user:
    ```sql
    CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
    FLUSH PRIVILEGES;
    ```

### Problem 3: Configuration Password Mismatch
*   **Issue:** Even after creating the user, the connection failed.
*   **Cause:** The DVWA `config.inc.php` file had a default password `'p@ssw0rd'`, whereas the database user was created with `'password'`.
*   **Solution:** Edited `/var/www/html/DVWA/config/config.inc.php` to match the database password.

### Problem 4: Forensic Feature Requirements
*   **Issue:** The "File Inclusion" module in DVWA was disabled.
*   **Solution:** Modified the PHP configuration file (`php.ini`) to set `allow_url_include = On` and restarted the Apache service.

---

## 4. Forensics Execution (Week 1: Traffic Capture)
To simulate the first stage of the investigation, we performed a live capture of an **SQL Injection** attack.

1.  **Capture Tool:** Used `tcpdump` on the Ubuntu server to record raw network traffic.
    `sudo tcpdump -i enp0s3 -w ~/graduation_week1.pcap`
2.  **Attack Execution:** Performed an SQL Injection (`' or 1=1 --`) from the Windows Host via a web browser.
3.  **Verification:** Used `strings` and `grep` on the Ubuntu server to verify the attack was recorded in the PCAP file.

---

## 5. Roadmap: Enhancing Server-Side Logging
For the upcoming phases (Week 2 & 3), we will move beyond network traffic and enable deep server logging to perform **Event Correlation**.

### A. Apache Web Server Logs
*   **Access Logs:** Located at `/var/log/apache2/access.log`. This records the IP, Timestamp, and the exact URL/Payload sent by the attacker.
*   **Error Logs:** Located at `/var/log/apache2/error.log`. This records failed PHP executions or database errors triggered by attacks.

### B. MySQL/MariaDB Query Logging
*   **Action:** Enable the "General Query Log" to see the exact SQL commands being executed on the database after an injection.
*   **Command:** `SET GLOBAL general_log = 'ON';` inside MySQL.
*   **Log Path:** `/var/lib/mysql/[hostname].log`.

### C. System Authentication Logs
*   **Action:** Monitor `/var/log/auth.log` for Brute Force attempts on the login page or unauthorized `sudo` attempts.

### D. Auditd (Optional but Professional)
*   **Action:** Install `auditd` to monitor file system changes. If an attacker uploads a shell (Week 3), `auditd` will log exactly which file was created and by which process.

---

## Summary of Week 1
We have successfully built the lab, resolved all connectivity and database issues, and successfully captured our first forensic evidence (PCAP). We are now ready to move into **Phase 2: Log Analysis and Correlation.**
