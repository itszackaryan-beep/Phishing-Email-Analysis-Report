# 🛡️ Detection Rules & Logic

This document translates the manual investigation findings into reusable detection logic
for **email security gateways**, **SIEM platforms**, and **SOAR playbooks**. These rules are
written in vendor-neutral pseudocode/pseudo-SPL and should be adapted to your specific
platform (Splunk, Microsoft Sentinel, Elastic, Proofpoint, Mimecast, etc.).

---

## 1. Authentication Failure Detection

Flag any inbound email where all three authentication mechanisms fail or are absent —
this was true for **3 of the 4** samples analyzed in this project.

```text
Rule: Email-Auth-Triple-Fail
Condition:
    header.spf  IN ("fail", "none", "temperror", "permerror")
    AND header.dkim IN ("fail", "none")
    AND header.dmarc IN ("fail", "none", "temperror")
Action:
    - Quarantine or flag for manual review
    - Tag: "auth-failure-suspicious"
```

## 2. Display Name / Domain Mismatch (Brand Impersonation)

```text
Rule: Display-Name-Brand-Mismatch
Condition:
    header.from_display_name CONTAINS any_of(trusted_brand_list)
    AND header.from_domain NOT IN approved_domains_for(trusted_brand_list)
Action:
    - Quarantine
    - Alert: "Possible display-name spoofing of {brand}"
```

**Example trusted_brand_list:** `Microsoft`, `Bradesco`, `PayPal`, `Google`, `Apple`,
`Receita Federal` (as seen in Sample-01, Sample-02, Sample-04).

## 3. Reply-To Manipulation

```text
Rule: Reply-To-Domain-Mismatch
Condition:
    header.reply_to_domain != header.from_domain
    AND header.reply_to_domain NOT IN approved_domains_for(header.from_domain)
Action:
    - Flag for review
    - Tag: "reply-to-manipulation"
```

Observed in Sample-02 (Reply-To routed to a free Gmail address) and Sample-03 (Reply-To
routed to a domain unrelated to the visible sender).

## 4. Suspicious Sending Infrastructure

```text
Rule: Mail-Origin-Infra-Mismatch
Condition:
    header.received_chain.origin_asn.owner IN
        ("DigitalOcean", "OVH SAS", "Generic VPS/Hosting Providers")
    AND header.from_display_name CONTAINS any_of(trusted_brand_list)
Action:
    - Quarantine
    - Tag: "brand-impersonation-from-vps"
```

Observed in Sample-01 (DigitalOcean-hosted VPS impersonating a bank) and Sample-03
(OVH SAS infrastructure).

## 5. Malicious Attachment Detection (Hash-Based)

```text
Rule: Attachment-Hash-Reputation-Check
Condition:
    email.attachment.sha256 IN virustotal_known_malicious_hashes
    OR virustotal_lookup(email.attachment.sha256).malicious_vendor_count >= 5
Action:
    - Block delivery
    - Quarantine
    - Alert: "Malicious attachment detected — {threat_label}"
```

Observed in Sample-04, where the attached PDF was flagged malicious by **23 of 63**
VirusTotal vendors under threat labels `Trojan.Abphisher` / `PDF:MalwareX-gen [Phish]`.

## 6. IOC Watchlist Correlation (SIEM)

```spl
index=email_gateway OR index=proxy OR index=dns
| lookup phishing_ioc_watchlist domain OR ip OR url
| where isnotnull(matched_ioc)
| stats count by src_user, matched_ioc, sourcetype
| where count > 0
```

Use this correlation search to detect any internal user who received, clicked, or
resolved DNS for one of the IOCs documented in this project's case files
(see per-sample IOC tables in `docs/Project_Report.pdf`).

## 7. Urgency / Social-Engineering Language Heuristic

```text
Rule: Urgency-Keyword-Heuristic
Condition:
    email.subject OR email.body MATCHES any_of(
        "expiring today", "unusual sign-in", "account suspended",
        "verify now", "saldo liberado", "vence hoje"
    )
    AND header.auth_status != "pass-pass-pass"
Action:
    - Increase spam/phishing confidence score by +25
```

This heuristic should **never** be used as a standalone blocking rule — combine it with
authentication and infrastructure signals above to reduce false positives on legitimate
marketing email.

---

## Recommended Rule Priority

| Priority | Rule | Rationale |
|---|---|---|
| 1 | Attachment-Hash-Reputation-Check | Highest-confidence, lowest false-positive signal |
| 2 | Mail-Origin-Infra-Mismatch | Strong brand-impersonation signal |
| 3 | Email-Auth-Triple-Fail | High recall, moderate false-positive rate |
| 4 | Display-Name-Brand-Mismatch | Effective against targeted brand phishing |
| 5 | Reply-To-Domain-Mismatch | Good supporting signal, low standalone confidence |
| 6 | Urgency-Keyword-Heuristic | Supporting signal only — combine with others |
