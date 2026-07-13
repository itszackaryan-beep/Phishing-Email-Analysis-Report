# Scripts

Placeholder for automation scripts that extend the manual investigation workflow
documented in this project.

## Suggested script: `ioc_check.py`

A minimal starting point for automating the VirusTotal lookups performed manually
throughout this investigation:

```python
import requests
import sys

VT_API_KEY = "YOUR_API_KEY_HERE"
HEADERS = {"x-apikey": VT_API_KEY}

def check_ip(ip: str) -> dict:
    url = f"https://www.virustotal.com/api/v3/ip_addresses/{ip}"
    return requests.get(url, headers=HEADERS).json()

def check_domain(domain: str) -> dict:
    url = f"https://www.virustotal.com/api/v3/domains/{domain}"
    return requests.get(url, headers=HEADERS).json()

def check_hash(file_hash: str) -> dict:
    url = f"https://www.virustotal.com/api/v3/files/{file_hash}"
    return requests.get(url, headers=HEADERS).json()

if __name__ == "__main__":
    ioc = sys.argv[1]
    print(check_ip(ioc))
```

> Requires a free or paid VirusTotal API key. Respect API rate limits.
