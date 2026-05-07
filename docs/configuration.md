---
title: SMArt Email configuration
description: "Configure the SMArt Email utility including credentials, INI file settings, and example configurations for subject, attachment, and body matching."
tags:
  - Reference
  - System Administrator
  - Smart Email
---

# Configuration

## What is it?

After SMArt Email is installed, configuration controls how the utility connects to your mailbox, which emails it matches, and which OpCon events it submits. This page covers credential setup, every setting in the `SMArtEmail.ini` file, and worked examples for subject, attachment, and body matching.

Use this page when you:

- Configure the utility for the first time after install.
- Add or modify configurations for new email masks, attachments, or body matching.
- Change email server credentials or OpCon credentials after install.

## How do I…

| Goal | Section |
|---|---|
| Get a minimum working configuration | [Quick start](#quick-start) |
| Understand how an email is matched | [How SMArt Email processes an email](#how-smart-email-processes-an-email) |
| Set or change mailbox / OpCon credentials | [Set or modify credentials](#set-or-modify-credentials) |
| Look up a specific INI setting | [INI file reference](#ini-file-reference) |
| Look up a token like `[[SENDER]]` | [Token reference](#token-reference) |
| Match emails by subject line | [Example 1: Match by subject](#example-1-match-by-subject) |
| Match emails by attachment name | [Example 2: Match by attachment](#example-2-match-by-attachment) |
| Match emails by body content | [Example 3: Match by body](#example-3-match-by-body) |
| Capture values from the subject and pass them to an event | [Regular expressions in masks](#regular-expressions-in-masks) |
| Restrict which senders can trigger events | `AllowedSenders` in [`[Configuration#]` settings](#configuration-settings) |
| Restrict the time window when emails are processed | `AllowedTimeFrame` in [`[Configuration#]` settings](#configuration-settings) |
| Log accepted or rejected emails | [`[Audit]` settings](#audit-settings) |
| Define reusable tokens | [`[Tokens]` settings](#tokens-settings) |
| Avoid common mistakes | [Common pitfalls](#common-pitfalls) |
| Recover from MSAL permission errors | [MSAL troubleshooting](./msal-troubleshooting.md) |

## Quick start

The smallest working `SMArtEmail.ini` matches emails whose subject contains the text `Test` and submits an OpCon event. Replace the placeholder values with values from your environment.

```ini
[General]
MSGINDirectory=%PROGRAMDATA%\OpConxps\SAM\MSGIN

[Mail]
Server=imap.example.com
EmailProtocol=IMAP
SecurityProtocol=TLS1_2
Port=993

[Configuration1]
SubjectLineMask=Test
Event=$CONSOLE:DISPLAY,the test passed
AllowedSenders=ALL
AllowedTimeFrame=0000-2400
```

Then run the credential setup once to encrypt the mailbox and OpCon credentials into the file:

```
smartemail.exe --credentials -user:[mailUser] -password:[mailPassword] -opconuser:[opconUser] -opconpassword:[opconPassword]
```

After this, run `smartemail.exe` (with the appropriate flags from [Operation](./operation.md)) on a schedule.

## How configuration is organized

Configuration happens in three places:

1. **Email server side** — protocol, encryption, and (for Microsoft-hosted mailboxes) administrator consent for MSAL.
2. **Credentials** — set with the `--credentials` operation, which encrypts the username and password in the INI file.
3. **`SMArtEmail.ini`** — sectioned by `[General]`, `[Mail]`, `[Audit]`, `[Tokens]`, and one `[Configuration#]` block per mask-and-event rule.

## How SMArt Email processes an email

For each incoming email, SMArt Email evaluates each `[Configuration#]` block in turn:

1. **Sender check.** If the sender is not in `AllowedSenders`, the email is rejected for this configuration. Use `ALL` to skip this filter.
2. **Time-window check.** If the email arrived outside `AllowedTimeFrame`, it is rejected for this configuration.
3. **Subject match.** SMArt Email compares the subject line to `SubjectLineMask`. A match makes the email eligible to generate the configured event.
4. **Body match (optional).** If `ProcessBody=YES`:
   - If `QualifyingSubjectLine` is empty, every line in the body is also evaluated against `SubjectLineMask`. Subject and body matches can both generate events.
   - If `QualifyingSubjectLine` is set, the subject is first compared to `QualifyingSubjectLine`. If that matches, every line in the body is then evaluated against `SubjectLineMask`. The qualifier itself does not generate events; it only filters.
5. **Attachment match (optional).** If `ProcessAttachments=true` and `AttachmentMask` is set, attachment names are evaluated against the mask.
6. **Event submission.** When the configuration matches, SMArt Email writes the configured `Event` (and `Event2`, `Event3`, …) to the MSGIN directory for the SAM to pick up.
7. **Cleanup.** Based on `DeleteEmails` (`Match`, `All`, or `None`), SMArt Email removes the email from the server.
8. **Audit.** If enabled, the email is logged and a notification email may be sent (see [`[Audit]` settings](#audit-settings)).

## Prerequisites

Gather these details before editing the INI file.

### Email server protocol

SMArt Email supports IMAP, POP, and MSAL.

| Mailbox type | Use this protocol |
|---|---|
| Microsoft Exchange Server (on-premises) | IMAP or POP — make sure the protocol is enabled on the server |
| Gmail | IMAP or POP — allow third-party app access |
| Office 365 / Outlook.com (Microsoft-hosted) | MSAL — see [MSAL troubleshooting](./msal-troubleshooting.md) |

### Encryption level (IMAP and POP)

SMArt Email supports SSL 3.0, TLS 1.1, and TLS 1.2 encryption levels.

### Port

Depending on the protocol and encryption level, set the port to 993, 465, 564, or any other acceptable port number.

### OpCon

Before SMArt Email can submit events, configure the following:

- The external event credentials are configured using the command syntax.
- The path to the MSGIN directory is set in the INI file.

## Set or modify credentials

The mailbox and OpCon credentials must be encrypted in the INI file. Run the `--credentials` operation once after install (and any time credentials change).

### Credential syntax

```
smartemail.exe --credentials -user:[value] -password:[value] -opconuser:[value] -opconpassword:[value] (-inifile:[value])
```

### Credential arguments

| Argument | Required | Description |
| -------- | -------- | ----- |
| `-credentials` | Y | Sets the user credentials and encrypts the username and passwords in the configuration file. The utility must run with this parameter once before normal operation. |
| `-user` | N | Used with `--credentials`. Defines the username for the email inbox monitored by SMArt Email. **IMAP** and **POP** only. |
| `-password` | N | Used with `--credentials`. Defines the password for the email inbox. **IMAP** and **POP** only. |
| `-opconuser` | Y | Used with `--credentials`. Defines the OpCon username that runs the events triggered by SMArt Email. |
| `-opconpassword` | Y | Used with `--credentials`. Defines the OpCon password for the user that runs the events. |
| `-inifile` | N | Specifies an alternate configuration file. Default: `SMArtEmail.ini` in `C:\ProgramData\OpConxps\SMArt Email`. |

:::info Note

The root `OpConxps` data folder is hidden by default. To access it, type `C:\ProgramData` directly into the File Explorer address bar.

:::

## INI file reference

All settings in `SMArtEmail.ini` apply to the machine on which the file resides. The sections below describe every configurable setting.

### `[General]` settings

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `MSGINDirectory` | `%PROGRAMDATA%\OpConxps\SAM\MSGIN` | Path to which SMArt Email writes the events. |
| `OpConUser` | Blank | OpCon user with privileges to create Notification Events. |
| `OpConUserPassword` | Blank | External event password for the defined OpConUser. |
| `DeleteEmails` | `Match` | Deletes emails after processing. Values: `Match`, `All`, or `None`. |
| `DownloadEmails` | `True` | If `TRUE`, downloads all processed emails to a specific location. Values: `True` or `False`. |
| `DownloadFolder` | `%PROGRAMDATA%\OpConxps\SMArt Email\Received` | Folder used for processing emails that match a mask. See note below. |
| `ExitCodeForNoMatchingEmails` | `0` | Exit code returned when no matching emails are found. See note below. |
| `UseLastRunDate` | `True` | If `TRUE`, tracks the most recently received email it processes in the `LastRunDate` value after each run. Values: `True` or `False`. |
| `LastRunDate` | Blank | Time stamp of the last email processed. On subsequent runs, only emails received on or after this date are processed. See note below. |

:::info Notes

**`DownloadFolder`** — As a best practice, periodically clean up this folder using the SMADirectory utility. For more information, see [SMADirectory](https://help.smatechnologies.com/opcon/core/utilities/Command-line-Utilities/SMADirectory) in the Utilities online help.

**`ExitCodeForNoMatchingEmails`** — Users who already modified their job failure criteria to allow exit code 3 do not need to modify their jobs. Users who want the job to fail when no matching emails are found can set this to a non-zero value. New users and users upgrading SMArt Email do not need to modify the INI configuration file and can choose the default during install.

**`LastRunDate`** — To re-process emails from a specific date range, override this value with a valid date and time, for example: `1/1/2017 12:30:00 AM`.

:::

### `[Mail]` settings

Set these values using the `--credentials` program switch since the utility requires the username and password to be encrypted.

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `User` | Blank | Used with `--credentials`. Username for the email inbox monitored by SMArt Email. |
| `Password` | Blank | Used with `--credentials`. Password for the email inbox. |
| `Server` | Blank | Name of the email server to connect to. See note below. |
| `EmailProtocol` | Blank | Values: `IMAP`, `POP`, or `MSAL`. |
| `SecurityProtocol` | Blank | Values: `SSL`, `SSL2`, `TLS1`, `TLS1_1`, or `TLS1_2`. See note below. |
| `Port` | Blank | Port number. The required port depends on `EmailProtocol` and `SecurityProtocol`. |
| `SelfSignedCertificate` | `True` | If `TRUE`, allows a self-signed certificate to be accepted. Values: `True` or `False`. |

:::info Notes

**`Server`** — If using TLS / SSL security, the value here should match the hostname on the certificate. Otherwise, you may receive a system error warning that the server certificate verification failed and the connection aborted.

**`SecurityProtocol`** — The value specified here serves as a minimum to meet during security protocol negotiation. The utility attempts to negotiate with the highest available protocol above the defined minimum and fails only if it does not connect:

- `SSL` — Try TLS options first, then SSL3 if that does not succeed.
- `SSL2` — Try TLS options first, then SSL3 or SSL2 if that does not succeed.
- `TLS1` — Allow TLS 1.0 or above.
- `TLS1_1` — Allow TLS 1.1 or above.
- `TLS1_2` — Allow only TLS 1.2.

:::

### `[Audit]` settings

The audit settings configure audit log file format and event notifications during processing. This section is optional. Use it to log accepted or rejected emails, send notification emails, and generate events on rejection.

#### Acceptance logging and notification

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `AuditAcceptLogFile` | `Audit.log` | Log file to which accepted emails are logged. |
| `LogAccepts` | `False` | Whether to log accepted emails to the file. Values: `True` or `False`. |
| `LogAcceptFormat` | `SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]` | Format of the log message for an accepted email. |
| `NotifyOnAccepts` | `False` | Whether to send an email back to the initiator when a configured `SubjectLineMask` matches the subject line. Values: `True` or `False`. |
| `NotifyOnAcceptSubject` | `SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]` | Subject of the notification email sent on acceptance. |
| `NotifyOnAcceptBody` | `Email message was accepted and generated event: [[EVENT]]` | Body of the notification email sent on acceptance. |
| `NotifyOnAcceptTo` | `[[SENDER]]` | Recipient list for accepted email notifications. For multiple users, use a semicolon-separated list. |

#### Rejection logging and notification

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `AuditRejectLogFile` | `Audit.log` | Log file to which rejected emails are logged. |
| `LogRejects` | `False` | Whether to log rejected emails to the file. Values: `True` or `False`. See note below. |
| `LogRejectFormat` | `SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]` | Format of the log message for a rejected email. |
| `NotifyOnRejects` | `False` | Whether to send an email back to the initiator when no `SubjectLineMask` matches. Values: `True` or `False`. |
| `NotifyOnRejectSubject` | `SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]` | Subject of the notification email sent on rejection. |
| `NotifyOnRejectBody` | `Email message sent on [[DATE]] was rejected for reason: [[REASON]]` | Body of the notification email sent on rejection. |
| `NotifyOnRejectTo` | `[[SENDER]]` | Recipient list for rejected email notifications. For multiple users, use a semicolon-separated list. |

:::info Note

**`LogRejects`** — An email is considered rejected in two scenarios:

- The subject of the email matched one of the configurations, but for some other reason it did not generate a match. For example, the sender was not in the allowed list, the time was not in the allowed time frame, or there were no matching attachments when `ProcessAttachments` is true.
- The email matched zero configurations.

:::

#### Rejection reason messages

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `RejectTimeframeMessage` | `Message received outside timeframe` | Message used in the `[[REASON]]` token if an email is rejected due to its time frame. |
| `RejectSenderMessage` | `Invalid Sender` | Message used in the `[[REASON]]` token if the sender is not in `AllowedSenders`. |
| `RejectNoMatchingSubjectMessage` | `No matching subjects found` | Message used in the `[[REASON]]` token if the email does not match any existing configurations. |
| `RejectNoAttachmentMessage` | `No matching attachments found` | Message used in the `[[REASON]]` token if the email does not have any attachments or any matching attachment, yet `ProcessAttachments` is `TRUE` and the subject of the email matches. |

#### Generated rejection event and event logging

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `RejectEvent` | N/A | Generates an event when an email is rejected. Add the event along with tokens from the email. If no value is present, no events are generated on rejects. |
| `LogOpConEvents` | N/A | Whether the events generated by email should be logged. Values: `True` or `False`. |
| `OpConEventLogFile` | N/A | File name and path of the file to log events. |
| `LogOpConEventFormat` | `Generated Event: [[Event]]` | Format of the logged events. |

### `[Tokens]` settings

The tokens settings define custom tokens that can be used throughout the `[Configuration#]` sections to avoid duplication.

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `Name` | N/A | Sets the value of your custom token so it can be referenced in the configurations. |

### `[Configuration#]` settings

The Configuration section of the file contains one section for each mask and event(s) to send. Name each section with the syntax `[Configuration#]`.

| Setting | Description |
| ------- | ----------- |
| `SubjectLineMask` | The mask to search for on the subject line, written as a regular expression. |
| `Event`, `Event2`, `Event3`, ... | One or more valid OpCon external event specifications. See note below. |
| `AllowedSenders` | A semicolon-separated list of one or more email addresses. See note below. |
| `AllowedTimeFrame` | A 24-hour clock representation of when an email is allowed for this configuration. See note below. |
| `ProcessBody` | Optional. Whether the body of the email should be processed against `SubjectLineMask`. See note below. |
| `QualifyingSubjectLine` | Optional. Used with `ProcessBody`. Default `""`. See `ProcessBody`. |
| `AttachmentMask` | Optional. The mask to search for an attachment name, written as a regular expression. |
| `ProcessAttachments` | `true` / `false`. Use when you want to generate events based on `AttachmentMask`. |

:::info Notes

**`Event` / `Event2` / `Event3`** — SMArt Email supports a special token named `[[SENDER]]`. If you place this token in your event, SMArt Email resolves it to the name of the sending address that caused the event to send. Do not include the external user event credentials at the end of the event; the utility automatically appends the credentials entered during initial credential setup.

**`AllowedSenders`** —

- Only senders included in this list cause this event to be processed.
- Use `ALL` to apply no filtering.
- Rejected emails are logged to `SMArtEmailRejections.log`.
- To allow all users for a specific domain, use `any` as the email address. For example, `any@yourorg.com`.

**`AllowedTimeFrame`** —

- The first four digits represent the hours and minutes of the beginning time.
- The last four digits represent the hours and minutes of the ending time.
- For example, `0000-2359` represents a time-frame of 12:00 AM through 11:59 PM.
- Rejected emails are logged to `SMArtEMailRejections.log`.

**`ProcessBody`** —

- Supported for both Plain Text and HTML-formatted emails.
- Default (if not specified) is `NO`. A value of `NO` forces SMArt Email to operate in "classic" mode — only the subject line is analyzed.
- This directive specifies that the body of the email should be included in the lines processed. It works with `QualifyingSubjectLine` to determine exactly how the email is handled. If `ProcessBody` is `YES`:
  - If `QualifyingSubjectLine` is empty, each line in the body is evaluated against `SubjectLineMask` in addition to comparing the subject line. Matches to the subject or body lines can generate events.
  - If `QualifyingSubjectLine` contains a value, the subject line is compared to it. If the subject line matches `QualifyingSubjectLine`, each line in the body is then compared against `SubjectLineMask`. Testing the subject against `QualifyingSubjectLine` does NOT generate events; it only filters out unwanted matches between email bodies and mask lines.

:::

## Token reference

SMArt Email substitutes the following tokens at runtime. Use them in `Event`, audit-log formats, notification subjects/bodies, and recipient fields.

| Token | Resolves to | Where it can be used |
|---|---|---|
| `[[SENDER]]` | Sending address of the matched email | `Event`, `Notify*To`, `LogAcceptFormat`, `LogRejectFormat` |
| `[[SUBJECT]]` | Subject line of the matched email | Audit log and notification formats (default values demonstrate this) |
| `[[EVENT]]` | The event string SMArt Email submitted | `NotifyOnAcceptBody`, `LogOpConEventFormat` |
| `[[DATE]]` | Date the email was sent | `NotifyOnRejectBody` |
| `[[REASON]]` | Reason an email was rejected, taken from the `Reject*Message` settings | `NotifyOnRejectBody` |
| `[[1]]`, `[[2]]`, … | Capture groups from `SubjectLineMask` (and other regex masks) | `Event` (see [Regular expressions in masks](#regular-expressions-in-masks)) |
| `[[CustomName]]` | The value defined under `[Tokens]` as `CustomName=value` | `Event`, `AllowedSenders`, and any other `[Configuration#]` value |

## Configuration examples

This section shows the three most common patterns: matching the subject only, adding attachment-name matching, and adding body matching.

The three examples share an identical `[General]`, `[Mail]`, `[Audit]`, and `[Tokens]` block. The only thing that varies is the `[Configuration1]` block. The full annotated INI is shown below for Example 1, and Examples 2 and 3 show only the `[Configuration1]` change. Full INIs for Examples 2 and 3 are available in collapsible blocks if you want them.

### Example 1: Match by subject

This configuration processes incoming emails from all senders all day. SMArt Email tries to match the email **subject** against `Test`. If a match is found, an external event is submitted to OpCon.

```ini
[General]
MSGINDirectory=%PROGRAMDATA%\OpConxps\SAM\MSGIN
OpConUser=
OpConUserPassword=
DeleteEmails=MATCH
DownloadEmails=TRUE
DownloadFolder=%PROGRAMDATA%\OpConxps\SMArt Email\Received

[Mail]
User=
Password=
Server=
EmailProtocol=
SecurityProtocol=
Port=

[Audit]
AuditAcceptLogFile=Audit.log
LogAccepts=FALSE
LogAcceptFormat=SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnAccepts=FALSE
NotifyOnAcceptSubject=SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnAcceptBody=Email message was accepted and generated event: [[EVENT]]
NotifyOnAcceptTo=[[SENDER]]

AuditRejectLogFile=Audit.log
LogRejects=FALSE
LogRejectFormat=SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnRejects=FALSE
NotifyOnRejectSubject=SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnRejectBody=Email message sent on [[DATE]] was rejected for reason: [[REASON]]
NotifyOnRejectTo=[[SENDER]]
RejectTimeframeMessage=Message received outside timeframe
RejectSenderMessage=Invalid Sender
RejectNoMatchingSubjectMessage=No matching subjects found
RejectNoAttachmentMessage=No matching attachments found
RejectEvent=N\A
LogOpConEvents=N\A
OpConEventLogFile=N\A
LogOpConEventFormat=Generated Event: [[Event]]

[Tokens]
ADMINEMAIL=admin@anycompany.com

#---------------------------------
# Configuration events definitions
#---------------------------------

[Configuration1]
SubjectLineMask=Test
Event=$CONSOLE:DISPLAY,the test passed
AllowedSenders=[[ADMINEMAIL]]
AllowedTimeFrame=0000-2400
```

### Example 2: Match by attachment

This configuration processes incoming emails from all senders all day. SMArt Email tries to match the email **subject** against `Test` AND the name of any **attachment** against `SMArtEmail Test`. If both conditions are met, the program downloads the email and submits an external event to OpCon.

The `[General]`, `[Mail]`, `[Audit]`, and `[Tokens]` blocks are identical to Example 1, except that `[General]` uses `DeleteEmails=NONE` and includes the optional `SendRejectionNotice=FALSE` and `SendAcceptedNotice=FALSE` lines. The `[Configuration1]` block adds two lines:

```ini
[Configuration1]
SubjectLineMask=Test
Event=$CONSOLE:DISPLAY,the test passed
AllowedSenders=[[ADMINEMAIL]]
AllowedTimeFrame=0000-2400
AttachmentMask=SMArtEmail Test
ProcessAttachments=True
```

<details>
<summary>Show the full INI for Example 2</summary>

```ini
[General]
MSGINDirectory=%PROGRAMDATA%\OpConxps\SAM\MSGIN
OpConUser=
OpConUserPassword=
DeleteEmails=NONE
SendRejectionNotice=FALSE
SendAcceptedNotice=FALSE
DownloadEmails=TRUE
DownloadFolder=%PROGRAMDATA%\OpConxps\SMArt Email\Received

[Mail]
User=
Password=
Server=
EmailProtocol=
SecurityProtocol=
Port=

[Audit]
AuditAcceptLogFile=Audit.log
LogAccepts=FALSE
LogAcceptFormat=SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnAccepts=FALSE
NotifyOnAcceptSubject=SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnAcceptBody=Email message was accepted and generated event: [[EVENT]]
NotifyOnAcceptTo=[[SENDER]]

AuditRejectLogFile=Audit.log
LogRejects=FALSE
LogRejectFormat=SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnRejects=FALSE
NotifyOnRejectSubject=SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnRejectBody=Email message sent on [[DATE]] was rejected for reason: [[REASON]]
NotifyOnRejectTo=[[SENDER]]
RejectTimeframeMessage=Message received outside timeframe
RejectSenderMessage=Invalid Sender
RejectNoMatchingSubjectMessage=No matching subjects found
RejectNoAttachmentMessage=No matching attachments found
RejectEvent=N/A
LogOpConEvents=N/A
OpConEventLogFile=N/A
LogOpConEventFormat=Generated Event: [[Event]]

[Tokens]
ADMINEMAIL=admin@anycompany.com

#---------------------------------
# Configuration events definitions
#---------------------------------

[Configuration1]
SubjectLineMask=Test
Event=$CONSOLE:DISPLAY,the test passed
AllowedSenders=[[ADMINEMAIL]]
AllowedTimeFrame=0000-2400
AttachmentMask=SMArtEmail Test
ProcessAttachments=True
```

</details>

### Example 3: Match by body

This configuration processes incoming emails from all senders all day. SMArt Email tries to match the email **subject** against `Test`. It also tries to match each line in the email **body** against `Test`. If both conditions are met, an external event is submitted to OpCon.

The `[General]`, `[Mail]`, `[Audit]`, and `[Tokens]` blocks are the same as Example 2 (with `DeleteEmails=MATCH` instead of `NONE`). The `[Configuration1]` block adds one line:

```ini
[Configuration1]
SubjectLineMask=Test
Event=$CONSOLE:DISPLAY,the test passed
AllowedSenders=[[ADMINEMAIL]]
AllowedTimeFrame=0000-2400
ProcessBody=Yes
```

<details>
<summary>Show the full INI for Example 3</summary>

```ini
[General]
MSGINDirectory=%PROGRAMDATA%\OpConxps\SAM\MSGIN
OpConUser=
OpConUserPassword=
DeleteEmails=MATCH
SendRejectionNotice=FALSE
SendAcceptedNotice=FALSE
DownloadEmails=TRUE
DownloadFolder=%PROGRAMDATA%\OpConxps\SMArt Email\Received

[Mail]
User=
Password=
Server=
EmailProtocol=
SecurityProtocol=
Port=

[Audit]
AuditAcceptLogFile=Audit.log
LogAccepts=FALSE
LogAcceptFormat=SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnAccepts=FALSE
NotifyOnAcceptSubject=SMArt Email - Message Accepted - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnAcceptBody=Email message was accepted and generated event: [[EVENT]]
NotifyOnAcceptTo=[[SENDER]]

AuditRejectLogFile=Audit.log
LogRejects=FALSE
LogRejectFormat=SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnRejects=FALSE
NotifyOnRejectSubject=SMArt Email - Message Rejected - From: [[SENDER]] Subject: [[SUBJECT]]
NotifyOnRejectBody=Email message sent on [[DATE]] was rejected for reason: [[REASON]]
NotifyOnRejectTo=[[SENDER]]
RejectTimeframeMessage=Message received outside timeframe
RejectSenderMessage=Invalid Sender
RejectNoMatchingSubjectMessage=No matching subjects found
RejectNoAttachmentMessage=No matching attachments found
RejectEvent=N/A
LogOpConEvents=N/A
OpConEventLogFile=N/A
LogOpConEventFormat=Generated Event: [[Event]]

[Tokens]
ADMINEMAIL=admin@anycompany.com

#---------------------------------
# Configuration events definitions
#---------------------------------

[Configuration1]
SubjectLineMask=Test
Event=$CONSOLE:DISPLAY,the test passed
AllowedSenders=[[ADMINEMAIL]]
AllowedTimeFrame=0000-2400
ProcessBody=Yes
```

</details>

## Regular expressions in masks

SMArt Email supports "masks" — regular expressions in `SubjectLineMask` and `AttachmentMask` — that capture parts of the subject or attachment name and substitute them into the event string.

To get started, note that:

- An expression in parentheses is captured for use in the event and replaces `[[1]]`. The second expression in parentheses replaces `[[2]]`, and so on.
- Expressions in parentheses can match numbers or strings. For example:
  - `\S+` matches one whitespace-delimited word.
  - `[0-9]+` captures a number with one or more digits.

:::tip Example

If the mask is:

```
OpCon/xps: (\S+) total = ([0-9]+\.[0-9]+)
```

If an email is received with a subject line that looks like:

```
OpCon/xps: ACHBalanceTotal total = 149926.22
```

The first captured argument is `(\S+)` or `"ACHBalanceTotal"`. The second captured argument is `([0-9]+\.[0-9]+)` or `"149926.22"`.

If the event to generate is defined as:

```
$TOKEN:SET,[[1]],[[2]]
```

The event sent to OpCon looks like:

```
$TOKEN:SET,ACHBalanceTotal,149926.22
```

:::

## Common pitfalls

:::caution Watch out

- **Port and encryption mismatch.** The `Port` value must be compatible with `SecurityProtocol`. Otherwise the connection is not established. See the `--ssl`, `--tls1_1`, and `--tls1_2` arguments in [Operation](./operation.md).
- **TLS / SSL hostname mismatch.** When using TLS or SSL, set `Server` to the hostname on the certificate. A mismatch produces a server certificate verification failure and the connection aborts.
- **Wrong protocol for Microsoft-hosted mailboxes.** Office 365 / Outlook.com mailboxes require MSAL — IMAP and POP do not work. On-premises Exchange continues to use IMAP or POP.
- **Manually editing MSAL or `[Mail]` values when using MSAL.** When using MSAL, do not modify any INI values for the MSAL or `[Mail]` sections — only the `User` field is used.
- **Including credentials in the `Event` value.** Do not include the external user event credentials at the end of the event. SMArt Email automatically appends the credentials that were entered during the initial `--credentials` setup.
- **Skipping the `--credentials` step.** Mailbox and OpCon passwords must be encrypted in the INI file before SMArt Email can use them. Run `--credentials` once after install.
- **Hidden `OpConxps` folder.** The default INI lives in `C:\ProgramData\OpConxps\SMArt Email`. The `OpConxps` folder is hidden by default — type `C:\ProgramData` directly into the File Explorer address bar.

:::

## Job output

All information produced by the job is available in the job output and can be retrieved using the View Job Output feature. For more information, refer to Viewing Job Output in the Enterprise Manager online help.

## FAQs

**Where is `SMArtEmail.ini` stored by default?**
In `C:\ProgramData\OpConxps\SMArt Email`. The `OpConxps` folder is hidden by default in File Explorer; type `C:\ProgramData` directly into the address bar to navigate to it.

**Can I use a different INI file?**
Yes. Use the `-inifile:[value]` argument with `--credentials` to specify an alternate configuration file.

**Why must I run `--credentials` before normal operation?**
The username and password values must be encrypted in the INI file before SMArt Email can use them. The first run with `--credentials` performs that encryption.

**Can I trigger multiple events from a single email?**
Yes. SMArt Email supports defining `Event`, `Event2`, `Event3`, and so on under a `[Configuration#]` section.

**Do I need MSAL or can I use IMAP / POP for Office 365?**
For Microsoft-hosted Office 365 / Outlook.com mailboxes, MSAL is the supported option. Private Exchange servers can continue to use IMAP or POP.
