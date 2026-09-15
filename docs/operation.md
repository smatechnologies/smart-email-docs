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

- The `--credentials` operation has been run once to encrypt the mailbox and OpCon credentials in the INI file. See [Set the credentials](#set-the-credentials).
- The `[Mail]`, `[General]`, and `[Configuration#]` sections of `SMArtEmail.ini` are populated.

For detailed setup, see [Configuration](./configuration.md).

## Operation syntax

SMArt Email runs in one of three modes.

Normal run:

```
smartemail.exe (-inifile:[value]) --[pop|imap|msal] (-server:[value]) (-port:[value]) (--[ssl2|ssl|tls1|tls1_1|tls1_2]) (--[delete|deleteall]) (--sort)
```

Set credentials — see [Set or modify credentials](./configuration.md#set-or-modify-credentials):

```
smartemail.exe --credentials -user:[value] -password:[value] -opconuser:[value] -opconpassword:[value] (-inifile:[value])
```

Renew an MSAL token:

```
smartemail.exe --msal --renewMsalToken (--[renewTokenSilent|doNotSaveAccount]) (-inifile:[value])
```

:::info Note

`( )` indicates an optional parameter. `[|]` indicates mutually exclusive options.

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

The SMArt Email (`smartemail.exe`) utility supports the following arguments, grouped by purpose.

### Protocol

Specify exactly one protocol, either as an argument or as `EmailProtocol` in the configuration file. Supplying both `--pop` and `--imap` ends the run with exit code `1`.

| Argument | Description |
| -------- | ----------- |
| `--imap` | Use IMAP as the email protocol. |
| `--pop` | Use POP as the email protocol. |
| `--msal` | Use MSAL as the email protocol. Microsoft-hosted mailboxes only. |

:::info Note

For IMAP, SMArt Email monitors the **Inbox**. Other mailbox folders are not monitored.

:::

### Connection

These arguments do not apply to `--msal`, which resolves the mailbox from the stored token.

| Argument | Required? | Description |
| -------- | --------- | ----------- |
| `-server` | Y | Email server IP address or hostname. Can be set as `Server` in the configuration file instead. |
| `-port` | Y | Port number for the email server. Can be set as `Port` in the configuration file instead. See note below. |
| `-inifile` | N | Full path to the configuration file to read. Defaults to `SMArtEmail.ini` in the SMArt Email program data directory. |

:::caution

There is no default port. Supply `-port` on the command line or set `Port` in the configuration file. Conventional values are `143` for IMAP, `993` for IMAP over TLS, `110` for POP, and `995` for POP over TLS, but SMArt Email does not apply any of them for you.

:::

### Encryption

Optional. Each option sets the **minimum** protocol version SMArt Email accepts, not an exact version. The port number must be compatible with the protocol; otherwise, the connection is not established. Can be set as `SecurityProtocol` in the configuration file instead.

| Argument | Accepted protocol versions |
| -------- | -------------------------- |
| `--ssl2` | SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2 |
| `--ssl` | SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2 |
| `--tls1` | TLS 1.0, TLS 1.1, TLS 1.2 |
| `--tls1_1` | TLS 1.1, TLS 1.2 |
| `--tls1_2` | TLS 1.2 only |

If you omit the encryption argument and `SecurityProtocol` is not set, SMArt Email connects without applying a protocol restriction.

### Message handling

Optional.

| Argument | Description |
| -------- | ----------- |
| `--delete` | Delete emails that matched a configuration, after processing. Applies to POP, IMAP, and MSAL. |
| `--deleteall` | Delete every email retrieved, whether or not it matched a configuration. Cannot be combined with `--delete`. |
| `--sort` | IMAP only. Retrieve emails sorted by date. |

:::danger

`--deleteall` removes every email the run retrieves, including emails that matched no configuration. Deletion is permanent and SMArt Email cannot recover the messages. Confirm the mailbox holds nothing you need before scheduling a job that uses this argument.

:::

Supplying both `--delete` and `--deleteall` ends the run with exit code `1`.

### MSAL token

Used with `--renewMsalToken`.

| Argument | Description |
| -------- | ----------- |
| `--renewMsalToken` | Interactively acquire a new token, or assign a different account to be used by SMArt Email. |
| `--renewTokenSilent` | Refresh the token without opening a browser. Requires `Tenant` and `User` to be set in the configuration file already. See [MSAL troubleshooting](./msal-troubleshooting.md). |
| `--doNotSaveAccount` | Prevents storing a token to an account on this machine. Typically used to prevent saving an access token to an Administrator's email. |

### Set the credentials

The `--credentials` operation encrypts the mailbox and OpCon credentials into the configuration file, and must be run once before the first normal run. For the syntax and its arguments, see [Set or modify credentials](./configuration.md#set-or-modify-credentials).

## FAQs

**Can `--delete` be used with POP?**
Yes. `--delete` removes matching emails on POP, IMAP, and MSAL alike. The protocol does not change whether the option takes effect.

**What is the difference between `--delete` and `--deleteall`?**
`--delete` removes only the emails that matched one of the `[Configuration#]` sections. `--deleteall` removes every email the run retrieved, including emails that matched nothing. The two cannot be combined.

**Can `-server` and `-port` be set in the configuration file instead of the command line?**
Yes. Both can be set in the configuration file. Command-line values take precedence at runtime.

**Does `-port` have a default?**
No. Supply it on the command line or set `Port` in the configuration file. SMArt Email does not fall back to a conventional port for the selected protocol.

**Which encryption flag should I use?**
Use the highest TLS option supported by your email server. `--tls1_2` is preferred because it is the only option that does not also permit older protocol versions. The encryption flag must be compatible with the port number for the connection to succeed.

**Which folder does SMArt Email monitor?**
For IMAP, the Inbox. Other folders are not monitored.
