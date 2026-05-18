# appium-mobile-e2e skill

Install this skill into another project with the open skills CLI:

```bash
npx skills add /absolute/path/to/appium-mobile-e2e --skill appium-mobile-e2e
```

After this repository is pushed to GitHub, install it by repo name instead:

```bash
npx skills add dxik/appium-mobile-e2e --skill appium-mobile-e2e
```

To inspect what the CLI can install before adding it:

```bash
npx skills add dxik/appium-mobile-e2e --list
```

Choose the target agent explicitly when installing:

```bash
# Codex
npx skills add dxik/appium-mobile-e2e --skill appium-mobile-e2e --agent codex

# GitHub Copilot
npx skills add dxik/appium-mobile-e2e --skill appium-mobile-e2e --agent github-copilot
```

Install to both agents when needed:

```bash
npx skills add dxik/appium-mobile-e2e \
  --skill appium-mobile-e2e \
  --agent codex \
  --agent github-copilot
```

If `--agent` is omitted, the CLI may auto-detect the current agent or prompt interactively when needed. The same `SKILL.md` is installed for both agents; the CLI handles the destination path for each one.

The CLI discovers this skill from `skills/appium-mobile-e2e/SKILL.md`.
