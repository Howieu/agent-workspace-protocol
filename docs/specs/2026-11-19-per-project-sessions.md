# 按项目开会话 + 英文流程图

状态：已完成
分支：无（文档项目，直接在 main）
负责人：Howieu
相关：workspace/_shared/AGENT-PROTOCOL.md 第 1 节

## 目标
支持每个项目在工具里有自己的会话分组（如 Claude Desktop 的环境），并让新用户知道一次性怎么配置；英文读者能看到英文流程图。

## 范围
- 协议第 1 节：对话可以在项目目录或工作区根目录开；项目目录里"继续"可省略项目名
- README 中英文：一次性准备里加"每个项目一个环境、关掉工具自带 worktree"；加"长期规则写进项目 AGENTS.md，不靠工具的自动记忆"
- 主流程图起点改为"项目环境或工作区"
- 三张流程图各出英文版，README.en.md 引用英文版

## 不做
- 不把协议正文和模板翻译成英文

## 约束
- README.md 与 README.en.md 内容一致（AGENTS.md）
- 公开仓库：不出现个人路径、姓名、账号

## 验收
- [x] 协议、workspace/AGENTS.md、workspace/README.md 对开对话位置的说法一致 — 核对：`grep -rn "本目录开\|项目目录开" workspace` 三处一致
- [x] 两份 README 段落一一对应 — 核对：对照阅读
- [x] assets/*.en.svg 三张存在、是合法 XML、无中文 — 核对：`python3 -c xml 解析` + `grep -P '[\x{4e00}-\x{9fff}]'`
- [x] 无个人信息 — 核对：`git grep` 本机用户名、本机工作区目录名，无结果

## 任务
- [x] T1 协议与 workspace 文件 — 会动：workspace/
- [x] T2 README 中英文 — 会动：README.md README.en.md
- [x] T3 流程图改起点 + 三张英文版 — 会动：assets/

## 变更记录
- 2026-11-19 创建并确认（Claude Code；用户在对话里要求"都做了"）
- 2026-11-19 完成：三张 .en.svg 经 qlmanage 渲染目检无溢出、XML 解析通过、无中文；git grep 无个人信息
