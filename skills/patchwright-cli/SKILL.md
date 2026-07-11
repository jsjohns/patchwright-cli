---
name: patchwright-cli
description: Automate bot-protected websites with Patchright when standard Playwright is blocked or challenged.
allowed-tools: Bash(patchwright-cli:*) Bash(npx:*) Bash(npm:*)
---

# Browser Automation with patchwright-cli

## Quick start

```bash
# open real Chrome with a persistent profile for bot-protected websites
patchwright-cli open --headed --browser=chrome --persistent
patchwright-cli resize 1280 720
# navigate to a page
patchwright-cli goto https://playwright.dev
# interact with the page using refs from the snapshot
patchwright-cli click e15
patchwright-cli type "page.click"
patchwright-cli press Enter
# take a screenshot (rarely used, as snapshot is more common)
patchwright-cli screenshot
# close the browser
patchwright-cli close
```

## Commands

### Core

```bash
patchwright-cli open
# open and navigate right away
patchwright-cli open https://example.com/
patchwright-cli goto https://playwright.dev
patchwright-cli type "search query"
patchwright-cli click e3
patchwright-cli dblclick e7
# --submit presses Enter after filling the element
patchwright-cli fill e5 "user@example.com"  --submit
patchwright-cli drag e2 e8
# drop files or data onto an element (from outside the page)
patchwright-cli drop e4 --path=./image.png
patchwright-cli drop e4 --data="text/plain=hello world"
patchwright-cli hover e4
patchwright-cli select e9 "option-value"
patchwright-cli upload ./document.pdf
patchwright-cli check e12
patchwright-cli uncheck e12
patchwright-cli snapshot
patchwright-cli eval "document.title"
patchwright-cli eval "el => el.textContent" e5
# get element id, class, or any attribute not visible in the snapshot
patchwright-cli eval "el => el.id" e5
patchwright-cli eval "el => el.getAttribute('data-testid')" e5
patchwright-cli dialog-accept
patchwright-cli dialog-accept "confirmation text"
patchwright-cli dialog-dismiss
patchwright-cli resize 1920 1080
patchwright-cli close
```

### Navigation

```bash
patchwright-cli go-back
patchwright-cli go-forward
patchwright-cli reload
```

### Keyboard

```bash
patchwright-cli press Enter
patchwright-cli press ArrowDown
patchwright-cli keydown Shift
patchwright-cli keyup Shift
```

### Mouse

```bash
patchwright-cli mousemove 150 300
patchwright-cli mousedown
patchwright-cli mousedown right
patchwright-cli mouseup
patchwright-cli mouseup right
patchwright-cli mousewheel 0 100
```

### Save as

```bash
patchwright-cli screenshot
patchwright-cli screenshot e5
patchwright-cli screenshot --filename=page.png
patchwright-cli pdf --filename=page.pdf
```

### Tabs

```bash
patchwright-cli tab-list
patchwright-cli tab-new
patchwright-cli tab-new https://example.com/page
patchwright-cli tab-close
patchwright-cli tab-close 2
patchwright-cli tab-select 0
```

### Storage

```bash
patchwright-cli state-save
patchwright-cli state-save auth.json
patchwright-cli state-load auth.json

# Cookies
patchwright-cli cookie-list
patchwright-cli cookie-list --domain=example.com
patchwright-cli cookie-get session_id
patchwright-cli cookie-set session_id abc123
patchwright-cli cookie-set session_id abc123 --domain=example.com --httpOnly --secure
patchwright-cli cookie-delete session_id
patchwright-cli cookie-clear

# LocalStorage
patchwright-cli localstorage-list
patchwright-cli localstorage-get theme
patchwright-cli localstorage-set theme dark
patchwright-cli localstorage-delete theme
patchwright-cli localstorage-clear

# SessionStorage
patchwright-cli sessionstorage-list
patchwright-cli sessionstorage-get step
patchwright-cli sessionstorage-set step 3
patchwright-cli sessionstorage-delete step
patchwright-cli sessionstorage-clear
```

### Network

