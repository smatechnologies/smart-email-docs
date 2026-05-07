---
title: SMArt Email operation
description: "Run the SMArt Email utility from the command line with the supported protocol, encryption, and MSAL options."
tags:
  - Reference
  - System Administrator
  - Smart Email
---

# Operation

## What is it?

This page describes how to run the SMArt Email utility (`smartemail.exe`) from the command line after the configuration has been updated and the credentials have been set with the `--credentials` operation. It documents the operation syntax and the full set of supported arguments.

Use this page when you:

- Schedule SMArt Email through an OpCon job that runs the executable directly.
- Run SMArt Email manually to verify configuration changes.
- Refresh or replace an MSAL token.

## Prerequisites

Before running SMArt Email in normal operation, make sure that:

- The `--credentials` operation has been run once to encrypt the mailbox and OpCon credentials in the INI file.
- The `[Mail]`, `[General]`, and `[Configuration#]` sections of `SMArtEmail.ini` are populated.

For detailed setup, see [Configuration](./configuration.md).

## Operation syntax

The full syntax string supports two mutually exclusive flows: MSAL and POP / IMAP.

```
smartemail.exe ((--msal (--renewMsalToken (--renewTokenSilent | --doNotSaveAccount))) | ((-server:[value]) (-port:[value]) --[pop|imap] (--[ssl|tls1_1|tls1_2])) (--delete)
```

:::info Note

`[|]` indicates mutually exclusive options.

:::

### POP / IMAP examples

Connect to an IMAP server with TLS 1.2:

```
smartemail.exe -server:imap.example.com -port:993 --imap --tls1_2
```

Connect to a POP server with TLS 1.2 and delete matching emails after processing:

```
smartemail.exe -server:pop.example.com -port:995 --pop --tls1_2 --delete
```

### MSAL examples

Run normally against a Microsoft-hosted mailbox using a previously stored token:

```
smartemail.exe --msal
```

Interactively acquire a new MSAL token:

```
smartemail.exe --msal --renewMsalToken
```

Refresh an existing MSAL token silently (useful when SMArt Email runs infrequently):

```
smartemail.exe --msal --renewMsalToken --renewTokenSilent
```

Acquire a token without storing the account on the local machine:

```
smartemail.exe --msal --renewMsalToken --doNotSaveAccount
```

## Operation arguments

The SMArt Email (`smartemail.exe`) utility supports the following arguments:

| Argument | Required? | Description |
| -------- | --------- | ----- |
| `-server` | Y | Email server IP address or hostname. May also be specified directly in the configuration file. |
| `-port` | Y | Port number for the email server. Defaults to standard ports; can also be specified in the configuration file. |
| `--pop` | Y | Use POP as the email protocol. |
| `--imap` | Y | Use IMAP as the email protocol. |
| `--ssl` | N | Use SSL 3.0 with the selected mail protocol. The port number must be compatible with this encryption level; otherwise, the connection is not established. |
| `--tls1_1` | N | Use TLS 1.1 with the selected mail protocol. The port must be compatible. |
| `--tls1_2` | N | Use TLS 1.2 with the selected mail protocol. The port must be compatible. |
| `--delete` | N | Delete emails after processing. Only works with the IMAP email protocol. |
| `--msal` | Y | Use MSAL as the email protocol (Microsoft-hosted mailboxes only). |
| `--renewMsalToken` | N | Interactively acquire a new token, or assign a different account to be used by SMArt Email. |
| `--renewTokenSilent` | N | Used with `--renewMsalToken`. If the refresh token is still valid (~1 week), refreshes the token to keep it alive longer. |
| `--doNotSaveAccount` | N | Used with `--renewMsalToken`. Prevents storing a token to an account on this machine. Typically used to prevent saving an access token to an Administrator's email. |

## FAQs

**Can `--delete` be used with POP?**
No. The `--delete` option only applies to IMAP. For POP, message removal is handled at the protocol level when the server is configured to do so.

**Can `-server` and `-port` be set in the configuration file instead of the command line?**
Yes. Both can be set in the configuration file. Command-line values take precedence at runtime.

**Which encryption flag should I use?**
Use the highest TLS option supported by your email server (`--tls1_2` is preferred). The encryption flag must be compatible with the port number for the connection to succeed.
