## Executive Summary

This lab involved analyzing network traffic using Wireshark to identify malicious activity, including network scanning, ARP poisoning (MITM), credential interception, brute-force attempts, and file transfer abuse. 

Key findings include:
- Detection of UDP port scanning behavior
- Identification of ARP spoofing and credential theft
- Correlation of user activity via Kerberos authentication
- Evidence of FTP brute-force attack and malicious file upload
- Decryption and analysis of HTTPS traffic to uncover hidden artifacts

This analysis demonstrates practical skills in network forensics, protocol analysis, and incident investigation.

## 1. Network Scanning Analysis (Nmap)

Analyzed packet capture data to identify network scanning activity and determine host and port behavior.

### Key Findings
- Identified scanning activity originating from 10.10.47.123 targeting 10.10.60.7  
- Detected high-volume ICMP "Destination Unreachable (Port Unreachable)" responses  
- Confirmed behavior consistent with UDP port scanning techniques  

### Evidence: UDP Scan Detection
<img width="1275" height="1237" alt="image" src="https://github.com/user-attachments/assets/997d156c-8834-4bbb-b32a-39a0887e7213" />
Applied the Wireshark filter `icmp.type == 3 && icmp.code == 3` to isolate ICMP error responses.

A total of 1083 ICMP "Port Unreachable" messages were observed, indicating closed UDP ports during the scan.

This pattern is consistent with Nmap UDP scanning behavior, where closed ports respond with ICMP errors, allowing enumeration of port states.
---

### 2. ARP Poisoning / Man-in-the-Middle Attack
Detected abnormal ARP traffic consistent with ARP spoofing (Man-in-the-Middle attack) originating from a single host.

Further analysis confirmed that HTTP traffic was successfully intercepted, exposing sensitive data transmitted in plaintext.

This confirms a successful Man-in-the-Middle attack where the attacker captured network traffic between hosts.

(ARP) 1 
<img width="1277" height="1235" alt="image" src="https://github.com/user-attachments/assets/2c66666c-5f55-48d7-98d8-fec473e97597" />
The attacker was identified as 192.168.1.25 based on repeated ARP requests originating from this host.
Applied targeted Wireshark filters to isolate attacker-generated traffic and validate findings:
<img width="1277" height="1234" alt="image" src="https://github.com/user-attachments/assets/dfd6c0a6-df5e-45e7-8e2a-1573c9c9378e" />
- Identified 284 ARP requests originating from the attacker, confirming ARP spoofing activity
<img width="1276" height="1238" alt="image" src="https://github.com/user-attachments/assets/699d3bd3-31ff-4f02-a906-055ace547aef" />
- Isolated 90 HTTP packets received by the attacker, demonstrating successful traffic interception
<img width="1275" height="1234" alt="image" src="https://github.com/user-attachments/assets/b2684eac-51f3-43ac-b5dd-a5c285859d55" />
Analyzed HTTP POST requests and extracted plaintext credentials:

- Username: client986  
- Password: clientnothere!

This confirms a successful Man-in-the-Middle attack where sensitive authentication data was captured by the attacker.

### 3. Protocol Analysis (DHCP, NetBIOS, Kerberos)

Performed protocol-level analysis to identify hosts, user activity, and authentication patterns within the captured traffic.

#### DHCP Analysis

Applied DHCP filters to identify host information and IP request behavior:

* Filter used: `dhcp.option.hostname contains "A30"`
* Identified host **Galaxy A30**
* Extracted MAC Address: **9a:81:41:cb:96:6c**
  <img width="1273" height="1237" alt="image" src="https://github.com/user-attachments/assets/2b5b528f-2b5d-4283-89e9-008753ba86e2" />
Additionally:

* Filter used: `dhcp.option.requested_ip_address == 172.16.13.85`
* Determined that host **Galaxy-A12** requested the IP address **172.16.13.85**
  <img width="1271" height="1236" alt="image" src="https://github.com/user-attachments/assets/14f9bc9a-c611-4685-811a-e86aede96dd5" />
This demonstrates the ability to correlate hostnames, MAC addresses, and requested IP assignments using DHCP traffic.

---

#### NetBIOS Analysis