```bash
patchwright-cli route "**/*.jpg" --status=404
patchwright-cli route "https://api.example.com/**" --body='{"mock": true}'
patchwright-cli route-list
patchwright-cli unroute "**/*.jpg"
patchwright-cli unroute
```

### DevTools

```bash
patchwright-cli console
patchwright-cli console warning
patchwright-cli requests
patchwright-cli request 5
patchwright-cli run-code "async page => await page.context().grantPermissions(['geolocation'])"
patchwright-cli run-code --filename=script.js
patchwright-cli tracing-start
patchwright-cli tracing-stop
patchwright-cli video-start video.webm
patchwright-cli video-chapter "Chapter Title" --description="Details" --duration=2000
patchwright-cli video-stop

# annotate each subsequent action (click, type, ...) with a callout naming the action and highlighting the target
patchwright-cli video-show-actions --duration=600 --position=top-right
patchwright-cli video-hide-actions

# launch the dashboard for UI review / design feedback — user annotates the page, you receive the annotated screenshot, snapshot, and notes
patchwright-cli show --annotate

# generate a Playwright locator for an element from its ref or selector
patchwright-cli generate-locator e5 --raw

# show a persistent highlight overlay for an element, optionally with a custom style
patchwright-cli highlight e5
patchwright-cli highlight e5 --style="outline: 3px dashed red"
# hide a single element highlight, or all page highlights when no target is given
patchwright-cli highlight e5 --hide
patchwright-cli highlight --hide
```

## Raw output

The global `--raw` option strips page status, generated code, and snapshot sections from the output, returning only the result value. Use it to pipe command output into other tools. Commands that don't produce output return nothing.

```bash
patchwright-cli --raw eval "JSON.stringify(performance.timing)" | jq '.loadEventEnd - .navigationStart'
patchwright-cli --raw eval "JSON.stringify([...document.querySelectorAll('a')].map(a => a.href))" > links.json
patchwright-cli --raw snapshot > before.yml
patchwright-cli click e5
patchwright-cli --raw snapshot > after.yml
diff before.yml after.yml
TOKEN=$(patchwright-cli --raw cookie-get session_id)
patchwright-cli --raw localstorage-get theme
```

For structured output wrapping every reply as JSON, pass --json
```bash
patchwright-cli list --json
```

## Open parameters
```bash
# Use specific browser when creating session
patchwright-cli open --browser=chrome
patchwright-cli open --browser=firefox
patchwright-cli open --browser=webkit
patchwright-cli open --browser=msedge

# Emulate a generic mobile device (Pixel 10 for Chromium, iPhone 17 for WebKit).
# Prefer this when a mobile layout is acceptable: mobile pages are usually
# lighter, so snapshots are smaller and cheaper.

# Use persistent profile (by default profile is in-memory)
patchwright-cli open --persistent
# Use persistent profile with custom directory
patchwright-cli open --profile=/path/to/profile

# Connect to browser via Playwright Extension
patchwright-cli attach --extension=chrome

# Connect to a running Chrome or Edge by channel name
patchwright-cli attach --cdp=chrome
patchwright-cli attach --cdp=msedge

# Connect to a running browser via CDP endpoint
patchwright-cli attach --cdp=http://localhost:9222

# Start with config file
patchwright-cli open --config=my-config.json

# Close the browser
patchwright-cli close
# Detach from an attached browser (leaves the external browser running)
patchwright-cli -s=msedge detach
# Delete user data for the default session
patchwright-cli delete-data
```

## URLs with `&` on Windows

On Windows, `cmd.exe` and PowerShell treat `&` as a command separator, so URLs with multiple query parameters get truncated before `patchwright-cli` runs. Escape `&` with `^&` in `cmd.exe`, or use `--%` in PowerShell:

```batch
patchwright-cli goto "https://example.com/?a=1^&b=2"
```

```powershell
patchwright-cli --% goto "https://example.com/?a=1&b=2"
```

## Snapshots

After each command, patchwright-cli provides a snapshot of the current browser state.

```bash
> patchwright-cli goto https://example.com
### Page
- Page URL: https://example.com/
- Page Title: Example Domain
### Snapshot
[Snapshot](.patchwright-cli/page-2026-02-14T19-22-42-679Z.yml)
```

