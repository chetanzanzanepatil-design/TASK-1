# Task 1 — Network Reconnaissance (Nmap scan)

**Internship:** Cyber Security Internship  
**Task:** Scan the local network for open ports and identify potential risks.

## Objective
Discover open ports on devices in the `192.168.1.0/24` network to learn network exposure and basic reconnaissance.

## Tools
- Nmap (TCP SYN scan): `nmap -sS`
- Zenmap (GUI) used to capture screenshots
- (Optional) Wireshark for packet analysis

## Commands I ran
```bash
# TCP SYN scan the entire /24 subnet and save results to a text file
nmap -sS 192.168.1.0/24 -oN scan_results.txt

# Example: scan single host
nmap -sS 192.168.1.3 -oN host_192.168.1.3.txt
```

## Screenshots
- `Screenshot_1.png` — Zenmap output / per-host (`192.168.1.3`)  
- `Screenshot_2.png` — Zenmap ports/services view  
- `Screenshot_3.png` — Full subnet scan output showing all discovered hosts  
- (Add any additional images you captured)

> Place these images in a folder named `screenshots/` in this repo.

## Results (summary)
| Host IP       | Open Ports (tcp)     | Service (common name)        |
|---------------|----------------------|------------------------------|
| 192.168.1.1   | (gateway may be present) | -                          |
| 192.168.1.3   | 88, 445, 5900        | kerberos-sec, microsoft-ds, vnc |
| 192.168.1.10  | 22, 80, (53 filtered)| ssh, http, domain (53 filtered) |
| 192.168.1.11  | 49152, 62078         | unknown, iphone-sync         |

*(Exact port/service names visible in screenshots — see `screenshots/` for full outputs.)*

## Key findings & security risks
- VNC service (5900) is open on `192.168.1.3` — if unauthenticated or weak passworded, can allow remote desktop takeover.
- SMB (445 / microsoft-ds) open — exposes file sharing services; misconfiguration or unpatched systems may be vulnerable (e.g., SMB vulnerabilities).
- SSH (22) is open on `192.168.1.10` — if password auth is allowed or weak creds used, attackers may gain shell access.
- High ephemeral ports (49152, 62078) on `192.168.1.11` — often legitimate (device services/sync), but should be checked for unnecessary exposure.
- DNS port 53 appears filtered on a host — could indicate a firewall or rate-limiting.

## Recommendations / Remediation
1. **Close or restrict unnecessary ports** — only expose services that are required. Use firewall rules to restrict by IP where possible.
2. **Harden services**:
   - VNC: require strong password, use tunneling (SSH) or VPN rather than exposing 5900 to the local network if not needed.
   - SMB: disable if not required; keep systems patched; use least privilege for shares.
   - SSH: disable password auth, use key-based authentication, change default port only as an additional layer.
3. **Network segmentation**: place IoT/mobile devices on a guest VLAN to reduce lateral movement.
4. **Apply updates/patches** to OS and services.
5. **Monitor logs** and run periodic scans to detect changes.

## How I saved results
```bash
nmap -sS 192.168.1.0/24 -oN scan_results.txt     # normal text
nmap -sS 192.168.1.0/24 -oX scan_results.xml     # XML (for tools)
```

## What I learned
- How to run a TCP SYN scan and interpret Nmap output.  
- How to associate open ports with likely services and evaluate exposure.

## Files to include in repo
- `scan_results.txt` (Nmap output)  
- `screenshots/` (Zenmap screenshots)  
- `README.md` (this file)
