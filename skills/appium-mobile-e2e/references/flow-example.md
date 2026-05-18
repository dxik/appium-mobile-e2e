# Worked example: mobile feature flow

## Goal

Turn a user request like `test flow <feature>` into a real Appium run that:

1. finds the route
2. enters the correct screen
3. drives the live UI interactively
4. verifies the end state
5. optionally saves a reusable script

## Example structure

```text
request
-> router map lookup
-> route-owner file
-> destination screen
-> live Appium session
-> interactive taps/type/scroll
-> assertion
-> ask whether to save a reusable script
```

## When to save a script

Save a reusable script only when:

- the flow is likely to be reused
- the path is deterministic enough to replay
- the user wants a saved test or script

## When to stay interactive

Stay interactive when:

- the path is still being discovered
- the UI is visually stable but semantically unclear
- you only need to answer whether the flow currently passes
