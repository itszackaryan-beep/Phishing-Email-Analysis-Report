# 🗺️ MITRE ATT&CK Mapping

This document consolidates the MITRE ATT&CK (Enterprise Matrix) technique mapping
performed across all four phishing samples investigated in this project. Each mapping is
supported by direct forensic evidence gathered during header analysis, IOC extraction, and
threat-intelligence validation.

Reference: [MITRE ATT&CK Enterprise Matrix](https://attack.mitre.org/matrices/enterprise/)

---

## Summary Table

| Sample | ATT&CK ID | Technique | Tactic | Confidence |
|---|---|---|---|---|
| Sample-01 (Bradesco Livelo) | T1566.002 | Spearphishing Link | Initial Access | High |
| Sample-02 (Microsoft Sign-in Alert) | T1566.001 / T1566.002 | Spearphishing Attachment / Link | Initial Access | High |
| Sample-03 (Zonnepanelen) | T1566.002 | Spearphishing Link | Initial Access | Low–Medium |
| Sample-04 (Liberação de IRPF) | T1566.001 | Spearphishing Attachment | Initial Access | Critical |

---

## Sample-01 — Bradesco Livelo Rewards Phishing

**Technique:** [T1566.002 – Phishing: Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/)
**Tactic:** Initial Access

**Justification:**
The email impersonated a Brazilian bank's loyalty program and pressured the recipient to
click a "redeem now" link before reward points expired. Header analysis showed SPF, DKIM,
and DMARC all failing, and the sending infrastructure resolved to a Digital Ocean-hosted
VPS rather than the bank's legitimate mail servers. Cisco Talos and VirusTotal flagged the
originating IP address as malicious/suspicious, corroborating a credential-harvesting
spearphishing link.

**Potential Follow-On Techniques (if link is clicked):**
- [T1078 – Valid Accounts](https://attack.mitre.org/techniques/T1078/) (if harvested credentials are reused)
- [T1056 – Input Capture](https://attack.mitre.org/techniques/T1056/) (fake login page credential capture)

---

## Sample-02 — Microsoft "Unusual Sign-In Activity" Alert

**Technique:** [T1566.001 / T1566.002 – Spearphishing Attachment / Link](https://attack.mitre.org/techniques/T1566/)
**Tactic:** Initial Access

**Justification:**
The email spoofed a Microsoft security-alert notification, complete with a fabricated
sign-in location (Russia/Moscow) designed to trigger panic and a fast, unconsidered click.
All three authentication mechanisms failed. The embedded domain (`thebandalisty[.]com`)
and its tracking URL were both flagged as malicious/phishing by 10+ VirusTotal vendors,
and Cisco Talos listed the domain on its Security Intelligence Block List with a
"Malicious" classification.

**Notable Spoofing Techniques Observed:** Display-name spoofing, domain spoofing, and
Reply-To manipulation (replies were routed to a free Gmail address unrelated to Microsoft).

---

## Sample-03 — Zonnepanelen (Dutch Solar Panel Marketing Lure)

**Technique:** [T1566.002 – Phishing: Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/)
**Tactic:** Initial Access

**Justification:**
This sample presented as a legitimate Dutch solar-panel marketing email rather than an
overt brand-impersonation attack. SPF, DKIM, and DMARC all failed, and Reply-To
manipulation was confirmed (replies were redirected to a domain unrelated to the visible
sender). All extracted IOCs (domain, URL, IP) returned clean results in VirusTotal and a
neutral reputation in Cisco Talos at the time of analysis — a reminder that **infrastructure
reputation lags behind campaign launch**, and authentication failures plus Reply-To
manipulation remain valid standalone indicators of manipulation, warranting a cautious
**Low** risk classification pending further monitoring.

---

## Sample-04 — Liberação de IRPF (Brazilian Tax Refund Lure with Malicious Attachment)

**Technique:** [T1566.001 – Phishing: Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/)
**Tactic:** Initial Access

**Justification:**
The email was sent from a free Gmail account referencing a fake "IRPF" (Brazilian income
tax) balance release, with a PDF attachment (`csWuYjyqO2IR.pdf`) as the primary payload.
While SPF passed (as expected for genuine Gmail infrastructure), DKIM and DMARC both
failed. The attached PDF's SHA-256 hash was submitted to VirusTotal and was flagged
**malicious by 23 of 63 vendors**, carrying threat labels including `Trojan.Abphisher` and
`PDF:MalwareX-gen [Phish]` — confirming active weaponized content delivery.

**Risk Classification:** Critical (High Likelihood × High Impact).

---

## Framework Notes

- All four samples map to the **Initial Access** tactic under the **Phishing (T1566)**
  parent technique, consistent with email being used as the initial intrusion vector
  rather than a later-stage technique.
- Sub-technique selection (T1566.001 vs. T1566.002) was based on whether the primary
  malicious payload was a **file attachment** (.001) or an **embedded hyperlink** (.002).
- Downstream techniques (T1078, T1056) are documented as **potential follow-on stages**
  only — they were not directly observed/executed in this passive analysis, since no
  links were clicked and no attachments were detonated.
