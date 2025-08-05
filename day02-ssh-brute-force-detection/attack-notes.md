#  Wazuh Lab – Task 2: SSH Brute Force Attack Detection + MITRE ATT&CK Mapping 

##  Objective  
Simulate an SSH brute-force attack and validate Wazuh’s ability to detect and classify the event using logs, rules, and MITRE ATT&CK mapping.  

---  

##  Lab Setup  
- **Attacker Machine**: Kali Linux 
- **Target Machine**: Ubuntu Server  
- **SIEM**: Wazuh  
- **Agent Name**: `soc` (on the Ubuntu server)  
- **Tool Used for Attack**: Hydra  

---  

## 🔧 Attack Execution  
We used the following command to simulate a brute-force SSH attack:  

```bash  
hydra -l soc -P /usr/share/wordlists/rockyou.txt ssh://192.168.x.x  
```  

Hydra attempted multiple login requests using a large password list for the user `soc`.  

---  

##  Detection by Wazuh  
Wazuh agent `soc` detected the following logs and generated alerts:  

| Rule ID | Level | Description                              | Source IP    | Trigger Count |  
|---------|-------|------------------------------------------|--------------|---------------|  
| 5760    | 5     | sshd: authentication failed              | 192.168.x.x | 3900+         |  
| 5758    | 8     | Maximum authentication attempts exceeded | 192.168.x.x | 700+          |  

- Wazuh correctly captured logs from `/var/log/auth.log` and `journald`.  
- Alerts triggered under rule group: `sshd`, `authentication_failed`.  
- Events seen in both raw logs and decoded alerts in Wazuh dashboard.  

---  

##  MITRE ATT&CK Mapping  

| Technique ID | Name                 | Tactic            |  
|-------------|----------------------|-------------------|  
| T1110       | Brute Force          | Credential Access |  
| T1110.001   | Password Guessing    | Credential Access |  
| T1021.004   | Remote Services: SSH | Lateral Movement  |  

These MITRE techniques were auto-tagged via Wazuh detection rules:  
- **T1110**: Detected based on repeated failed attempts (rule ID 5758).  
- **T1110.001**: Individual authentication failures (rule ID 5760).  
- **T1021.004**: Context inferred from the use of SSH for movement.  

---  

## 📸 Screenshots  
Screenshots saved in the repo under `screenshots`:  
- `task2-ssh-bruteforce-alerts.png`: Visual of Wazuh dashboard showing alert spikes.  
- `task2-mitre-filter.png`: Filtered MITRE technique view showing T1110 and others.  

---  

##  Compliance Standards Mapped  
Wazuh mapped this detection to the following standards automatically:  

| Standard        | Reference Codes            |  
|-----------------|----------------------------|  
| **PCI DSS**     | 10.2.4, 10.2.5             |  
| **HIPAA**       | 164.312.b                  |  
| **NIST 800-53** | AU.14, AC.7                |  
| **GDPR**        | IV_35.7.d, IV_32.2         |  
| **TSC**         | CC6.1, CC6.8, CC7.2, CC7.3 |  
| **GPG13**       | 7.1                        |  

---  

## Analysis  
- **Detection Strength**: Wazuh is highly effective at identifying brute-force behavior.  
- **Rule Depth**: Escalation from level 5 (individual failures) to level 8 (threshold exceeded).  
- **Log Sources**: Both journald and traditional log files were parsed successfully.  
- **Tagging**: Auto-tagging to MITRE framework enables quick triage and correlation with adversary techniques.  

---  

##  Conclusion  
This task validates that **Wazuh detects brute-force attacks with high accuracy**, generates appropriate alerts, and integrates smoothly with **MITRE ATT&CK for threat classification**. Such detections form a foundational element in building a SOC detection capability.  

---  

