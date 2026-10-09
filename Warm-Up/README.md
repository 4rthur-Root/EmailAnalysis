# Warm-Up: Email Analysis Fundamentals

The Warm-Up is the project's starting point: a guided introduction to
investigating suspicious email, followed by practice with real-world-style
training challenges. The notes focus on how to examine evidence, explain
findings, and distinguish direct observations from conclusions that rely on
external reports.

## Learning path

### 1. Build the foundation

These introductory resources cover phishing indicators and investigation
basics:

- [How Email Data Helps Identify Phishing: A SOC Analyst's Guide to Early Detection and Response](https://cyberdefenders.org/blog/how-to-identify-phishing-a-soc-analysts/)
- [Phishing Email Examples: 15 Analyzed by a SOC Analyst](https://www.socsimulator.com/blog/phishing-email-examples)
- [MITRE ATT&CK: Phishing (T1566)](https://attack.mitre.org/techniques/T1566/)
- [How to analyse an email](https://www.socsimulator.com/blog/phishing-email-analysis)
- [SPF, DKIM, and DMARC](https://www.cloudflare.com/learning/email-security/dmarc-dkim-spf/)

### 2. Prepare the lab environment

See the [Debian lab setup guide](../lab-setup/). Hack The Box-specific VPN
and remote-desktop setup is documented in its
[HTB guide](./HTB/README.md#lab-setup).

### 3. Work through the platform exercises

Each platform guide contains its own walkthrough and evidence:

| Platform | Exercise | What to practice |
| --- | --- | --- |
| [Hack The Box](./HTB/README.md) | Phishing Email Analysis | Header fields, reply routing, and the `Received` chain |
| [Blue Team Labs Online](./BTLO/README.md) | The Planet's Prestige | MIME parsing, attachment signatures, and file metadata |
| [CyberDefenders](./CyberDefenders/README.md) | PhishStrike | Header authentication, URL triage, and threat-intelligence findings |

Some exercises rely on information from sandboxes or published challenge
walkthroughs because the original payloads are unavailable or could not be
independently reproduced. The individual notes identify those cases instead
of presenting them as direct observations.

## Investigation habits

- Preserve the original `.eml` and work from a copy.
- Read authentication results in context; a pass or failure is one signal, not
  a complete verdict.
- Compare visible sender fields with `Reply-To`, `Return-Path`, and trusted
  transport headers.
- Inspect MIME parts and validate file contents rather than trusting filenames
  or declared content types.
- Defang suspicious indicators in reports and never visit malicious URLs
  directly.
- Record the source and confidence of each conclusion, particularly when it
  comes from third-party intelligence.

## Continue the project

The Warm-Up is the first stage, not the full project. See the root
[project README](../README.md) for the overview and the
[roadmap](../ROADMAP.md) for planned work beyond these manual investigations.
