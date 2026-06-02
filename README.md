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

## Visual Test Runner (VS Code Extension)

For a richer experience — browsing, running, and reviewing results directly in VS Code — install the **Appium Visual** extension:

**Marketplace:** [dungtran.appium-visual](https://marketplace.visualstudio.com/items?itemName=dungtran.appium-visual)

**Quick install via VS Code:**

```bash
code --install-extension dungtran.appium-visual
```

Or open the Extensions panel (`Cmd+Shift+X`), search for **Appium Visual**, and click **Install**.

### What it gives you

| Feature | Detail |
|---------|--------|
| **Test discovery** | Automatically detects Appium JS test files in `e2e/flows/` and lists them in a dedicated sidebar |
| **Multiple test runs** | Select one or more tests from the sidebar and run them in sequence |
| **Live log streaming** | Streams Appium output to a visual dashboard as the test runs |
| **Results panel** | Shows final status, assertion results, logs, and local artifacts per run |
| **Device selection** | Detects connected Android devices (`adb devices`) and booted iOS simulators (`xcrun simctl`); pick a default device once |
| **Appium server management** | Starts a local Appium server automatically when none is running |
| **Cancel support** | Stop an in-progress test run at any time |

### Troubleshooting

- **No tests appear** — add `.js` test files under `e2e/flows/` and click **Refresh** in the Appium Tests sidebar.
- **No Android devices** — run `adb devices` to verify the device is connected and authorised.
- **No iOS simulators** — boot a simulator first, then check `xcrun simctl list devices booted`.
- **Appium not running** — use the **Appium: Start Appium** command or set `appiumVisual.appiumServerUrl` to point at a running server.
