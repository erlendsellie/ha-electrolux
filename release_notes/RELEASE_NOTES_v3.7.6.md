# Release Notes v3.7.6

## 🛠 Fixes

- __fix(button): derive executeCommand availability from the appliance, not its type (#164)__ by @tanarchytan Command buttons (Start, Pause, Stop/Reset, On, Off…) are now gated by the appliance's __own__ reported state machine instead of a per-model-type table. Fixes cases where a button was shown enabled but the Electrolux cloud rejected the command with `406 COMMAND_VALIDATION_ERROR` — e.g. __Start offered while a dryer/dishwasher is idle, when the appliance actually requires switching it on first__ (see issue #191). The catalog tables remain as a fallback for appliances that publish no state triggers.
- __fix(button): gate source-scoped executeCommand buttons by their own applianceState (#189)__ by @TTLucian Follow-up to #164: buttons on sub-appliances (e.g. structured ovens with an `upperOven` cavity) are now gated by the cavity's own state machine instead of the main appliance's, and a missing state no longer causes all rule-covered buttons to become unavailable.
- __feat(button): gate executeCommand by cyclePhase triggers (#178)__ by @TTLucian (reported by @netflash) Command availability now also honours `cyclePhase` triggers published by the appliance, OR-ed with the `applianceState` dimension. Fixes Stop/Reset staying disabled during anti-crease on models such as the AEG TR969PB4C heat-pump dryer (TD-916900511), where the cloud allows the command while `cyclePhase` is `ANTICREASE` — a value `applianceState` never reports. Models without `cyclePhase` triggers are unaffected.
- __fix(coordinator): add progressive exponential backoff for SSE stream reconnects (#179)__ by @IvanAlekseev Watchdog-initiated stream restarts are debounced with progressive backoff (15s → up to 30 min) to avoid hammering the cloud during sustained outages.
- __fix(coordinator): add progressive exponential backoff on stream setup and rotation failure (#184)__ by @IvanAlekseev Extends the same backoff to SSE stream *setup* and connection-rotation failures, not just stall-triggered restarts.
- __fix(auth): enforce strict backoff cooldown on token refresh failures and classify transient errors (#176)__ by @IvanAlekseev Repeated token-refresh failures no longer hammer the login endpoint; transient network errors are classified correctly so they don't count as auth failures.
- __fix: cancel timeToEnd and SSE watchdog tasks on config entry unload (#181)__ by @TTLucian Watchdog timers are properly cleaned up when the integration is reloaded, preventing orphaned background tasks.
- __fix(coordinator): replace timer-based stream watchdog with REST state desync validation (#186)__ by @IvanAlekseev
- __fix(models): map catalog-only string+readwrite capabilities to SELECT entities (#195)__ by @TTLucian

## ✨ Features

- __feat(diagnostics): add Electrolux Cloud service device with REST API and SSE stream diagnostic sensors (#183)__ by @IvanAlekseev New service device exposing REST API and SSE stream connectivity diagnostics (with failure debouncing and a reconnection grace period), disabled by default — useful for debugging connectivity issues.
- __feat(rvc): add 700series vacuum entity + diagnostic sensors (#175)__ by @netflash
- __feat(coordinator): add event-driven desync recovery when timeToEnd decrements__ (#187) by @IvanAlekseev

## 🔧 Internal / chores

- Fix duplicate SSE/websocket pipeline creation (@TTLucian)
- Test suite hardening: execute-state derivation now regression-tested against real appliance samples (#188/#190 by @TTLucian)
- Python dependency bumps

## ⬆️ Upgrade notes

- __If command buttons previously appeared enabled but the cloud rejected the command__ (e.g. *Start* on an idle AEG 7000 / Electrolux 7000-series dishwasher or dryer — issue #191): this is fixed. Some buttons may now appear __unavailable in certain appliance states__ — that is intentional and mirrors what the appliance's cloud state machine actually accepts. For dishwashers/dryers that are switched on but idle, press __On__ first; *Start* becomes available once the appliance reports *Ready to start*.

## ⚠️ Special note
Many features and appliance types supported by this integration have __not been tested__ on physical appliances in the wild. Since I do not own most of the supported appliance types and models, development and testing often rely on diagnostic data, API capabilities, and reported appliance behaviour rather than direct testing on physical appliances.

I'm therefore __counting on the community__ to help validate these features. If you encounter anything unexpected, incorrect, missing, or broken, please report it with __as much detail as possible__, ideally including __diagnostics__ and relevant __debug logs__.

Even seemingly small issues or unusual appliance behaviour can be valuable, as they help improve compatibility and prevent incorrect assumptions and guesswork from becoming permanent parts of the integration.

If you own an appliance type that hasn't been tested, your feedback is especially valuable. See [README.md](https://github.com/TTLucian/ha-electrolux/blob/main/README.md) for more information.

## 🌟 Credits
BIG thank-yous to all contributors:
- [netflash](https://github.com/netflash)
- [IvanAlekseev](https://github.com/IvanAlekseev)
- [tanarchytan](https://github.com/tanarchytan)

BIG thank-yous to all supporters!

Without you, this project would not have been possible.