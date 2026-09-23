# Testing AI Coding CLIs: Copilot, Claude Code, Codex

Fuller command reference for the three mainstream terminal-based AI coding agents — install, auth, session management, permissions/sandboxing, MCP, and maintenance. Run from a terminal, in the project folder you want to test in.

**Prerequisite for all three:** Node.js 22+ (`node --version` to check).

> These are the three most widely used agent CLIs right now. Others worth knowing about: **Aider** (open-source, model-agnostic), **Cursor CLI**, **Gemini CLI** (Google). Say the word if you want those added too.

---

## 1. GitHub Copilot CLI

Requires an active GitHub Copilot subscription (individual, or via an org with the CLI policy enabled).

### Install & update
```bash
npm install -g @github/copilot        # install
copilot --version                      # check version
copilot update                         # update to latest
npm uninstall -g @github/copilot       # uninstall
```

### Auth
```bash
copilot login                # OAuth in browser
copilot login --device-code  # device-code flow (headless machines)
copilot login --host <url>   # GitHub Enterprise
export COPILOT_GITHUB_TOKEN="<token>"  # token-based, no interactive login
```

### Running it
```bash
copilot                                    # interactive session
copilot explain "what does this repo do?"  # one-off question
copilot init                               # generate repo custom-instructions file
```

### Inside a session (slash commands)
```
/login            sign in
/resume           reopen a past session
/settings         open settings sidebar
/tasks            open the tasks dialog
/diff             enter diff-review mode
/lsp              manage language servers
```

### Management subcommands
```bash
copilot mcp        # manage MCP server configs
copilot skill       # list/add/remove/enable/disable agent skills
copilot plugin      # manage plugins and marketplaces
copilot instruction  # list discovered custom-instruction sources
copilot lsp          # list configured language servers
copilot help <topic> # billing, config, permissions, sandbox, etc.
copilot completion <shell>  # bash/zsh/fish completion script
```

**MCP (Model Context Protocol)** is an open standard that lets an agent talk to external tools and data sources — a Jira server, a database, a Slack workspace — through one common interface instead of a custom integration per tool. `copilot mcp` registers which MCP servers this agent can reach.

**Plugins/skills** are packaged, reusable instructions or tool bundles a third party (or you) can publish, so the agent doesn't need the same context re-explained every session. They run with the agent's full permissions, so treat installing one like adding any other dependency — check the source first.

---

## 2. Claude Code

Requires Claude Pro, Max, Team, Enterprise, or Console (not the free claude.ai plan), or a third-party provider (Bedrock, Vertex AI, Microsoft Foundry).

### Install & update
```bash
curl -fsSL https://claude.ai/install.sh | bash   # native install (macOS/Linux/WSL), auto-updates
npm install -g @anthropic-ai/claude-code          # alternative: npm
brew install --cask claude-code                   # alternative: Homebrew

claude --version    # check version
claude doctor       # full install/config health check
claude update       # update now (native/npm)
```

### Auth
```bash
claude auth login            # sign in (browser)
claude auth login --console  # console/API-key account
claude auth logout
claude auth status           # JSON status; --text for plain text
claude setup-token            # generate a long-lived OAuth token
```

### Running it
```bash
claude                          # interactive session
claude "explain this project"   # interactive, pre-filled first message
claude -p "explain this project"  # print mode: answer once, then exit
cat file.py | claude -p "review this"  # pipe input in
```

**Print / non-interactive mode** (`-p`) runs the agent once on a single prompt, prints the result, and exits — instead of an open-ended chat. This is what makes it scriptable: pipe input in, capture output to a file, or call it from CI.

### Session management
```bash
claude -c                       # continue most recent session
claude -r "<session-id>"        # resume a specific session
claude --bg "investigate flaky test"  # run as a background agent
claude agents                   # view background sessions
claude attach <id>              # attach to a background session
claude logs <id>                # print a background session's output
claude stop <id>  /  claude kill <id>
claude rm <id>                  # remove a background session
```

A **background session** (`--bg`) keeps the agent working after you move on, like a job running in the background of your terminal — check in later with `agents`/`logs` instead of watching it live. This is what lets you fire off several tasks and walk away.

**Running several agents in parallel on the same repo:** pair background sessions with **git worktrees** — a git feature that lets you check out several working copies of the same repo at once, each on its own branch, without re-cloning. Give each agent its own worktree so two agents editing the "same" repo never touch the same files on disk:
```bash
git worktree add ../task-a -b feature/task-a
git worktree add ../task-b -b feature/task-b
(cd ../task-a && claude --bg "implement task A")
(cd ../task-b && claude --bg "implement task B")
```
Review and merge each branch independently once its agent is done.

