---
description: 功能做完後，照完成的定義檢查並同步更新相關文件
argument-hint: <feature_name>
---

功能「$ARGUMENTS」已完成一輪開發，請照 `docs/DEFINITION_OF_DONE.md` 檢查並同步更新文件：

1. 讀 `features/$ARGUMENTS/` 底下目前的 scene、腳本與 `CLAUDE.md`，以及 `docs/DEFINITION_OF_DONE.md`。
2. **驗證**：
   - 執行 `tools/check_scenes.sh res://features/$ARGUMENTS`（必做）
   - 有自動測試的話，執行 `tools/run_tests.sh`
   - 有視覺的話，用 `tools/screenshot.sh` 截圖，自己看過一次
   - 任何一項失敗：停下來告訴我，不要繼續更新文件
3. 更新該功能的 `CLAUDE.md`：「檔案與節點結構」、「對外介面」、「相依」、「驗證」、「已知的坑」要符合現況，更新「最後更新」日期。
4. 寫一份**試玩清單**到「驗證」區，告訴我實際玩的時候要注意什麼。狀態改成「待試玩」，我確認之後才改成「完成」。
5. 更新 `docs/GDD.md` 的功能清單中這個功能的狀態。
6. 如果開發過程有做出取捨（選了 A 沒選 B），在 `docs/DECISIONS.md` 補一行。
7. 檢查 `docs/GDD.md` 的「點子池」與「未決定事項」，如果有因為這個功能而已經有答案的項目，告訴我並建議怎麼處理，先不要自己改。
8. 通常不需要動 root 的 `CLAUDE.md`，除非有新的全域規則。如果需要，先問我。
9. 如果有通用的經驗（不只適用這個 MVP），提醒我考慮回饋到 game-lab。
10. commit 並 push 目前的 branch，不要合併到 `main`。
11. 最後用簡短列表回報：驗證結果、改了哪些檔案、試玩清單。
