
## **Architecture Layers & Core Services**

| **Layer**                 | **Purpose**                                                | **Primary Services (with cost)**                                                                   |
| ------------------------- | ---------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Edge Protection**       | Secure public traffic, DNS filtering, TLS, DDoS mitigation | Cloudflare Gateway — **$100/month**                                                                |
| **Security Operations**   | Centralized monitoring, alerting, and response             | Microsoft Sentinel SIEM — **$250/month**, Defender MDR Lite or Arctic Wolf MDR — **$200/month**    |
| **Cloud SaaS & Identity** | Access control, MFA, and data loss prevention              | Microsoft 365 Business Premium — **$22/user × 50 = $1,100/month**, Bitwarden Teams — **$75/month** |
| **Endpoints**             | XDR, antivirus, MDM, patch management                      | Microsoft Defender for Business (included), Intune (included)                                      |
| **Corporate Network**     | Firewall, segmentation, VPN, site-to-site tunnels          | Fortinet FortiGate 60F or Ubiquiti UDM SE — **$1,500 CapEx**                                       |
| **Backup & Recovery**     | Immutable and offsite data protection                      | Acronis Cyber Protect — **$100/month**, Wasabi Cloud Storage — **$50/month**                       |

---

## **NIST CSF Function Implementation**

| **Function**      | **Implementation Highlights**                                                                          |
| ----------------- | ------------------------------------------------------------------------------------------------------ |
| **ID – Identify** | Asset inventory via Intune, risk posture via Secure Score, governance templates in SharePoint or Drata |
| **PR – Protect**  | MFA, DLP, XDR, patching, Bitwarden vaults, Cloudflare filtering, NGFW segmentation                     |
| **DE – Detect**   | Sentinel SIEM log correlation, Defender anomaly detection, optional MDR support                        |
| **RS – Respond**  | Incident triage through Sentinel + Jira/PagerDuty integration, root cause documentation                |
| **RC – Recover**  | Immutable Acronis/Wasabi backups, tested restoration, DR runbooks in SharePoint                        |

---

## **Budget Breakdown**

| **Category**                              | **CapEx**    | **Monthly OpEx**   | **Notes**                                           |
| ----------------------------------------- | ------------ | ------------------ | --------------------------------------------------- |
| Network gear (firewall, Wi-Fi)            | **$1,500**   | –                  | FortiGate or Ubiquiti gear for HQ and branch        |
| Microsoft 365 Business Premium (50×$22)   | –            | **$1,100**         | Includes email, Intune, Defender, SharePoint        |
| Cloudflare Gateway                        | –            | **$100**           | DNS filtering, DDoS protection, TLS termination     |
| Backup (Acronis + Wasabi)               | –            | **$150**           | Immutable SaaS and endpoint backups                 |
| SIEM / MDR (Sentinel + Arctic Wolf)     | –            | **$450**           | Centralized logging, threat detection, and response |
| Password management (Bitwarden)         | –            | **$75**            | Team password and secret storage                    |
| Ticketing / alerting (Jira / PagerDuty) | –            | **$100**           | Incident routing and response coordination          |
| **Total**                                 | **≈ $1,500** | **≈ $1,475/month** | Within budget target                                |

