# The Planet's Prestige: Answers and Evidence

This file records each BTLO question, the relevant observation from the
evidence, and the answer in the requested format. For the complete
chronological investigation and technical explanations, see the
[README walkthrough](./README.md).

Unless noted otherwise, run the shell commands from the `Warm-Up/BTLO/`
directory.

## Answer summary

| # | Question topic | Answer |
| --- | --- | --- |
| 1 | Email service | `emkei.cz` |
| 2 | Reply-To address | `negeja3921@pashter.com` |
| 3 | Attachment file type | `.zip` |
| 4 | Malicious actor's name | `Pestero Negeja` |
| 5 | Attacker's location | `The Martian Colony, Beside Interplanetary Spaceport` |
| 6 | Probable C2 domain | `pashter.com` |

## 1. What is the email service used by the malicious actor?

**Requested format:** `domain.tld`

The receiving server's `Received` trace records a connection from
`emkei.cz` (`93.99.104.210`). This identifies the service host shown in the
message trace; it does not independently identify the individual sender.

```text
Received: from localhost (emkei.cz. [93.99.104.210])
```

See [header triage in the walkthrough](./README.md#2-triage-the-headers-and-authentication-results)
and the [Received evidence](./evidences/received.png).

**Answer:** `emkei.cz`

## 2. What is the Reply-To email address?

The message's `Reply-To` field gives the address used for replies:

```shell
grep -i '^Reply-To:' 'A Hope to CoCanDa.eml'
```

```text
Reply-To: negeja3921@pashter.com
```

See [header triage in the walkthrough](./README.md#2-triage-the-headers-and-authentication-results).

**Answer:** `negeja3921@pashter.com`

## 3. What is the file type of the received attachment that helped continue the investigation?

**Requested format:** `.filetype`

The MIME headers label the attachment as a PDF, but decoding its contents
reveals the ZIP signature `50 4B 03 04`. Listing the archive shows the
challenge files inside. The declared filename and content type therefore do
not match the actual container format.

```shell
unzip -l attachment.zip
```

See [attachment validation in the walkthrough](./README.md#4-validate-the-attachment-instead-of-trusting-its-filename),
the [MIME evidence](./evidences/trick.png), and the
[signature comparison](./evidences/header.png).

**Answer:** `.zip`

## 4. What is the name of the malicious actor?

**Requested format:** `FirstName LastName`

The PDF's embedded `Author` metadata contains the name used for this lab
answer:

```shell
exiftool -Author PuzzleToCoCanDa/GoodJobMajor.pdf
```

```text
Author                          : Pestero Negeja
```

Metadata is a lead, not conclusive proof of authorship. See
[metadata analysis in the walkthrough](./README.md#5-examine-document-metadata-and-hidden-spreadsheet-content).

**Answer:** `Pestero Negeja`

## 5. What is the location of the attacker in this Universe?

**Requested format:** `SomePlace, Near Somewhere`

The second worksheet contains Base64 text that decodes to the location:

```text
VGhlIE1hcnRpYW4gQ29sb255LCBCZXNpZGUgSW50ZXJwbGFuZXRhcnkgU3BhY2Vwb3J0Lg==
```

```shell
printf '%s' 'VGhlIE1hcnRpYW4gQ29sb255LCBCZXNpZGUgSW50ZXJwbGFuZXRhcnkgU3BhY2Vwb3J0Lg==' \
  | base64 --decode
```

The decoded value is `The Martian Colony, Beside Interplanetary Spaceport.`
The answer is written without the final punctuation. See
[spreadsheet analysis in the walkthrough](./README.md#5-examine-document-metadata-and-hidden-spreadsheet-content)
and the [worksheet evidence](./evidences/sheet-3.png).

**Answer:** `The Martian Colony, Beside Interplanetary Spaceport`

## 6. What could be the probable C2 domain to control the attacker's autonomous bots?

**Requested format:** `domain.tld`

The `Reply-To` address uses the domain `pashter.com`. In the context of the
lab, this is the probable command-and-control (C2) domain; the address alone
does not prove that the domain actually hosted C2 infrastructure.

```shell
grep -i '^Reply-To:' 'A Hope to CoCanDa.eml'
```

```text
Reply-To: negeja3921@pashter.com
```

See [header triage in the walkthrough](./README.md#2-triage-the-headers-and-authentication-results).

**Answer:** `pashter.com`
