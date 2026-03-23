# Agent Kernel

Three files that make any AI coding agent stateful. It remembers between sessions, takes notes, and builds on past work.

## How it works

AI coding agents (Claude Code, OpenCode, Codex, Cursor, etc.) start every session with amnesia. They don't remember what happened yesterday.

This repo fixes that. Drop these three files into any project and your agent becomes stateful:

- **`AGENTS.md`** — The kernel. Tells the agent it's stateful, how to manage memory, how to communicate. Fully generic — works with any agent, any domain.
- **`IDENTITY.md`** — Who this specific agent is. Starts blank — the agent asks you on first run.
- **`KNOWLEDGE.md`** — Index of what the agent knows. Starts empty — grows as the agent learns.

The agent stores facts in `knowledge/` and daily session logs in `notes/`. Everything is plain markdown in a git repo. Human-readable, version-controlled, inspectable.

## Quick start

```bash
git clone https://github.com/oguzbilgic/agent-kernel.git my-agent
cd my-agent
```

Then open it with your agent:

```bash
# OpenCode
opencode

# Claude Code
claude

# Codex
codex
```

On the first session, the agent reads `IDENTITY.md`, realizes it has no identity, and asks you who it is. You tell it. It writes that down. Next session, it remembers.

## What happens

First session:
1. Agent reads `AGENTS.md` — learns it's stateful, how memory works
2. Agent reads `IDENTITY.md` — sees it's blank, asks you for name and role
3. Agent updates `IDENTITY.md`, creates first daily note in `notes/`
4. Agent commits and pushes

Every session after:
1. Agent reads `IDENTITY.md` — knows who it is
2. Agent reads `KNOWLEDGE.md` — knows what state files exist
3. Agent reads recent `notes/` — picks up where it left off
4. Agent works, updates knowledge, writes notes, commits

## Memory structure

```
AGENTS.md          ← kernel (generic, don't edit)
IDENTITY.md        ← who this agent is (agent maintains)
KNOWLEDGE.md       ← index of knowledge files (agent maintains)
knowledge/         ← facts about the world (mutable)
notes/             ← daily session logs (append-only)
```

Two kinds of memory:

- **`knowledge/`** — State. Facts about how things are right now. The agent updates these when reality changes.
- **`notes/`** — Narrative. What happened each session — decisions, actions, open items. Append-only. Never modified after the day ends.

## Why this works

AI agents already read `AGENTS.md` (or `CLAUDE.md`, `.cursorrules`, etc.) as project instructions. This kernel uses that mechanism to teach the agent *how to remember*.

The agent doesn't need a database, a vector store, or a custom framework. It just needs:
- A file that says "you are stateful, here's how"
- A git repo to store memory in
- Plain markdown files

## Multiple agents

The kernel is generic. To create a new agent:

```bash
cp -r agent-kernel/ new-agent/
cd new-agent && git init
```

Each agent gets its own repo, its own identity, its own knowledge. The `AGENTS.md` kernel is identical across all of them — only `IDENTITY.md` changes.

## Design

- `AGENTS.md` is the operating system. Generic. Never mentions a specific domain.
- `IDENTITY.md` is swappable. Change it and you have a different agent.
- `KNOWLEDGE.md` is the index. The agent maintains it as knowledge files grow.
- Everything is markdown + git. No dependencies, no frameworks, no databases.

Born from running real agents on a homelab. Tested with investing agents, health agents, infrastructure agents — same kernel, different identities, all stateful.

## License

MIT
