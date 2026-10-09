# Email Analysis

This repository documents a learning project on phishing-email investigation.
It starts with manual analysis of `.eml` files and training labs, with the
longer-term goal of building a small, explainable analysis workflow. The
project's current starting point is the **Warm-Up** material; the wider
pipeline and later project phases are still being developed.

## Start here

Begin with the [Warm-Up guide](./Warm-Up/README.md). It introduces the topic,
links the training-platform walkthroughs, and points to the shared lab setup.

### Current Warm-Up material

- [Hack The Box: Phishing Email Analysis](./Warm-Up/HTB/README.md) — module
  notes, lab setup, header walkthroughs, and quiz notes.
- [BTLO: The Planet's Prestige](./Warm-Up/BTLO/README.md) — a chronological
  email investigation, with question-by-question notes in
  [answers.md](./Warm-Up/BTLO/answers.md).
- [CyberDefenders: PhishStrike](./Warm-Up/CyberDefenders/README.md) — a
  phishing-email and threat-intelligence investigation, with answers and
  evidence notes in [answers.md](./Warm-Up/CyberDefenders/answers.md).

These notes distinguish observations from the supplied email, conclusions
based on local evidence, and findings taken from external reports. Some
malware-analysis questions depend on third-party sandbox or challenge
write-ups and are marked accordingly.

## Project direction

The project is intended to grow from manual analysis toward a documented,
testable workflow for inspecting email headers, authentication results,
message bodies, links, and attachments. Future work may include parsing
samples, extracting and defanging indicators, enrichment, reporting, and
isolated lab exercises. These are planned directions, not claims that those
capabilities already exist.

See the [project roadmap](./ROADMAP.md) for the current phased plan and
[RESOURCES.md](./RESOURCES.md) for collected reading and analysis resources.

## Safe handling

Emails and related files in this repository may contain suspicious links,
active HTML, or malware indicators. Treat them as untrusted evidence:

- Do not click links or open attachments on a personal or production system.
- Do not execute samples or enable active content.
- Use an isolated, disposable analysis environment for any work that requires
  opening potentially unsafe files.
- Treat public reputation and sandbox results as time-sensitive evidence, not
  as proof of attribution or as a substitute for direct analysis.
