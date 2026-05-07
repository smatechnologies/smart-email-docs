---
sidebar_label: 'SMArt Email'
title: SMArt Email
description: "Overview of the SMArt Email utility, its capabilities, and intended audience."
tags:
  - Conceptual
  - System Administrator
  - Smart Email
---

# SMArt Email

## What is it?

The SMArt Email utility periodically checks a mailbox and submits OpCon events when an email matches the masks defined in its configuration file. Masks can be applied to the email's subject line, body, or attachment names.

Use SMArt Email to:

- Trigger OpCon schedules or jobs from inbound email.
- Generate events from data captured in the subject line using regular-expression masks.
- Generate events based on the names of email attachments.
- Generate events based on content within the body of an email.

## Key capabilities

- Connects over IMAP, POP, or MSAL (for Microsoft-hosted Office 365 / Outlook.com mailboxes).
- Supports TLS 1.1 and 1.2 for IMAP and POP connections.
- Supports multi-line matching in the subject line or body using regular expressions.
- Allows you to choose whether to download processed emails locally.
- Allows you to download attachments and generate events based on attachment names.

## Audience

This online help is written for users with a working knowledge of email server configuration and a basic understanding of automated job scheduling concepts.

## Where to go next

- [Installation](./installation.md) — install SMArt Email for POP, IMAP, or MSAL.
- [Configuration](./configuration.md) — set credentials and configure the `SMArtEmail.ini` file.
- [Operation](./operation.md) — run SMArt Email from the command line.
- [Exit codes](./exit-codes.md) — exit codes returned by `smartemail.exe`.
- [MSAL troubleshooting](./msal-troubleshooting.md) — recover from MSAL setup or token issues.
- [Release notes](./release-notes.md) — version history.

## Notes for installation and file paths

### Microsoft compliance

To meet compliance as a Microsoft Certified Development Partner, SMA Technologies has standardized on storing application data files in the `ProgramData` directory by default when you install to the system drive. For more information, refer to [Determining Installation Locations](https://help.smatechnologies.com/opcon/core/installation/system-requirements#determining-installation-locations) in the OpCon Installation online help.

### Windows file names

Some systems do not allow long file names (for example, `C:\Program Files\OpConxps\`). To work around this, revert to method 8.3. In this method, the 7th character becomes a tilde followed by a 1 (for example, `C:\Progra~1\OpConxps\`).

## Glossary

- **MSAL** — Microsoft Authentication Library. Used by SMArt Email to authenticate against Microsoft-hosted Office 365 / Outlook.com mailboxes.
- **IMAP** — Internet Message Access Protocol. A standard protocol used to retrieve messages from a mail server while leaving the messages on the server.
- **POP** — Post Office Protocol. A standard protocol used to retrieve messages from a mail server, typically removing them from the server.
- **MSGIN directory** — The directory monitored by the SAM where SMArt Email writes external event files.
- **External event** — An OpCon event submitted from outside the OpCon scheduling components, used to trigger schedules, jobs, or other actions.
