# MAS AppPoint Estimate for Maximo 7.6

A read-only BIRT report for IBM Maximo 7.6 that estimates what your users will cost in IBM Maximo Application Suite (MAS) AppPoints.

It applies the user-type rules of IBM's Magic Script 2.6.3 to your security groups and login history, and shows:

- each active user's MAS user type (Self service, Limited, Base, Premium) and why,
- the AppPoints if every paying user were logged in at once (authorized basis),
- the three busiest moments in your login history and the AppPoints each one costs (concurrent basis),
- headroom against the AppPoints you own,
- the security groups that make users Premium, so you know where to act first.

Unlike the Magic Script, it needs no database access and creates no tables or indexes: every query is a `SELECT`. A Maximo administrator imports it through Report Administration and runs it like any other report.

## Download

[`mas_apppoints.rptdesign`](mas_apppoints.rptdesign) (one file).

## Install

1. In **Report Administration**, click **New Report** and enter:
   - Report File Name: `mas_apppoints.rptdesign`
   - Description: `MAS AppPoint Estimate`
   - Application: `LICTRACK` (License Usage Monitor)
   - Report Folder: `LICTRACK`
2. Save, then choose **Import Report** in the left menu, pick the file and press **OK**.
3. Click **Generate Request Page**.
4. Click **Preview** (or run it from License Usage Monitor) and enter:
   - **Days_Back**: days of login history to search for peaks, 1 to 730 (default 90).
   - **Owned AppPoints**: your MAS AppPoint entitlement, or 0 to skip the headroom figure.

Login tracking must be switched on for the peak sections. The user-type sections work without it.

## Databases

One file covers Oracle, SQL Server and DB2. Each data set reads the database product name from Maximo's report connection and sends the matching SQL dialect.

| Database | User types match IBM's Magic Script | All report queries run | Run inside Maximo 7.6 |
|---|---|---|---|
| Oracle | yes (114 of 114 demo users) | yes | yes |
| SQL Server | yes (88 of 88 demo users) | yes | yes |
| DB2 | yes (128 of 128 demo users) | yes | yes |

Each database was tested inside Maximo 7.6.1 (import, Generate Request Page, Preview). On a SQL Server and a DB2 Maximo built from the same demo data, the report returns the same figures.

If it fails on your system, please open an issue with the database version and the error.

## How the user type is decided

| # | Rule | Type |
|---|---|---|
| 1 | No read access outside self-service applications | Self service (0) |
| 2 | Creates records in 3 modules or fewer (Work Orders not counted) and has no save rights in Security, System Configuration, Integration, Utilities or Scheduler | Limited (5) |
| 3 | Otherwise, creates records in an add-on application (name starts with `PLUS`) | Premium (15) |
| 4 | Otherwise | Base (10) |

These are the rules and AppPoint values the Magic Script uses. The built-in accounts MAXADMIN, MXINTADM and MAXREG are left out of the peak sessions, as in the original script.

## Limits

- An estimate, not a licence audit. Confirm the AppPoint rates and whether your entitlement is authorized or concurrent with IBM or your business partner.
- Maximo records its logged-in user count at most every 15 minutes, so the real peak can be slightly higher.
- Manage only: install-based charges (for example Spatial) and other MAS applications are not included.

Provided as is, without warranty. Not an IBM product.
