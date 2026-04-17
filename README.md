# line-mcp-connector

讓 Claude (Claude Code / Claude Desktop) 能主動發送 LINE 訊息的 MCP Plugin。

透過 LINE Messaging API，Claude 可以傳訊息給你的 LINE 帳號、群組，或對所有 Bot 好友廣播通知。

---

## 功能

| 指令說法 | 效果 |
|---|---|
| 「LINE 傳訊息給我說記得帶傘」 | 傳給預設收件人 |
| 「LINE 傳給小明說明天改時間」 | 傳給聯絡人名冊中的「小明」 |
| 「廣播 LINE 通知今天放假」 | 傳給所有 Bot 好友 |
| 「查一下我的 LINE 聯絡人」 | 列出聯絡人名冊 |

---

## 前置需求

| 項目 | 說明 |
|---|---|
| Python 3.10 以上 | 需安裝在電腦上 |
| LINE Official Account | 非個人帳號，需建立 Messaging API Channel |
| LINE Channel Access Token | 從 LINE Developers Console 取得 |
| LINE 用戶 ID | 以 `U` 開頭，共 33 個字元 |

> **注意**：此 Plugin 透過 LINE Messaging API 運作，需要 LINE Official Account（免費方案可用），**無法直接操作個人 LINE 帳號**。

---

## 安裝步驟

### 第一步：安裝 Python 套件

```bash
pip install "mcp[cli]" httpx pydantic
```

### 第二步：建立 LINE Bot 並取得 Token

1. 前往 [LINE Developers Console](https://developers.line.biz/console/) 登入
2. 點「**Create a new provider**」
3. 點「**Create a Messaging API channel**」，填入名稱後建立
4. 進入 Channel，點「**Messaging API**」分頁
5. 找到「**Channel access token**」，點「**Issue**」
6. 複製產生的 Token 備用

### 第三步：取得你的 LINE 用戶 ID

格式範例：`Ucb84b19f4ce16c9690ee3a651722cac5`（U 開頭，33 個字元）

1. 用手機掃描 LINE Bot 頁面上的 QR Code，加 Bot 為好友
2. 傳任意一則訊息給 Bot
3. 在 LINE Developers Console 的 Webhook 紀錄找 `source.userId`

> 群組 ID 以 `C` 開頭，從群組訊息的 `source.groupId` 取得。

### 第四步：修改 Plugin 設定

1. 下載 `line-connector.plugin`
2. 將副檔名改成 `.zip`（改成 `line-connector.zip`）
3. 用 Windows 檔案總管打開這個 zip
4. 找到 `.mcp.json`，用記事本打開
5. 填入你的資料：

```json
"LINE_CHANNEL_ACCESS_TOKEN": "貼上你的Token",
"LINE_DEFAULT_USER_ID": "貼上你的用戶ID",
"LINE_CONTACTS": "{\"名字\":\"用戶ID\",\"另一個名字\":\"另一個用戶ID\"}"
```

**LINE_CONTACTS 範例：**
```json
"LINE_CONTACTS": "{\"小明\":\"Uabc123456\",\"家人群組\":\"Cxyz789012\"}"
```

6. 存檔，將 `.zip` 改回 `.plugin`

### 第五步：安裝到 Claude Code / Cowork

1. 打開 Claude Cowork
2. 點左側「**Directory**」→「**Plugins**」→「**Local**」
3. 將 `line-connector.plugin` 拖進去，或點上傳按鈕選擇
4. 出現預覽後點「**Upload**」

安裝後在「**Connectors**」分頁會看到「**line**」。

---

## 確認安裝成功

重啟 Cowork 後，跟 Claude 說：

> 「幫我確認 LINE Bot 有沒有連線成功」

Claude 回傳 Bot 名稱和 ID 就代表成功。

---

## 換電腦時

1. 保存 `line-connector.plugin`（含所有程式碼）
2. 記下 Token、用戶 ID、聯絡人名冊
3. 新電腦執行 `pip install "mcp[cli]" httpx pydantic`
4. 重新安裝 Plugin，填入同樣設定

---

## 常見問題

**Q：401 錯誤？**
Token 無效，去 LINE Developers Console 重新 Issue，更新 `.mcp.json` 後重新安裝。

**Q：403 錯誤（傳群組）？**
確認 Bot 已加入群組，且群組允許 Bot 發言。

**Q：Upload failed？**
`.mcp.json` 格式有誤，用記事本確認 JSON 括號和逗號是否正確。

**Q：LINE 和 n8n 可以同時用嗎？**
可以！Token 可以同時被多個系統使用。收訊息由 n8n 處理，主動發訊息由 Claude 處理。

---

## License

MIT
