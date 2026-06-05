---
name: cosub-install
description: Install the Zapier SDK CLI for Code Substrate Early Access and bootstrap the cosub-* sibling skills. Use when the user wants to set up Code Substrate, get started with CodeZaps, install CoSub skills, or asks how to start with cosub. Designed to be pasted as the first message in a fresh Cursor workspace.
---

# Code Substrate Early Access Install

Imperative recipe. Each step gates the next. Do not skip a step that failed.

This is the public-first EA path. It uses the Zapier SDK CLI and does not install the legacy `@zapier/zapier-sdk-code-substrate` package.

## Flow

```mermaid
flowchart TD
  probe["1. Probe environment"] --> envOk{"Node 18+, npm, git OK?"}
  envOk -->|no| stopEnv["STOP: tell user what to install"]
  envOk -->|yes| installCli["2. Install or update SDK CLI"]
  installCli --> verifyExperimental["3. Verify experimental commands"]
  verifyExperimental --> commandsOk{"Code Workflows commands visible?"}
  commandsOk -->|no| stopCli["STOP: diagnose SDK CLI install"]
  commandsOk -->|yes| installSkills["4. Bootstrap sibling skills"]
  installSkills --> checkAuth["5. Check Zapier auth"]
  checkAuth --> authOk{"JSON has data and no errors?"}
  authOk -->|no| login["5b. Ask user to run interactive login"]
  login --> checkAuth
  authOk -->|yes| smoke["6. Read-only smoke test"]
  smoke --> report["7. Report success and next steps"]
```

## What This Installs

- `@zapier/zapier-sdk-cli@latest` — public npm package that provides `zapier-sdk`, `zapier-sdk-cli`, and `zapier-sdk-experimental`. Installed globally.
- Four sibling skills copied into the workspace's `.cursor/skills/`: `cosub-build`, `cosub-list-zaps`, `cosub-show-history`, and `cosub-modify-zap`.

What this does not install:

- `@zapier/zapier-sdk-code-substrate` — old private CLI path. Do not install it for EA.
- `@zapier/zapier-durable` globally. The build skill installs or pins it inside workflow projects when needed.

## Step 1: Probe Environment

Run each check. If any fails, stop and tell the user how to fix it.

```bash
node --version
npm --version
git --version
```

Expected output:

```bash
v18.0.0   # or higher
10.x.x    # npm version; any current version is fine
git version 2.x.x
```

Requirements:

| Tool | Minimum version | Install if missing |
|---|---|---|
| Node | 18 | `brew install node` or use nvm |
| npm | any current version | bundled with Node |
| git | any | usually preinstalled on macOS; otherwise `brew install git` |

## Step 2: Install Or Update The Zapier SDK CLI

Check for an existing binary:

```bash
which zapier-sdk
zapier-sdk --version
```

If `zapier-sdk` is missing or older than the latest npm package, install the CLI globally:

```bash
npm install -g @zapier/zapier-sdk-cli@latest
```

Verify the binary is on PATH:

```bash
which zapier-sdk
zapier-sdk --version
```

If global npm installs fail because of permissions, tell the user to fix their Node/npm setup before retrying. Prefer a user-owned Node install through nvm or Homebrew over `sudo npm install -g`.

## Step 3: Verify Code Workflows Experimental Commands

```bash
zapier-sdk --experimental --help
```

Expected output includes the Code Workflows command group, including commands such as:

```text
create-workflow
list-workflows
run-durable
publish-workflow-version
list-workflow-runs
```

The equivalent binary may also work:

```bash
zapier-sdk-experimental --help
```

If neither form exposes Code Workflows commands, stop and diagnose the SDK CLI install. Do not fall back to `@zapier/zapier-sdk-code-substrate`.

## Step 4: Bootstrap The cosub-* Sibling Skills

Install the companion skills into the current workspace. This does not require Zapier authentication, so do it before the login gate.

```bash
WORKSPACE="$(pwd)"
COSUB_SKILLS_SOURCE_URL="${COSUB_SKILLS_SOURCE_URL:-https://github.com/tones/cosub-skills-ea-test.git}"
CLONE_DIR="$(mktemp -d "${TMPDIR:-/tmp}/cosub-skills-XXXXXX")"
git clone --depth 1 "$COSUB_SKILLS_SOURCE_URL" "$CLONE_DIR"
mkdir -p "$WORKSPACE/.cursor/skills"
cp -R "$CLONE_DIR/cosub-build" "$WORKSPACE/.cursor/skills/"
cp -R "$CLONE_DIR/cosub-list-zaps" "$WORKSPACE/.cursor/skills/"
cp -R "$CLONE_DIR/cosub-show-history" "$WORKSPACE/.cursor/skills/"
cp -R "$CLONE_DIR/cosub-modify-zap" "$WORKSPACE/.cursor/skills/"
```

