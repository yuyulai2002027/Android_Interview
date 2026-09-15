# Senior Android（完全遠端）面試準備課表

> 總期程 21 週 ／ 每天 1–2 小時
> 弱項優先序：英文表達 > Mobile System Design > 演算法／take-home

---

## 使用說明

**每天只看一格。** 打開檔案 → 找到今天 → 照著做 → 打勾。不要預讀後面的日子。

- **主時段 60 分**：當天的核心科目，時間不夠也不能砍。
- **副時段 25 分**：Speak 或收尾工作，只有 1 小時的日子直接跳過。
- **週日**：休息。只花 30 分鐘整理本週卡點，不做新進度。

**錄音是必要的，不是選配。** 第 1 / 8 / 15 週會用同一題錄英文獨白，這三段錄音是你唯一可靠的進步證據。檔名統一存成 `W01-image-loading.m4a` 這種格式，不要散落。

**卡住時的規則**：演算法卡超過 25 分鐘，直接看 NeetCode 影片解法。你的時間預算不允許硬耗。英文卡住時不要停下來查字典，先講完再回頭補。

---

# 第一階段：開口期（第 1–5 週）

**這階段的唯一目標：把「開口講技術英文」從痛苦變成習慣。**

演算法刻意壓到每週 2 題，不要焦慮，第二階段會補回來。這五週英文吃掉將近一半的時間，是刻意的投資。

---

## 第 1 週

### Day 1（一）System Design — Image Loading Library

設計一個類似 Coil / Glide 的圖片載入庫。這題當開場是因為範圍夠明確，不會發散。

**主時段 60 分**
- [ ] 用這個固定順序在紙上／白板切一次（**用中文思考**）：
  1. 釐清需求：要支援哪些來源？記憶體上限？要不要支援 GIF？
  2. 分層架構：Request → Interceptor → Fetcher → Decoder → Cache → Target
  3. 深挖快取：memory cache（LRU、bitmap 佔用計算）vs disk cache（大小上限、淘汰策略）
  4. 深挖生命週期：Activity 銷毀時如何取消 request？如何避免 ImageView 錯置（recycle 問題）
  5. 非功能面：記憶體壓力、OOM、大圖 downsampling、主執行緒
  6. 取捨：為什麼用 LRU 不用 LFU？為什麼不自己寫 HTTP 層？
- [ ] 把架構圖畫成一張 A4，拍照存檔（週三要用）

**副時段 25 分｜Speak**
- [ ] 本週技術詞重音（見附錄 A）：`cache` `schema` `Gradle` `architecture`
- [ ] 每個詞用 Speak 唸到判定通過，然後造一個含該詞的完整句子

---

### Day 2（二）演算法 — Hash Map / Array

**主時段 60 分**
- [ ] LC 49 Group Anagrams（medium）— **用 Kotlin 寫**
- [ ] LC 347 Top K Frequent Elements（medium）
- [ ] 兩題都寫完後，用一句話寫下這個 pattern 的辨識訊號（什麼題目看到就該想到 hash map）

**副時段 25 分｜Speak**
- [ ] 自由對話，主題設定成「描述你今天解的其中一題」
- [ ] 刻意使用一次：`The key insight here is...`

---

### Day 3（三）英文獨白 — 第一次基準線錄音

**今天會很不舒服，這是正常的。**

**主時段 60 分**
- [ ] 拿出 Day 1 的架構圖，**站著**用英文講 10 分鐘，錄音（第 1 次）
- [ ] 休息 3 分鐘，再講一次，錄音（第 2 次）
- [ ] 再講第三次，錄音（第 3 次）—— 這次通常會明顯順很多

**副時段 25 分**
- [ ] 只回放第 3 次
- [ ] 記下 5 個卡住的地方：是想不出單字、想不出句型，還是內容本身不熟？
- [ ] **這段錄音要永久保留**，第 8 週和第 15 週會回來對照

---

### Day 4（四）STAR 故事 — 技術決策

