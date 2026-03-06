# Cyber Security Analyst Lab – SIEM Investigation 


This project simulates attacker behaviour against a vulnerable Linux system and investigates the resulting activity using a Security Information and Event Management (SIEM) platform.

A Kali Linux machine was used to perform reconnaissance and enumeration attacks against a Metasploitable 2 server. The logs generated during these activities were collected and analysed using Splunk to identify suspicious behaviour.

The aim of the lab was to demonstrate how a security analyst can detect attacker activity through log analysis.



# Lab Environment

The lab environment consisted of three systems running on the same network.

Kali Linux – attacker machine used to perform scanning and enumeration  
Metasploitable 2 – deliberately vulnerable Linux server used as the target  
Windows Host – used to run Splunk for log ingestion and analysis



# Tools Used

Kali Linux  
Metasploitable 2  
Splunk Enterprise  
Nmap  
Hydra  
Gobuster



# Reconnaissance

Initial reconnaissance was performed using Nmap to identify open ports and services running on the Metasploitable server.

Command used:

nmap <target-ip>

nmap -sV <target-ip>

An aggressive scan was also performed to gather additional service information.

nmap -A <target-ip>

The scans revealed several exposed services including SSH, HTTP, FTP and Telnet.



# Attempted SSH Brute Force

A brute force attempt was made against the SSH service using Hydra.

hydra -l msfadmin -P rockyou.txt ssh://<target-ip>

Due to compatibility issues between modern SSH clients and the legacy configuration on Metasploitable, the attack could not fully execute. However authentication related events were still generated in the system logs.



# Web Enumeration

Directory enumeration was performed against the web server using Gobuster.

gobuster dir -u http://<target-ip> -w wordlist.txt

The scan discovered several directories and files including:

/phpinfo.php  
/phpMyAdmin  
/test  
/twiki  

These directories could potentially expose sensitive information or provide entry points for further attacks.



# Log Collection

Logs generated during the attack were collected from the Metasploitable system.

The main logs used for analysis were:

/var/log/auth.log  
/var/log/apache2/access.log

An attempt was made to transfer the logs using SCP but this was unsuccessful due to the legacy SSH configuration on the target system.

Instead, the logs were downloaded using HTTP and moved to the host machine for analysis.



# Log Ingestion into Splunk

The logs were uploaded into Splunk using the **Add Data** function and indexed under:

cyber-analyst-lab

Example queries used during investigation:

index=cyber-analyst-lab sourcetype=linux_auth_log

index=cyber-analyst-lab sourcetype=linux_access_log



# Authentication Log Analysis

Authentication logs were searched for suspicious login or connection attempts.

Events were identified showing connections originating from the attacking machine. One event indicated that the attacking host attempted to interact with a legacy remote shell service.



# Web Log Analysis

Apache access logs were analysed to identify suspicious requests generated during the enumeration phase.

Multiple requests were detected for hidden directories including:

GET /phpinfo.php  
GET /phpMyAdmin  
GET /test  
GET /twiki  

The logs also showed the user agent used during the scan:

gobuster/3.8.2

This confirmed that automated directory enumeration had taken place.



# Identifying the Attacker

To identify the source of the suspicious activity, searches were performed for the attacker IP address.

index=cyber-analyst-lab 192.168.120.128

The logs clearly showed requests originating from the Kali machine used during the attack simulation.



# Indicators of Compromise

Attacker IP address  
192.168.120.128

Suspicious user agent  
gobuster/3.8.2

Suspicious web requests  
GET /phpinfo.php  
GET /phpMyAdmin  
GET /test  
GET /twiki  



# Attack Timeline

Reconnaissance – Nmap scanning identified open services  
Enumeration – Gobuster discovered hidden web directories  
Exploitation Attempt – Hydra attempted SSH brute force  
Log Collection – auth.log and access.log retrieved from target  
SIEM Analysis – logs ingested and investigated in Splunk  



# Conclusion

This project demonstrates how attacker activity can be detected through log analysis using a SIEM platform.

By simulating reconnaissance and enumeration attacks in a controlled lab environment, realistic log data was generated and analysed using Splunk. The investigation successfully identified authentication events, automated web enumeration activity, and the source of the attack.
