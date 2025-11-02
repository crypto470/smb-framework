# Traffic Flow

## F1. Public customers → Edge → App → DB (E-commerce)

1. Customer → **AFD/WAF** (TLS, DDoS, bot control).
2. AFD → **App Service** (per-site rate limits; health probes).
3. App → **Azure SQL (PE)** via private link; secrets from **Key Vault** (managed identity).
4. App/AFD/WAF/SQL diagnostics → **Sentinel**.
5. DB protected by Azure Defender for SQL; long-term backups via **Azure Backup Vault** → **Immutable**.

**Outcome:** Only sanitized HTTPS reaches the app; DB never exposed publicly; all activity logged.

---

## F2. Remote staff

1. Device compliance via **Intune** (AV, patch, disk enc).
2. User signs in with **Entra ID + MFA**; policy evaluated in **Entra Private Access (ZTNA)**.
3. If SaaS: ZTNA → **M365 / allowed SaaS** directly.
4. If on-prem app: ZTNA → **NGFW App Publishing** → **Server VLAN** (published ports only).
5. **Explicitly blocked**: ZTNA → **Staff VLAN** landing.
6. Non-compliant device → **Quarantine VLAN** or limited web.

**Outcome:** Per-app ZTNA, zero trust posture; no broad network access.

---

## F3. On-site users & segmentation (802.1X → VLANs → NGFW → Internet)

1. 802.1X/RADIUS admits endpoints into **Staff / Guest / IoT / Quarantine**.
2. All egress from VLANs → **NGFW/IPS** (app control, URL categories, TLS decrypt where allowed).
3. **Policy matrix (egress summary)**

   - Staff → Internet (filtered), Staff ↔ Server (app ports only)
   - Server → Staff (restricted/admin), Guest → Internet only
   - **IoT → Internet allow-list only; IoT/Guest → any internal = DENY**

**Outcome:** Strong east–west controls and least-privilege egress.

---

## F4. Email & SaaS hardening

1. **Defender for O365** (anti-phishing, Safe Links/Attachments, impersonation).
2. SPF/DKIM/DMARC enforced (Azure DNS).
3. **SCIM** from Entra ID to allowed SaaS (role mapping, de-provision on exit).
4. All auth/sign-in + O365 security events → **Sentinel**.

**Outcome:** Phishing resistance, lifecycle governance, auditable access.

---

## F5. IoT isolation & kill-switch

1. Devices join **IoT VLAN**; **default-deny to internal**, allow-listed outbound only (NTP, vendor cloud).
2. **Kill-switch playbook** on incident: disable IoT SSID/WiFi, shut IoT switchports, push NGFW rule **IoT→any: DENY** (keep mgmt if needed), optionally move offenders to **Quarantine VLAN**.
3. IoT telemetry and any anomaly → **Sentinel** for alerting.

**Outcome:** Compromise of IoT cannot laterally impact HQ/corporate assets.

---

## F6. Logging, SIEM resilience & response

1. AFD/WAF, App, SQL, NGFW/IPS, Entra, M365, ZTNA, backups → **Sentinel** (Azure Monitor Agent/DCR).
2. **Resilience**: ingestion heartbeats, queue/diagnostic settings, per-source schema checks; alerts on delayed or missing logs; alternate ingestion path for critical sources.
3. **SOAR**: Sentinel playbooks notify **Teams/Ticketing** and can auto-contain (block IP, disable account, revoke sessions, isolate device, trigger IoT kill-switch).
4. **MDR/SOC** triages & closes incidents with runbooks and evidence trail.

**Outcome:** Timely detection even if a source lags; automated, audited containment.

---

## F7. Backup & restore hygiene

1. **M365 Backup** (separate tenant; immutability).
2. **Azure Backup Vault** protects App & **Azure SQL**; copies to **Immutable/WORM**; optional **secondary region (GRS/RA-GRS)**.
3. **Restore-to-STAGING first**: malware scan & data validation → then restore to PROD.
4. Targets: **RPO ≤ 24h; RTO ≤ 8h (App), ≤ 4h (M365)**.
5. Quarterly technical restores + tabletop exercises; least-privilege “Backup Operator” role; break-glass account; **no write** from prod to backup stores.

**Outcome:** Ransomware-resilient backups with verified, clean restores.

---

## F8. Data path summary

- **Web checkout:** Customer → AFD/WAF → App → SQL (PE) → (optional) payment gateway/SaaS via allow-listed egress.
- **Admin to internal app:** Remote Staff → ZTNA → NGFW publish → Server VLAN app (no Staff VLAN).
- **Email:** Internet ↔ M365 through Defender for O365; events/sign-ins → Sentinel.
- **IoT telemetry:** IoT VLAN → vendor cloud (allow-list) → logs/alerts → Sentinel.
- **Backups:** M365/App/SQL → Backup Vaults → Immutable → optional GRS.

---

## Egress/Access Matrix

| Source → Destination |            Internet |                        Server VLAN |             Staff VLAN | IoT VLAN | Guest VLAN |
| -------------------- | ------------------: | ---------------------------------: | ---------------------: | -------: | ---------: |
| **Staff VLAN**       |    Allow (filtered) |                  Allow (app ports) |                      — |     Deny |          — |
| **Server VLAN**      |            Restrict |                                  — |       Restrict (admin) |     Deny |       Deny |
| **Guest VLAN**       |       Internet-only |                               Deny |                   Deny |     Deny |          — |
| **IoT VLAN**         | **Allow-list only** |                           **Deny** |               **Deny** |        — |       Deny |
| **ZTNA remote**      |         SaaS direct | Via NGFW publish → **Server VLAN** | **Not a landing zone** |     Deny |       Deny |

(“—” = not applicable)

---

### Notes on SIEM data delay/corruption

- **Detect**: set Sentinel rules for **ingestion latency** and **log integrity** (sequence gaps, schema drift).
- **Buffer & retry**: use Azure Diagnostics/Azure Monitor with **delivery retries/queues**; for agents, enable local buffering.
- **Alternate path**: critical logs mirrored to a secondary Log Analytics workspace or storage account for recovery.
- **Integrity**: hash/sign high-value logs; use immutable storage for long-term retention.