**主時段 60 分**
- [ ] 用中文寫兩個故事（每個 250–300 字）：
  1. **你主導的一次架構決策**（為什麼是你決定？有哪些備選？你怎麼說服團隊？結果如何？）
  2. **一次你後來後悔的技術決策**（當時的資訊是什麼？現在會怎麼做？）
- [ ] 每個故事都要有具體數字或具體結果，沒有就是還沒寫完

**副時段 25 分｜Speak**
- [ ] Roleplay 設定（直接貼進去）：

> You are a hiring manager at a remote-first company, interviewing me for a senior Android engineer position. Ask behavioral questions one at a time. After each answer, ask one follow-up that digs into my specific decisions and trade-offs.

---

### Day 5（五）Android 深度 — Coroutines 結構化併發

**主時段 60 分**
- [ ] 弄清楚這幾個問題，每題都要能口頭解釋：
  - `coroutineScope` 和 `supervisorScope` 的差別？什麼時候該用哪個？
  - 子 coroutine 拋例外時，父層發生什麼事？`CoroutineExceptionHandler` 為什麼有時候不生效？
  - `viewModelScope` 在 ViewModel 清除時做了什麼？
  - 取消是協作式的，什麼情況下 coroutine 取消不掉？`ensureActive()` 和 `isActive` 怎麼用？
  - `withContext` 和 `launch` 的 dispatcher 切換成本差在哪？
- [ ] 寫一段 20 行的程式碼示範「一個子任務失敗但其他要繼續」

**副時段 25 分｜Speak**
- [ ] 本週技術詞第二組：`asynchronous` `parameter` `deprecated` `hierarchy`

---

### Day 6（六）英文長時段 90 分 — 自我介紹

自我介紹是每一場面試的第一題，也是唯一 100% 會被問到的題目。值得單獨花一個週六。

**90 分**
- [ ] 英文寫出 **2 分鐘版本**：現在在做什麼 → 最相關的一段經歷 → 為什麼找遠端／為什麼是這家
- [ ] 英文寫出 **5 分鐘版本**：加入一個代表性專案的技術細節
- [ ] 2 分鐘版本連講 5 次，第 1 次和第 5 次錄音
- [ ] 對照兩段錄音，把還會卡的句子改寫成更短的句子

**重點**：自我介紹不要背稿。背稿在被追問時會整個崩掉。要練到「同樣的內容每次講法都不同」。

---

### Day 7（日）休息

- [ ] 30 分鐘：整理本週卡點清單（英文卡點 / 技術不熟的點 / 演算法沒掌握的）
- [ ] 卡點清單是活的文件，整份課表只維護這一份

---

## 第 2 週

### Day 8（一）System Design — Offline-first 筆記 App

**主時段 60 分**
- [ ] 一樣的七步切法，重點放在第 4 步：
  1. 釐清需求：多裝置同步嗎？離線可以編輯多久？衝突頻率高嗎？
  2. 分層：UI → Repository → Local(Room) + Remote(API) → Sync Engine
  3. 資料模型：本地 ID vs 伺服器 ID、`isDirty` / `updatedAt` / `deletedAt` 欄位設計
  4. **深挖同步**：什麼時候觸發？WorkManager 的約束條件？衝突怎麼解（last-write-wins vs 欄位級合併 vs 讓使用者選）？
  5. 非功能面：電量、流量、同步失敗的重試退避
  6. 取捨：為什麼不直接用 Firebase？tombstone 刪除 vs 硬刪除
- [ ] 畫成 A4 架構圖存檔

**副時段 25 分｜Speak**
- [ ] 本週技術詞：`latency` `nullable` `coroutine` `dependency`

---

### Day 9（二）演算法 — Two Pointers

**主時段 60 分**
- [ ] 先花 10 分鐘複習上週兩題（**只講思路，不重寫程式碼**）
- [ ] LC 15 3Sum（medium）
- [ ] LC 11 Container With Most Water（medium）
- [ ] 寫下 two pointers 的辨識訊號

**副時段 25 分｜Speak**
- [ ] 自由對話：用英文解釋 3Sum 為什麼要先排序

---

### Day 10（三）英文獨白 — Offline-first

