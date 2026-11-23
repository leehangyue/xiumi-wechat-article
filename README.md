# 秀米公众号推文 Skill（xiumi-wechat-article）

根据用户给定的推送文本和参考秀米图文样式，在网页端搭建组件、逐块填入内容，保存为可审核的草稿。主题、配色和版式由参考图文决定；必要信息缺失时在正文对应位置标注 `【待补充】`，不中途追问。不自动发布。

## 目录结构

```
xiumi-wechat-article/
├── SKILL.md                        # 输入、排版流程和交付标准
├── references/
│   ├── article-structure.md        # 参考样式解析和组件映射
│   ├── layout-review-checklist.md  # 保存前后验收清单
│   └── xiumi-ui-notes.md           # 秀米编辑器选择器速查、落库规则、实测踩坑
└── scripts/
    ├── cdp.py                      # 极简 CDP 客户端（连接本机 Chrome 调试端口）
    ├── xm.py                       # CDP 公共动作（点击、输入、剪贴板、粘贴）
    ├── layout.py                   # 排版操作台：state / activate / insert / delete-comp / verify / zero-padding
    └── xiumi.py                    # 旧路径（Playwright）：登录、图库上传、封面
```

## 安装

把整个 `xiumi-wechat-article/` 目录放进 Agent 的 Skills 根目录：

```bash
cp -r xiumi-wechat-article ~/.codex/skills/     # Codex
cp -r xiumi-wechat-article ~/.claude/skills/    # Claude Code
```

Windows PowerShell：

```powershell
Copy-Item -Recurse .\xiumi-wechat-article "$env:USERPROFILE\.codex\skills\"
```

重启 Agent 会话后，可说：“按这篇参考图文的样式，把我提供的推送文本排到目标秀米草稿并保存。”

## 依赖

排版主路径只需要两个纯 Python 库，**不需要 Playwright**：

```bash
pip install requests websocket-client
```

只有旧的 `scripts/xiumi.py`（自动登录、图库上传）需要 Playwright，且通常需要提权运行。

## 快速开始

**(1) 准备一个专用 Chrome**（人工登录一次，Cookie 长期有效）：

```bash
chrome.exe --remote-debugging-port=9222 --user-data-dir="<专用目录>" https://xiumi.us/
```

**(2) 用 `layout.py` 操作编辑器**（`--draft` 为秀米草稿 ID）：

```bash
python scripts/layout.py --draft <草稿ID> state
python scripts/layout.py --draft <草稿ID> insert --cell <索引> --html-file <片段路径>
```

操作前先阅读 [技能流程](SKILL.md) 与 [编辑器操作笔记](references/xiumi-ui-notes.md)。完成后按 [验收清单](references/layout-review-checklist.md) 检查保存状态、组件结构与实际排版。

## License

MIT
