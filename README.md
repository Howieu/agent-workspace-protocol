# agent-workspace-protocol

[English](README.en.md)

一套放在本地文件夹里的约定，让 Claude Code、Codex、Cursor 等多个 agent 在同一批项目上接力和并行干活：一个工作区放所有项目，每个项目一份规则、一份交接、一个功能一份 spec，改代码走分支，进度随做随存。

![项目主流程](assets/flow.svg)

## 快速开始

1. 建工作区：

```bash
git clone https://github.com/Howieu/agent-workspace-protocol.git /tmp/awp
```

```bash
mkdir -p ~/agent-workspace && cp -R /tmp/awp/workspace/. ~/agent-workspace/
```

```bash
cd ~/agent-workspace && git init && git add -A && git commit -m "init workspace"
```

2. 让各工具认这套协议（路径换成你的工作区）：

```bash
sed "s#<工作区>#$HOME/agent-workspace#g" /tmp/awp/global/CLAUDE.md >> ~/.claude/CLAUDE.md
```

```bash
sed "s#<工作区>#$HOME/agent-workspace#g" /tmp/awp/global/AGENTS.md >> ~/.codex/AGENTS.md
```

```bash
mkdir -p ~/.config/git && echo ".worktrees/" >> ~/.config/git/ignore
```

3. 在 `~/agent-workspace` 开对话，说"新项目：<名字>，要做 <什么>"。

## 三句开场白

| 你说 | 结果 |
| --- | --- |
| 继续 <项目> | 按 HANDOFF 的下一步接着做 |
| <项目>：<要什么> | 先判断归哪份 spec，告诉你结论再动手；新项目说"新项目：<名字>" |
| 收工 | 可选。归档交接、确认长期规则、给下次的开场白 |

你只在两处拍板：确认 spec 时说"做吧"，验收后说"合并"。推送和部署只在你当次要求时做。

## spec 分流

![spec 分流](assets/routing.svg)

## 分支与 main

![分支与 main](assets/branch.svg)

- 有代码的项目：改代码一律在 `feat/<功能>` 或 `fix/<简述>` 分支，放在独立的 worktree，多个 agent 并行互不干扰
- 分支上只改代码和本功能那份 spec；HANDOFF 只在 main 改
- 只有文档的项目直接在 main 上改
- 合并的条件：测试通过、验收项全部勾完并附证据、你说"合并"

## 随做随存

每完成一个子任务，agent 立刻提交代码、勾掉 spec 里的任务和验收项、重写 HANDOFF 的现状和下一步、更新会话表里自己那行的"最后更新"。任何时候关掉对话都不丢进度，下次说"继续 <项目>"就接上。超过 2 小时没更新的会话行，下一个 agent 当作已结束删掉。

## 目录

```
~/agent-workspace/                对话永远在这里开
├── AGENTS.md · CLAUDE.md         路由规则
├── README.md                     项目索引
├── _shared/
│   ├── AGENT-PROTOCOL.md         协议
│   └── templates/                AGENTS.md · HANDOFF.md · spec.md
└── <项目>/                       每个项目一个独立 git 仓库
    ├── AGENTS.md · CLAUDE.md     项目规则
    ├── .agent/HANDOFF.md         全局进度，只在 main
    ├── .agent/log/               旧交接归档
    ├── docs/specs/               一个功能一份 spec
    ├── docs/decisions/           决策记录
    └── work/ deliverables/ sources/ tmp/
```

| 信息 | 位置 |
| --- | --- |
| 长期规则、命令、禁区 | 项目 AGENTS.md |
| 哪些 spec 在做、在哪个分支、下一步、待拍板 | 项目 .agent/HANDOFF.md |
| 功能做成什么样、怎么验收、做到哪一步 | 项目 docs/specs/ |
| 方向性决定及理由 | 项目 docs/decisions/ |
| 跨项目通用的规矩 | _shared/AGENT-PROTOCOL.md |

逐条规则见 [workspace/_shared/AGENT-PROTOCOL.md](workspace/_shared/AGENT-PROTOCOL.md)。

## 相关项目

- [agents.md](https://github.com/agentsmd/agents.md)：AGENTS.md 文件格式
- [OpenSpec](https://github.com/Fission-AI/OpenSpec)、[spec-kit](https://github.com/github/spec-kit)：更完整的规格驱动开发工具链

## License

[MIT](LICENSE)
