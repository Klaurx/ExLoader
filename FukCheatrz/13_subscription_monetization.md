# 13 - Subscription and Monetization

## Overview

ExLoader operates a subscription-based paid tier alongside its free offering. The subscription system is fully implemented in app.so with lifecycle management, expiry tracking, and user-facing messaging.

## Subscription Lifecycle Strings

Extracted from app.so:

```
subscriptionExpires
subscriptionUpdate
isSubscriptionActive
subscriptionDaysToAddCount
```

subscriptionExpires stores the expiry timestamp of the user's active subscription. subscriptionUpdate handles subscription state refresh, likely triggered on application launch or when the user reconnects to the backend. isSubscriptionActive is a boolean property checked throughout the application to gate premium features. subscriptionDaysToAddCount is used when extending a subscription, suggesting the backend grants time-based increments rather than fixed renewal periods.

## User-Facing Confirmation

A string extracted from app.so addressed directly to the user:

```
Users who buy this modification with ExLoader receive pleasant and useful bonuses for each payment
```

This is marketing copy embedded in the application binary, confirming a paid tier exists and that it is promoted within the UI.

## License and Terms Infrastructure

```
.exloader.net/end_user_license_agreement
```

The EULA is hosted on the developer's own backend domain. Its presence as a string in the binary confirms the application presents legal terms to users at some point in the onboarding flow.

A string referencing a satirical or dismissive privacy policy was observed across multiple language localizations. Its exact content is not reproduced here, but it was present in all 22 supported languages, indicating it was translated and deliberately distributed globally. The legal standing of such a document across 22 jurisdictions is not assessed in this report.

## Integrity Verification for Mod Packages

```
MD5
SHA256
zipCrypto
```

Downloaded mod packages are verified with MD5 and SHA256 before use, and encrypted ZIP archives are supported. This indicates the premium mod distribution system has basic integrity checks, which is consistent with a commercial product that has reputational incentive to ensure delivered mods are not corrupted or tampered with in transit.
