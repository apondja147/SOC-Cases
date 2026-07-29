# **Incident Report INC-308: SQL Injection Attack and Data Exfiltration**

Document Type: Incident Response and Threat Analysis

Project Name: Web Application Security Incident

Author: P. Apondja

Status: Closed \- Resolved

## **Case Description and Scenario**

A critical alert from the Web Application Firewall (WAF) and SIEM flagged a targeted SQL injection attack against host WEB-APP-02 (10.0.30.22). The attacker (198.51.100.77) followed a structured attack chain: vulnerability testing, boolean bypass validation, and execution of a UNION SELECT query to extract credentials and credit card numbers. This resulted in the immediate exfiltration of 25 MB of sensitive data.

## **Indicators of Compromise (IOC) Matrix**

* External Attacker IP: 198.51.100.77 (Malicious / Primary Threat Actor)  
* Vulnerable Endpoint: /products.php?id= (Targeted parameter)  
* Malicious Payloads:  
  * id=1' (Error-based reconnaissance)  
  * id=1%27%20OR%201=1-- (Boolean logic testing)  
  * id=-1%20UNION%20SELECT%201,username,password,credit\_card%20FROM%20users-- (Data exfiltration query)  
* Attacker User-Agent: Mozilla/5.0 (X11; Linux x86\_64)  
* Exfiltration Volume: 26,214,400 bytes / 25 MB (Confirmed data breach)  
* Legitimate Traffic (Noise):  
  * 10.0.10.12 (Internal user web traffic)  
  * 10.0.0.10 (Nagios health monitoring service)  
  * 10.0.0.5:53 (Internal DNS service)

## **Incident Timeline and Log Analysis**

4. 

```shell
2026-07-29 17:02:11 UTC - Initial request to /products.php?id=1 (HTTP 200) from 198.51.100.77
2026-07-29 17:02:15 UTC - Error-based fuzzing id=1' triggered HTTP 500 database error response
2026-07-29 17:02:40 UTC - Successful boolean bypass id=1' OR 1=1-- (HTTP 200, 8900 bytes)
2026-07-29 17:03:12 UTC - Execution of UNION SELECT database dump resulting in 25 MB exfiltration (HTTP 200, 26214400 bytes)
```

## 

## **Analyst Response and Case Resolution**

### **Diagnostic Steps and Execution**

The analyst demonstrated exemplary detection precision by breaking down the exact sequence of the attack. Key steps included:

> 1. Precise Attack Reconstruction: Identified the progression from initial fuzzing to syntax verification and data extraction via UNION SELECT targeting the 'users' table.  
> 2. Complete IOC Extraction: Successfully isolated all malicious HTTP GET strings, the source IP (198.51.100.77), and the exact exfiltration metric (26,214,400 bytes) while ignoring background noise (Nagios and internal user traffic).  
> 3. Multi-Layered Containment Strategy:  
   * Network / WAF Block: Immediate drop rule applied on perimeter firewall and WAF for IP 198.51.100.77.  
   * Application Isolation: Temporary restriction of /products.php endpoint pending code patch (implementing parameterized queries / prepared statements).  
   * Regulatory & Risk Escalation: Escalated to IR Management for compulsory password resets and GDPR data breach notification due to exposed credit card data.  
> 4. Comprehensive Forensic Next Steps: Initiated cross-SIEM log correlation for 198.51.100.77 across all infrastructure, database log auditing for write commands (e.g., INTO OUTFILE), and host filesystem checks for uploaded web shells.

\[END OF INCIDENT REPORT \#INC-308\]  
