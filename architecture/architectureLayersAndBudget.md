# Architecture Layers & Budget


| **Category** | **CapEx (one‑time)** | **Monthly OpEx** | **Basis / Notes** |
|---|---:|---:|---|
| **NGFW + branch gear** | **$1,500** | – | FortiGate/UDM SE class for HQ + small branch |
| **Microsoft 365 Business Premium** | – | **$1,100** | $22 × 50; includes Intune + Defender for Business |
| **Entra ID P2 (admins)** | – | **$90** | ~$9 × 10 admins (PIM, risk‑based access) |
| **Azure Front Door + WAF** | – | **$120** | Low/medium traffic e‑commerce (rules + data transfer) |
| **Microsoft Sentinel** | – | **$250** | ~10 GB/day effective, commitment or benefit pricing |
| **Azure Backup (App/DB)** | – | **$80** | Vault + LTR; small DB + app artifacts; WORM tier |
| **M365 Backup (sep tenant)** | – | **$60** | SMB tier for 50 users (mail/OD/SP) |
| **Ticketing/Alerting (Jira/PD/Teams apps)** | – | **$100** | Basic plan for routing/on‑call |
| **TOTAL** | **≈ $1,500** | **≈ $1,800 / month** | Microsoft‑first; removable line items marked optional below |

**Optional / Swappable**  
- Password manager (if desired in addition to Entra creds): **Bitwarden Teams** ~$75/mo.  
- Third‑party MDR add‑on: ~$200–$400/mo.
