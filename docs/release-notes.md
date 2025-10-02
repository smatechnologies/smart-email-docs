# Release Notes

## Version 20.1.1 Fixes 

### 2025 October

:white_check_mark: Fixed issue where inline attachments were skipped

## Version 20.1 Fixes

### 2025 April

:white_check_mark: Fixed an issue with MSAL mode using incorrect locale name for "Inbox".

:white_check_mark: Fixed an issue with timezone overflow in MSAL mode on timezones east of GMT.

## Version 20.0 New Features

### 2022 September

:eight_spoked_asterisk: SMArt Email now supports MSAL for Office365/Outlook.com
* See the updated instructions in the installation, configuration, and troubleshooting sections.

## Version 17.0 New Features

### 2017 May
:eight_spoked_asterisk:	SMArt Email configuration file has been updated with two new settings for processing emails:
* UseLastRunDate - Specifies whether or not to track the most recently received email the program processed during each run.
* LastRunDate - Specifies the time/date of the last email the program processed and process only emails received on or after this date on subsequent runs.

:eight_spoked_asterisk: SMArt Email configuration file has been updated with a new setting for allowing self-signed certificates to be validated.

:eight_spoked_asterisk: Modified the security protocol behavior to attempt to negotiate with the highest available protocol above the defined minimum and fail only if it does not connect.

:eight_spoked_asterisk: SMArt Email has been updated to allow a single email to trigger multiple events.
 
## Version 16.2 New Features

### 2016 December

:eight_spoked_asterisk: SMArt Email now allows a user-defined exit code if no emails or no email matches are found for the matching text.
 
## Version 16.1 New Features

### 2016 September

:eight_spoked_asterisk: SMArt Email configuration file has been updated with the following two new sections:
* [Tokens] - This section is used to defining custom tokens that can be used throughout the [Configuration] sections to avoid duplicating information.
* [Audit] - This section is used to configure audit log file format and event notifications during processing.
 
## Version 18.3.0 Fixes

### 2018 November

:white_check_mark: Updated the ipWorks Library to fix an issue with certain encodings such as CP-1252.
 
## Version 18.2.0 Fixes

### 2018 September

:white_check_mark: Fixed an issue in SMArt Email where a certain encoding was not recognized and gave an error.

:white_check_mark: Fixed an issue in SMArt Email where a certain date encoding was not recognized and gave an error.
 
## Version 17.1 Fixes

### 2017 December

:white_check_mark: SMArt Email behaves in the following manner in this release:
* Saves temporary downloaded email attachments in an application temp folder rather than in a system temp folder.
* Avoids downloading email attachments if they are not required by any settings in the configuration file.

:white_check_mark: Fixed an issue in SMArt Email where the date formats were not recognized for certain email servers.

:white_check_mark: Fixed an issue in SMArt Email where a POP3 email server configured with a lower version of TLS could not be connected in some cases.

:white_check_mark: Fixed an issue in SMArt Email where the AllowedSenders field would not accept tokens. Not only can tokens now be used for this parameter, but a semicolon-separated list of email addresses is also permitted in the token.

:white_check_mark: Updated the SMArt Email installer so that it now preserves the Tokens settings when upgrading.

:white_check_mark: Fixed an issue in SMArt Email where it would not submit events when performing multi-line pattern matching.

:white_check_mark: Fixed an issue where invalid regular expressions were not handled correctly.

:white_check_mark: Fixed an issue in SMArt Email where it could not handle the forward slash (/) character in file names sent over email.

:white_check_mark: Fixed an issue in SMArt Email where it could not download attachments for HTML emails.

:white_check_mark: Fixed a SMArt Email installer issue, where sometimes the configuration file would remove certain settings during an upgrade.
 
## Version 17.0.2 Fixes

### 2017 July

:white_check_mark: Fixed an issue in SMArt Email where the date formats were not recognized for certain email servers.

:white_check_mark: Fixed an issue in SMArt Email where a POP3 email server configured with a lower version of TLS could not be connected in some cases.

:white_check_mark: Fixed an issue in SMArt Email where the AllowedSenders field would not accept tokens. Not only can tokens now be used for this parameter, but a semicolon-separated list of email addresses is also permitted in the token.
 
## Version 17.0 Fixes

### 2017 May

:white_check_mark: Fixed an issue where the body of some IMAP or POP emails was not being returned properly. When ProcessBody=TRUE was set in the INI, emails with matching lines may not have been processed.
 
## Version 16.1 Fixes

### 2016 September

:white_check_mark: Fixed an issue where dates within the emails being processed were not parsed correctly.

:white_check_mark: Fixed an issue where, under certain conditions, email addresses were not accepted when any@[domain] was used in the AllowedSenders list and the sender's email was in the specified domain.
