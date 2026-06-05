# Code Substrate Early Access Skills

Temporary public test bundle for Code Substrate Early Access setup in Cursor.

This repository is intended for a clean-machine install test. The current path is:

1. Open a fresh Cursor workspace.
2. Give Cursor the contents of `cosub-install/SKILL.md`.
3. Ask Cursor to set up Code Substrate Early Access.
4. Follow any browser login prompt from `zapier-sdk login`.
5. Reload Cursor after the install skill copies the companion skills into `.cursor/skills/`.

## What It Installs

- `@zapier/zapier-sdk-cli@latest`, which provides the `zapier-sdk` CLI.
- The companion `cosub-*` skills:
  - `cosub-build`
  - `cosub-list-zaps`
  - `cosub-show-history`
  - `cosub-modify-zap`

## Requirements

- Node 18 or newer.
- npm.
- git.
- A Zapier account with access to Code Workflows / Code Substrate Early Access.
- Cursor with project skills support.

## Caveats

This is an Early Access test bundle. Trigger wiring and workflow visibility controls are not fully documented in v1 because the verified SDK CLI surface does not expose the old trigger-publishing or private-workflow flags.

Do not put secrets, API keys, or npm tokens in this repo.