Analyzed NetBIOS Name Service (NBNS) traffic to identify host registration behavior:
<img width="1277" height="1238" alt="image" src="https://github.com/user-attachments/assets/11e1c289-5be4-4ffc-8946-6cb48f3498b0" />
* Filter used: `nbns.name contains "LIVALJM" && nbns.flags.opcode == 5`
* Identified **16 NetBIOS registration requests** associated with the "LIVALJM" workstation

This indicates repeated broadcast-based name registration activity within the network.

---

#### Kerberos Analysis

Inspected Kerberos authentication traffic to identify user and host relationships:

* Filter used: `kerberos.CNameString contains "$"`
<img width="1277" height="1288" alt="image" src="https://github.com/user-attachments/assets/57b549dc-b337-4f89-809b-7f8024b38bac" />
  * Identified hostname: **xp1$**
    
<img width="1275" height="1236" alt="image" src="https://github.com/user-attachments/assets/fbf466f4-3f80-4f36-b8db-141620a4325f" />
* Filter used: `kerberos.CNameString contains "u5"`

  * Identified user: **u5**
  * Source IP address: **10.1.12.2**

The extracted IP address was converted to defanged format using CyberChef:
<img width="1276" height="1324" alt="image" src="https://github.com/user-attachments/assets/01a537e4-0fff-4d6e-95d2-2d8ad262d037" />

* Defanged IP: **10[.]1[.]12[.]2**

This confirms the ability to map authenticated users to their originating systems through Kerberos traffic analysis.

---

### Summary of Findings

* Successfully identified hosts using DHCP hostname and MAC address data
* Detected NetBIOS registration activity and quantified request frequency
* Correlated Kerberos authentication data to identify both user accounts and associated systems
* Demonstrated safe handling of sensitive data through IP defanging

This analysis highlights practical skills in network traffic inspection, host identification, and authentication tracking within enterprise environments.

### 4. Cleartext Protocol Analysis (FTP)

Conducted analysis of FTP traffic to identify authentication activity, file access, and potential adversarial behavior. Since FTP transmits data in cleartext, it allows visibility into credentials, commands, and file transfers.

---

#### Authentication Analysis (Failed Logins)

* Applied filter: `ftp.response.code == 530`
* <img width="1278" height="1237" alt="image" src="https://github.com/user-attachments/assets/afd331bb-aa27-4772-8a7a-0ef09b4d91a2" />
* Identified **737 failed login attempts**
  

This indicates repeated authentication failures, consistent with **brute-force or credential guessing activity** against the FTP service.

---

#### File Access Analysis

* Applied filter: `ftp.response.code == 213`
* <img width="1277" height="1239" alt="image" src="https://github.com/user-attachments/assets/1a92ced5-5177-46be-ab5b-d6ee116a1b2f" />
* Extracted file size associated with FTP activity:

  * File size: **39424 bytes**

This confirms successful interaction with a file on the FTP server and provides context for data transfer size.

---

#### File Upload Investigation

To identify files uploaded by the adversary:

* Manually inspected FTP requests
* Located file interaction commands
* Followed the TCP stream for full session visibility
* <img width="1278" height="1236" alt="image" src="https://github.com/user-attachments/assets/864d2c3e-38a7-4a7a-9a06-7ba885ee1de8" />
Using **Follow TCP Stream**, identified:
<img width="1275" height="1237" alt="image" src="https://github.com/user-attachments/assets/846c0ad5-dbad-49c5-bb72-ccee24bf7b2f" />
This demonstrates the ability to reconstruct attacker activity by analyzing full protocol conversations.

---

#### Command Execution Analysis

Within the same TCP stream, further inspection revealed:

* Command used by adversary: **CHMOD 777 resume.doc**
<img width="1277" height="1237" alt="image" src="https://github.com/user-attachments/assets/a7b2536d-8fd1-493b-af8a-6d230f065dd7" />
This indicates an attempt to modify file permissions, potentially to make the file executable or accessible to all users — a common post-upload action during malicious activity.

---

### Summary of Findings

* Detected **high-volume failed login attempts (737)** indicating possible brute-force activity
* Identified successful file interaction and extracted file size (**39424 bytes**)
* Discovered uploaded file: **resume.doc**
* Observed adversary behavior modifying file permissions using **CHMOD 777**