### Model, effort, permissions
```bash
claude --model opus                       # pick a model
claude --effort high                      # low/medium/high/xhigh/max
claude --permission-mode plan             # default/acceptEdits/plan/auto/bypassPermissions
claude --dangerously-skip-permissions     # no prompts — sandbox only
claude --allowedTools "Bash(git log *)" "Read"
claude --disallowedTools "Edit" "Bash(rm *)"
claude --add-dir ../apps ../lib           # extra working directories
```

**Effort level** controls how much reasoning the model does before answering, `low` through `max`. Higher effort is slower (and pricier on usage-based plans) but more likely to get a hard problem right first try — save `high`/`max` for gnarly debugging or architecture, `low` for quick fixes.

**Permission mode** controls how much the agent can do without asking first: `plan` proposes a plan before touching anything, `acceptEdits` applies file edits automatically but still asks about commands, `bypassPermissions`/`--dangerously-skip-permissions` asks nothing at all. Reserve the no-prompt modes for a sandboxed or disposable environment — never your real machine with real credentials.

### Non-interactive / scripting
```bash
claude -p "query" --output-format json    # text/json/stream-json
claude -p "query" --max-turns 3
claude -p "query" --max-budget-usd 5.00
```

### MCP & plugins
```bash
claude mcp                  # configure MCP servers
claude mcp login <name>     # OAuth for an MCP server
claude plugin install <name>@<marketplace>
```

Some MCP servers sit behind a service that needs its own sign-in (a Jira or Slack MCP server needs your Jira/Slack account) — `claude mcp login <name>` is that separate step, distinct from logging into Claude Code itself.

### Maintenance
```bash
claude doctor
claude update
claude install stable   # or a specific version number
```

---

## 3. OpenAI Codex CLI

Requires ChatGPT Plus/Pro/Business/Edu/Enterprise, or an OpenAI API key.

### Install & update
```bash
npm install -g @openai/codex     # install
codex --version                  # check version
npm uninstall -g @openai/codex   # uninstall
```

### Auth
```bash
codex login     # OAuth via ChatGPT, device auth, or API key
codex logout    # remove stored credentials
```

### Running it
```bash
codex                                       # interactive session
codex exec "what does this repo do?"        # non-interactive / scripting mode
codex resume                                # resume most recent session
codex resume <session-id>                   # resume a specific session
```

### Model & sandbox/approval
```bash
codex exec -m gpt-5.6-terra "..."           # pick a model
codex exec -s read-only "..."               # sandbox: read-only | workspace-write | danger-full-access
codex exec -a on-request "..."              # ask-for-approval: on-request | never
codex exec --yolo "..."                     # bypass sandbox + approvals entirely (use with care)
codex exec -C ../other-repo "..."           # run against a different working directory
codex exec --image ./screenshot.png "..."   # attach an image
codex exec --json "..."                     # newline-delimited JSON output, for scripting
```

**Sandbox** controls what the agent can touch without asking: `read-only` (look but not change), `workspace-write` (edit and run, confined to the project folder), `danger-full-access` (your whole filesystem, no fence). **Approval mode** controls *when* it pauses to ask: `on-request` (asks before anything risky) vs `never`. `--yolo` turns both off — only for a throwaway environment, never a machine with real credentials on it (this is exactly the failure mode behind real incidents like an agent running `rm -rf ~/` or deleting a production database because nothing was actually stopping it).

### Config & MCP
```bash
codex exec -c model_reasoning_effort=high "..."   # inline config override
# persistent config lives in ~/.codex/config.toml
codex mcp   # list/add/remove/authenticate MCP servers
```

Inside a session: `/model` switches model or reasoning level.

---

## Quick comparison test

Same prompt through all three, from the same repo:

```bash
copilot explain "what does this repo do?" > /tmp/copilot-out.txt
claude -p "what does this repo do?" > /tmp/claude-out.txt
codex exec "what does this repo do?" > /tmp/codex-out.txt

diff /tmp/copilot-out.txt /tmp/claude-out.txt
diff /tmp/claude-out.txt /tmp/codex-out.txt
```

## Sources

- [Installing GitHub Copilot CLI — GitHub Docs](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
- [GitHub Copilot CLI command reference — GitHub Docs](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference)
- [Claude Code Setup — code.claude.com](https://code.claude.com/docs/en/setup)
- [Claude Code CLI Reference — code.claude.com](https://code.claude.com/docs/en/cli-reference)
- [openai/codex — GitHub](https://github.com/openai/codex)
- [Codex developer commands (CLI) — learn.chatgpt.com](https://learn.chatgpt.com/docs/developer-commands?surface=cli)
