# studio-kit

個人遊戲工作室的 Claude Code plugin marketplace。目的是讓 skill 與 command **只維護一份**，所有 MVP 專案都用同一個來源。

## 結構

```
.claude-plugin/marketplace.json        marketplace 定義
plugins/godot-workflow/                 Godot MVP 開發用的 plugin
├─ .claude-plugin/plugin.json
├─ commands/
│  ├─ new-feature.md                    /godot-workflow:new-feature <名稱>
│  └─ wrap-feature.md                   /godot-workflow:wrap-feature <名稱>
└─ skills/
   ├─ style-shader-iteration/SKILL.md   風格 shader 迭代（自動觸發）
   └─ studio-kit-check/SKILL.md         測試用：確認 plugin 有沒有載入
```

## 怎麼在專案裡使用

在專案的 `.claude/settings.json`：

```json
{
  "extraKnownMarketplaces": {
    "studio-kit": { "source": { "source": "github", "repo": "ooops24/studio-kit" } }
  },
  "enabledPlugins": { "godot-workflow@studio-kit": true }
}
```

- 雲端 session：開機時自動安裝（game-lab 實驗 005 驗證過）
- 新的 private repo 要先授權給 Claude GitHub App
- `plugin.json` 不寫 `version`，push 到 `main` 就是新版

## 修改 skill / command

改完先在 branch 上測試，確認沒問題再合併到 `main`。**`main` 上的內容會直接影響所有使用這個 plugin 的專案。**

## 狀態

✅ 雲端 session 已驗證（game-lab 實驗 005）。⏳ 本機待驗證。

## 本機安裝（Claude Code CLI / 桌面版）

```bash
/plugin marketplace add ooops24/studio-kit
/plugin install godot-workflow@studio-kit
```

測試：問 Claude「studio-kit 有沒有載入？」，應該會回覆暗號。
