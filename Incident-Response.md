# 🚨 Incident Response Playbook — Phishing Email Triage

This playbook outlines the recommended response process for a phishing email reported by
an end user or flagged by automated detection, based on the methodology and findings from
this project's four case studies.

---

## Phase 1 — Identification

1. Receive the reported email (via user-report button, mail gateway alert, or SOC ticket).
2. Preserve the original `.eml` / `.msg` file — **do not forward as a plain reply**, which
   strips headers. Use "Forward as attachment" or export the raw message.
3. Record initial metadata: Sender, Subject, Recipient(s), Date/Time received.

```text
Checklist:
[ ] Original .eml/.msg preserved
[ ] Ticket opened with unique case ID
[ ] Reporting user identified and NOT blamed for reporting (positive reinforcement)
```

## Phase 2 — Header & Authentication Analysis

1. Extract the complete raw header using EML Analyzer, MX Toolbox, or Google Admin Toolbox.
2. Record: `From`, `Return-Path`, `Reply-To`, `Message-ID`, `Received` chain.
3. Verify SPF, DKIM, and DMARC results.
4. Determine whether the originating IP/ASN matches the claimed sender's known
   infrastructure.

> Reference: Section 7.4–7.5, 8.4–8.5, 9.4–9.5, 10.4–10.5 of `docs/Project_Report.pdf`
> for worked examples.

## Phase 3 — Spoofing & Social Engineering Assessment

Check for each of the following (see [`Detection-Rules.md`](Detection-Rules.md) for
automatable versions of these checks):

- [ ] Display Name Spoofing
- [ ] Domain Spoofing
- [ ] Reply-To Manipulation
- [ ] Typosquatting
- [ ] Homograph Attack
- [ ] Urgency / fear-based language
- [ ] Unexpected attachment

## Phase 4 — IOC Extraction & Enrichment

1. Extract all observable IOCs: sender email, domain(s), URL(s), IP address(es), file
   hash(es) (SHA256 preferred, MD5 if that's all that's available).
2. **Defang** all URLs/domains before sharing in tickets, chat, or reports:
   `http://evil.com` → `hxxp://evil[.]com`
3. Submit each IOC to:
   - **VirusTotal** (detection ratio + community score)
   - **Cisco Talos Intelligence** (reputation + blocklist status)
   - **WHOIS** (domain age — newly registered domains are higher risk)
4. Treat **any single malicious verdict** as sufficient to escalate, even if other IOCs
   return clean (see Sample-03 lesson learned: clean IOCs ≠ safe email).

## Phase 5 — Containment

```text
Actions:
[ ] Block sender address / domain at the mail gateway
[ ] Block malicious URL(s) / domain(s) at the web proxy / DNS firewall
[ ] Block malicious IP(s) at the perimeter firewall
[ ] Add file hash to EDR/AV blocklist
[ ] Purge the email from all mailboxes it was delivered to (mail gateway search & destroy)
[ ] Identify and isolate any user who clicked the link or opened the attachment
```

## Phase 6 — Eradication & Recovery

- If credentials may have been entered on a phishing page: force password reset and
  revoke active sessions/tokens for the affected account(s).
- If an attachment was opened: isolate the endpoint, run an EDR sweep, and check for
  persistence mechanisms or C2 beaconing consistent with the identified threat label.
- Verify MFA is enabled going forward for any account potentially exposed.

## Phase 7 — Lessons Learned / Post-Incident

- Update the MITRE ATT&CK mapping for this campaign in [`MITRE-Mapping.md`](MITRE-Mapping.md).
- Add newly confirmed IOCs to the organization's threat-intelligence watchlist / SIEM
  lookup table.
- Feed identified brand-impersonation patterns into user awareness training.
- Review whether SPF/DKIM/DMARC policy enforcement (`p=reject`) could have prevented
  delivery in the first place.

---

## Severity-Based Response SLA (Suggested)

| Severity | Example Case | Response SLA |
|---|---|---|
| Critical | Sample-04 (malicious attachment confirmed) | Immediate (< 1 hour) — full containment |
| High | Sample-01, Sample-02 (auth failure + malicious infra/IOC) | < 4 hours |
| Medium | Mixed signals, partial IOC confirmation | < 8 hours |
| Low | Sample-03 (auth failure only, clean IOCs) | < 24 hours — monitor for escalation |

---

## Escalation Contacts Template

```text
SOC Tier 1 Analyst      → Initial triage & IOC extraction
SOC Tier 2 / IR Lead    → Containment decision & stakeholder communication
Email/Messaging Admin   → Mailbox purge, gateway rule deployment
Endpoint/EDR Team       → Host isolation & forensic sweep (if attachment executed)
Security Awareness Lead → Campaign-specific user notification, if widespread
```
