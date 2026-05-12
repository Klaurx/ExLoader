# 14 - Bundleware and PUA Behavior

## Overview

ExLoader implements a geo-targeted third-party software offer system. Three products are offered periodically to users, with re-prompt timing controlled by per-product timestamps stored in the application's local state. The offers are served based on the user's detected country, determined by their IP address.

## Offer Products

Timestamp keys extracted from app.so, one per offer product:

```
kLastOfferReactivationTimestamp_Avast_v2
kLastOfferReactivationTimestamp_Opera_v2
kLastOfferReactivationTimestamp_Yandex_v2
```

The three products offered are Avast antivirus, Opera browser, and Yandex browser. Each has an independent re-prompt timestamp, meaning the application tracks when each offer was last shown and waits a defined interval before showing it again. The _v2 suffix on all three keys suggests a prior versioning iteration of the offer system existed.

## Geo-Targeting Logic

Function names extracted from app.so:

```
_getUserCountryWithDecimalIP
_isGoodGeoByList
_offerIsFromThisGeo
```

_getUserCountryWithDecimalIP resolves the user's IP address to a country code. _isGoodGeoByList checks the resolved country against a list of eligible countries for a given offer. _offerIsFromThisGeo determines whether a specific offer should be shown to a user in a given geography.

The practical implication is that different users in different countries see different offers. This is standard PUA monetization practice, where offer conversion rates and payout rates vary by country, and the offer stack is tuned per market.

## Offer Endpoints

Confirmed offer-related URLs extracted from app.so:

```
https://net.geo.opera.com/opera/...
https://www.avast.com/privacy-policy
https://www.opera.com/privacy
```

These are the landing pages and reactivation endpoints used in the offer redirect flow.

## Classification

This behavior pattern, periodic re-prompting for third-party software installs with geo-targeting and suppression of the offer for users who already have the product installed, is the defining characteristic of a Potentially Unwanted Application (PUA) monetization model. The software is distributed free of charge and monetized through third-party install conversions. This is consistent with how ExLoader's user-facing copy describes the product as free.
