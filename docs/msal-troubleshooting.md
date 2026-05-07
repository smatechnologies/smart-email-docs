---
title: MSAL troubleshooting
description: "Resolve common MSAL setup, permission, and token issues for the SMArt Email utility."
tags:
  - Procedural
  - System Administrator
  - Smart Email
---

# MSAL troubleshooting

## What is it?

MSAL troubleshooting covers recovery procedures for the SMArt Email utility when MSAL authentication fails or token state becomes invalid.

The MSAL option only works for email servers hosted by Microsoft. A private Exchange server still needs to use IMAP or POP. If a setup mistake produces permission errors, you may need to reset and retry the setup using the procedures on this page.

Use this page when you:

- Receive permission errors that prevent SMArt Email from connecting to a Microsoft-hosted mailbox.
- Need to revoke or reissue administrator consent for the SMArt Email Enterprise Application.
- Need to keep an MSAL token alive when SMArt Email runs infrequently.

## Common scenarios

| Symptom | Resolution |
|---|---|
| Permission errors after a botched MSAL setup | [Remove SMArt Email from your Enterprise Applications](#remove-smartemail-from-your-enterprise-applications), then reinstall and re-consent. |
| Stale or corrupted token cache | [Clear the MSAL token cache](#clear-the-msal-token-cache). |
| Token expired because SMArt Email rarely runs | [Schedule a silent token refresh](#if-smartemail-is-not-used-frequently). |

## Remove SMArtEmail from your Enterprise Applications

To remove SMArtEmail from your Azure Enterprise Applications, complete the following steps:

1. Go to [portal.azure.com](https://portal.azure.com).
2. Go to **Enterprise Applications** and select **SMArtEmail**.
3. Go to the **Properties** blade.
4. Select **Delete** and remove SMArtEmail.

## Clear the MSAL token cache

To clear the MSAL token cache, complete the following steps:

1. Go to your `AppData\Local` folder (for example, `C:\Users\myUser\AppData\Local\`).
2. Delete the file `msal.smatechnologies.cache`.

## If SMArtEmail is not used frequently

The token SMArtEmail acquires is renewable for up to a week, but the expiration resets every time SMArtEmail connects to your account. If SMArt Email does not run frequently, schedule an OpCon job that runs:

```
SMArtEmail.exe --msal --renewMsalToken --renewTokenSilent
```

Running this command keeps the refresh token alive and prevents you from needing to go through interactive authorization again.

## FAQs

**Does the MSAL option work for an on-premises Exchange server?**
No. The MSAL option only works for Microsoft-hosted mailboxes. For on-premises Exchange servers, use IMAP or POP.

**How long does the MSAL refresh token remain valid?**
The token is renewable for up to a week. Each successful connection from SMArtEmail resets the expiration window.

**What happens if SMArt Email does not connect within the refresh window?**
You need to go through interactive authorization again to acquire a new token. Scheduling `SMArtEmail.exe --msal --renewMsalToken --renewTokenSilent` in OpCon prevents this.

**Where is the MSAL token cache stored?**
The cache file `msal.smatechnologies.cache` is stored in `%LOCALAPPDATA%` (for example, `C:\Users\myUser\AppData\Local\`).

## Glossary

- **MSAL** — Microsoft Authentication Library. Used by SMArt Email to authenticate against Microsoft-hosted Office 365 / Outlook.com mailboxes.
- **Enterprise Application** — A registered application object in Azure Active Directory used to grant tenant-wide consent and permissions.
- **Refresh token** — A long-lived token used to obtain new access tokens without requiring the user to authenticate interactively each time.
- **Interactive authorization** — A sign-in flow where the user signs in through a web browser to grant consent and obtain a fresh token.
