# studio-kit

個人遊戲工作室的 Claude Code plugin marketplace。目的是讓 skill 與 command **只維護一份**，所有 MVP 專案都用同一個來源。

## 結構

```
.claude-plugin/marketplace.json        marketplace 定義
plugins/godot-workflow/                 Godot MVP 開發用的 plugin
├─ .claude-plugin/plugin.json
└─ skills/
   └─ studio-kit-check/SKILL.md         測試用：確認 plugin 有沒有載入
```

## 狀態

🧪 實驗中（game-lab 實驗 005）：確認帳號層級的 plugin 能不能同時用在雲端 session 跟本機。
驗證成功之後，才會把 game-lab 的 skill 與 command 搬過來。

## 本機安裝（Claude Code CLI / 桌面版）

```bash
/plugin marketplace add ooops24/studio-kit
/plugin install godot-workflow@studio-kit
```

測試：問 Claude「studio-kit 有沒有載入？」，應該會回覆暗號。