You can also take a snapshot on demand using `patchwright-cli snapshot` command. All the options below can be combined as needed.

```bash
# default - save to a file with timestamp-based name
patchwright-cli snapshot

# save to file, use when snapshot is a part of the workflow result
patchwright-cli snapshot --filename=after-click.yaml

# snapshot an element instead of the whole page
patchwright-cli snapshot "#main"

# limit snapshot depth for efficiency, take a partial snapshot afterwards
patchwright-cli snapshot --depth=4
patchwright-cli snapshot e34

# include each element's bounding box as [box=x,y,width,height]
```

## Targeting elements

By default, use refs from the snapshot to interact with page elements.

```bash
# get snapshot with refs
patchwright-cli snapshot

# interact using a ref
patchwright-cli click e15
```

You can also use css selectors or Playwright locators.

```bash
# css selector
patchwright-cli click "#main > button.submit"

# role locator
patchwright-cli click "getByRole('button', { name: 'Submit' })"

# test id
patchwright-cli click "getByTestId('submit-button')"
```

## Browser Sessions

```bash
# create new browser session named "mysession" with persistent profile
patchwright-cli -s=mysession open example.com --persistent
# same with manually specified profile directory (use when requested explicitly)
patchwright-cli -s=mysession open example.com --profile=/path/to/profile
patchwright-cli -s=mysession click e6
patchwright-cli -s=mysession close  # stop a named browser
patchwright-cli -s=mysession delete-data  # delete user data for persistent session

patchwright-cli list
# Close all browsers
patchwright-cli close-all
# Forcefully kill all browser processes
patchwright-cli kill-all
```

## Installation

If global `patchwright-cli` command is not available, try a local version via `npx playwright cli`:

```bash
npx --no-install playwright --version
```

When local version is available, use `npx playwright cli` in all commands. Otherwise, install `patchwright-cli` as a global command:

```bash
npm install -g @playwright/cli@latest
```

## Example: Form submission

```bash
patchwright-cli open https://example.com/form
patchwright-cli snapshot

patchwright-cli fill e1 "user@example.com"
patchwright-cli fill e2 "password123"
patchwright-cli click e3
patchwright-cli snapshot
patchwright-cli close
```

## Example: Multi-tab workflow

```bash
patchwright-cli open https://example.com
patchwright-cli tab-new https://example.com/other
patchwright-cli tab-list
patchwright-cli tab-select 0
patchwright-cli snapshot
patchwright-cli close
```

## Example: Debugging with DevTools

```bash
patchwright-cli open https://example.com
patchwright-cli click e4
patchwright-cli fill e7 "test"
patchwright-cli console
patchwright-cli requests
patchwright-cli close
```

```bash
patchwright-cli open https://example.com
patchwright-cli tracing-start
patchwright-cli click e4
patchwright-cli fill e7 "test"
patchwright-cli tracing-stop
patchwright-cli close
```

## Example: Interactive session

Ask the user for UI review or design feedback. The user draws boxes on the live page and types comments; you receive the annotated screenshot, the snapshot of the marked region, and the user's notes. Use this whenever the user asks for "UI review", "design feedback", or to "ask the user what they think / want / mean":

```bash
patchwright-cli open https://example.com
patchwright-cli show --annotate
```

## Specific tasks

* **Running and Debugging Playwright tests** [references/playwright-tests.md](references/playwright-tests.md)
* **Request mocking** [references/request-mocking.md](references/request-mocking.md)
* **Running Playwright code** [references/running-code.md](references/running-code.md)
* **Browser session management** [references/session-management.md](references/session-management.md)
* **Storage state (cookies, localStorage)** [references/storage-state.md](references/storage-state.md)
* **Test generation (plan / generate / heal)** [references/test-generation.md](references/test-generation.md)
* **Tracing** [references/tracing.md](references/tracing.md)
* **Video recording** [references/video-recording.md](references/video-recording.md)
* **Inspecting element attributes** [references/element-attributes.md](references/element-attributes.md)
