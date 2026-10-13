# Cyber Kill Chain Incident Analysis: E-Commerce Data Breach

## Overview
This repository contains a detailed threat intelligence and incident analysis report based on a simulated cyberattack against a mid-sized e-commerce company. The incident involved a targeted phishing campaign, zero-day exploitation, lateral movement, and the exfiltration of customer payment card information. 

The primary objective of this project is to apply the **Lockheed Martin Cyber Kill Chain** framework to dissect the attack, document attacker techniques, map Indicators of Compromise (IOCs), and establish structured incident reporting practices.

---

## The 7 Phases of the Cyber Kill Chain & Incident Summary

### 1. Reconnaissance
* **Attacker Actions:** While not explicitly detailed in the initial trigger, the attacker performed preparatory research targeting the e-commerce company's employees to identify high-value targets or personnel handling billing and invoicing.
* **Goal:** Gather intelligence on organizational structure, software stack (specifically popular PDF readers), and email nomenclature to craft a convincing lure.

### 2. Weaponization
* **Attacker Actions:** Bundled a malicious payload into a PDF file (`Invoice_Payment.pdf`) designed to exploit a zero-day vulnerability within a widely used PDF reader application.
* **Goal:** Create a stealthy, delivery-ready artifact capable of bypassing static signature-based security controls by leveraging an unpatched software flaw.

### 3. Delivery
* **Attacker Actions:** Transmitted the weaponized PDF via a targeted phishing email with the subject line *"Urgent Invoice: Payment Due"* to an employee workstation.
* **Goal:** Successfully transmit the malicious payload into the target network perimeter via trusted communication channels (email).

### 4. Exploitation
* **Attacker Actions:** The recipient opened `Invoice_Payment.pdf`, triggering the zero-day vulnerability in the PDF reader. This allowed the attacker to execute arbitrary code without immediate user awareness.
* **Goal:** Trigger the vulnerability to execute the initial malware payload on the victim's workstation.

### 5. Installation
* **Attacker Actions:** The malware established persistence on the compromised workstation and reached out to download secondary payloads, including lateral movement utilities and data exfiltration scripts.
* **Goal:** Secure a persistent foothold within the internal network environment and prepare tools necessary for deeper penetration.

### 6. Command and Control (C2) & Lateral Movement
* **Attacker Actions:** The attacker utilized credential-dumping tools (such as Mimikatz) to harvest cached credentials and escalate privileges. Using compromised accounts, they moved laterally across the network until they reached the payment processing server (`PAYMENT-SRV01`).
* **Goal:** Elevate privileges, map the internal network, and navigate toward high-value data repositories (the payment server).

### 7. Actions on Objectives (Exfiltration & Impact)
* **Attacker Actions:** Sensitive customer payment card data was exfiltrated to an external server (`exfiltration-ecommerce[.]com`) over encrypted HTTPS (Port 443). To cover their tracks, the attackers deleted local logs and dropped a ransom note (`READ_ME.txt`) demanding 5 Bitcoin.
* **Goal:** Steal valuable financial data for monetary gain, obscure forensic evidence, and extort the organization.

---

## Incident Mapping & Indicators of Compromise (IOC) Table

| Cyber Kill Chain Phase | Attacker Activity / Description | Indicators of Compromise (IOCs) / Artifacts | Defensive Countermeasures / Recommendations |
| :--- | :--- | :--- | :--- |
| **1. Reconnaissance** | Information gathering on target organization and employees. | *None directly observed in logs.* | Security awareness training, minimizing public employee footprint. |
| **2. Weaponization** | Pairing exploit code with `Invoice_Payment.pdf`. | `Invoice_Payment.pdf` | Egress/Ingress file sandboxing and email attachment inspection. |
| **3. Delivery** | Phishing email sent with subject "Urgent Invoice: Payment Due". | **Sender Address:** Phishing infrastructure<br>**Subject:** Urgent Invoice: Payment Due | Advanced Email Security (Secure Email Gateway), DSPF/DKIM/DMARC policies. |
| **4. Exploitation** | Zero-day execution via vulnerable PDF reader. | Zero-day exploit signature / anomalous PDF parser behavior. | Rapid patch management, application whitelisting, alternative PDF reader hardening. |
| **5. Installation** | Malware dropped staging scripts and utility tools on workstation. | Dropped executable binaries, unexpected outbound connections. | Endpoint Detection and Response (EDR) behavioral monitoring. |
| **6. Lateral Movement** | Credential dumping (Mimikatz) and network pivoting to `PAYMENT-SRV01`. | **Tools:** Mimikatz<br>**Target Host:** `PAYMENT-SRV01`<br>**Account Usage:** Compromised domain/local credentials | Network segmentation, multi-factor authentication (MFA) for internal hops, credential guard. |
| **7. Actions on Objectives** | Data exfiltration, log deletion, and ransomware/extortion drop. | **Exfiltration Domain:** `exfiltration-ecommerce[.]com`<br>**Protocol/Port:** HTTPS / Port 443<br>**Ransom Note:** `READ_ME.txt`<br>**Demand:** 5 Bitcoin | Data Loss Prevention (DLP) solutions, immutable log backup/SIEM centralization, strict egress filtering. |

---

## Key Takeaways & Recommendations
1. **Patch Management & Zero-Days:** Relying solely on signature-based antivirus is insufficient against zero-day exploits. Implementing application sandboxing and prompt software patching is critical.
2. **Network Segmentation:** Flat networks allow attackers to move easily from standard workstations to critical servers like `PAYMENT-SRV01`. Implementing strict micro-segmentation limits blast radius.
3. **Centralized Logging:** Attackers attempted to cover their tracks by deleting local logs. Forwarding logs in real-time to a secure, immutable SIEM ensures forensic visibility remains intact.

---

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
