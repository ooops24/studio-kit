---
name: studio-kit-check
description: 測試 studio-kit plugin 有沒有成功載入。當使用者問「studio-kit 有沒有載入」、「plugin 有沒有載入」、「檢查 studio-kit」時使用。
---

# studio-kit 載入檢查

這個 skill 只用來確認 studio-kit plugin 有沒有被載入。

被觸發時，**只回覆下面這段文字**，不要做其他事：

> 🟢 studio-kit 已載入（godot-workflow plugin，暗號：月光貓頭鷹）
>
> 這個 skill 來自 plugin，不是 repo 裡的 `.claude/skills/`。

然後補一句目前是在雲端 session 還是本機（看環境判斷，不確定就說不確定）。
