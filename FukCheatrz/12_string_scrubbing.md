# 12 - Game Memory String Scrubbing

## Overview

ExLoader scans the memory of target game processes and erases strings that match a defined list. The purpose is to remove signatures that anti-cheat memory scanners would find in regions of memory where the application has injected code or modified data. The feature has a dedicated UI screen confirmed by profanity.png in the asset manifest.

The name "profanity" is the developer's internal label for this feature, likely borrowed from the concept of a profanity filter, applied here to strings that would be "offensive" to an anti-cheat scanner.

## Function Names

Extracted from app.so:

```
_isProfanityPresent
_cleanupStrings
_optimizedWithGamesStringsList
_fullStringsList
_WrapProfanity
```

_isProfanityPresent checks whether any flagged strings are currently present in the target process's memory. _cleanupStrings performs the erasure. _optimizedWithGamesStringsList and _fullStringsList indicate two tiers of string lists exist: a smaller optimized list used for specific games and a full list used for broader coverage. _WrapProfanity wraps a string in the internal representation used for matching.

## Partial Matching

A specific example of the partial matching behavior was extracted from app.so:

```
ground -> battleground, playground
```

This shows the string scrubber uses substring matching, not exact matching. A rule targeting "ground" will also erase "battleground" and "playground" wherever they appear in process memory. This approach catches more potential signature fragments at the cost of potentially erasing legitimate strings that happen to contain the target substring.

## Network String Scrubbing

Beyond application-specific strings, the scrubber also applies regex rules to erase network-related strings from game memory. These rules are documented in 04_network_infrastructure.md and target IP addresses, IP:port combinations, protocol-prefixed URLs, and executable filename references. The intent is to erase any network indicators left in memory by injected code that could be detected by anti-cheat network signature scanning.