Verify:

```bash
ls "$WORKSPACE/.cursor/skills/"
```

Expected output includes:

```text
cosub-build
cosub-list-zaps
cosub-modify-zap
cosub-show-history
```

If any folder is missing, the clone or copy failed. Diagnose before proceeding.

Note: this recipe intentionally leaves the temporary clone in place rather than deleting it automatically. If the user wants cleanup, show them the exact path and let them remove it.

## Step 5: Authenticate To Zapier

Check auth state first:

```bash
zapier-sdk get-profile --json
```

Treat auth as successful only if the JSON has a non-null `data` object with an email and the `errors` array is empty. Do not rely on exit code alone; some SDK CLI auth failures return exit code 0 with errors in the JSON body.

Expected successful output includes the user's email:

```json
{
  "data": {
    "email": "user@example.com"
  },
  "errors": []
}
```

If `data` is null, `errors` is non-empty, or the error message says authentication is required, stop and ask the user to run the interactive login command in a real terminal:

```bash
zapier-sdk login
```

This opens a browser. Do not run `zapier-sdk login` inside a non-interactive shell or background process unless the user explicitly asks you to manage the interactive login. After the user finishes login, rerun `zapier-sdk get-profile --json` and inspect the JSON again.

If the user wants non-interactive auth for automation, note that the CLI error message may mention `ZAPIER_CREDENTIALS` or client credential environment variables. For this EA install path, prefer browser login unless the user already has client credentials.

## Step 6: Smoke Test

Confirm the install path works end-to-end with a read-only Code Workflows call:

```bash
zapier-sdk --experimental list-workflows --json
```

Expected output is JSON containing workflow data or an empty list. This command should not create or modify cloud state.

Treat the smoke test as successful only if the JSON has workflow data or an empty workflow list and no errors. If `data` is null, `errors` is non-empty, or the error message says authentication is required, return to Step 5. If it says the command is unknown, return to Step 3 and diagnose the CLI version.

## Step 7: Report Success

Tell the user:

- Zapier SDK CLI is installed and on PATH, confirmed via `which zapier-sdk`.
- Code Workflows experimental commands are available.
- The authenticated Zapier account email from `zapier-sdk get-profile --json`.
- Four sibling skills are installed in `.cursor/skills/`: `cosub-build`, `cosub-list-zaps`, `cosub-show-history`, `cosub-modify-zap`.
- Read-only workflow listing succeeded.

Next steps for the user:

- Configure app connections at https://zapier.com/app/assets/connections before attempting to build workflows.
- Reload the Cursor workspace or restart Cursor so the new skills in `.cursor/skills/` are picked up.
- Ask Cursor to build a CodeZap, for example: "Build me a Zap that takes a manual input and sends a Slack message."

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `node --version` prints less than `v18` | Old Node | `brew upgrade node` or use nvm to install a current LTS |
| `npm install -g` fails with permissions errors | Global npm prefix is not user-writable | Use nvm or Homebrew Node; avoid `sudo npm install -g` unless the user explicitly accepts that system-level change |
| `zapier-sdk --experimental --help` lacks Code Workflows commands | Old CLI or wrong package installed | Install `@zapier/zapier-sdk-cli@latest`, then rerun `zapier-sdk --version` and the help command |
| `zapier-sdk get-profile` says not logged in | User has not authenticated the CLI | Run `zapier-sdk login` in an interactive terminal, then retry |
| `get-profile` succeeds but `list-workflows` returns an access or permission error | The Zapier account may not be allowlisted for Code Substrate EA | Tell the user the SDK install worked, but their Zapier account needs Code Substrate EA access before the smoke test can pass |
| `zapier-sdk login` does not open a browser | No default browser configured, or remote/SSH session | Try `zapier-sdk login --no-browser` if supported by the installed CLI, or run from a local terminal |
| `zapier-sdk login` hangs in a non-interactive shell | `login` is browser-interactive; cannot run unattended | Ask the user to run it manually in an actual terminal |
| `git clone` of `cosub-skills` fails | Temporary source URL is not accessible | Confirm the GitHub repository exists and is public, or set `COSUB_SKILLS_SOURCE_URL` to the correct source URL |
| Skills do not auto-invoke after install | Cursor has not reloaded `.cursor/skills/` | Reload workspace or restart Cursor |
