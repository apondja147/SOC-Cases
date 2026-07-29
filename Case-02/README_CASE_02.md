# **Incident Response Report: INC-204**

INC-204: Phishing-Led Malware Infection & C2 Communication

Document Type: Incident Response & Threat Analysis

Project Name: Endpoint Malware Infection Incident

Author: P. Apondja

Status: Closed \- Remediated

# 

# **1\. Case Description & Scenario**

An inbound phishing email impersonating a billing service ("support@billing-update-service.com") was delivered to user m\_jansen on WORKSTATION-44 (10.0.50.112). The user clicked a link in the email, which triggered a PowerShell command executing with execution policy bypass to download an executable payload ("invoice\_99.exe") from a malicious external server. Upon execution, the payload established an active Command & Control (C2) channel to an external IP address (185.220.101.45:443).

# 

# **2\. Indicator of Compromise (IOC) Matrix**

* Malicious Email Sender: support@billing-update-service.com (Phishing vector)  
* Mail Gateway IP: 203.0.113.15 (Attacker infrastructure)  
* Payload Host URL: [http://198.51.100.200/payload.exe](https://www.google.com/search?q=http://198.51.100.200/payload.exe) (Malware download location)  
* Command & Control IP: 185.220.101.45:443 (Active C2 channel)  
* Dropped Executable: C:\\Users\\m\_jansen\\AppData\\Local\\Temp\\invoice\_99.exe (Malware payload)  
* Affected Asset: WORKSTATION-44 / 10.0.50.112 (Compromised host)  
* Internal DNS Server: 10.0.0.5:53 (Benign noise / Legitimate network traffic)

# 

# **3\. Incident Timeline & Log Analysis**

```shell
2026-07-29 16:01:10 UTC - Email delivered from support@billing-update-service.com to m_jansen@company.com
2026-07-29 16:05:12 UTC - PowerShell downloaded invoice_99.exe from 198.51.100.200
2026-07-29 16:05:20 UTC - Malicious binary invoice_99.exe executed under user m_jansen
2026-07-29 16:05:22 UTC - Outbound TCP connection established from WORKSTATION-44 to C2 IP 185.220.101.45:443
```

# 

# **4\. Analyst Response & Case Resolution**

Analyst Performance Summary:

The analyst correctly identified the initial phishing vector (support@billing-update-service.com), the sender IP (203.0.113.15), and the dropped executable (invoice\_99.exe). The analyst proposed blocking the malicious sender email and source IP.

Key Learning Points & Missed Artifacts:

> 1. C2 Traffic vs. DNS Noise: The analyst flagged internal DNS traffic (10.0.0.5:53) as suspicious. It was clarified that port 53 to an internal IP represents standard DNS resolution. The true malicious network indicator was the outbound connection to the external C2 server at 185.220.101.45:443.  
> 2. Host Isolation Priority: The initial response focused on perimeter blocks. Immediate endpoint containment (isolating WORKSTATION-44 from the network and killing the invoice\_99.exe process) was identified as a critical missing step.

Final Remediated Resolution Steps:

> 1. Isolated WORKSTATION-44 (10.0.50.112) from the network via EDR to halt C2 communications.  
> 2. Terminated process invoice\_99.exe and removed the executable from AppData\\Local\\Temp.  
> 3. Blocked C2 IP 185.220.101.45 and payload IP 198.51.100.200 on perimeter firewall.  
> 4. Purged phishing emails from billing-update-service.com across the email gateway.

\[END OF INCIDENT REPORT \#INC-204\]

