# 解謎邏輯草稿

這份文件整理目前 `rusty_lake_demo.html` 的解謎資料。後續可以先改這份，再把內容套回 HTML 的 `gameState`、`OBJECTS`、hotspot 和存檔欄位。

## 遊戲狀態

| 狀態欄位 | 初始值 | 用途 |
| --- | --- | --- |
| `inventory` | `[]` | 玩家目前持有物品。 |
| `keyTaken` | `false` | 鑰匙是否已被拿走。拿走後場景上的鑰匙消失。 |
| `doorUnlocked` | `false` | 門是否已被鑰匙打開。 |
| `clockExamined` | `false` | 玩家是否查看過時鐘特寫。 |
| `deskSearched` | `false` | 玩家是否查看過書桌特寫。 |

## 目前解謎流程

1. 玩家調查房間中的物件，取得線索。
2. 書桌特寫提供數字線索：`3 · 17 · 11`。
3. 時鐘停在三點十七分，呼應 `3 · 17`。
4. 銅鑰匙上刻著 `11`，呼應最後一個數字。
5. 玩家拿起鑰匙後，鑰匙加入物品欄，場景上的鑰匙熱區隱藏。
6. 玩家持有 `鑰匙` 時點門，門會解鎖並自動儲存進度。

## 互動物件

| 物件 ID | 顯示名稱 | 熱區位置 | 基本回饋 | 特寫 | 狀態影響 |
| --- | --- | --- | --- | --- | --- |
| `door` | 門 | `left:50 top:60 width:100 height:200` | 門鎖著、門縫有冷風、鎖孔老式 | 無 | 若持有 `鑰匙`，設定 `doorUnlocked = true` 並儲存。 |
| `window` | 窗 | `left:220 top:70 width:120 height:90` | 窗外是霧、倒影、霧不動 | `[ 窗外 ]` | 無。 |
| `desk` | 書桌 | `left:380 top:200 width:160 height:100` | 紙、蠟燭、無字書本 | `[ 書桌 ]` | 查看特寫後設定 `deskSearched = true`。 |
| `portrait` | 畫像 | `left:168 top:80 width:60 height:80` | 眼睛很黑、他在看你、他認識你 | `[ 畫像 ]` | 無。 |
| `clock` | 時鐘 | `left:480 top:60 width:60 height:80` | 停在三點十七分、秒針還在走 | `[ 時鐘 ]` | 查看特寫後設定 `clockExamined = true`。 |
| `key` | 鑰匙 | `left:428 top:216 width:28 height:28` | 銅鑰匙、刻著 11 | `[ 銅鑰匙 ]` | 點「拿起」後設定 `keyTaken = true`，加入 `鑰匙`，隱藏熱區並儲存。 |

## 解鎖條件

| 事件 | 條件 | 結果文字 | 狀態變更 |
| --- | --- | --- | --- |
| 點門但沒有鑰匙 | `doorUnlocked = false` 且物品欄沒有 `鑰匙` | 隨機顯示門的普通調查文字 | 無。 |
| 點門且有鑰匙 | 物品欄包含 `鑰匙` | `你把鑰匙插進鎖孔——...` | `doorUnlocked = true`，自動儲存。 |
| 點已打開的門 | `doorUnlocked = true` | `門是開著的...` | 無。 |
| 拿起鑰匙 | `keyTaken = false` | `你拿起了鑰匙...` | `keyTaken = true`，物品欄加入 `鑰匙`，自動儲存。 |

## 存檔內容

存檔使用瀏覽器 `localStorage`，key 是：

```text
rusty_hall_v1
```

目前會保存：

```text
inventory
keyTaken
doorUnlocked
clockExamined
deskSearched
```

如果未來新增謎題狀態，請同步加入 `gameState`、`saveGame()` 和 `loadGame()`。

## 後續修改建議格式

新增物件時，可以先照這個格式寫：

```text
物件 ID：
顯示名稱：
熱區位置：
普通調查文字：
特寫標題：
特寫描述：
是否有按鈕：
按鈕文字：
觸發條件：
狀態變更：
回饋文字：
```

新增解謎條件時，可以先照這個格式寫：

```text
事件：
必要條件：
成功結果：
失敗結果：
要新增或修改的狀態欄位：
是否需要自動儲存：
```
