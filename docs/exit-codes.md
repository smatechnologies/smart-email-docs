---
title: SMArt Email exit codes
description: "Reference of exit codes returned by the SMArt Email utility and how to interpret them in OpCon job failure criteria."
tags:
  - Reference
  - System Administrator
  - Smart Email
---

# Exit codes

## What is it?

This page lists the exit codes returned by the SMArt Email utility (`smartemail.exe`) so you can configure OpCon job failure criteria correctly.

Use this reference when you:

- Configure job failure criteria in OpCon for jobs that run SMArt Email.
- Diagnose why a SMArt Email job ended with an unexpected status.

## Exit code reference

| Exit code | Default | Meaning | Action |
| --------- | ------- | ------- | ------ |
| `0` | N/A | OK. Some emails were found that matched. | None. |
| `1` | N/A | Command-line arguments error. | Verify the syntax. See [Operation](./operation.md). |
| `2` | N/A | General error. | Review the job output and logs for details. |
| User-defined | `0` | No matching emails were found. | See the note below. |

:::info Note — User-defined exit code for "no matches"

The "no matches" exit code is set by `ExitCodeForNoMatchingEmails` in `SMArtEmail.ini` (or chosen during install).

- **Existing users** who already modified their job failure criteria to allow exit code 3 do not need to modify their jobs.
- **Users who want the job to fail** when no matching emails are found can set this to a non-zero value.
- **New users and users upgrading** SMArt Email do not need to modify the INI configuration file; the default during install is acceptable.

:::

## FAQs

**What does the user-defined exit code default to?**
It defaults to `0`, so SMArt Email runs that find no matching emails do not fail unless you explicitly set a non-zero value during install or in the configuration file.

**How do I make a SMArt Email job fail when no emails match?**
Set the `ExitCodeForNoMatchingEmails` configuration value (in the INI file or during install) to a non-zero value, then update the OpCon job failure criteria to treat that exit code as a failure.

**Why does exit code 1 occur?**
A command-line argument error: SMArt Email was started with an invalid or incomplete set of arguments. Verify the syntax described in [Operation](./operation.md).
