# NetworkWalks — Week 2 Cybersecurity Practical

**Topics:** Footprinting, Reconnaissance, and Network Discovery  
**Program:** NetworkWalks Cybersecurity Training — Batch B084  
**Author:** Ghalib Farras Ahmad  
**Report date:** 11/11/2026

---

## 1. Project Overview

This repository records two Week 2 practical modules:

- **W2-PM1 — Footprinting with Multiple Kali Tools:** collect and review publicly available information about `networkwalks.com`.
- **W2-PM5 — Zenmap-Based Network Scanning:** discover live hosts on the author's permitted local lab network and view the resulting topology.

The exercises demonstrate how a security tester builds an initial picture of a target before deciding what should be examined next. The activities documented here are reconnaissance and host discovery only. They do **not** demonstrate a confirmed vulnerability or successful system compromise.

## 2. What Is Reconnaissance?

**Reconnaissance (recon)** means collecting information about a target so a tester can understand what exists, what is exposed, and where further security checks may be useful.

In simple terms:

> Recon is the process of learning about the target before testing its security.

A tester may learn:

- Which domains, IP addresses, and systems are in scope.
- Which services and technologies appear to be exposed.
- How DNS is configured.
- Which hosts respond on an authorized network.
- Which areas deserve closer review.

Recon is not the same as exploitation. Finding a domain, server, technology, or live host does not automatically mean that it is vulnerable.

## 3. Passive Recon vs Active Recon

The main difference is whether the tester directly sends probes or requests to the target's systems.

| Type | Simple meaning | Common examples | Target interaction |
|---|---|---|---|
| **Passive recon** | Collect information from sources that already publish it. | Public search engines, public company pages, public WHOIS/RDAP records, certificate-transparency logs, archived pages. | No direct probing of the target's services. |
| **Active recon** | Send a request or probe to a target system to learn how it responds. | HTTP requests, technology fingerprinting, DNS queries to target infrastructure, ping/host discovery, port scanning. | The target or its infrastructure may receive and log the activity. |

### Important point

A tool is not automatically passive or active just because of its name. The method and destination matter. For example, a DNS lookup sent to a public recursive resolver is different from querying a target's authoritative DNS server directly. WhatWeb, cURL, WAFW00F, and Nmap host discovery generate traffic toward the target or its exposed services and should be treated as active testing.

**Rule of thumb:** passive recon observes public information; active recon asks the target or its infrastructure to respond. Active testing requires clear authorization and a defined scope.

## 4. How Recon Fits Into a Professional Security Assessment

Professional penetration testing is not simply “run tools, then attack.” The tester works within an agreed scope, records evidence, checks whether observations matter, and only proceeds when the next activity is authorized.

A simplified workflow is:

1. **Pre-engagement and authorization** — agree on the owner, targets, IP ranges, test window, allowed techniques, prohibited actions, emergency contacts, and reporting expectations.
2. **Passive reconnaissance** — review public information without directly probing target services.
3. **Active reconnaissance and enumeration** — make controlled requests to identify reachable hosts, services, technologies, DNS information, and application entry points.
4. **Attack-surface mapping and threat modelling** — organize the discovered assets, identify trust boundaries, and decide which exposed components matter most.
5. **Vulnerability analysis** — compare the observed configuration and behavior with known weaknesses; validate carefully and avoid treating a scanner result as proof.
6. **Gaining access / exploitation** — only when explicitly authorized, attempt a controlled proof of a specific weakness. Stop once the agreed proof objective is met.
7. **Post-exploitation impact verification** — determine the minimum impact needed to demonstrate risk. Do not access unrelated data, maintain persistence, or disrupt services unless the Rules of Engagement explicitly permit it.
8. **Cleanup and restoration** — remove test artifacts, restore changed settings, and confirm the target is left in the agreed state.
9. **Reporting** — explain scope, methods, evidence, observations, validated findings, impact, limitations, and practical remediation.

The flow can loop. New information may send the tester back to recon or threat modelling. **Gaining access is not an automatic next step**: if no weakness is validated, or exploitation is outside scope, the tester stops and reports the observations.

### What does “advanced or professional recon” look like?

The difference is not simply using more tools. Professional recon is structured and evidence-led:

- It starts with written scope and rules of engagement.
- It combines passive sources with carefully controlled active checks.
- It links domains, IPs, DNS records, services, technologies, applications, and user-facing entry points into an asset map.
- It records where each fact came from, when it was collected, and how confident the tester is.
- It prioritizes assets by exposure and business importance.
- It produces a clear hand-off to vulnerability analysis, not an unsupported claim that a vulnerability exists.

## 5. Scope and Lab Context

### W2-PM1 — Domain reconnaissance

