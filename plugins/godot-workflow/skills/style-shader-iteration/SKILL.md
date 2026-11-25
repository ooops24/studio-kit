---
name: style-shader-iteration
description: 在 Godot 裡把一個美術風格（參考圖、影片、文字描述）做成 shader 與整體 look-dev，並用雲端截圖自我迭代。只要使用者丟出參考圖、提到某個作品的畫風（例如 Arcane / 奧術、吉卜力、水彩、手繪、賽璐璐、NPR、toon、painterly）、說「我想要這種感覺」、「做一個 XX 風 shader」、「跟參考圖差太多」、「幫我調這個 shader」，或要做後處理、光影氛圍、材質風格化，都要使用這個 skill，即使使用者沒有明講 shader。Use for any stylized shader / look-dev request in Godot, including follow-ups that tweak an existing style shader.
---

# 風格 Shader 迭代

把「我想要這種畫風」變成 Godot 裡可以用的 shader，用「拆解 → 實作 → 截圖 → 自我檢查 → 交給人判斷」的循環推進，每一輪都留下筆記。

使用者是 TA（technical artist），懂 shader 也懂美術，但對 AI 工作流還在摸索。最在意的是**美術上像不像**，以及**能不能自己在 Inspector 裡接手調整**。

## 先判斷在哪種 repo

這個 skill 來自 studio-kit plugin，會用在兩種 repo，資料放的位置不同：

| | **game-lab**（有 `experiments/`、`notes/`） | **MVP**（有 `features/`，沒有 `experiments/`） |
|---|---|---|
| 實驗放哪 | `experiments/NNN_主題/` | `features/<feature_name>/`（還沒有就先用 `/godot-workflow:new-feature` 建立） |
| 筆記寫哪 | `notes/YYYY-MM-DD_主題.md` + README 實驗紀錄表 | 該功能的 `CLAUDE.md`（「已知的坑」、「驗證」）+ `docs/DECISIONS.md` |
| 截圖存哪 | `notes/img/` | 先放 scratchpad，需要長期保留的放 `features/<name>/screenshots/` |
| 測試模型 | `assets/test_models/` | 用專案自己的模型 |

下面寫「實驗資料夾」、「筆記」的地方，都照這張表對應。

## 0. 開工前先問問題

使用者的習慣是「先確認再動手」，不要直接開做。先問 2–3 個最關鍵的問題，等回答之後再開始，通常是：

- **參考來源**：參考圖、影片、作品名稱是什麼？如果是教學影片，有沒有標題或說明欄文字？（雲端環境連不到 YouTube，只能靠截圖跟文字）
- **要套在什麼上**：角色、場景、道具？手邊有沒有適合的模型？
- **範圍**：只要材質，還是包含墨線、後處理、光影、環境？

如果是「接續上次的實驗」，先讀最新的相關筆記（game-lab：`notes/`；MVP：該功能的 `CLAUDE.md`），問題可以少問一點，只問這次要改的方向。

## 1. 拆解參考圖

把風格拆成元素，用表格列出來給使用者看。**一定要包含「shader 能不能補」這欄**，誠實說出哪些差距跟 shader 無關：

| 風格元素 | 參考圖的樣子 | 技術做法 | shader 能補嗎？ |
|---|---|---|---|
| 例：明暗交界 | 筆刷撕裂的邊緣、乾刷顆粒 | 筆觸貼圖扭曲 NdotL + 細 noise | ✅ |
| 例：形體細節 | 雕刻級肌肉 | — | ❌ 要換模型 |

常見的「非 shader」因素：模型細節、貼圖品質、光的方向、鏡頭構圖、背景顏色。這些常常比 shader 本身影響更大，早點講出來可以避免使用者期待落空。

能推測技法就說出來（例如「看起來像 Blender 的 Shader to RGB → Color Ramp」），並對應到 Godot 的做法。

## 2. 選測試素材

- 先用基本形狀（球、方塊、圓環）確認 shader 會動
- **很快就換成真實模型**，基本形狀太好看，會讓人誤以為效果已經到位
- game-lab 的現有素材：`assets/test_models/`（用 `tools/fetch_test_models.sh` 下載；FlightHelmet 有貼圖、細節最多，最適合當測試對象）
- 已知問題先看 `shaders/<name>/CLAUDE.md`（例如骨架模型的 inverted hull 墨線會壞）

## 3. 建立實驗

- 新風格或大改版：開新的實驗資料夾（game-lab：`experiments/NNN_主題/`，編號接續 README 的實驗紀錄；MVP：在功能資料夾裡）
- 小調整：在同一個實驗裡做，但**舊版 shader 保留不刪**，新版另存檔案，才能並排比較
- 可以重用的東西先看 `tools/`：
  - `tools/painterly_apply.gd`：把匯入模型的材質全部換成指定 shader，保留原本的貼圖，可以烘焙平滑法線給墨線用
  - `tools/gen_brush_texture.py`：產生可無縫拼接的筆觸貼圖
- 共用的 shader 在 `shaders/`（例如 `shaders/painterly/`），先讀該資料夾的 `CLAUDE.md`
- 驗證過、可以跨專案用的 shader，提醒使用者考慮「畢業」到 `shaders/`

### Shader 寫法慣例

使用者會自己在 Inspector 裡接手調參數，所以：

