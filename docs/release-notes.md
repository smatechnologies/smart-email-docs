---
sidebar_label: 'Release notes'
title: SMArt Email release notes
description: "Version history and change details for the SMArt Email utility, including new features, improvements, and bug fixes."
tags:
  - Reference
  - System Administrator
  - Smart Email
---

# SMArt Email release notes

## 26

### 26.0.0

2026 January

#### Fixes

:white_check_mark: Fixed issue where email messages from Exchange Online were not being read correctly.

:white_check_mark: Fixed application crashes that occurred when processing emails without a subject line from Exchange Online.

## 20

### 20.1.1

2025 October

#### Fixes

:white_check_mark: Fixed issue where inline attachments were skipped.

### 20.1

2025 April

#### Fixes

:white_check_mark: Fixed an issue with MSAL mode using incorrect locale name for "Inbox".

:white_check_mark: Fixed an issue with timezone overflow in MSAL mode on timezones east of GMT.

### 20.0

2022 September

#### What's new

:eight_spoked_asterisk: SMArt Email now supports MSAL for Office365/Outlook.com.

- See the updated instructions in the installation, configuration, and troubleshooting sections.

## 18

### 18.3.0

2018 November

#### Fixes

:white_check_mark: Updated the ipWorks library to fix an issue with certain encodings such as CP-1252.

### 18.2.0

2018 September

#### Fixes

:white_check_mark: Fixed an issue in SMArt Email where a certain encoding was not recognized and gave an error.

:white_check_mark: Fixed an issue in SMArt Email where a certain date encoding was not recognized and gave an error.

## 17

### 17.1

2017 December

#### Fixes

:white_check_mark: SMArt Email behaves in the following manner in this release:

- Saves temporary downloaded email attachments in an application temp folder rather than in a system temp folder.
- Avoids downloading email attachments if they are not required by any settings in the configuration file.

:white_check_mark: Fixed an issue in SMArt Email where the date formats were not recognized for certain email servers.

:white_check_mark: Fixed an issue in SMArt Email where a POP3 email server configured with a lower version of TLS could not be connected in some cases.

:white_check_mark: Fixed an issue in SMArt Email where the AllowedSenders field would not accept tokens. Not only can tokens now be used for this parameter, but a semicolon-separated list of email addresses is also permitted in the token.

:white_check_mark: Updated the SMArt Email installer so that it now preserves the Tokens settings when upgrading.

:white_check_mark: Fixed an issue in SMArt Email where it would not submit events when performing multi-line pattern matching.

:white_check_mark: Fixed an issue where invalid regular expressions were not handled correctly.

:white_check_mark: Fixed an issue in SMArt Email where it could not handle the forward slash (/) character in file names sent over email.

:white_check_mark: Fixed an issue in SMArt Email where it could not download attachments for HTML emails.

:white_check_mark: Fixed a SMArt Email installer issue, where sometimes the configuration file would remove certain settings during an upgrade.

### 17.0.2

2017 July

#### Fixes

:white_check_mark: Fixed an issue in SMArt Email where the date formats were not recognized for certain email servers.

:white_check_mark: Fixed an issue in SMArt Email where a POP3 email server configured with a lower version of TLS could not be connected in some cases.

:white_check_mark: Fixed an issue in SMArt Email where the AllowedSenders field would not accept tokens. Not only can tokens now be used for this parameter, but a semicolon-separated list of email addresses is also permitted in the token.

### 17.0

2017 May

#### What's new

:eight_spoked_asterisk: SMArt Email configuration file has been updated with two new settings for processing emails:

- UseLastRunDate — Specifies whether or not to track the most recently received email the program processed during each run.
- LastRunDate — Specifies the time/date of the last email the program processed and process only emails received on or after this date on subsequent runs.

:eight_spoked_asterisk: SMArt Email configuration file has been updated with a new setting for allowing self-signed certificates to be validated.

:eight_spoked_asterisk: Modified the security protocol behavior to attempt to negotiate with the highest available protocol above the defined minimum and fail only if it does not connect.

:eight_spoked_asterisk: SMArt Email has been updated to allow a single email to trigger multiple events.

#### Fixes

:white_check_mark: Fixed an issue where the body of some IMAP or POP emails was not being returned properly. When ProcessBody=TRUE was set in the INI, emails with matching lines may not have been processed.

## 16

### 16.2

2016 December

#### What's new

:eight_spoked_asterisk: SMArt Email now allows a user-defined exit code if no emails or no email matches are found for the matching text.

### 16.1

2016 September

#### What's new

:eight_spoked_asterisk: SMArt Email configuration file has been updated with the following two new sections:

- [Tokens] — This section is used to define custom tokens that can be used throughout the [Configuration] sections to avoid duplicating information.
- [Audit] — This section is used to configure audit log file format and event notifications during processing.

#### Fixes

:white_check_mark: Fixed an issue where dates within the emails being processed were not parsed correctly.

:white_check_mark: Fixed an issue where, under certain conditions, email addresses were not accepted when any@[domain] was used in the AllowedSenders list and the sender's email was in the specified domain.
