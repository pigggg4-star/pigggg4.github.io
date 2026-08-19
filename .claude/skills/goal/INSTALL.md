# goal — 安裝說明

來源：<https://github.com/jthack/claude-goal>（MIT License，作者 Joseph Thacker，見 `LICENSE`）

## 這個 repo 裡的安裝方式（已完成）

技能已 vendor 到專案層級，跟著 repo 走，任何在這個 repo 開的 Claude Code session
（含 claude.ai/code 網頁版）都會自動載入：

```text
.claude/skills/goal/SKILL.md                  技能本體
.claude/skills/goal/scripts/claude_goal.py    狀態管理腳本（無外部相依，只用標準庫）
.claude/skills/goal/references/               上游參考筆記
.claude/skills/goal/tests/                    上游測試（python3 -m pytest .claude/skills/goal/tests）
.claude/settings.json                         Stop hook：目標進行中時擋下停止
```

## 與上游的差異

上游 `install.sh` 是把 `goal/` symlink 到 `~/.claude/skills/goal`（使用者層級），
腳本裡的提示文字因此寫死了 `~/.claude/skills/goal/...` 路徑。改成專案層級安裝後做了兩處調整：

1. `SKILL.md`：執行指令改成先解析路徑，專案層級與使用者層級兩種安裝都能用。
2. `scripts/claude_goal.py`：新增 `SCRIPT_PATH = str(Path(__file__).resolve())`，
   並在 `CONTINUATION_INSTRUCTIONS` / `STOP_HOOK_REASON` 定義後把寫死的
   `~/.claude/skills/goal/scripts/claude_goal.py` 換成實際腳本路徑。

上游 9 項測試在修改後全部通過。

## 用法

```text
/goal 找出並修好會閃退的登入測試
/goal --tokens 250K 做完整研究並寫出原型
/goal            顯示目前目標與續作指示
/goal status     查看狀態
/goal pause      暫停（Stop hook 不再擋）
/goal resume     恢復
/goal clear      刪除目標
/goal complete   完成（需先通過完成度稽核）
```

狀態存在 `~/.claude/goal/goals.sqlite`，可用 `CLAUDE_GOAL_HOME` 或 `CLAUDE_GOAL_DB` 覆寫。
Stop hook 預設最多自動續作 500 次，用 `CLAUDE_GOAL_MAX_STOP_CONTINUES` 調整。

注意：遠端／網頁 session 的容器是暫時性的，`~/.claude/goal/goals.sqlite` 不會跨 session 保留；
在本機用 Claude Code CLI 才會累積長期狀態。

## 若要改裝在使用者層級（所有專案共用）

在自己的機器上跑上游安裝腳本即可，兩種安裝可並存（`SKILL.md` 會優先用專案層級那份）：

```bash
git clone https://github.com/jthack/claude-goal.git
cd claude-goal && ./install.sh
```
