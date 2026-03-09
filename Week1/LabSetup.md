# Technical Report: Hybrid Forensic Investigation Environment Setup

## 1. System Architecture & Role Distribution

To simulate a realistic Cyber Attack and Forensic Investigation, we deployed a three-node architecture. Each node serves a specific role in the **Attack-Defend-Investigate** lifecycle.

### A. Windows Machine (The Adversary / Attacker)
*   **Role:** The entry point of the attack. It simulates an external threat actor targeting the organization's web infrastructure.
*   **Tools Used:**
    *   **Browser (Brave/Chrome):** Used to interact with the target web application.
    *   **Burp Suite:** Acting as an interception proxy to perform manual and automated web attacks (SQLi, Brute Force).
    *   **Nmap (Optional):** Used for initial network reconnaissance and port scanning.
*   **Connection:** Connects to the Ubuntu server via the `192.168.56.0/24` Host-Only network.

### B. Ubuntu Server (The Victim / Evidence Source)
*   **Role:** The target production server hosting the **Damn Vulnerable Web Application (DVWA)**. It is the primary source of "Live Evidence."
*   **Services:** Apache2 (Web Server), MariaDB (Database), PHP 7.4 (Application Logic).
*   **Forensic Artifacts:** Generates `access.log`, `error.log`, `auth.log`, and raw network packets (`.pcap`).

### C. Kali Linux (The Forensic Investigator)
*   **Role:** The dedicated forensic workstation used by the Digital Forensics and Incident Response (DFIR) team.
*   **Functions:**
    *   **Traffic Analysis:** Running **Wireshark** to inspect the `.pcap` files captured from the network.
    *   **Protocol Intelligence:** Using **Zeek (Bro)** to convert raw packets into high-level, searchable connection logs.
    *   **Evidence Processing:** Serving as the central point for correlating logs from the Ubuntu server with the network traffic.

---

## 2. Project Network Schema

Below is the logical representation of the environment. The **Windows Attacker** sends malicious traffic to the **Ubuntu Target**, while the **Kali Investigator** monitors and analyzes the interaction.

```text
       +-----------------------+                    +-----------------------+
       |   WINDOWS (Host)      |                    |    KALI LINUX (VM)    |
       |  (The Attacker)       |                    |   (The Investigator)  |
       +-----------+-----------+                    +-----------+-----------+
                   |                                            |
                   | [192.168.56.1]              [192.168.56.10] |
                   |                                            |
      =============+====================+=======================+=============
                                        | [Virtual Network Switch]
                                        |
                          +-------------+-------------+
                          |      UBUNTU SERVER (VM)   |
                          |        (The Target)       |
                          |       [192.168.56.101]    |
                          +---------------------------+
                                        |
                          +-------------+-------------+
                          |   FORENSIC ARTIFACTS      |
                          | - Network PCAPs           |
                          | - Apache Access Logs      |
                          | - MySQL Query Logs        |
                          +---------------------------+
```

---

## 3. Setup Documentation & Troubleshooting

### Phase 1: Environment Connectivity
*   **Challenge:** Initial communication failed because the VMs were on a NAT network, isolating them from the Windows Host.
*   **Solution:** Migrated the Ubuntu and Kali network adapters to **"Host-Only Adapter."** 
*   **Result:** Established a private lab network where the Windows Host acts as a gateway and attacker.

### Phase 2: Application & Database Configuration
*   **Database Error (#1698):** Access was denied for the `dvwa` user. This was resolved by manually creating a local database user with full privileges:
    ```sql
    CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
    ```
*   **Credential Sync:** We identified a mismatch between the database user password and the DVWA configuration file (`config.inc.php`). We updated the file to ensure seamless connectivity.

### Phase 3: Forensic Feature Enablement
*   To support the **Week 3: Web Application Forensics** phase (specifically File Inclusion attacks), we manually enabled the `allow_url_include` directive in the `php.ini` file and restarted the Apache service.

---

## 4. Forensic Logging Strategy (Next Steps)

To achieve the "Hybrid Investigation" goal, we are moving beyond simple packet captures. The following logging mechanisms are being enabled on the Ubuntu server:

1.  **Network Evidence:** Continuing the use of `tcpdump` to capture raw traffic for Wireshark analysis.
2.  **Web Evidence:** Monitoring `/var/log/apache2/access.log` to identify HTTP request patterns and IP origins.
3.  **Database Evidence:** Enabling the **MySQL General Query Log** to record exactly what data the attacker attempted to extract via SQL Injection.
4.  **Correlation Logic:** Using timestamps to link a specific packet in **Wireshark** to a specific entry in the **Apache Access Log**, creating a "Chain of Custody" for the evidence.

---

## 5. Conclusion of Week 1
We have successfully established a functional forensic lab. We have simulated a **SQL Injection** attack, captured the traffic in a `.pcap` file, and verified that the attack payload is visible during analysis. The environment is now ready for deep-dive log analysis and report generation.