**主時段 60 分**
- [ ] Day 8 那題，英文講 10 分鐘 × 3 次，全部錄音
- [ ] 本週新增要求：開場一定要先問澄清問題，用這句起手

> Before I jump into the design, can I clarify a couple of assumptions?

**副時段 25 分**
- [ ] 回放第 3 次，更新卡點清單
- [ ] 對照上週的卡點，有沒有重複出現的？重複出現的就是要優先處理的

---

### Day 11（四）STAR 故事 — 衝突與說服

**主時段 60 分**
- [ ] 中文寫兩個故事：
  1. **跟 PM / backend / iOS 的一次技術分歧**（對方的論點是什麼？你怎麼處理？最後誰讓步？）
  2. **你說服團隊接受一個不受歡迎的方案**（阻力在哪？你用什麼證據？）
- [ ] 檢查：故事裡有沒有把對方寫成笨蛋？有的話重寫。Senior 面試在看你怎麼處理分歧，不是看你多正確

**副時段 25 分｜Speak**
- [ ] Roleplay，主題鎖定 conflict 類問題

---

### Day 12（五）Take-home 樣板 repo — 初始化

這個 repo 之後會直接變成你的作品集，值得認真做。

**主時段 60 分**
- [ ] 建立 repo，決定模組結構（`:app` `:core:data` `:core:ui` `:feature:xxx`）
- [ ] Gradle version catalog（`libs.versions.toml`）
- [ ] 加入 Hilt、Retrofit、Room、Compose 的基礎設定
- [ ] 先跑起來一個空白畫面就好，不要一次做完

**副時段 25 分｜Speak**
- [ ] 本週技術詞第二組：`migration` `threshold` `algorithm` `variable`

---

### Day 13（六）英文長時段 90 分

**90 分**
- [ ] Day 8 的 offline-first 題，英文完整講 15 分鐘（比平常多 5 分鐘）
- [ ] 講完後把逐字稿打出來貼給我（Claude），請我扮演面試官追問
- [ ] 針對追問即時用英文回答，再錄一次音

---

### Day 14（日）休息

- [ ] 30 分鐘整理卡點清單

---

## 第 3 週

### Day 15（一）System Design — Instagram Feed

**主時段 60 分**
- [ ] 七步切法，重點在分頁與快取：
  1. 釐清需求：feed 排序是伺服器決定還是本地？要支援離線瀏覽嗎？貼文有影片嗎？
  2. 分層：Paging 3 的 RemoteMediator + Room 當 single source of truth
  3. 資料模型：cursor-based vs offset-based 分頁，為什麼 feed 一定要用 cursor？
  4. **深挖**：prefetch 距離怎麼設？pull-to-refresh 時舊資料怎麼處理？使用者捲到一半 App 被殺掉，回來要停在哪？
  5. 非功能面：捲動時的 jank、圖片預載、記憶體
  6. 取捨：快取要留幾頁？過期策略？
- [ ] 畫成 A4 存檔

**副時段 25 分｜Speak**
- [ ] 本週技術詞：`queue`（唸 kyoo）`cached` `throughput` `concurrency`

---

### Day 16（二）演算法 — Sliding Window

**主時段 60 分**
- [ ] 複習上週 two pointers 兩題（只講思路）
- [ ] LC 3 Longest Substring Without Repeating Characters（medium）
- [ ] LC 424 Longest Repeating Character Replacement（medium）
- [ ] 寫下 sliding window 和 two pointers 的差別在哪

**副時段 25 分｜Speak**
- [ ] 自由對話：用英文解釋什麼時候窗口該收縮

---

### Day 17（三）英文獨白 — Instagram Feed

**主時段 60 分**
- [ ] Day 15 那題，英文講 10 分鐘 × 3 次，錄音
- [ ] 本週新增要求：講到取捨那段時，一定要說出「為什麼不選另一個」

**副時段 25 分**
- [ ] 回放，更新卡點清單

---

### Day 18（四）STAR 故事 — 帶人與 Code Review

**主時段 60 分**
- [ ] 中文寫兩個故事：
  1. **你 mentor 過的一個人**（他的問題是什麼？你做了什麼？他後來怎麼樣？）
  2. **一次困難的 code review**（你怎麼給負面回饋？對方反應如何？）
