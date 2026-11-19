# agent-workspace-protocol

[中文](README.md)

A set of plain-file conventions that lets Claude Code, Codex, Cursor and other agents hand off and work in parallel on the same projects: one workspace holds every project; each project has one rules file, one handoff file, and one spec per feature; code changes go through branches; progress is saved after every step.

The protocol and templates are written in Chinese. The diagrams below show the flow. Agents understand the openers in English too (“continue”, “go ahead”, “merge”, “wrap up”).

![Main flow](assets/flow.en.svg)

## Quick start

1. Create a workspace:

```bash
git clone https://github.com/Howieu/agent-workspace-protocol.git /tmp/awp
```

```bash
mkdir -p ~/agent-workspace && cp -R /tmp/awp/workspace/. ~/agent-workspace/
```

```bash
cd ~/agent-workspace && git init && git add -A && git commit -m "init workspace"
```

2. Point each tool at the protocol (use your own workspace path):

```bash
sed "s#<工作区>#$HOME/agent-workspace#g" /tmp/awp/global/CLAUDE.md >> ~/.claude/CLAUDE.md
```

```bash
sed "s#<工作区>#$HOME/agent-workspace#g" /tmp/awp/global/AGENTS.md >> ~/.codex/AGENTS.md
```

```bash
mkdir -p ~/.config/git && echo ".worktrees/" >> ~/.config/git/ignore
```

3. Open a session in `~/agent-workspace` and say "新项目：<name>，要做 <what>" ("new project: <name>, building <what>").

4. Give the project its own session group: in Claude Desktop create an environment whose working folder is `~/agent-workspace/<name>`, with worktree turned off (the agent opens branches inside the project itself). Open every later session for this project there and just say "继续" (continue). Other tools work the same way: open them in the project folder.

## Three openers

| You say | What happens |
| --- | --- |
| 继续 <project> (continue) | Picks up the next step from HANDOFF; inside a project environment just "继续" |
| <project>：<request> | Decides which spec the request belongs to, tells you, then starts; for a new project say "新项目：<name>" |
| 收工 (wrap up) | Optional. Archives the handoff, confirms new long-term rules, gives you the next opener |

You decide at two points only: approve a spec ("做吧", go ahead) and approve a merge ("合并"). Pushes and deploys happen only when you ask.

## Spec routing

![Spec routing](assets/routing.en.svg)

| Case | Handling |
| --- | --- |
| Belongs to an in-progress spec | Add a task to it, continue on its branch |
| Changes a finished feature within its scope | `fix/` branch, note it in that spec's changelog |
| Changes a finished feature's goal or scope | New spec that partly supersedes the old one |
| New capability or new project | New spec |

## Branches and main

![Branches and main](assets/branch.en.svg)

- Code projects: every code change happens on a `feat/<feature>` or `fix/<summary>` branch in its own worktree, so parallel agents never collide
- A branch touches only code and its own spec; HANDOFF is edited on main only
- Document-only projects work directly on main
- Merge requires passing tests, every acceptance item checked with evidence, and your "合并"

## Save as you go

After every subtask the agent commits, checks off the spec's tasks and acceptance items, rewrites HANDOFF's status and next step, and refreshes its "last updated" time in the session table. Closing a session at any moment loses nothing; say "继续 <project>" next time. Session rows idle for more than 2 hours are treated as finished.

For long-term rules, say "以后都……" (from now on…) or "记住……" (remember…); the agent writes them into the project's AGENTS.md. Don't rely on a tool's auto-memory: it is stored per folder, so another environment or another tool won't see it.

## Layout

```
~/agent-workspace/                new projects and cross-project work
├── AGENTS.md · CLAUDE.md         routing rules
├── README.md                     project index
├── _shared/
│   ├── AGENT-PROTOCOL.md         protocol
│   └── templates/                AGENTS.md · HANDOFF.md · spec.md
└── <project>/                    its own git repo; open everyday sessions here
    ├── AGENTS.md · CLAUDE.md     project rules
    ├── .agent/HANDOFF.md         overall progress, main only
    ├── .agent/log/               archived handoffs
    ├── docs/specs/               one spec per feature
    ├── docs/decisions/           decision records
    └── work/ deliverables/ sources/ tmp/
```

Full rules: [workspace/_shared/AGENT-PROTOCOL.md](workspace/_shared/AGENT-PROTOCOL.md).

## Related

- [agents.md](https://github.com/agentsmd/agents.md): the AGENTS.md format
- [OpenSpec](https://github.com/Fission-AI/OpenSpec), [spec-kit](https://github.com/github/spec-kit): fuller spec-driven development toolchains

## License

[MIT](LICENSE)
