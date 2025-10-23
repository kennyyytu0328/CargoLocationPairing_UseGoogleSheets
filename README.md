# CP 資料綁定作業系統（前端→Google Sheets）

[![GitHub](https://img.shields.io/badge/GitHub-CargoLocationPairing_UseGoogleSheets-blue?logo=github)](https://github.com/kennyyytu0328/CargoLocationPairing_UseGoogleSheets)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![HTML5](https://img.shields.io/badge/HTML5-E34C26?logo=html5&logoColor=white)](https://html5.org/)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](https://www.javascript.com/)

## 📌 簡介

**CP 資料綁定作業系統**是一款**前端 WebApp 工具**，專為倉庫、物流中心的**貨件與棧板位置綁定**業務設計。支援條碼掃描、離線佇列、Google Sheets 即時同步等功能，適合在 Android/iOS 手機或桌機上使用。

### 核心特性
✅ **條碼掃描**（ZXing + WebRTC 相機）  
✅ **進貨綁定** / **暫存綁定** / **資料查詢**  
✅ **離線支援**（IndexedDB 佇列，自動同步）  
✅ **Google Sheets 整合**（OAuth 2.0 認證）  
✅ **響應式設計**（手機 / 平板 / 桌機）  
✅ **手電筒 / 自動對焦**（相機增強功能）

---

## 🚀 快速開始

### 環境需求
- 現代瀏覽器（Chrome、Safari、Edge、Firefox）
- Google 帳號（用於 OAuth 認證, 且需要預先在Google Sheets授權）
- 相機設備（手機或電腦 webcam）
- 穩定的網路連線

### 本地運行

#### 1️⃣ 複製倉庫
```bash
git clone https://github.com/kennyyytu0328/CargoLocationPairing_UseGoogleSheets.git
cd CargoLocationPairing_UseGoogleSheets
```

#### 2️⃣ 啟動本地伺服器
使用 Python：
```bash
# Python 3.x
python -m http.server 8000

# 或 Python 2.x
python -m SimpleHTTPServer 8000
```

或使用 Node.js：
```bash
# 安裝全域 http-server
npm install -g http-server

# 啟動伺服器
http-server . -p 8000
```

或使用 VS Code Live Server 擴充：
- 右鍵點擊 `index.html` → "Open with Live Server"

#### 3️⃣ 開啟瀏覽器
前往：
```
http://localhost:8000
```

#### 4️⃣ 配置 Google Sheets 認證
1. 取得 **Client ID** 與 **Registry Sheet ID**
2. 編輯 `index.html`，找到以下常數：
   ```javascript
   const CLIENT_ID = 'YOUR_CLIENT_ID_HERE';
   const REGISTRY_SHEET_ID = 'YOUR_SHEET_ID_HERE';
   ```
3. 替換為你的實際值
4. 重新整理頁面

---

## 📖 功能說明

### 🔐 登入與初始化
- 點擊「Google 登入以啟用上傳」
- 選擇你的 Google 帳號
- 系統會自動從 Registry 工作表取得資料表 ID

### 📸 相機與條碼掃描

#### 支援的條碼格式
| 格式 | 用途 |
|------|------|
| **EAN-13 / EAN-8** | 商品條碼 |
| **UPC-A / UPC-E** | 美國商品條碼 |
| **Code128 / Code39 / Code93** | 倉庫 / 物流條碼 |
| **ITF / Codabar** | 特殊行業條碼 |
| **QR Code** | 二維碼 |
| **PDF417 / Aztec / DataMatrix** | 高密度條碼 |

#### 相機控制
- **開啟相機**：啟動相機並開始連續掃描
- **關閉相機**：停止掃描並釋放相機資源
- **手電筒**：開啟 / 關閉閃光燈（需支援的設備）
- **對焦**：觸發自動對焦（進階相機模式）

**最佳實踐：**
- 距離：10-30cm
- 光線：充足、無反光
- 角度：直視條碼中心
- 清晰度：確保條碼無模糊

### 🏭 進貨綁定（BIND_INBOUND）

**操作流程：**
1. 輸入「操作人姓名」與「工作站」
2. 掃描或輸入「棧板碼」，點「設定棧板」
3. 掃描或輸入「貨件碼」，點「上傳一件」
4. 重複步驟 3，直到該棧板所有貨件完成

**上傳內容：**
```
[時間戳] | 操作人 | 工作站 | 棧板碼 | 貨件碼 | 來源別
```

### 📦 暫存綁定（BIND_STAGING）

**操作流程：**
1. 切換到「暫存綁定」頁籤
2. 掃描或輸入「貨件/棧板碼」
3. 掃描或輸入「區位碼」
4. 點「上傳一筆」

**應用場景：**
- 臨時存放貨件
- 棧板位置調整
- 區域盤點

### 🔍 查詢（QUERY）

**操作流程：**
1. 切換到「查詢（最近）」頁籤
2. 輸入或掃描「貨件碼」或「棧板碼」
3. 點「查詢」
4. 顯示該貨件最近 50 筆綁定記錄

### 📡 離線與同步

#### IndexedDB 佇列
- 自動儲存未能立即上傳的資料
- 網路恢復時自動同步
- 顯示「待傳」計數

#### 手動同步
- 點「手動同步（上傳待傳佇列）」
- 系統會逐筆上傳所有待傳資料

#### 清空佇列
- 點「清空待傳」
- 謹慎操作（不可復原）

---

## 🔧 Google Sheets 設定

### 必需工作表結構

#### 1️⃣ Registry 工作表（讀取設定）
| 欄位 | 值 | 說明 |
|------|-----|------|
| current_sheet_id | `1xyz...` | 當日資料表 ID |
| ... | ... | 其他設定 |

#### 2️⃣ Data 工作表（寫入資料）
| A | B | C | D | E | F | G | H | I | J | K | L | M |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| ts_server | client_ts | operator_name | station_id | op_type | op_batch_id | pallet_code | item_code | zone_code | source_type | req_id | device_id | note |

**欄位說明：**
- **ts_server**：伺服器時間（留空，由 Sheets 自動填充）
- **client_ts**：客戶端時間戳
- **operator_name**：操作人姓名
- **station_id**：工作站代碼
- **op_type**：操作類型（`BIND_INBOUND`, `BIND_STAGING`）
- **op_batch_id**：批次 ID（進貨綁定時使用）
- **pallet_code**：棧板碼
- **item_code**：貨件碼
- **zone_code**：區位碼
- **source_type**：來源別（GCGP / RCRT / OTHER）
- **req_id**：請求 ID（UUID）
- **device_id**：設備 ID（自動生成）
- **note**：備註

---

## 📱 使用場景

### 場景 1：進貨入庫
1. 收到一批貨
2. 掃描「棧板碼」，點「設定棧板」
3. 逐個掃描各個「貨件碼」，點「上傳一件」
4. 系統記錄每件貨的位置

### 場景 2：臨時調整
1. 發現某批貨需要移位
2. 切到「暫存綁定」
3. 掃描「貨件碼」和「新區位」，點「上傳一筆」
4. 系統記錄位置變更

### 場景 3：查詢追蹤
1. 客戶查詢某件貨
2. 切到「查詢（最近）」
3. 輸入「貨件碼」
4. 看到所有綁定記錄

---

## 💾 本地存儲

### 設備 ID（localStorage）
- 自動生成 UUID，存於瀏覽器 localStorage
- 用於識別設備

### 待傳佇列（IndexedDB）
- 資料庫名：`wmscp`
- 物件儲存：`queue`
- 存儲未上傳的所有資料

---

## 🎨 UI 技術

- **框架**：原生 JavaScript（無外部框架依賴）
- **樣式**：CSS3（深色主題，響應式設計）
- **相機**：WebRTC MediaDevices API
- **條碼**：ZXing.js（@zxing/library@0.21.3）
- **認證**：Google Identity Services
- **離線**：IndexedDB + localStorage

### 斷點設計
- **768px 以下**：手機版
- **480px 以下**：小屏手機版

---

## 🔐 安全與隱私

- ✅ 前端應用，**無伺服器端**
- ✅ OAuth 2.0 認證，只需讀寫權限
- ✅ 離線使用，無網路依賴
- ✅ 設備 ID 本地存儲，不上傳到第三方
- ⚠️ **HTTPS 建議**（生產環境）

---

## 🐛 故障排除

### 相機無法啟動
- 檢查瀏覽器相機權限（設定 → 隱私 → 相機）, 同時需在HTTPS環境下才能開啟
- 確認無其他應用佔用相機
- 嘗試在新分頁重新整理

### 條碼讀不到
- 確保光線充足
- 條碼清晰（無模糊、污漬）
- 距離 10-30cm
- 查看 Console 檢查條碼格式是否支援

### Google 登入失敗
- 檢查網路連線, 請確認你的Google Sheets API服務開啟, 同時獲得授權
- 確認 Client ID 正確
- 檢查 OAuth 重定向 URI 設定

### 資料未上傳
- 檢查是否已登入
- 查看「待傳」計數
- 點「手動同步」重試
- 檢查 Google Sheets 權限

### 查看詳細日誌
按 F12 開啟瀏覽器 Developer Tools，查看 **Console** 頁籤

---

## 📝 Console 日誌

系統提供詳細的 Console 日誌便於調試：

```javascript
// ZXing 初始化
✓ ZXing 已初始化，支援條碼格式：...

// 相機啟動
✓ 相機設定: {width: 1920, height: 1080, ...}
✓ 自動對焦、曝光、白平衡已啟用

// 條碼成功
✓ 成功讀取條碼: XXXXXX format: CODE_128

// 上傳狀態
✓ 棧板碼已設定
✓ 貨件碼已設定，準備上傳...
```

---

## 📚 技術棧

| 技術 | 版本 | 用途 |
|------|------|------|
| ZXing.js | 0.21.3 | 條碼掃描 |
| Google API Client | 最新 | OAuth 2.0 認證 |
| IndexedDB | HTML5 | 離線佇列 |
| Sheets API v4 | v4 | 資料同步 |

---

## 🤝 貢獻

歡迎提交 Issue 與 Pull Request！

### 開發流程
1. Fork 本倉庫
2. 建立功能分支 (`git checkout -b feature/AmazingFeature`)
3. Commit 更改 (`git commit -m 'Add AmazingFeature'`)
4. Push 到分支 (`git push origin feature/AmazingFeature`)
5. 提交 Pull Request

---

## 📄 授權

本專案採用 MIT License，詳見 [LICENSE](LICENSE) 檔案

---

## 📞 聯絡

- **GitHub Issues**：[提交問題](https://github.com/kennyyytu0328/CargoLocationPairing_UseGoogleSheets/issues)
- **郵件**：kennyyytu0328@gmail.com（如有重大問題）

---

## 🎯 計劃中的功能

- [ ] 批次上傳優化（減少 API 呼叫）
- [ ] 資料匯出（CSV / Excel）
- [ ] 多語言支援（中文 / 英文 / 日文）
- [ ] 深色/淺色主題切換
- [ ] 離線編輯暫存
- [ ] 條碼重複檢測告警
- [ ] 統計報表

---

## 📸 截圖示例

| 頁面 | 說明 |
|------|------|
| 進貨綁定 | 設定棧板並逐個上傳貨件 |
| 暫存綁定 | 快速綁定貨件與區位 |
| 查詢 | 即時查詢貨件綁定記錄 |
| 離線佇列 | 網路離線時自動佇列待傳 |

---

**最後更新：2025年10月23日**

祝使用愉快！🚀