| Item | Value |
|---|---|
| Target | `networkwalks.com` |
| Platform | Kali Linux |
| Activity | WHOIS/RDAP-style registration lookup, DNS lookup, HTTP header review, technology fingerprinting, WAF detection, DNS enumeration |
| Test limitation | No exploitation or vulnerability validation documented |

### W2-PM5 — Local network discovery

| Item | Value |
|---|---|
| Network range | `10.0.0.0/24` |
| Kali Linux address | `10.0.0.2/24` |
| Gateway shown in report | `10.0.0.1` |
| Scan profile | Zenmap Ping Scan |
| Nmap command | `nmap -sn 10.0.0.0/24` |
| Activity | Host discovery and topology visualization |

Only scan networks and systems that you own or have explicit permission to test. The local subnet in this project is described as the author's lab network.

## 6. Tools Used and What They Do

| Tool | Purpose in this exercise |
|---|---|
| **WHOIS** | Retrieves domain registration and registrar information that is publicly available. |
| **WhatWeb** | Makes web requests and reports technologies that its fingerprints recognize. Results are indicators, not a definitive software inventory. |
| **nslookup** | Queries DNS to resolve a domain name. The saved result used Google Public DNS (`8.8.8.8`). |
| **cURL** | Sends an HTTP request; `-I` requests response headers without downloading the page body. |
| **WAFW00F** | Sends test requests and checks whether responses indicate a Web Application Firewall. Detection is not a full WAF configuration audit. |
| **DNSRecon** | Queries DNS information and can enumerate common record types. The original report contains a screenshot of results; the separately supplied text export is empty. |
| **Zenmap / Nmap** | Zenmap is the graphical interface; Nmap performs the host discovery. `-sn` discovers hosts without performing a port scan. |

## 7. Key Observations From the Supplied Evidence

These are observations from the supplied captures, not confirmed vulnerabilities.

- **WHOIS:** the screenshot shows GoDaddy as registrar and the nameservers `NS6135.HOSTGATOR.COM` and `NS6136.HOSTGATOR.COM`. The supplied raw WHOIS text contains additional response data and inconsistent nameserver/update details; verify against a fresh authoritative lookup before relying on a single set.
- **DNS lookup:** the saved `nslookup` output, using `8.8.8.8`, resolved `networkwalks.com` to `192.232.216.135`.
- **WhatWeb:** the saved fingerprint reported Apache, WordPress `7.1.4`, WordPress Download Manager `3.3.76`, jQuery `3.7.1`, and Bootstrap `7.1.4`. These are tool-detected indicators and were not independently verified.
- **HTTP headers:** cURL received `HTTP/2 200`. The headers advertise a WordPress REST API link at `/wp-json/`. The cookie value is redacted in the evidence copy.
- **WAFW00F:** version `2.4.2` reported ModSecurity (SpiderLabs) after two requests. This is a detection result, not a configuration audit.
- **DNSRecon:** the screenshot embedded in the original report shows a completed enumeration with six records reported. The separate text export is zero bytes, and the screenshot uses a different terminal account (`tester@tester`) from other supplied captures (`l3minerale`). Treat this as a provenance limitation.
- **Zenmap:** the supplied topology graphic displays `10.0.0.1`, `10.0.0.2`, and `10.0.0.12`. The earlier report text listed only `.1` and `.2`; the identity and role of `.12` are not established by the supplied evidence.

## 8. Observation Is Not the Same as a Vulnerability

A public IP address, software fingerprint, DNS record, WAF detection, or responding host is not automatically a security weakness.

Use these terms carefully:

- **Observation:** a fact shown by the evidence, such as an HTTP response or a live host.
- **Potential risk:** a reason the observation may matter, such as unnecessary technology disclosure.
- **Validated vulnerability:** a weakness confirmed through an authorized test with a reproducible result and demonstrated impact.

This project primarily records observations. It does not contain a validated exploitation finding.


## 9. References

- [OWASP Web Security Testing Guide — Information Gathering Overview](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/00-Information_Gathering_Overview)
- [OWASP Web Security Testing Guide — Penetration Testing Methodologies](https://owasp.org/www-project-web-security-testing-guide/v42/3-The_OWASP_Testing_Framework/1-Penetration_Testing_Methodologies)
- [NIST SP 800-115 — Technical Guide to Information Security Testing and Assessment](https://csrc.nist.gov/pubs/sp/800/115/final)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Zenmap Reference Guide](https://nmap.org/book/zenmap.html)
- [Kali Linux Documentation](https://www.kali.org/docs/)

---

**Week 2 Practical Project — NetworkWalks**  
*Learn the target. Verify the evidence. Respect the scope.*
