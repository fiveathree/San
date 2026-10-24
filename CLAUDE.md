# 三國亂世錄 — 交接筆記

> 公開 repo：本檔與任何 commit 都不可寫密碼、密鑰、Token、電郵或個人資料。

## 工作守則

- 用戶用廣東話溝通；回覆用廣東話書面口語，先講結果，簡潔。
- **遊戲介面文字用標準書面繁體中文，不可以出現港式粵語。**
- 參考光榮《三國志》4 代至今，主要仿 7 代武將制（在野／武將／太守／主公）。不可使用光榮的名稱、圖像或素材；能力值為自訂。

- **所有頁面一屏過，頁面不可上下或左右捲動（用戶要求）**。`html,body` 及 `#app` 固定 100dvh、`overflow:hidden`；長清單一律用 `listBox()` + `fillList()` 按可用高度分頁（列高必須固定），不可用 `overflow:auto`。新增畫面或對話框時要在手機（375×812）與矮桌面（1024×600）確認。

## 架構

- 純靜態，單一 `index.html`（HTML + CSS + JS），加 `favicon.svg`。沒有 build、沒有後端。
- 資料在 `<script>` 開頭：`CITY_RAW`（名, x, y, 規模, 州）、`ROADS`、`OFF_RAW`（姓名, 統武智政魅, 生, 卒, 出身城）、`SCENARIOS`（君主、城市、部下@駐地）。未列入劇本的武將，16 歲起以在野身分在出身城登場。
- 狀態 `G` 全部可 JSON 化，存 localStorage：`luanshi.auto`（每月自動）、`luanshi.save`（手動）。
- 身分由 `roleOf()` 計算；`canCommand()` 決定可否對城市下令。
- 月結 `endMonth()`：各勢力 `aiForce()`（太守、內政、登用、補人、進攻）→ `economy()` → 日期 → `people()`（逝世、登場、在野遊走）→ `checkForces()`（滅亡、繼承）→ 邀請仕官 → 統一判定。
- 玩家是主公時，電腦只替「委任」的城市做內政，不替玩家出兵。
- 戰鬥 `makeBattle()` → `runBattle()`（`B.ui` 時開戰場畫面，`B.ctrl` 是玩家可操控的部隊）→ `applyBattle()`（城池易手、俘虜、逃走）→ `processCaptives()`。電腦之間的戰鬥不顯示畫面。
- 介面：桌面為地圖 + 右側分頁（行動／城市／記事）；寬度 < 900px 改為底部分頁（地圖／行動／城市／記事）。地圖縮放不足 0.6 倍時改為局部放大並可拖曳（`fitMap()`、`UI.vb`）。所有按鈕由 `app` 上的單一 click 監聽處理（`data-pa`、`data-ca`、`data-oid` 等）。
- 地圖點選：`pickOnMap(title, targets)` 回傳 Promise，地圖顯示紅圈與「取消」橫條（`UI.pickMap`），城市點擊經 `cityClick()`；目前用於移動。
- 登用：`recruitScope()`（主公全國、太守本城＋相鄰）、`targetChance()`（敵將看忠誠、他城 −5%），先揀目標再揀派去的武將，成功後移到下令城市。
- 同伴（在野結伴）：`G.party`（最多 `PARTY_MAX` 人）、`G.ptroops`（義勇兵）。同伴隨玩家移動、每月親密 +2，不會被電腦登用或遊走；`bringParty()` 在求仕／受邀仕官時一同加入，舉兵時亦加入。玩家不再在野時清空。
- 本地預覽：上層 `.claude/launch.json` 的 `san`（port 8141）。

## 未做

外交（同盟、停戰）、計略（離間、流言）、軍團、兵種、天氣地形、事件劇情、自訂武將。
