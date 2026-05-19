# appium-mobile-e2e skill

## Prerequisites

Install Appium before using this skill:

```bash
npm install -g appium
```

Verify that Appium is available:

```bash
appium --version
```

Appium also needs the platform setup for the device you want to automate, such as Android SDK tooling plus the matching Appium driver for Android, or Xcode tooling plus the matching Appium driver for iOS.

## MCP setup

### VS Code / GitHub Copilot

Create `.vscode/mcp.json` in the target project:

```json
{
  "servers": {
    "appium-mcp": {
      "disabled": false,
      "timeout": 100,
      "type": "stdio",
      "command": "/absolute/path/to/appium-mcp",
      "args": [],
      "env": {
        "ANDROID_HOME": "/absolute/path/to/android-sdk"
      }
    }
  }
}
```

Update `command` and `ANDROID_HOME` when your local paths are different.

### Codex

Add this to `~/.codex/config.toml`:

```toml
[mcp_servers.appium-mcp]
command = "npx"
args = ["-y", "appium-mcp@latest"]

[mcp_servers.appium-mcp.env]
ANDROID_HOME = "/absolute/path/to/android-sdk"
```

Then verify the MCP server is registered:

```bash
codex mcp list
```

You can also point Codex at an installed binary instead of `npx`:

```toml
[mcp_servers.appium-mcp]
command = "/absolute/path/to/appium-mcp"
args = []

[mcp_servers.appium-mcp.env]
ANDROID_HOME = "/absolute/path/to/android-sdk"
```

## Install

Choose the target agent explicitly when installing:

```bash
# Codex
npx skills add dxik/appium-mobile-e2e --skill appium-mobile-e2e --agent codex

# GitHub Copilot
npx skills add dxik/appium-mobile-e2e --skill appium-mobile-e2e --agent github-copilot
```



If `--agent` is omitted, the CLI may auto-detect the current agent or prompt interactively when needed. The same `SKILL.md` is installed for both agents; the CLI handles the destination path for each one.

The CLI discovers this skill from `skills/appium-mobile-e2e/SKILL.md`.
