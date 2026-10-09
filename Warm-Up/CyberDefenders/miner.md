# CoinMiner: Case Note

**CoinMiner** is a broad antivirus/threat-intelligence label for software
associated with unauthorized cryptocurrency mining, not necessarily one
specific malware family or binary. In the PhishStrike challenge, it is the
reported answer to the question about the cryptocurrency-mining payload.

The [URLhaus evidence](./evidences/analysis.png) associates the download
infrastructure with CoinMiner, BitRAT, and AsyncRAT payload labels. Those
labels come from external reporting; this project did not execute or
independently classify the samples.

For the case context and the limits of that attribution, see the
[PhishStrike walkthrough](./README.md#4-record-and-defang-the-messages-url-indicators)
and [answer 4](./answers.md#4-which-malware-family-is-responsible-for-cryptocurrency-mining).

## Why the label matters

Cryptomining malware can use a compromised computer's processing resources
without the owner's consent. In an investigation, resource abuse is one
possible impact to consider alongside credential theft, remote access,
persistence, and data theft. The CoinMiner label alone does not establish
which behavior occurred on a particular victim's system.
