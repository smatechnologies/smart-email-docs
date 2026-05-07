---
title: SMArt Email installation
description: "Install or upgrade the SMArt Email utility for POP, IMAP, or MSAL email protocols, including silent mode."
tags:
  - Procedural
  - System Administrator
  - Installation
---

# Installation

This page covers installing SMArt Email for the first time and upgrading an existing installation. The installer guides you through configuring OpCon event submission and the email server connection. You can also configure these later in the `SMArtEmail.ini` file. See [Configuration](./configuration.md).

## Requirements

Before beginning, make sure the following requirements are met:

- A supported version of Windows with .NET Framework 4.8 installed.
- A supported version of the MSLSAM installed on the machine.

## Choose an installation path

| Mailbox type | Use this procedure |
|---|---|
| IMAP or POP — for example, on-premises Exchange or other mail servers | [Install for POP and IMAP](#install-for-pop-and-imap) |
| MSAL — for Microsoft-hosted Office 365 or Outlook.com mailboxes | [Install for MSAL](#install-for-msal) |

## Install for POP and IMAP

To install SMArt Email for **POP** or **IMAP**, complete the following steps.

### 1. Start the installer

1. Log in to the Windows machine as a Local Administrator.
2. Download the SMArt Email utility installation file from the [files.smatechnologies.com](https://files.smatechnologies.com/) site.
3. Enter your username and password and select **Login**.
4. Go to **Root Folder/SMArt Email**.
5. Open **SMA OpCon SMArt Email Install.exe**. The Select Language screen displays.
6. Select the desired language and select **OK**. The Welcome screen displays.

### 2. Configure paths and OpCon credentials

1. Select **Next**. The Destination Folder screen displays.
2. Choose your Destination Folder and select **Next**. The **Configure SMA MSGIN Directory** screen displays.
3. Choose the path to the SAM's MSGIN directory where external events are submitted, or accept the default path.
4. Select **Next**. The **Configure OpCon User** screen displays.
5. *(Required)* Enter the OpCon user used to submit external events.
6. *(Required)* Enter the password of the OpCon user.
7. Select **Next**. The **Configure Email Server** screen displays.

### 3. Configure the email server

1. Select the protocol used to connect to your email server: **IMAP** or **POP**.
2. Select the encryption level: **SSL**, **TLS1_1**, or **TLS1_2**.
3. Enter the name of the email server.
4. Enter the port number.
5. *(Required)* Enter the user used to connect to the email server.
6. *(Required)* Enter the password used to connect to the email server.

:::info Note

You can configure the External Event authentication and Email Server connection during install, or after install by editing your configuration file. See [Configuration](./configuration.md).

:::

### 4. Complete the installation

1. Select **Next**. The **Setup Type** screen displays.
2. Select the **Complete** option and select **Next**. The **Ready to Install the Program** screen displays.
3. Select **Install**.
4. Wait while the installation completes. This may take a few minutes.
5. Select **Finish** on the **InstallShield Wizard Completed** screen.

## Install for MSAL

The MSAL option works only for Microsoft-hosted mailboxes (Office 365 / Outlook.com). On-premises Exchange servers must use IMAP or POP. To install SMArt Email for **MSAL**, complete the following steps.

### 1. Start the installer

:::info Note

Log in as a domain user that is included as a Batch User in OpCon. This is the user the MSAL token is created for and should be the account that runs the SMArt Email job in OpCon.

:::

1. Log in to the Windows machine as a Local Administrator.
2. Download the SMArt Email utility installation file (version 20 or above) from the [files.smatechnologies.com](https://files.smatechnologies.com/) site.
3. Enter your username and password and select **Login**.
4. Go to **Root Folder/SMArt Email**.
5. Open **SMA OpCon SMArt Email Install.exe**. The Select Language screen displays.
6. Select the desired language and select **OK**. The Welcome screen displays.

### 2. Configure paths and OpCon credentials

1. Select **Next**. The Destination Folder screen displays.
2. Choose your Destination Folder and select **Next**. The **Configure SMA MSGIN Directory** screen displays.
3. Choose the path to the SAM's MSGIN directory where external events are submitted, or accept the default path.
4. Select **Next**. The **Configure OpCon User** screen displays.
5. *(Required)* Enter the OpCon user used to submit external events.
6. *(Required)* Enter the password of the OpCon user.
7. Select **Next**. The **Configure Email Server** screen displays.
8. Select **MSAL**.

### 3. Grant administrator consent

Choose the path that matches your environment.

#### Path A: SMArtEmail email account also has administrator privileges for Outlook

1. Select **Next**.
2. A web browser opens a Microsoft Login page; select your SMArtEmail account.
3. Review the claims requested and select the option to give administrator consent.

#### Path B: An administrator needs to grant access for the SMArtEmail account

1. Select the option labeled **Do Not Associate or Store Account**.
2. A web browser opens a Microsoft Login page; select your SMArtEmail account.
3. Review the claims requested and select the option to give administrator consent.
4. Open a command prompt and go to the SMArtEmail installation directory.
5. Run `SMArtEmail.exe --renewMsalToken`.
6. A web browser opens a Microsoft Login page; select your SMArtEmail account.

## Upgrade installation

To upgrade SMArt Email, install the new package to the same directory as the previous installation. The installer preserves your configuration files automatically.

## Silent mode

To install SMArt Email in silent mode, refer to [Silent Mode](https://help.smatechnologies.com/opcon/core/installation/components#silent-mode) in the **OpCon Installation** online help.
