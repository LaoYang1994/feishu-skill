# Feishu Skill

An English [Agent Skill](https://agentskills.io) for **Codex and Claude Code** to set up and use Feishu / Lark through the official [lark-cli](https://github.com/larksuite/cli).

Give your agent this repository URL and ask it to install and configure the skill. It can install the CLI, guide app setup, request the permissions needed for your task, and then work with your documents, wiki, spreadsheets, Base, Drive, calendar, tasks, meetings, and messages. You complete account login and consent in your browser.

This is a community-maintained skill. Each user connects their own app and account; the repository contains no shared credentials or hosted service.

## Install from GitHub

Replace `YOUR_GITHUB_USERNAME/feishu-skill` below with this repository's GitHub owner and name after publication.

### Ask your agent

```text
Install the feishu skill from https://github.com/YOUR_GITHUB_USERNAME/feishu-skill
for this coding agent. Read skills/feishu/SKILL.md and its setup reference.
Set up Feishu using my existing CLI configuration if available, then help me
read this document: <my document URL>. Guide me through any browser steps.
```

The installation and configuration instructions for the agent are in [skills/feishu/references/setup.md](skills/feishu/references/setup.md), including a direct Git clone alternative if the skill installer is unavailable. Inspect the repository before asking an agent to execute its setup instructions.

### Or install with the Skills CLI

Requires Node.js / npm and Git. Install for either agent:

```bash
# Codex
npx skills add YOUR_GITHUB_USERNAME/feishu-skill --skill feishu --agent codex --global

# Claude Code
npx skills add YOUR_GITHUB_USERNAME/feishu-skill --skill feishu --agent claude-code --global
```

After installation, ask Codex to `Use $feishu to set up Feishu and read <URL>`, or run `/feishu` in Claude Code with the same request. If the skill does not appear, start a new agent session.

Installing the skill alone does not install the CLI or grant account permissions. Ask the agent to complete setup, or follow the [setup reference](skills/feishu/references/setup.md). An existing skill named `feishu` must be reviewed before replacement.

## Can I use a personal Feishu account?

**Individual developers can use this skill, but a personal-only Feishu identity is not sufficient for the documented custom-app setup.** Feishu's Open Platform documentation requires a user to create or join an enterprise / organization before developing an app. You can create your own organization for development; belonging to an employer's paid organization is not the stated prerequisite. Actual features, approval requirements, quotas, and API access depend on the tenant and account.

| Account / environment | What to expect |
| --- | --- |
| Individual with a Feishu organization and permission to create an app | Can follow the custom-app setup, subject to app scopes and resource access. |
| Feishu personal identity with no organization | Must first create or join an organization for the documented setup. Do not assume existing personal-space documents become accessible after switching identity. |
| Member of an organization's Feishu tenant | Can use an app available to that user; app creation or new permissions may require an administrator. |
| Lark account | The CLI supports Lark. Use the correct Lark brand/profile and its tenant's app setup; this repository has no separate live Lark-account verification. |

`--as user` means **act as the signed-in user**, not “enable personal-edition APIs.” Access still depends on the app's enabled scopes, the user's OAuth consent, application availability, and the user's access to the target resource. A link being viewable in a browser does not prove that this app can access it through the API.

Sources, reviewed on 2026-10-19:

- [Official CLI: personal developers and enterprise integration](https://github.com/larksuite/cli#personal-or-enterprise).
- [Feishu custom-app development process](https://open.feishu.cn/document/home/introduction-to-custom-app-development/self-built-application-development-process), especially the requirement to become an enterprise user.
- [Codex skill format and discovery](https://developers.openai.com/codex/skills).
- [Claude Code skills](https://code.claude.com/docs/en/skills).
- [Skills CLI installation and supported agents](https://github.com/vercel-labs/skills).

## How it works

- Uses `lark-cli` from the user's environment, with explicit user identity for supported business commands.
- Reads the CLI's embedded official guides on demand, so API details track the installed CLI version.
- Uses explicit, task-specific OAuth scopes rather than requesting every business domain.
- Reuses existing profiles and the CLI's credential storage. Setup permission does not authorize sending messages, deleting resources, or changing sharing settings.
- Preserves comments and resource references during document edits, then reads changes back to verify them.

The skill itself has no runtime dependencies. Operating Feishu requires the official CLI, network access, a supported credential store, and a configured app/account. If Node.js or Git is missing, the agent should explain the required prerequisite rather than install system tools without authorization.

## Package layout

```text
skills/feishu/
├── SKILL.md                      # Shared instructions for both agents
├── agents/openai.yaml            # Codex display metadata
└── references/
    ├── setup.md                  # Installation, app setup, OAuth, verification
    └── document-editing.md        # Document-editing guidance and observed pitfalls
```

This ships as a standard skill, with no MCP server or agent-specific plugin runtime.

## Verification

Commands follow the installed CLI's current help and embedded guides. Verify writes by reading back the result; the [editing notes](skills/feishu/references/document-editing.md) provide historical observations for troubleshooting.

Skill discovery and installation into isolated Codex and Claude Code project directories were verified with the Skills CLI. Skill-format validation, local links, and local CLI help were also checked. A fresh-account connection, a personal-only account, live Lark access, and the full browser authorization flow were not tested end to end for this release. Do not treat the compatibility table as a claim of those tests.

## License

[MIT](LICENSE). The official CLI is a separate project under its own license. Use of Feishu / Lark APIs remains subject to their platform terms.