- [ ] 如果你沒帶過人，改寫成「你如何提升團隊的某個實踐」（導入測試、改善 CI、建立規範）

**副時段 25 分｜Speak**
- [ ] Roleplay，主題鎖定 leadership / mentorship

---

### Day 19（五）Android 深度 — Flow

**主時段 60 分**
- [ ] 每題都要能口頭解釋：
  - cold flow 和 hot flow 的本質差別？
  - `StateFlow` vs `SharedFlow`：replay、conflation、初始值，什麼場景用哪個？
  - `flowOn` 為什麼只影響上游？
  - `buffer` / `conflate` / `collectLatest` 三者處理背壓的方式差在哪？
  - `stateIn` 的 `SharingStarted.WhileSubscribed(5000)` 那個 5000 是在解決什麼問題？
  - 為什麼 `LiveData` 被 `StateFlow` 取代，但 `StateFlow` 在 UI 層要配 `repeatOnLifecycle`？
- [ ] 寫一段程式碼示範 conflate 和 collectLatest 的行為差異

**副時段 25 分｜Speak**
- [ ] 技術詞第二組：`iterate` `deploy` `pipeline` `sequence`

---

### Day 20（六）英文長時段 90 分 — STAR 轉英文

**90 分**
- [ ] 把前四個 STAR 故事（Day 4、Day 11）全部用英文講一輪，每個 3 分鐘，錄音
- [ ] 回放，標出中式英文的句子
- [ ] 把標出來的句子貼給我，請我給更自然的講法

---

### Day 21（日）休息

- [ ] 30 分鐘整理卡點清單

---

## 第 4 週

### Day 22（一）System Design — 即時聊天

**主時段 60 分**
- [ ] 七步切法：
  1. 釐清需求：1對1 還是群組？要已讀回條嗎？訊息量級？要支援多久的離線？
  2. 連線層：WebSocket vs 長輪詢 vs FCM，為什麼手機端通常是 WebSocket + FCM 混合？
  3. 資料模型：本地訊息 ID、`sending` / `sent` / `delivered` / `read` 狀態機
  4. **深挖**：離線時送出的訊息怎麼排隊？重連後怎麼補回缺失訊息（gap detection）？同一則訊息重送怎麼去重（idempotency）？
  5. 非功能面：省電（背景時斷線改推播）、訊息排序（用伺服器時間還是本地時間？）
  6. 取捨：訊息本地保留多久？全文搜尋要不要做？
- [ ] 畫成 A4 存檔

**副時段 25 分｜Speak**
- [ ] 本週技術詞：`mutable` `immutable` `lifecycle` `granular`

---

### Day 23（二）演算法 — Binary Search

**主時段 60 分**
- [ ] 複習上週 sliding window 兩題
- [ ] LC 33 Search in Rotated Sorted Array（medium）
- [ ] LC 153 Find Minimum in Rotated Sorted Array（medium）
- [ ] 把 binary search 的邊界條件寫成一個你自己的模板（`while (l < r)` 還是 `l <= r`，什麼時候用哪個）

**副時段 25 分｜Speak**
- [ ] 自由對話：用英文解釋為什麼旋轉陣列還能二分

---

### Day 24（三）英文獨白 — 聊天 App

**主時段 60 分**
- [ ] Day 22 那題，英文講 10 分鐘 × 3 次，錄音
- [ ] 本週新增要求：中間刻意製造一次停頓，用過渡語接住（見附錄 B）

**副時段 25 分**
- [ ] 回放，更新卡點清單

---

### Day 25（四）STAR 故事 — 失敗與模糊

**主時段 60 分**
- [ ] 中文寫兩個故事：
  1. **一次線上事故或專案失敗**（你的責任是什麼？怎麼止血？事後改了什麼制度？）
  2. **需求模糊時你如何推進**（這題對遠端職缺特別重要，要能展現自主性）
- [ ] 失敗故事的檢查點：有沒有承認自己的責任？只推給別人的失敗故事會直接扣分

