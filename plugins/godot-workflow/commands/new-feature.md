---
description: 建立新功能資料夾並套用 know how 範本
argument-hint: <feature_name>
---

請建立一個新功能，功能名稱是：$ARGUMENTS

步驟：

1. 功能名稱要是 `snake_case`。如果我給的不是，轉換成 snake_case 並告訴我。
2. 如果 `features/<feature_name>/` 已經存在，停止並告訴我，不要覆蓋。
3. 建立 `features/<feature_name>/`，把 `features/_template/CLAUDE.md` 複製過去，填入功能名稱與今天日期。
4. 在 `docs/GDD.md` 的「功能清單」加一列（狀態：規劃中，對應資料夾填 `features/<feature_name>/`）。如果已經有同名功能，更新那一列，不要重複新增。
5. 問我一句：「這個功能要做什麼？」，把我的回答整理進新資料夾 `CLAUDE.md` 的「用途」。
6. 讀 `docs/GDD.md`，如果這個功能牽涉到「未決定事項」裡的架構問題，提出來問我。
7. 不要自己建立 scene 或腳本，除非我明確要求。
8. 最後告訴我建立了哪些東西。
