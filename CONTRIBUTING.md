# Contributing
**Please do not contribute to this repository. Instead, look up to contribute to https://github.com/RJNY/Obtainium-Emulation-Pack and/or https://github.com/omeritzics/Updatium.**

## Quick Start

```bash
# Clone the repository
git clone https://github.com/RJNY/Updatium-Emulation-Pack.git
cd Updatium-Emulation-Pack

# Make your changes to src/applications.json (or use just add-app)
just test              # verify configs resolve to real APKs
just build             # test, validate, normalize, and generate all output files
```

## Project Structure

```
justfile                         # Primary task runner (run `just` to see commands)
utility.just                     # Private helper recipes (imported by justfile)
src/
  applications.json              # Source of truth - all app definitions
scripts/
  constants.py                   # Shared constants and Updatium source schema
  utils.py                       # Shared utility functions and .env loader
  help_formatter.py              # Styled argparse help formatter (ANSI colors)
  validate-json.py               # Validates applications.json
  test-apps.py                   # Live-tests configs resolve to downloadable APKs
  add-app.py                     # Interactive CLI to add a new app
  generate-table.py              # Generates the README table
  generate-readme.py             # Stitches markdown files into README
  minify-json.py                 # Creates release JSON files
  normalize-json.py              # Normalize key order and backfill defaults
  release.py                     # Automated release workflow (tag, push, gh release)
updatium-emulation-pack-latest.json           # The file to import to Updatium
```

## Adding a New Application

### Option A: Quick Add (Recommended for GitHub apps)

Use the interactive CLI to quickly add a new app:

```bash
just add-app
```

This will:

- Prompt you for the GitHub URL
- Auto-detect the source, author, and app name
- Ask for the Android package ID and category
- Generate proper Updatium settings
- Add the app to `applications.json`

> **Tip:** To find the package ID, open the app in Updatium - the package ID is displayed directly below the source URL (e.g., `com.example.android`).

After running, execute `just build` to regenerate all files.

### Option B: Manual Add (For complex configs or non-GitHub sources)

#### Step 1: Export the app config from Updatium

1. Open Updatium on your device
2. Add the app you want to include (configure it how you want)
3. Long-press the app and select "Export"
4. Choose "Updatium Export" format
5. Transfer the JSON to your computer

#### Step 2: Add the app to applications.json

Open `src/applications.json` and add your app to the `apps` array:

```json
{
  "id": "com.example.emulator",
  "url": "https://github.com/example/emulator",
  "author": "example",
  "name": "Example Emulator",
  "preferredApkIndex": 0,
  "additionalSettings": "{...}",
  "categories": ["Emulator"],
  "overrideSource": "GitHub"
}
```

#### Step 3: Add meta fields (optional)

Add a `meta` object to customize how the app appears:

```json
{
  "id": "com.example.emulator",
  "url": "https://github.com/example/emulator",
  "author": "example",
  "name": "Example Emulator",
  "preferredApkIndex": 0,
  "additionalSettings": "{...}",
  "categories": ["Emulator"],
  "overrideSource": "GitHub",
  "meta": {
    "nameOverride": "Example Emu",
    "urlOverride": "https://example-emu.org"
  }
}
```

#### Step 4: Validate, test, and regenerate

```bash
just validate          # check for structural errors
just test              # verify your app config resolves to a real APK
just build             # test, validate, normalize, and generate all output files
```

## Available Commands

Run `just` to see all available commands. Recipes with `*args` accept `-h` for help.

| Command                 | Description                                                 |
| ----------------------- | ----------------------------------------------------------- |
| `just add-app`          | Interactive CLI to add a new app                            |
| `just validate`         | Validate applications.json (structure, regex, source types) |
| `just normalize`        | Normalize key order and backfill defaults                   |
| `just test`             | Live-test all app configs resolve to downloadable APKs      |
| `just test AppName`     | Live-test a single app by name (partial match)              |
| `just test --verbose`   | Live-test all apps with APK URL details                     |
| `just generate`         | Generate all output files (README, release JSONs)           |
| `just generate table`   | Generate the README table only                              |
| `just build`            | Test, validate, normalize, and generate all output files    |
| `just release`          | Tag, push, and create a GitHub release                      |

## Meta Field Reference

These fields in the `meta` object control how apps are processed:

| Field                 | Type   | Default | Description                                                         |
| --------------------- | ------ | ------- | ------------------------------------------------------------------- |
| `excludeFromExport`   | bool   | `false` | Exclude from both release JSON files. Use for beta/unstable apps.   |
| `excludeFromTable`    | bool   | `false` | Exclude from the README table.                                      |
| `nameOverride`        | string | `null`  | Override the display name in the README table.                      |
| `urlOverride`         | string | `null`  | Override the homepage link in the README table.                     |

## Categories

Apps are organized into categories that appear as sections in the README table:

| Category       | Description                                                      |
| -------------- | ---------------------------------------------------------------- |
| `Emulator`     | Console/handheld emulators (Dolphin, RetroArch, PPSSPP, etc.)    |
| `Frontend`     | Emulator launchers and game library managers (Daijisho, Pegasus) |
| `Utilities`    | Helper apps (Syncthing, OdinTools, LED controllers, etc.)        |
| `PC Emulation` | Windows/PC game layers (Winlator, etc.)                          |
| `Streaming`    | Game streaming clients (Moonlight, etc.)                         |

An app can belong to multiple categories.


3. **Is this app stable and ready for users?**
   - Yes: Include normally
   - No: Set `excludeFromExport: true` (still visible in table but not in release JSONs)