**副時段 25 分｜Speak**
- [ ] Roleplay，主題鎖定 failure / ambiguity

---

### Day 26（五）Take-home 樣板 repo — 資料層與測試

**主時段 60 分**
- [ ] Room entity / DAO / migration 寫一組
- [ ] Repository 層 + 一個 fake 資料源
- [ ] 寫 3 個單元測試（ViewModel 的 state 測試 + Repository 測試）
- [ ] 設定 GitHub Actions：push 時跑 lint + test

**副時段 25 分｜Speak**
- [ ] 技術詞第二組：`heuristic` `idempotent` `resilient` `scalable`

---

### Day 27（六）英文長時段 90 分 — 反問環節

大部分人完全不準備反問，但遠端職缺的反問特別能加分，因為你問的問題會暴露你懂不懂遠端協作。

**90 分**
- [ ] Day 22 的聊天題，英文講 15 分鐘
- [ ] 準備 8 個英文反問，至少涵蓋：
  - 團隊的時區分佈與重疊時段怎麼安排
  - 非同步溝通的實際做法（文件文化？決策怎麼記錄？）
  - 聘僱形式（EOR 還是 contractor？有沒有排除台灣？）
  - onboarding 怎麼做、code review 的節奏
  - Android 團隊規模、技術債現況
- [ ] 每個反問都出聲講一次，錄音

---

### Day 28（日）休息

- [ ] 30 分鐘整理卡點清單

---

## 第 5 週

### Day 29（一）System Design — 檔案上傳管理器

**主時段 60 分**
- [ ] 七步切法：
  1. 釐清需求：檔案大小上限？要背景上傳嗎？App 被殺掉要續傳嗎？
  2. 架構：WorkManager + 分塊上傳 + 進度回報
  3. **深挖續傳**：chunk 切多大？已上傳的 chunk 怎麼記錄？伺服器端怎麼配合（resumable upload protocol）？
  4. 深挖重試：指數退避的參數？哪些錯誤該重試、哪些不該？
  5. 非功能面：只在 Wi-Fi 上傳、電量約束、進度通知
  6. 取捨：為什麼用 WorkManager 不用 Foreground Service？什麼時候該反過來？
- [ ] 畫成 A4 存檔

**副時段 25 分｜Speak**
- [ ] 本週技術詞：`modular` `abstraction` `encapsulation` `refactor`

---

### Day 30（二）演算法 — Stack

**主時段 60 分**
- [ ] 複習上週 binary search 兩題
- [ ] LC 150 Evaluate Reverse Polish Notation（medium）
- [ ] LC 739 Daily Temperatures（medium，monotonic stack）
- [ ] 寫下 monotonic stack 的辨識訊號

**副時段 25 分｜Speak**
- [ ] 自由對話：用英文解釋 monotonic stack

---

### Day 31（三）英文獨白 — 上傳管理器

**主時段 60 分**
- [ ] Day 29 那題，英文講 10 分鐘 × 3 次，錄音
- [ ] 本週新增要求：全程不看架構圖

**副時段 25 分**
- [ ] 回放，更新卡點清單

---

### Day 32（四）STAR 故事 — 效能與動機

**主時段 60 分**
- [ ] 中文寫兩個故事：
  1. **一次效能優化**（怎麼發現問題？用什麼工具測量？改善了多少？要有數字）
  2. **為什麼想要完全遠端 / 為什麼離開現職**（這題答不好會被當成紅旗，要準備）
- [ ] 遠端動機的檢查點：不要只講「想要自由」。要講你已經具備的遠端工作能力（自主性、書面溝通、非同步習慣）

**副時段 25 分｜Speak**
- [ ] Roleplay，主題鎖定 motivation / remote work

---

### Day 33（五）Android 深度 — Compose Recomposition

