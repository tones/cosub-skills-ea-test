# Zapier Workflows Early Access: Getting Started

Draft for closed-beta testers using Zapier Workflows from an agent-enabled coding workspace such as Cursor.

## Who This Is For

Zapier Workflows Early Access is currently intended for technically comfortable users who can work in an agentic coding tool and are comfortable with Node/npm, command-line setup, and Zapier app connections.

Access is limited to members of the closed beta. If your account is not allowlisted yet, setup will stop before installing the companion workflow skills.

## Request Access

Fill out the beta sign-up form:

https://next-gen-zaps.zapier.app/

Use the same email address you use to log in to Zapier. Submitting the form does not grant access immediately. The Zapier team will review your request and let you know once access has been granted.

After you are allowlisted, rerun the setup instructions below.

## Set Up Zapier Workflows

Open the Cursor workspace where you want to build Zapier Workflows. Then copy and paste this block into your Cursor agent prompt:

> Install the Zapier Workflows setup skill with:
> `npx skills add zapier/agent-skills --skill workflows-install`
>
> Then run the `workflows-install` skill to set up this workspace.

The `npx skills add ...` command installs the setup skill. It does not run setup by itself. The second sentence tells the Cursor agent to use the installed skill and walk through setup.

## What Setup Does

The `workflows-install` skill should:

1. Check that Node, npm, and git are available.
2. Install or update `@zapier/zapier-sdk-cli@latest`.
3. Verify the Zapier SDK experimental Code Workflows commands are available.
4. Check whether you are logged in to the Zapier SDK CLI.
5. Ask you to run `zapier-sdk login` in a real terminal if browser login is needed.
6. Run a read-only access check with `zapier-sdk --experimental list-workflows --json`.
7. Stop with a clear closed-beta message if your account is not allowlisted.
8. Install the companion workflow skills only after auth and Early Access are confirmed:
   - `workflows-create`
   - `workflows-list`
   - `workflows-history`
   - `workflows-modify`

The setup skill should not ask for your Zapier password, copied auth tokens, npm tokens, API keys, or private registry credentials.

## Before Building Workflows

Configure any app connections you expect your workflow to use:

https://zapier.com/app/assets/connections

When a workflow needs to run a Zapier app action, the agent will use your selected app connections. If a needed connection is missing, create it in Zapier first, then return to Cursor.

## Security Expectations

Zapier Workflows is beta software. Use judgment about which accounts, apps, documents, and connections you make available to an AI agent.

Important cautions:

- Do not paste passwords, API keys, npm tokens, or copied auth tokens into Cursor.
- Be careful with app connections that can access highly sensitive data, such as legal documents, financial records, customer exports, or private company files.
- Review agent actions before approving them, especially actions that send messages, write files, call external APIs, or use app connections.
- Be cautious with auto-approval settings. An agent with access to private data, exposure to untrusted instructions, and the ability to send data elsewhere can create real exfiltration risk.
- If you want a stricter safety boundary, use a separate Zapier account, less-sensitive app connections, or a dedicated test workspace.

The closed beta is intended for trusted early testers, but it should not be treated as a fully hardened general-availability product.

## First Thing To Try

After setup completes and you reload Cursor, try a small workflow with low-risk inputs and outputs before connecting sensitive apps.

Example prompt:

```text
Use the workflows-create skill to create a Zapier workflow that takes a manual input with a name and returns a greeting.
```

After that works, try a simple connected-app workflow using an app connection you are comfortable testing with.

## If Setup Fails

If setup says your account does not have Zapier Workflows EA access:

1. Confirm you used the same email address for Zapier login and the beta sign-up form.
2. Wait until the Zapier team confirms access has been granted.
3. Rerun the setup prompt in Cursor.

Reinstalling Node, npm, git, or the Zapier SDK CLI will not fix a closed-beta access failure.

If setup fails for another reason, include this information when asking for help:

- Your operating system.
- The Cursor workspace path.
- The exact command or step that failed.
- The terminal output from the failed command.
- The email shown by `zapier-sdk get-profile --json`, if login succeeded.
- Whether `zapier-sdk --experimental --help` shows Code Workflows commands.

## Known Limitations

- Zapier Workflows is closed beta; access is allowlist-based.
- The install path verifies setup and read-only workflow access. It does not prove every build, publish, trigger, or app-action path will work.
- Trigger wiring and workflow visibility controls are still early and may not match older internal examples.
- Early Access support is hands-on. Expect the Zapier team to ask for logs, repro steps, and feedback on confusing parts of the setup flow.
