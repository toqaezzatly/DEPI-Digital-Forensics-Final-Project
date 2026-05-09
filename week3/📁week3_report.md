# Week 3 Report – Forensic Analysis & Correlation

## Project: Hybrid Network & Web Forensic Investigation  
## Phase: Week 3 – Analysis & Correlation  

---

## 1. Objective
The objective of this phase was to analyze and correlate forensic evidence collected from web logs, system logs, and network traffic in order to reconstruct the attack timeline.

---

## 2. Data Sources
- Apache Web Logs  
- System Authentication Logs (auth.log)  
- Network Traffic (PCAP)  
- DVWA Web Application Traffic  

---

## 3. Web Log Analysis
Apache logs showed repeated POST requests to /DVWA/login.php indicating automated login attempts against DVWA (Damn Vulnerable Web Application).

### Findings:
- Multiple login attempts detected
- Repeated requests in short time
- Suspicious automated behavior

---

## 4. System Log Analysis
Authentication logs showed multiple failed SSH login attempts targeting user "devasc" from IP 192.168.56.103.

### Findings:
- Repeated failed password attempts
- Same source IP
- High frequency activity

---

## 5. Network Analysis (Wireshark)
Network traffic confirmed repeated HTTP POST requests to /DVWA/login.php and HTTP 302 responses.

### Findings:
- Same behavior as Apache logs
- Confirmation of login attempts at network level

---

## 6. Correlation
All logs point to the same attacker IP (192.168.56.103) showing coordinated activity across multiple layers.

---

## 7. Attack Timeline
1. Reconnaissance using scanning tools  
2. Web login attempts on DVWA  
3. SSH brute force attempts  
4. Network validation via packet capture  

---

## 8. Conclusion
The evidence confirms a multi-stage attack involving reconnaissance, web exploitation attempts, and brute force authentication attacks.