**主時段 60 分**
- [ ] 每題都要能口頭解釋：
  - recomposition 的觸發條件是什麼？為什麼「讀取 state 的最小範圍」很重要？
  - 什麼是 skippable / restartable composable？怎麼用 compiler metrics 檢查？
  - `@Stable` 和 `@Immutable` 的差別？為什麼 `List<T>` 是 unstable？
  - `remember` 和 `derivedStateOf` 什麼時候該用？誤用會怎樣？
  - `LaunchedEffect` 的 key 怎麼選？`rememberUpdatedState` 解決什麼問題？
  - lazy list 的 `key` 參數不設會有什麼後果？
- [ ] 打開 compiler metrics 看你自己專案的 skippable 比例

**副時段 25 分｜Speak**
- [ ] 技術詞第二組：`regression` `instrument` `telemetry` `bottleneck`

---

### Day 34（六）第一次迷你模擬 — 全英文 120 分

第一階段的驗收。這場會很不舒服，那就對了。

**120 分**
- [ ] **錄影**（不只錄音，要看到自己的表情和肢體）
- [ ] 全英文，中途不准停、不准查字典、不准重來：
  1. 自我介紹 2 分鐘
  2. 一個 STAR 故事 4 分鐘
  3. 從前五題 system design 隨機抽一題，講 15 分鐘
  4. 反問 3 個問題
- [ ] 回放，寫下三件事：最嚴重的問題是什麼、最明顯的進步是什麼、第二階段要優先修什麼

---

### Day 35（日）第一階段總檢討

- [ ] 把 Day 3 的錄音和 Day 34 的錄音放在一起聽
- [ ] 回答：我現在卡的是英文，還是內容不熟？
- [ ] 把答案告訴我（Claude），我們用這個結果決定第二階段的權重要怎麼調

---

# 第二~四階段（第 6–21 週）

第一階段結束後才展開細節，因為權重要看 Day 35 的檢討結果調整。先放骨架和題庫。

## 第二階段：綁定期（第 6–11 週）

英文不再有獨立時段，改成所有科目的預設語言。

| | 主時段 60 分 | 副時段 25 分 |
|---|---|---|
| 一 | System Design 新題，**全程英文思考＋講** | Speak roleplay |
| 二 | 演算法 medium × 3，**邊寫邊用英文講思路** | 技術詞重音 |
| 三 | 跟 Claude 做 system design roleplay（追問） | 整理表達問題 |
| 四 | STAR 英文口說 ＋ 錄音 | Speak roleplay |
| 五 | take-home repo ／ Android 深度 | Speak 自由對話 |
| 六 | 2 小時完整模擬（英文） | — |

**System Design 題庫**：Analytics SDK ／ 影音串流播放器 ／ 地圖與定位追蹤 ／ 多語系與 A/B testing 框架 ／ 推播系統 ／ 電商購物車與離線結帳

**演算法 pattern**：BFS/DFS ／ Tree ／ Heap ／ Graph ／ Backtracking ／ 基礎 DP（每週一個 pattern × 3 題）

**Android 深度題庫**：Lifecycle 與 process death ／ 多模組與 Gradle convention plugin ／ 記憶體洩漏與 LeakCanary ／ Baseline Profile 與啟動優化 ／ KMP 基礎

## 第三階段：加壓期（第 12–17 週）

每週至少一場真人 mock interview（Pramp、interviewing.io 或同行互練）。Speak 降級成每天 15 分鐘暖身。

**關鍵規則：檢討時間要跟練習時間一樣長。** 一場沒檢討的 mock 價值接近零。

- 週一 System Design mock
- 週二 演算法計時模擬（45 分鐘一場）
- 週三 跟 Claude 做追問訓練
- 週四 行為面試 mock
- 週五 repo 收尾 ／ 履歷與 LinkedIn 英文化
- 週六 完整模擬 ＋ 錄影檢討

## 第四階段：實戰期（第 18–21 週）

- **第 18 週**：投 3–5 家「你其實沒那麼想去」的公司，當最高品質的模擬
- **第 20 週**：開始投真正的目標公司
- 課表縮成每天 60 分維持手感，多出來的時間研究公司、準備反問、處理流程
- LeetCode Premium 這時候再訂一個月，看目標公司的題庫

**遠端職缺來源**：We Work Remotely、RemoteOK、Himalayas、Wellfound。關鍵字過濾 `worldwide` 或 `APAC`。