This analysis highlights the risks of cleartext protocols like FTP, where sensitive data such as authentication attempts, file names, and commands can be easily intercepted and analyzed. Demonstrates proficiency in Wireshark filtering, stream reconstruction, and attacker behavior identification.

### 5. Encrypted Protocol Analysis (HTTPS / TLS Decryption)

Performed analysis of encrypted HTTPS traffic by leveraging TLS key logging to decrypt and inspect secure communications. This allowed visibility into HTTP/2 traffic, headers, and transferred objects that are normally hidden.

---

#### TLS Handshake Analysis (Client Hello Identification)

* Applied filter: `http.request or tls.handshake.type == 1`
* Expanded the **Server Name Indication (SNI)** field
* Added the **Server Name** as a column for easier identification\
<img width="1277" height="1236" alt="image" src="https://github.com/user-attachments/assets/e0c5ce1b-32bf-4ab0-910e-9612f9304979" />
Identified the **Client Hello** request to:
<img width="1277" height="1235" alt="image" src="https://github.com/user-attachments/assets/b5820e53-c407-44b6-880c-a97a229ea5de" />
* **accounts.google.com**
* Frame number: **16**

This step demonstrates the ability to analyze TLS handshakes and extract relevant metadata from encrypted sessions.

---

#### Decrypting HTTPS Traffic

To decrypt TLS traffic:

* Navigated to: **Edit → Preferences → TLS**
  <img width="1276" height="1293" alt="image" src="https://github.com/user-attachments/assets/9112a6e9-883a-4520-9f94-23c1c0acf1af" />
* Loaded provided key file: `KeysLogFile.txt`
* Enabled Wireshark TLS decryption using pre-master secrets

After applying the key log file, encrypted traffic was successfully decrypted, revealing **HTTP/2 protocol data**.

* Applied filter: `http2`
* Total HTTP/2 packets identified: **115**

---

#### HTTP/2 Header Analysis

* Navigated to **Frame 322**
* Inspected HTTP/2 headers
<img width="1275" height="1234" alt="image" src="https://github.com/user-attachments/assets/dce459ee-90c0-4876-b57f-d4dca3db7636" />
Extracted the **authority header**:

* `safebrowsing.googleapis.com`

Converted to defanged format for safe documentation:

* **safebrowsing[.]googleapis[.]com**

This step highlights the ability to safely document potentially sensitive domains.

---

#### Object Extraction & Flag Discovery

To identify hidden or transferred files within decrypted traffic:

* Used search (`Ctrl + F`) with keyword: `flag.txt`
* Located HTTP/2 GET request for the file
* Navigated to:

  * **File → Export Objects → HTTP**
<img width="1276" height="1238" alt="image" src="https://github.com/user-attachments/assets/3cb26f8d-b1cb-46d2-9823-1192b9aab7bf" />

Exported objects revealed the flag:
<img width="1278" height="1238" alt="image" src="https://github.com/user-attachments/assets/7f55dcad-5599-40de-852f-e832f4c85c5e" />

* **FLAG{THM-PACKETMASTER}**

---

## Attack Timeline

1. Attacker performs UDP scan to identify open/closed ports
2. ARP spoofing used to position attacker as Man-in-the-Middle
3. HTTP traffic intercepted, exposing plaintext credentials
4. FTP service targeted with brute-force login attempts
5. Successful access used to upload malicious file (resume.doc)
6. File permissions modified using CHMOD 777
7. Encrypted HTTPS traffic analyzed and decrypted for further investigation

This analysis demonstrates the ability to bypass encryption barriers (with proper keys), inspect secure traffic, and extract meaningful artifacts. Showcases proficiency in TLS decryption, HTTP/2 analysis, and object reconstruction using Wireshark.


---

## Key Skills Gained
- Packet analysis with Wireshark
- Network traffic investigation
- Protocol identification and filtering
- Detection of suspicious network behavior
- Understanding of encrypted vs unencrypted traffic

## Conclusion
This lab provided hands-on experience analyzing real network traffic and identifying potential threats. It strengthened my ability to investigate network-based attacks and improved my understanding of how data moves across networks in both secure and insecure environments.