- 用 `group_uniforms` 把參數分組（例如 Color / Bands / Strokes / Outline）
- 每個 uniform 加上**中文註解**，說明它控制什麼，必要時加上 `hint_range`
- 顏色類參數用 `source_color`
- 色階用 `GradientTexture1D`（`.tres`），使用者可以像在 Blender 調 Color Ramp 一樣拉顏色
- 檔案開頭用幾行註解說明這個 shader 的原理

## 4. 截圖

```bash
tools/install_godot.sh        # 如果 godot 指令不存在
tools/screenshot.sh res://<實驗資料夾>/scene.tscn 輸出.png 1280x720 forward_plus 60
tools/check_scenes.sh res://<實驗資料夾>   # 確認場景能載入（不會編譯 shader，shader 要靠截圖）
python3 tools/compare_images.py compare.png old.png:v1 new.png:v2 --crop 340,60,940,660
```

- 截圖先存在 scratchpad，要保留的才複製到筆記用的圖片資料夾
- 每一輪都**跟上一版並排比較**，看出進步或退步
- 預設用**側光**，正面光會讓暗部消失，看不出風格
- 鏡頭跟光在同一輪比較中保持一致，不然比的不是 shader
- 雲端是 CPU 軟體渲染，960x540 一張大約 10–20 秒；太重的場景（GI、大量粒子）可能超時，可以改用 `gl_compatibility` 或降解析度

## 5. 自我檢查（最多 3 輪）

每張截圖都用 Read 工具打開，**實際看過**再下判斷。對照參考圖檢查：

- **光**：光向能不能看出風格？暗部的面積夠不夠？
- **明暗**：整體對比夠不夠？暗部會不會跟背景混在一起？
- **顏色**：飽和度、暗部偏色是否接近參考？有沒有東西搶走主體（例如地板）？
- **紋理 / 筆觸**：尺度對不對？看起來像筆觸，還是像雲狀 noise、像迷彩？
- **墨線**（如果有）：有沒有斷掉、穿插、粗細合不合理？
- **構圖**：主體有沒有被切到、是否在畫面中央？

找出最明顯的 1–3 個問題，修正後重拍。**自己最多跑 3 輪**，然後一定要停下來交給使用者。原因是 Claude 擅長抓「明顯的問題」，但「像不像那個畫風」需要使用者的美術眼光，繼續自己跑下去容易往錯的方向越調越遠。

## 6. 交給使用者

用 SendUserFile 傳**並排比較圖**，然後說明：

- 這幾輪改了什麼、為什麼改
- 現在跟參考圖還差在哪（誠實說，包含 shader 補不了的部分）
- **一個具體的問題**，讓使用者容易回答，例如「你覺得離參考圖最遠的是色塊大小、顏色，還是筆觸方向？」
- 回家可以在 Inspector 裡調哪幾個參數最有感

## 7. 卡關時的 Debug 階梯

照順序一步一步縮小範圍，每一步都截圖確認：

1. **誇張化**：把參數加大 10–100 倍（例如墨線寬度），確認效果到底有沒有出現
2. **換渲染器**：`forward_plus` ↔ `gl_compatibility`，判斷是 shader 邏輯的問題還是渲染器的問題
3. **除錯顏色**：輸出純色（例如紅色），關掉 culling，看幾何形狀跟位置
4. **隔離**：只留一個物件、一個效果，排除其他干擾

**停損**：如果問題要深入引擎內部才能解（例如骨架 skinning 的細節），就停下來，把症狀、試過的方法、猜測、下次可以試的方向寫進筆記，跟使用者說明後先做別的。花的時間不成比例的話，記錄下來比硬解更有價值。

## 8. 寫筆記與提交

每個實驗結束（或交給使用者之前）都要寫筆記，這是使用者最重視的產出之一。

**在 MVP 裡**：把發現寫進該功能 `CLAUDE.md` 的「已知的坑」與「驗證」，取捨寫進 `docs/DECISIONS.md`，收尾用 `/godot-workflow:wrap-feature`。發現通用的經驗時，提醒使用者考慮回饋到 game-lab。

**在 game-lab 裡**：

- 檔名：`notes/YYYY-MM-DD_主題.md`，同主題有新進展就**開新日期的檔案**，不改舊的，開頭用「接續」連到上一份
- 圖片：`notes/img/YYYY-MM-DD_描述.png`
- 固定欄位，照下面的模板
- 更新 `README.md` 的實驗紀錄表（結果用 ✅ / 🟡 / 🟢 / ❌）
- 新的通用工具放進 `tools/`，並在 README 的常用指令補上

**兩邊都一樣**：在 branch 上 commit 並 push，不要自己合併到 `main`（照 repo 的 `CLAUDE.md` 工作規則）

```markdown
# YYYY-MM-DD 主題

**實驗資料夾**：`experiments/NNN_xxx/`
**接續**：[上一份筆記](檔名.md)（如果有）
**結論**：🟢/🟡/❌ 一句話結論

## 想驗證什麼
## 怎麼做
（風格元素 → 技術做法 的表格）
## 結果
（每一版的截圖，加一句說明）
## 發現 / 卡關的地方
## 下一步
```

筆記要寫**發現跟教訓**，不只是做了什麼。例如「筆觸貼圖 900 筆太碎，320 筆才像顏料」這種具體數字，下次才用得上。