**時區現實**：歐洲公司最可行（他們上午＝你下午，天然 4–5 小時重疊）。美國公司除非明確寫 async-first，否則重疊會落在你的深夜。投之前先確認對方的 EOR 有沒有排除台灣。

---

# 附錄 A：技術詞重音表

只挑面試中會反覆出現的詞。一場面試講錯二三十次，對方會持續分神。

| 詞 | 正確唸法 | 常見錯誤 |
|---|---|---|
| cache | **kash**（一個音節） | ka-SHAY |
| schema | **SKEE**-ma | SHE-ma |
| queue | **kyoo**（一個音節） | 唸出後面的 ueue |
| Gradle | **GRAY**-dl | GRA-dle |
| architecture | **AR**-chi-tec-ture | ar-CHI-tec-ture |
| asynchronous | a-**SYN**-chro-nous | a-syn-CHRO-nous |
| parameter | pa-**RA**-me-ter | PA-ra-me-ter |
| deprecated | **DE**-pre-ca-ted | de-PRE-cated |
| hierarchy | **HI**-er-ar-chy | hi-ER-archy |
| latency | **LAY**-ten-cy | LA-tency |
| algorithm | **AL**-go-rithm | al-GO-rithm |
| variable | **VA**-ri-a-ble | va-RI-able |
| mutable | **MYOO**-ta-ble | MU-table |
| idempotent | i-dem-**PO**-tent | — |
| telemetry | te-**LE**-me-try | TE-le-metry |

其餘詞彙（`nullable` `coroutine` `dependency` `migration` `threshold` `throughput` `concurrency` `iterate` `deploy` `pipeline` `sequence` `granular` `heuristic` `resilient` `scalable` `modular` `abstraction` `encapsulation` `refactor` `regression` `instrument` `bottleneck`）用 Speak 逐一確認。

---

# 附錄 B：過渡語句型庫

**目標不是消滅停頓，是讓停頓聽起來像思考。** 練到反射為止。

**爭取時間**
- Let me think through that for a second.
- That's a good question — the way I'd approach it is...
- Let me make sure I understand the question correctly.

**開場澄清**
- Before I jump into the design, can I clarify a couple of assumptions?
- Just to set the scope — are we optimizing for X or Y here?

**表達取捨**
- The trade-off here is between A and B. I'd lean towards A because...
- There's no perfect answer here, but given the constraints I'd...
- If the requirements changed to X, I'd reconsider and go with...

**承認不確定**
- I haven't worked with that specifically, but based on how X works, I'd expect...
- I'm not certain about the exact API, but the approach would be...

最後這組特別重要。Senior 面試會故意問到你的知識邊界，**誠實地說不確定然後給出推理，分數高於硬掰**。

---

# 附錄 C：STAR 故事清單（目標 10 個）

- [ ] 1. 你主導的架構決策
- [ ] 2. 你後悔的技術決策
- [ ] 3. 跨團隊技術分歧
- [ ] 4. 說服團隊接受不受歡迎的方案
- [ ] 5. Mentor 一個人
- [ ] 6. 困難的 code review
- [ ] 7. 線上事故或專案失敗
- [ ] 8. 模糊需求下自主推進
- [ ] 9. 效能優化（要有數字）
- [ ] 10. 為什麼要遠端 / 為什麼離開現職

每個故事都要能用 3 分鐘講完，而且能被追問三層而不崩。

---

# 附錄 D：錄音存檔清單

| 檔名 | 內容 | 用途 |
|---|---|---|
| `W01-image-loading.m4a` | 第一次英文獨白 | 基準線 |
| `W05-mock-01.mp4` | 第一次迷你模擬（錄影） | 第一階段驗收 |
| `W08-image-loading.m4a` | 同一題重錄 | 中期對照 |
| `W15-image-loading.m4a` | 同一題重錄 | 後期對照 |

**第 8 週和第 15 週要用完全同一題**，這是整份課表裡唯一能客觀證明你有進步的東西。五個月很長，中間一定有兩三週覺得自己完全沒進步，那三段錄音就是你的證據。
