# A-Tokyo-Xtreme-Racer-Additional-Settings-Mod-
東京極限賽車的其他設置，包括HDR、VSM、串流媒體、天氣、時間等等
# 快捷鍵使用說明（README）

簡介
---
本 README 彙整 main.lua 腳本中已註冊的快捷鍵與使用說明（中文）。內容以實務流程與注意事項為主，方便直接貼到專案說明或發給玩家/測試人員。

目錄
---
- 快捷鍵總覽
- 使用範例（精準定位、凍結、恢復）
- UDS 時間格式說明
- 注意事項與建議綁定
- 常見問題（FAQ）
- 授權
- Credits
- 範例輸出（TXRLog）

快捷鍵（總覽）
---
以下表格列出所有在腳本中已註冊的快捷鍵、功能與中文說明。

| 快捷鍵 | 功能（中文） | 作用 / 備註 |
|---:|---|---|
| M | 顯示：TXR TIME MONITOR（時間監控） | 顯示系統實時與遊戲時間（HH:MM），優先使用 `_G.TXR_CURRENT_TOD`，找不到則讀 Actor。 |
| Alt + 3 | 顯示：TXR WEATHER: MONITOR（天氣預報監控） | 顯示目前樣式、下一個預設、預定觸發時間（HH:MM）與倒數分鐘。 |
| U | 系統重置 / 恢復（TXR SYSTEM RECOVERY） | 關閉手動控制，恢復時間動畫與 Weather 的 Manual Override（回到自動模式）。 |
| Alt + Y | 時間 +10 UDS（約 +6 分鐘） | 直接修改 TOD 並啟用手動控制（暫停自動排程）。 |
| Shift + Y | 時間 +100 UDS（約 +60 分鐘 / +1 小時） | 同上（大幅增加）。 |
| Alt + T | 時間 -10 UDS（約 -6 分鐘） | 啟用手動控制。 |
| Shift + T | 時間 -100 UDS（約 -60 分鐘 / -1 小時） | 啟用手動控制。 |
| Alt + P | 霧 +0.5 | 修改 Weather['Fog'] 並設為 Manual Override（手動覆蓋），會啟用手動控制。 |
| Shift + P | 霧 +2.0（較大幅） | 同上（大幅增加）。 |
| Alt + O | 霧 -0.5 | 減少霧並啟用手動控制。 |
| Shift + O | 霧 -2.0（較大幅） | 同上（大幅減少）。 |
| Alt + L | 雲 +0.5 | 修改 Weather['Cloud Coverage'] 並設為 Manual Override，會啟用手動控制。 |
| Shift + L | 雲 +2.0（較大幅） | 同上（大幅增加）。 |
| Alt + K | 雲 -0.5 | 減少雲並啟用手動控制。 |
| Shift + K | 雲 -2.0（較大幅） | 同上（大幅減少）。 |
| Alt + F | 時間凍結開關（TXR TIME FREEZE） | 切換 `Animate Time of Day`（播放 ↔ 暫停），不改 TOD。 |
| Alt + D | 快速跳轉到早晨（預設 07:30） | 呼叫 txr_fast_snap(730.0)，直接設 TOD 並強制刷新光影。 |
| Alt + N | 快速跳轉到黃昏（預設 17:30） | 呼叫 txr_fast_snap(1730.0)，直接設 TOD 並強制刷新光影。 |

使用範例（常見操作流程）
---
- 精準跳到 07:30（建議流程）
  1. Alt + F（凍結時間，避免跳動）  
  2. Alt + D（跳至 07:30）  
  3. 必要時用 Alt/Shift + Y/T 微調  
  4. 若要回到自動，按 U（系統重置）

- 臨時調整雲/霧並返回自動
  1. Alt + L / Shift + L（增加雲量）或 Alt + P / Shift + P（增加霧）  
  2. 測試完成後按 U 還原自動排程（會把 Weather 的 Manual Override 關閉）

UDS 時間格式說明
---
腳本使用 UDS 時間（0–2400）：  
- UDS 的百位小數部分範圍為 0–99（非標準 60 進位）。腳本內的 FormatToTime 已把 UDS 的 0–99 區段轉換為 0–59 分鐘。  
- 範例：UDS 730 → 07:30；UDS 1930 → 19:30。  
- 常見換算：UDS 100 ≒ 60 分鐘（現實）。

注意事項與建議
---
- 大部分修改時間 / 天氣的快捷鍵會啟用手動控制（_ManualControlActive = true）或把 Weather 標為 Manual Override。此時自動天氣/時間調度不會覆寫你的修改。按 U 可恢復自動行為。
- Alt + F 僅切換動畫（播放/暫停），不會修改 TOD 值；常與 txr_fast_snap 結合使用。
- 腳本中存在 `HeadlightManualComboEnabled = true` 設定，但本檔案未看到對應組合鍵實作（可視需要新增綁定）。
- 若要快速套用或清除天氣預設（apply_preset / clear_preset / apply_preset_random_now），可考慮新增快捷鍵綁定，方便測試。

常見問題（FAQ）
---
Q: 為何我改完雲/霧後系統仍不回復？  
A: 修改雲/霧會把對應屬性設為 Manual Override。請按 U 復原（會關閉 Manual Override 並恢復自動）。

Q: txr_fast_snap 會破壞場景光照嗎？  
A: txr_fast_snap 會切換一次 `Animate Time of Day` 的狀態以強制 UDS 引擎刷新，通常能正確刷新光影，但在少數情況下可能需等待幀刷新���重載材質。

Q: 我看到設定有 HeadlightManualComboEnabled，但沒反應？  
A: 該旗標存在但沒有對應的鍵位註冊；可自行在腳本中加入 RegisterKeyBind 以實作車燈組合鍵。

擴充建議（可選）
---
- 綁定以下未綁定但實用的函數：
  - apply_preset_random_now → 建議快捷鍵：Alt + R（立即套用隨機天氣）
  - clear_preset → 建議快捷鍵：Alt + C（清除當前預設並回復）
- 新增「跳到指定 HH:MM」的互動 UI（或鍵入介面），便於 QA 測試。
- 若需英文版 README 或帶圖示/截圖版本，我可協助產出。

Credits
---
- Car_Killer — the original Time of Day/Weather Control script  
- Silent — 相關說明與資源：[Silent — Tokyo Xtreme Racer 2025 Day/Night Cycle](https://cookieplmonster.github.io/mods/tokyo-xtreme-racer-2025/#day-night-cycle)


範例輸出（TXRLog）
---
下面為「使用腳本內 TXRLog 的對齊風格（帶縮排、標題）」在 CMD / 控制台上按 M 鍵後的範例顯示。此為純文字範例，實際顯示會依遊戲環境或控制台而定（TXRLog 會加上固定縮排）。

範例（按 M 後顯示）：
```text
      >>> [ TXR TIME MONITOR ] <<<
      >  Real Time : 14:23:51
      >  Game Time : 07:30
      >  Status    : ACTIVE
      ------------------------
```

結尾
---
版本：整理自 Car_Killer — the original Time of Day/Weather Control script 及 Silent — 相關說明與資源
