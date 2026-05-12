# 15 - Telemetry and Backend Infrastructure

## Overview

ExLoader sends telemetry to its backend infrastructure and maintains a full administrative panel accessible to the developer. The telemetry covers application launches, crashes, and exceptions. The admin panel includes screens for crash statistics, mod reports, and configuration reports.

## Telemetry Function Names

Extracted from app.so:

```
_sendCrashData
_sendLaunchData
_sendExceptionToServer
```

_sendLaunchData fires on application start, reporting that the application was launched. _sendCrashData and _sendExceptionToServer fire when the application encounters an unhandled exception or crash, sending diagnostic data to the developer's servers. The data sent by these functions is not known from static analysis alone, but at minimum they confirm the user's application activity is reported to exloader.net on each launch.

## Admin Panel Screens

State class names extracted from app.so:

```
_AdminPanelCrashStatsScreenState
_AdminPanelModsReportsScreenState
_AdminPanelConfigsReportsScreenState
```

These are Dart widget state classes, each corresponding to a distinct screen in a developer-facing admin panel embedded in the application. CrashStats aggregates crash telemetry across the user base. ModsReports handles user-submitted reports about mods, consistent with a moderation workflow. ConfigsReports covers configuration-level reporting.

The admin panel being embedded in the same binary as the user-facing application means it is accessible on any device running ExLoader, gated only by whatever authentication logic the developer has implemented. It is not a separate developer tool.

## Complaints and Reports System

UI asset from AssetManifest.json:

```
complain.png
```

A user-facing complaints or reporting screen exists in the application. This allows users to submit reports about mods or other users, feeding into the ModsReports admin panel screen documented above.

## Backend Domain

All telemetry and admin panel traffic targets the developer's backend infrastructure at exloader.net, encrypted using the scheme documented in 05_encryption_and_comms.md. The specific endpoints within exloader.net that receive telemetry data are not known from static analysis.
