# Installation and account setup

Read this for requests to install/connect the skill, missing CLI setup, or actual authentication, identity, and scope failures. Setup connects the user's own app and account. It cannot bypass tenant policies, browser consent, resource permissions, or the agent host's execution approvals.

## Install this skill from a repository

If this skill is already installed, skip this section. When the user supplies a GitHub repository URL and asks for installation:

1. Inspect the repository's README and `skills/feishu/SKILL.md`. Use the actual repository URL supplied by the user in the commands below.
2. Choose the current agent and installation scope from the user's request. Use user scope for a personal assistant, or project scope when the user asks to share it with a project. Check for an existing `feishu` skill; preserve it until the user has authorized its replacement.
3. With Node.js / npm available, install only this skill for the intended agent:

   ```bash
   # Substitute the actual GitHub owner/repo; choose the current agent.
   npx skills add <owner/repo> --skill feishu --agent codex --global
   npx skills add <owner/repo> --skill feishu --agent claude-code --global
   ```

   Omit `--global` for a project installation. Do not install for other agents merely because they are detected. The Skills CLI is a third-party installer; use the host's normal package-execution approval flow.
4. Alternatively, clone the supplied repository to a user-chosen local directory and copy `skills/feishu/` as a complete folder into a supported discovery location. Current Codex supports `~/.agents/skills/feishu` or a project's `.agents/skills/feishu`; Claude Code supports `~/.claude/skills/feishu` or a project's `.claude/skills/feishu`. Respect an explicitly configured skill location and existing files. Do not copy `.git` or the whole repository into the skill folder.
5. Read the installed `SKILL.md`. If discovery has not refreshed, tell the user to start a new agent session. Continue the authorized CLI setup in the current session using the inspected instructions when possible.

Do not install Node.js, Git, or system packages automatically when absent. Explain the missing prerequisite and obtain the authorization the host requires.

## Inspect and reuse the environment

Locate `lark-cli` and read `--version` and the relevant `--help`. For setup or authentication diagnosis, read:

```bash
lark-cli skills read lark-shared
lark-cli skills read lark-shared references/lark-shared-identity-and-permissions.md
lark-cli auth status --help
lark-cli auth status --json
```

Inspect status privately and summarize only the needed identity/profile information. Do not dump raw configuration or credential files. If multiple profiles could match and the user's choice cannot be inferred, ask which account/app to use. Preserve the selected profile across configuration, login, verification, and business operations using the CLI's supported `--profile` option.

Do not reinstall a working CLI, replace an existing app, switch Feishu/Lark brands, or initiate fresh consent merely because a status field says a token needs refresh. Diagnose actual authentication failures with the current official guide.

## Install the CLI if missing

For an authorized setup request, install the official npm package using a user-writable npm prefix:

```bash
npm install --global @larksuite/cli@latest
lark-cli --version
```

Do not use `sudo` or change system ownership to resolve an installation failure. If the npm prefix is not writable, explain the issue and choose a user-local installation with the user. Use the installed executable's actual path if PATH has not refreshed.

The upstream `npx @larksuite/cli@latest install` wizard can also configure an app, install additional skills, and launch broad authorization. Prefer installing the package alone here so subsequent setup follows the user's selected agent, profile, and task-specific scopes. Do not silently run the full wizard as an equivalent package-only step.

## Configure an app only when necessary

Before creating an app, explain that this will configure the local CLI to connect to the user's own Feishu/Lark application. A request to install and configure the skill authorizes the necessary setup; do not repeatedly ask for the same permission. Respect host approvals for writes outside the workspace and keychain/network access.

For Feishu, the documented custom-app flow requires an enterprise/organization identity. If the user only has a personal identity, explain the requirement to create or join an organization and let the user complete it. Do not create a tenant or claim personal-space resource migration on their behalf. Lark requires its own correct brand/profile and applicable tenant setup.

Read the current setup guide and help:

```bash
lark-cli skills read lark-shared references/lark-shared-config-init.md
lark-cli config init --help
```

For a fresh environment without an existing app, the guided flow is:

```bash
lark-cli config init --new
```

Run it with a host-supported background/session mechanism that lets the user receive its setup URL while the process remains pending. Present that URL unchanged, explain the browser step, and resume the process after completion. If this host cannot retain the process across turns, tell the user how to run this interactive step in their terminal, then verify the resulting setup. Do not leave an invisible blocking process running while the user cannot see its URL.

For a supplied existing app, an Agent-managed app, a named profile, or a Lark-brand setup, follow the installed CLI's documented init/bind/profile options. Do not force a new app, use `--force-init`, overwrite a profile, or ask the user to paste secrets into chat. If credentials must be supplied locally, use the CLI's documented stdin/secure entry mechanism.

Keep authorization/setup URLs opaque: do not decode, shorten, or rebuild their query strings. Present the link directly; add a QR code if the user requests one or their environment requires it. Do not put these URLs, device codes, or credentials in repository files.

## Authorize the task with explicit scopes

App-enabled scopes and user OAuth consent are separate requirements. A valid token or a browser-accessible document does not grant additional app permissions. Administrator approval may be needed before an enabled app scope becomes usable.

1. Derive the smallest sufficient scopes from the requested operation's current guide/schema or a permission error. If the user only asked to connect and gave no first task, ask which capability to enable before requesting business scopes.
2. If an error lists alternative scopes, choose the sufficient least-privileged alternative. Combine independently required missing scopes from the same task into one request. Include `offline_access` when persistent access is needed; explain that it enables refresh-based access without repeated interactive login.
3. Before requesting `im`, `mail`, or `contact` scopes, explain their actual reach and obtain consent unless already explicitly authorized for this scope and purpose. Do not infer consent from a general setup request.
4. Use explicit scopes, not `--domain all`, `--recommend`, a whole business domain, or a parameterless `auth login`. These broad examples in the upstream guide do not apply to this skill's setup.

Use the nonblocking split flow (substitute the exact scope names):

```bash
lark-cli auth login --scope "<required-scope> offline_access" --no-wait --json
```

Present the returned verification URL unchanged and ask the user to complete consent and reply when finished. Keep the device code only in the current authentication flow. **End the turn after displaying the URL.** Do not immediately start blocking device-code polling in the same turn.

After the user confirms completion, resume the same selected profile's flow:

```bash
lark-cli auth login --device-code <device-code>
```

If it expires, initiate a fresh flow for the same intended scopes and present the new URL. Never reuse expired URLs or request broader scopes to fix an expired code. Do not repeatedly prompt for consent when the error requires app/admin approval instead.

## Verify and hand back a working connection

After successful authorization, use the current help to verify the selected profile:

```bash
lark-cli auth status --json --verify
lark-cli whoami --as user
```

Then perform the user's first authorized operation, preferably a read when it satisfies the task. Check the effective identity, result, and relevant warnings. Setup success alone is not proof of content access. Do not send a test message, create a test document, or broaden scopes as an unsolicited connectivity test.

Report what is installed/configured, which task was verified, and any remaining browser/admin step. If sandbox or credential-store access is blocked, use the host's approval mechanism; do not downgrade keychain storage, disable protections, log out, or recreate the app as a workaround.
