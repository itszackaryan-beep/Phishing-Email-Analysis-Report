# 📧 Phishing Email Analysis using MITRE ATT&CK

## 📌 Project Overview

This project demonstrates a practical phishing email investigation performed using publicly available phishing email samples. The objective was to analyze email headers, identify spoofing techniques, extract Indicators of Compromise (IOCs), verify the reputation of malicious artifacts, and map attacker behavior to the MITRE ATT&CK framework.

The investigation follows a SOC Analyst Level-1 workflow used during email security incident analysis.

---

# 🎯 Objectives

- Analyze phishing email headers
- Detect email spoofing techniques
- Extract Indicators of Compromise (IOCs)
- Verify IP, Domain and Hash reputation
- Perform WHOIS domain investigation
- Analyze sender infrastructure
- Map attacker techniques to MITRE ATT&CK
- Assess phishing risk and impact
- Prepare an incident investigation report

---

# 🛠 Tools Used

- Google Admin Toolbox Messageheader
- EML Analyzer
- VirusTotal
- Cisco Talos Intelligence
- WHOIS Lookup
- MITRE ATT&CK Framework

---

# 🔍 Investigation Workflow

1. Collected phishing email samples
2. Examined email headers
3. Performed header authentication analysis
4. Identified spoofing techniques
5. Extracted Indicators of Compromise (IOCs)
6. Verified IOC reputation using VirusTotal
7. Investigated domains using WHOIS Lookup
8. Checked IP and domain reputation using Cisco Talos
9. Mapped attacker behavior to MITRE ATT&CK
10. Documented findings and recommendations

---

# 📂 Repository Structure

```
Phishing-Email-Analysis/
│
├── docs/
│   └── Phishing_Email_Analysis_Report.pdf
│
├── images/
│   ├── Sample-01/
│   ├── Sample-02/
│   ├── Sample-03/
│   └── Sample-04/
│
├── reports/
│   ├── 
│   ├── Incident-Response.md
│   └── MITRE-Mapping.md
│
├── sample-data/
│
├── README.md
```

---

# 📊 Analysis Performed

### Email Header Analysis

- SPF Validation
- DKIM Validation
- DMARC Validation
- Return-Path Analysis
- Message-ID Analysis
- Received Chain Analysis

### Spoofing Detection

- Display Name Spoofing
- Domain Spoofing
- Reply-To Manipulation
- Typosquatting Detection
- Homograph Detection

### IOC Extraction

- IP Addresses
- Domains
- URLs
- Email Addresses
- File Hashes (when available)

### Threat Intelligence

- VirusTotal Reputation Check
- Cisco Talos Reputation Analysis
- WHOIS Domain Investigation

### MITRE ATT&CK Mapping

- T1566.001 – Spearphishing Attachment
- T1566.002 – Spearphishing Link

---

# 📌 Key Findings

- Multiple phishing indicators were identified.
- Suspicious sender domains were detected.
- Malicious URLs were observed in phishing emails.
- Several samples exhibited spoofing characteristics.
- Threat intelligence confirmed malicious infrastructure.
- MITRE ATT&CK techniques successfully mapped.

---

# 🛡 Security Recommendations

- Block malicious domains and IP addresses.
- Enforce SPF, DKIM and DMARC policies.
- Enable email filtering and anti-phishing protection.
- Verify suspicious senders before responding.
- Educate users on phishing awareness.
- Continuously monitor email traffic for malicious activity.

---

# 📈 Skills Demonstrated

- Email Header Analysis
- Threat Hunting
- Threat Intelligence
- IOC Extraction
- VirusTotal Investigation
- Cisco Talos Analysis
- WHOIS Investigation
- MITRE ATT&CK Mapping
- Phishing Detection
- Incident Analysis
- Technical Documentation

---

# 👨‍💻 Author

**SUMIT KUMAR**

Cybersecurity Enthusiast | SOC Analyst (L1) Aspirant

---

## ⭐ If you found this project useful, consider giving it a star.
