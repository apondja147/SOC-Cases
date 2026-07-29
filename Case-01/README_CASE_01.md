## **INC-101 Incident Response Report**

## **1\. Report Information**

**Document Type:** Incident Response and Threat Analysis Report

**Project Name:** Linux Authentication Security Incident

**Author:** P. Apondja

**Status:** Closed \- Remediated

## **2\. Executive Summary**

On July 29, 2026, at 15:11:14 UTC, an unauthorized external actor successfully executed a SSH brute-force attack against host DB-FINANCE-01 (10.0.20.15), obtaining full root administrative access. The attacker originated from external IP address 198.51.100.88. Immediate containment measures were executed to terminate active malicious sessions, block the attacker's IP at the perimeter firewall, and rotate system credentials.

## **3\. Indicators of Compromise (IOC) Matrix**

* **External IP:** 198.51.100.88 (Malicious / IOC) \- Source of the SSH brute-force attack.  
* **Target Host:** 10.0.20.15 / DB-FINANCE-01 (Targeted Asset) \- Internal financial database server.  
* **Compromised Account:** root (Critical IOC) \- Privileged access via SSH password authentication.  
* **Internal IP:** 10.0.10.5 (Benign / Noise) \- Internal management host (j\_devries public key authentication).  
* **System Process:** CRON\[4102\] (Benign / Noise) \- Scheduled system maintenance task.

## **4\. Incident Timeline and Log Analysis**

4. 

```shell
2026-07-29 15:11:02 UTC - Failed SSH attempt for invalid user 'admin' from 198.51.100.88
2026-07-29 15:11:05 UTC - Failed SSH attempt for invalid user 'user1' from 198.51.100.88
2026-07-29 15:11:09 UTC - Failed SSH attempt for user 'root' from 198.51.100.88
2026-07-29 15:11:14 UTC - SUCCESSFUL SSH authentication for 'root' from 198.51.100.88
```

## **5\. Remediation and Containment Measures**

**Session Termination:** Disconnection of all active SSH sessions associated with IP 198.51.100.88 and the root user.

2. 

```shell
pkill -u root
```

**Network Perimeter Blocking:** Addition of address 198.51.100.88 to the inbound firewall drop list.

**Credential Rotation:** Immediate change of the root password and audit of the /root/.ssh/authorized\_keys file for potential backdoor keys.

**Hardening Recommendation:** Disable SSH password authentication for root in /etc/ssh/sshd\_config.

```shell
PermitRootLogin prohibit-password
```

*\[END OF INCIDENT REPORT \#INC-101\]*

