<!-- Generated from private documentation source. Do not edit directly. Source SHA256: 298812575d603489bd95353b053ffbed60bc5923ac9e486ff3baa100a91970db -->

# kimi-skill

Claude Code skills for delegating work to MoonshotAI's [Kimi Code CLI](https://github.com/MoonshotAI/kimi-code). Ask Kimi to review your changes or take on a coding task without leaving your Claude Code session.

| Skill | What it does |
|---|---|
| `/kimi-review` | Runs a Kimi code review of your working tree or branch diff and presents the findings verbatim |
| `/kimi-task` | Delegates a coding task to Kimi, then reports its output and what files changed |
| `/kimi-setup` | Checks that the Kimi CLI is installed and authenticated, with install/login guidance |

## Prerequisites

- The Kimi Code CLI installed and logged in (`/kimi-setup` will walk you through it):
  - `npm install -g @moonshot-ai/kimi-code` (Node >= 22.19), or
  - `curl -fsSL https://code.kimi.com/kimi-code/install.sh | bash`
  - then `kimi login`

## Install

Skills go in `~/.claude/skills/` (available in every project) or a project's `.claude/skills/` (that project only).

```bash
git clone https://github.com/nicodes/kimi-skill.git
cd kimi-skill
python3 scripts/prepare-runtime.py

# personal — every project
ln -s "$PWD"/skills/* ~/.claude/skills/

# or project-scoped — copy into a repo
cp -r skills/* /path/to/project/.claude/skills/
```

Start a new Claude Code session and the `/kimi-review`, `/kimi-task`, and `/kimi-setup` commands are available.

## Usage

```
/kimi-review                          # review uncommitted changes (or branch diff if tree is clean)
/kimi-review --base origin/main       # review this branch against a base ref
/kimi-review --background focus on error handling
/kimi-task add input validation to the signup form
/kimi-task --background -m kimi-code/kimi-for-coding refactor the auth module
/kimi-task --continue also update the tests
/kimi-setup
```

Reviews and tasks can run in the background — Claude Code notifies you when the result is ready, and you can keep working in the meantime.

## A note on read-only reviews

Headless Kimi (`kimi -p`) auto-approves its own tool calls and has no read-only sandbox. `/kimi-review` instructs Kimi not to modify anything and verifies afterwards that the working tree is untouched, warning you loudly if it isn't — but that is a prompt-level contract, not an enforced sandbox. For hard enforcement, add deny rules for write tools in `~/.kimi-code/config.toml`.

## Configuration

The skills use your local Kimi CLI as-is: its auth, model defaults, and permission rules all come from `~/.kimi-code/config.toml`. Pick a model per-run with `/kimi-task -m <alias>`. A custom endpoint can be configured entirely from the environment via `KIMI_MODEL_NAME` / `KIMI_MODEL_API_KEY` / `KIMI_MODEL_BASE_URL`.

## Prepare runtime files

After cloning this repository, run the following with Python 3 before loading its skills, agents, or plugins:

```sh
python3 scripts/prepare-runtime.py
```

This creates the native discovery files from the tracked functional inputs. Run it again after pulling an update; if a generated file has local edits, preserve those edits before replacing it.
