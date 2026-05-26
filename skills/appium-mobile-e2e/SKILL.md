---
name: appium-mobile-e2e
description: Use when the user asks to automatically test a mobile feature or flow in any Flutter or React Native app with Appium, including requests like "test flow X", "auto test feature Y", or "write/run an Appium flow". Follow the repo's navigation map when present, drive the real app interactively, verify the result, and add or update a reusable script when the flow is worth keeping.
---

# Appium Mobile E2E

Use this skill to turn a product-level mobile flow request into a real Appium run.

## Prerequisite gate

Before any Appium mobile E2E work, verify the local Appium installation and server first. This is mandatory before selecting devices, preparing simulators, creating sessions, driving flows, or writing reusable scripts.

1. Check that Appium is installed and callable, for example with `appium --version` or the repo's documented Appium command.
2. Check whether a local Appium server is already running at `http://127.0.0.1:4723/status`.
3. If the local server is not running, start Appium locally with the repo's preferred command or the lightest standard command, then re-check `/status`.
4. Stop and report the blocker if Appium is not installed, cannot start, or `/status` does not respond successfully.
5. Only proceed to device selection, session creation, interactive driving, or script execution after the local Appium server is confirmed healthy.

## Core workflow

1. Pass the prerequisite gate: Appium installed, local Appium server running, and `/status` healthy.
2. Read `docs/mobile-screen-map.md` first. If it does not exist, create it before proceeding and seed it from the screens/routing you can confirm in a simple route-to-screen format such as `/booking -> BookingHomeScreen (lib/screens/booking_home_screen.dart)`.
3. Resolve the requested user flow from navigation, not by grepping the entire UI tree.
4. Inspect only the route-owner file and destination screen files needed for that flow.
5. Reuse an existing script in `e2e/flows/` when one already matches the flow. Name reusable scripts after the feature or flow, for example `e2e/flows/booking.js`.
6. Run against the real Appium server and a concrete device or simulator.
7. Verify the end state from the app, not only that taps succeeded.
8. Report:
   - Appium local install/server status
   - route taken
   - whether the flow passed
   - any blocker that prevents reliable automation
9. If no reusable script existed before the run and the flow passed, ask the user whether they want this flow added to the repo as a reusable Appium script for future reruns. Do not create a new script before asking unless the user explicitly requested a reusable test/script.

## Default execution rules

- Prefer visible product text, native predicates, and route knowledge over brittle broad searches.
- Use the router map to answer "which screen owns this flow?" before inspecting implementation.
- Prefer a known local Appium server when one is already running. Otherwise start Appium locally and verify `/status` before any session setup.
- When a device or simulator target is already known, create the Appium session directly with explicit capabilities. Do not call device-selection or simulator-prep helpers first unless the target is unknown or unavailable.
- During discovery, drive the live session directly: inspect the current screen, tap, type, scroll, and re-check state with Appium calls. Do not write ad-hoc temporary scripts just to probe the next step.
- Temporary scripts are disallowed for ordinary flow exploration. Use them only when the user explicitly asks for a saved script, when an existing reusable script is being validated, or when a long deterministic replay is clearly faster than continuing interactively.
- Do not call `generate_locators` as a default discovery step. Start with visible text and small targeted `find` queries; use full locator generation only as a debugging fallback.
- Do not fetch page source repeatedly by habit. Capture it at decision points: initial state, after an unexpected screen, or when a locator strategy fails.
- Accept first-launch Terms if present.
- Log in only when the login screen is actually visible.
- Do not mutate user data casually. Use unique temporary values when possible, or restore the original value after verification.
- If a shared UI component collapses controls into one native node, first inspect screenshot + source. Use sheet-relative or element-relative taps only when text/native locators are unavailable.
- Keep scripts runnable with environment variables and `e2e/environment.js` instead of hardcoded credentials or device ids.

## Reusable scripts

When the user explicitly asks for a reusable script, or asks to save a successful flow for reruns:

- put the flow script under `e2e/flows/` with a short feature name
- keep configuration in environment variables with optional `e2e/environment.js`
- write a JSON result file under `e2e/results/`
- follow the detailed template in `references/reusable-script-template.md`

## Fast path

Use this order when the local Appium server is already running and the target device is known:

```text
1. create remote Appium session directly
2. inspect only enough state to identify current screen
3. drive the flow interactively in the live session
4. verify the result
5. ask whether to save a reusable script if none existed
```

Avoid this slower default path unless necessary:

```text
select device -> prepare simulator -> create embedded session -> dump source -> generate all locators
```

Only fall back to the slower path when:

- no target device is known
- the device/simulator is not ready or cannot be reached
- the remote Appium server is unavailable
- the app is not installed on the target device

## Interactive-first rule

For a new flow, the default loop is:

```text
look at current screen
-> make one small Appium action
-> inspect the result
-> continue
```

This is preferred over:

```text
write /tmp script
-> run script
-> patch script
-> rerun script
```

The second pattern is acceptable only after the path is already understood and replay value outweighs exploration speed.

## Project-specific notes

- If shared UI components collapse multiple visible controls into one native node, derive coordinates from a stable containing element rather than hardcoding global screen coordinates.
- If the repo already contains Appium scripts, prefer copying their session and verification style.

## When blocked

- If the requested flow cannot be found in the router map, inspect the smallest likely route-owner files and update the map after confirming the path.
- If Appium cannot interact with a visual control, capture screenshot and page source before guessing.
- If a flow depends on unstable external data, state the dependency and use the least destructive verification possible.
- If a selector is ambiguous because the same label appears multiple times, disambiguate by container geometry or route context.

## Optional reference

- Read `references/flow-example.md` when you need a concrete worked example of how to move from route lookup to a live Appium run.
- Read `references/reusable-script-template.md` only when you need the full template for a saved reusable script.
