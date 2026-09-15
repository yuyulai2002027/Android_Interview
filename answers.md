# 對照答案

> 寫完再看。看之前先確認你已經自己完整做過一次——提前看會讓你以為自己懂了。
>
> 演算法和 Android 深度題有標準答案。System design 和 STAR 沒有，那兩類給的是**檢核表**：對照你涵蓋了什麼、漏了什麼、面試官會從哪裡追問。不要照抄。

---

### Day 1

**強答案應該涵蓋**

- [釐清] 有沒有問資料來源（網路／本地／資源檔）、最大圖片尺寸、記憶體預算、要不要支援動圖。沒問就直接開畫是扣分點。
- [分層] Request → Interceptor chain → Fetcher（取 bytes）→ Decoder（轉 Bitmap）→ Transformation → Cache → Target。分層講不清楚，後面全部會亂。
- [記憶體快取] LRU，容量以**位元組**計算而非張數（`Bitmap.allocationByteCount`）。通常取 app available memory 的 1/8。
- [磁碟快取] 用 URL + transformation 參數組成 key。注意兩層 key 不同：記憶體 key 要含尺寸，磁碟 key 通常只含原圖。
- [生命週期] Request 綁定 Activity/Fragment lifecycle，onDestroy 時 cancel。`ImageView` 上存一個 tag 記錄當前 request，新 request 進來先取消舊的——這就是 RecyclerView 圖片錯置的根因。
- [記憶體壓力] `inSampleSize` downsampling 到目標 View 尺寸，不要載入原圖。`Bitmap` 重用池（`inBitmap`）。
- [取捨] LRU vs LFU：LFU 對「一次爆紅然後沒人看」的圖片不友善，且需要額外計數空間。HTTP 層交給 OkHttp：重複造輪子沒有價值，而且失去 connection pool 和 HTTP cache 語意。

**面試官會追問**

> 同一張圖同時被 20 個 item 請求，你怎麼處理？（答案：request 去重／合併，同一個 key 只發一次網路請求，多個 target 共享結果）

> 你怎麼測試這個庫？（答案：Fetcher/Decoder 可注入的介面 + fake，快取策略用純函式測試）

---

### Day 2

**LC 49 Group Anagrams**

用字元計數當 key，不要用 sorted string——排序是 O(k log k)，計數是 O(k)。

```kotlin
fun groupAnagrams(strs: Array<String>): List<List<String>> {
    val map = HashMap<String, MutableList<String>>()
    for (s in strs) {
        val count = IntArray(26)
        for (c in s) count[c - 'a']++
        val key = count.joinToString(",")
        map.getOrPut(key) { mutableListOf() }.add(s)
    }
    return map.values.toList()
}
```

時間 O(n·k)，空間 O(n·k)。

**LC 347 Top K Frequent Elements**

bucket sort 是 O(n)，比 heap 的 O(n log k) 好。關鍵觀察：頻率最大不超過 n，所以可以用頻率當索引。

```kotlin
fun topKFrequent(nums: IntArray, k: Int): IntArray {
    val freq = nums.toList().groupingBy { it }.eachCount()
    val buckets = Array(nums.size + 1) { mutableListOf<Int>() }
    freq.forEach { (n, c) -> buckets[c].add(n) }
    val res = mutableListOf<Int>()
    for (i in buckets.indices.reversed())
        for (n in buckets[i]) {
            res.add(n)
            if (res.size == k) return res.toIntArray()
        }
    return res.toIntArray()
}
```

面試時先講 heap 解法（比較直覺），再說「但其實可以做到 O(n)」——展現你有想過最佳化。

**Pattern 辨識訊號**：需要「分組」「計數」「查是否出現過」且順序不重要 → hash map。

---

### Day 3

**這天沒有標準答案，但有品質檢核**

回放第 3 次錄音，逐項確認：

- 開場有沒有先問澄清問題，而不是直接跳進架構
- 有沒有明確說出「我先講整體分層，再深入其中一塊」這種**路標句**（面試官最怕跟丟）
- 講到快取時，有沒有說出「為什麼是 LRU 不是 LFU」
- 整段有沒有出現超過 5 秒的靜默，而且靜默時嘴裡是空的
- 有沒有出現至少三個複雜句（含 because / which / rather than），還是全程短句串接

**路標句範本**

> Let me start with the high-level architecture, then go deeper on the caching layer.
> That covers the happy path — now let me talk about what happens when things fail.
> Before I move on, does this level of detail work for you?

最後一句特別好用。它讓你喘一口氣，同時顯得你在意對方的需求。

---

### Day 4

**STAR 檢核表（技術決策類）**

強答案的骨架不是 STAR 四個字母，而是這五件事：

1. **當時的約束是什麼**（時間、人力、既有系統、技術債）——沒有約束的決策不叫決策
2. **你考慮過哪些備選**——至少要說出兩個，且要說出你為什麼否決它們
3. **你用什麼依據決定**——量測數據？POC？團隊熟悉度？沒有依據只有直覺是 senior 的扣分點
4. **結果與數字**——延遲降低多少、crash rate、build time、開發速度
5. **事後回看**——你會不會改變決定

**Senior 的分水嶺**：junior 講「我做了什麼」，senior 講「我為什麼選這個而不是那個，以及我後來發現我漏想了什麼」。

**後悔類故事的紅線**

- 不能把責任全推給別人或環境
- 不能選一個無關痛癢的假後悔（「我後悔沒早點寫測試」太空泛）
- 要有一個具體的、你當時可以做不同選擇的岔路口

---

### Day 5

**1. coroutineScope vs supervisorScope**

`coroutineScope`：任一子 coroutine 失敗 → 取消所有兄弟 → 例外向上拋。
`supervisorScope`：子失敗只影響自己，兄弟繼續跑。

用 supervisorScope 的場景：首頁同時載入三個獨立區塊，其中一個 API 掛掉不該讓整頁空白。

**2. 例外傳播**

子 coroutine 拋例外會取消父 job 和所有兄弟（結構化併發的預設行為）。

`CoroutineExceptionHandler` 只在**根 coroutine** 的 context 上有效。裝在子 `launch` 上不會生效，因為例外會先往上傳到根。而 `async` 的例外是在 `await()` 時拋出的，handler 完全不會接到——這是最常考的陷阱。

**3. viewModelScope**

`onCleared()` 時呼叫 `cancel()`。它的 context 是 `SupervisorJob() + Dispatchers.Main.immediate`。用 SupervisorJob 是為了讓一個 UI 操作失敗不要癱瘓整個 ViewModel。

**4. 取消是協作式的**

純 CPU 迴圈不會自動取消，需要 `ensureActive()` 或 `yield()`。

更隱蔽的坑：`catch (e: Exception)` 會吞掉 `CancellationException`，導致取消失效。正確做法是 catch 具體型別，或把 `CancellationException` rethrow。

**5. withContext vs launch**

`withContext` 掛起當前 coroutine、切 dispatcher、執行完再切回來，保證順序。`launch` 另起一個不等待。

切換成本本身很小（一次 dispatch），但在迴圈裡反覆 `withContext` 會累積。正確做法是把 `withContext` 提到迴圈外。

**示範程式碼**

```kotlin
suspend fun loadHome(): HomeState = supervisorScope {
    val banner  = async { runCatching { api.banner() }.getOrNull() }
    val feed    = async { api.feed() }               // 這個失敗才算真的失敗
    val notices = async { runCatching { api.notices() }.getOrElse { emptyList() } }
    HomeState(banner.await(), feed.await(), notices.await())
}
```

---

### Day 6

**自我介紹檢核表**

2 分鐘版的結構：

1. 現在的角色與規模（一句）— "I'm an Android engineer at X, working on a shopping app with about N million monthly users."
2. 最相關的一段經歷（三到四句）— 挑跟這個職缺最相關的，不是最得意的
3. 為什麼在找機會 + 為什麼是遠端（兩句）

**常見失分點**

- 從大學開始講起（沒人在乎）
- 條列所有技術棧（`Kotlin, Compose, Hilt, Retrofit, Room...` 這種唸法完全沒有資訊量）
- 講超過 3 分鐘（面試官會開始分心）
- 每次講的一模一樣（明顯是背稿，追問時會崩）

**檢查方法**：對照第 1 次和第 5 次錄音。如果兩次幾乎逐字相同，代表你在背稿，不是在講。

---

### Day 8

**強答案應該涵蓋**

- [釐清] 單裝置還是多裝置？衝突頻率高不高？離線可以編輯多久？這三題決定整個設計的複雜度。
- [SSOT] Room 是唯一真相來源，UI 只觀察 Room，網路只負責同步。**不要讓 UI 同時訂閱網路和資料庫**。
- [資料模型] 本地 UUID 當主鍵（離線建立時沒有伺服器 ID），另存 `serverId` 可為 null。三個同步欄位：`isDirty`、`updatedAt`、`deletedAt`（軟刪除／tombstone）。
- [觸發時機] 使用者編輯後 debounce、App 進前景、WorkManager 週期性、手動下拉。
- [衝突解決] 最少要說出三種：last-write-wins（簡單但會丟資料）、欄位級合併（適合結構化資料）、讓使用者選（適合筆記這種高價值內容）。**並說出你選哪個、為什麼**。
- [刪除] 為什麼要 tombstone 而不是硬刪除：不然離線裝置同步回來會把已刪除的資料「復活」。
- [重試] 指數退避。區分可重試（5xx、網路）與不可重試（4xx、驗證失敗）。

**面試官會追問**

> 使用者離線編輯了 50 筆，一次上線，你怎麼同步？（批次上傳、部分成功怎麼處理、要不要保證順序）

> 時鐘不同步怎麼辦？（用伺服器時間，或 logical clock / version vector）

---

### Day 9

**LC 15 3Sum**

排序 + 固定第一個數 + two pointers。難點全在去重。

```kotlin
fun threeSum(nums: IntArray): List<List<Int>> {
    nums.sort()
    val res = mutableListOf<List<Int>>()
    for (i in nums.indices) {
        if (nums[i] > 0) break                              // 剪枝
        if (i > 0 && nums[i] == nums[i - 1]) continue       // 去重（第一個數）
        var l = i + 1; var r = nums.size - 1
        while (l < r) {
            val sum = nums[i] + nums[l] + nums[r]
            when {
                sum < 0 -> l++
                sum > 0 -> r--
                else -> {
                    res.add(listOf(nums[i], nums[l], nums[r]))
                    l++
                    while (l < r && nums[l] == nums[l - 1]) l++  // 去重（第二個數）
                }
            }
        }
    }
    return res
}
```

時間 O(n²)，空間 O(1)（不算輸出）。

**LC 11 Container With Most Water**

```kotlin
fun maxArea(height: IntArray): Int {
    var l = 0; var r = height.size - 1; var best = 0
    while (l < r) {
        best = maxOf(best, (r - l) * minOf(height[l], height[r]))
        if (height[l] < height[r]) l++ else r--
    }
    return best
}
```

**關鍵洞察**（面試時一定要講出來）：移動比較高的那一邊，面積絕對不會變大——寬度減少，高度受限於較短邊不會增加。所以只能移動較短的那邊。

**Pattern 辨識訊號**：有序陣列 ／ 從兩端往中間收斂 ／ 要找一對或一組滿足條件的元素 → two pointers。

---

### Day 10

**檢核**（同 Day 3，加上本週新要求）

- 開場有沒有用澄清句起手
- 有沒有明確說出你選了哪種衝突解決策略、為什麼
- 卡點清單裡，有沒有跟上週重複的項目？重複的就是真正的弱點，優先處理

**衝突解決的英文講法**

> For conflict resolution, I'd start with last-write-wins because it's simple, but for notes specifically that risks silently losing user content. So I'd go with field-level merge, and fall back to showing both versions when the same field changed on both sides.

這段值得練熟。它示範了 senior 的思路：先給簡單方案 → 指出它的問題 → 給出符合情境的方案 → 說明 fallback。

---

### Day 11

**STAR 檢核表（衝突類）**

面試官在這題想看的是：**你會不會把技術分歧變成人際衝突**。

- [ ] 你有沒有準確複述對方的論點？（能複述 = 你真的聽懂了）
- [ ] 對方的論點有沒有合理之處？（如果你的版本裡對方完全沒道理，要重寫）
- [ ] 你用什麼把討論從「意見對決」推向「證據比較」？（POC、量測、寫 RFC、找第三方意見）
- [ ] 最後怎麼收的？如果是你讓步，你怎麼 disagree and commit？
- [ ] 這件事之後你們的關係如何？

**紅旗**

- 「後來證明我是對的」當結尾——真實面試裡這句會讓對方皺眉
- 完全沒有讓步經驗——代表你要嘛沒真的合作過，要嘛不好共事
- 把對方講成不懂技術的 PM

**說服類的加分點**：說出你怎麼調整表達方式來配合對方的關注點（跟 PM 講排期風險，跟 backend 講介面成本，跟 EM 講維護成本）。

---

### Day 12

**Repo 檢核表**

- [ ] `libs.versions.toml` 存在，沒有硬編版本號在 build.gradle.kts 裡
- [ ] 模組之間的依賴方向是單向的（feature → core，core 不依賴 feature）
- [ ] `:core:data` 不依賴任何 Android UI 元件
- [ ] 有 convention plugin 或至少共用的 build logic，不是每個模組複製貼上
- [ ] 空白畫面能跑起來，Hilt 的 `@HiltAndroidApp` 和 `@AndroidEntryPoint` 有掛上

**這天的目標不是做完，是做對地基。** 如果你花 60 分鐘只完成模組結構和 version catalog，那是成功的。

---

### Day 13

**追問準備**

把逐字稿貼給我之前，先自己預測三個追問，並準備好答案。常見的追問方向：

> What happens if the sync fails halfway through a batch?
> How would you test the conflict resolution logic?
> How does this behave on a flaky network — not offline, just slow?
> How much local storage would this use after a year?

**被問倒時的正確反應**

> That's a good point — I hadn't considered that. My first instinct would be X, but I'd want to validate that with real usage data.

這比硬掰分數高。Senior 面試會**故意**問到你的邊界，對方在看你怎麼處理不確定，不是在看你是不是全知。

---

### Day 15

**強答案應該涵蓋**

- [釐清] 排序是伺服器決定還是本地？要離線瀏覽嗎？有影片嗎？**這三題不問，設計一定偏掉**。
- [分頁] cursor-based 而非 offset-based。理由必須說出來：feed 會即時插入新貼文，offset 會導致重複或跳過項目。
- [架構] Paging 3 + `RemoteMediator`，Room 當 SSOT。網路寫進 Room，UI 只讀 Room。
- [prefetch] `prefetchDistance` 設成約一個螢幕的項目數。太小會卡頓，太大浪費流量。
- [refresh] 下拉刷新時舊快取怎麼辦——整批清掉會讓使用者失去位置；比較好的做法是新資料插入頂端、保留捲動位置。
- [process death] 存 `lastVisibleItemKey`（不是 index），回來後用 key 定位。存 index 在資料變動後會錯位。
- [效能] 捲動 jank 的來源：主執行緒解碼圖片、item 佈局太深、`LazyColumn` 沒設 `key`、每個 item 都在重組。
- [快取策略] 保留幾頁、什麼時候失效、圖片快取和資料快取的生命週期不同。

**面試官會追問**

> 使用者離線時打開 App 應該看到什麼？（快取的前 N 頁 + 明確的離線指示，不是空白或錯誤頁）

> 一則貼文被按讚，你怎麼更新？（樂觀更新 + 失敗回滾，不要整頁重載）

---

### Day 16

**LC 3 Longest Substring Without Repeating Characters**

```kotlin
fun lengthOfLongestSubstring(s: String): Int {
    val last = HashMap<Char, Int>()
    var left = 0; var best = 0
    for (r in s.indices) {
        val c = s[r]
        last[c]?.let { if (it >= left) left = it + 1 }   // 只有落在窗內才跳
        last[c] = r
        best = maxOf(best, r - left + 1)
    }
    return best
}
```

`if (it >= left)` 這個判斷是最常寫錯的地方——舊的重複字元如果已經在窗外，不該影響 left。

時間 O(n)，空間 O(min(n, 字元集大小))。

**LC 424 Longest Repeating Character Replacement**

```kotlin
fun characterReplacement(s: String, k: Int): Int {
    val count = IntArray(26)
    var left = 0; var maxCount = 0; var best = 0
    for (r in s.indices) {
        count[s[r] - 'A']++
        maxCount = maxOf(maxCount, count[s[r] - 'A'])
        while (r - left + 1 - maxCount > k) {
            count[s[left] - 'A']--
            left++
        }
        best = maxOf(best, r - left + 1)
    }
    return best
}
```

**這題的精妙處**：`maxCount` 從不減少，看起來是 bug 但其實是特性。窗口只會在找到更好的答案時擴大，所以 `maxCount` 過時不會影響最終結果。面試時能解釋這一點，等於解釋了整題。

**Sliding window vs two pointers 的差別**：two pointers 通常從兩端往中間收斂；sliding window 是兩個指標同向移動、維護一個滿足條件的區間。看到「連續子陣列／子字串」「最長／最短」→ sliding window。

---

### Day 17

**檢核**

本週新要求：講到取捨那段，每一個選擇都要說出「為什麼不選另一個」。

自我檢查：回放時數一下你說了幾次 `rather than` / `instead of` / `as opposed to`。少於三次，代表你在陳述設計，不是在論證設計。

**取捨句型**

> I'd go with cursor-based pagination rather than offset, because the feed changes underneath the user and offsets would cause duplicates.
> I chose Room as the single source of truth instead of having the UI observe both network and cache, because it keeps the offline path and the online path identical.

---

### Day 18

**STAR 檢核表（帶人類）**

- [ ] 你有沒有描述那個人**具體**的問題？（「他 code 寫不好」不算，「他的 PR 平均要來回五次，因為沒有先對齊設計」才算）
- [ ] 你做了什麼具體的介入？（配對編程、改流程、設計文件先審、定期一對一）
- [ ] 你怎麼知道有效？（PR 來回次數下降、他開始自己 review 別人）
- [ ] 你有沒有調整過方法？（第一個方法無效後改了什麼）

**如果你沒帶過人**，改成「你如何提升團隊的某個實踐」，但骨架完全一樣：具體問題 → 介入 → 量測 → 調整。

**Code review 類的加分點**：說出你怎麼區分「必須改」和「我的偏好」。Senior 的 review 會標明哪些是 blocking、哪些是 nit——這個細節會讓面試官眼睛一亮。

---

### Day 19

**1. cold vs hot**

cold：每個 collector 觸發一次獨立的產生流程，沒有 collector 就不執行（`flow { }`、Room 的 Flow、Retrofit 的 suspend 包裝）。
hot：不管有沒有 collector 都存在，所有 collector 共享同一份（`StateFlow`、`SharedFlow`）。

**2. StateFlow vs SharedFlow**

| | StateFlow | SharedFlow |
|---|---|---|
| 初始值 | 必須有 | 沒有 |
| replay | 固定 1 | 可設定 |
| 去重 | 有（equals 相同不發） | 沒有 |
| 用途 | UI 狀態 | 一次性事件 |

去重那一行是最常被考的：`StateFlow` 發射相同值時 collector 不會收到，所以拿它送「顯示 snackbar」這種事件會漏掉第二次。一次性事件用 `SharedFlow(replay = 0)` 或 `Channel`。

**3. flowOn 為什麼只影響上游**

因為 Flow 遵守 **context preservation**：collector 的執行 context 必須由 collect 端決定，否則呼叫端無法推理自己的程式碼跑在哪個執行緒。`flowOn` 改變的是它**之前**（上游）那段的 context。

**4. buffer / conflate / collectLatest**

- `buffer`：不丟資料，讓上下游並行，用緩衝吸收速度差
- `conflate`：丟掉中間值，只保留最新（等於 `buffer(CONFLATED)`）
- `collectLatest`：新值到達時**取消**尚未完成的上一次處理

差別在於「丟掉的是哪一端」：conflate 丟上游的值，collectLatest 丟下游未完成的工作。

**5. WhileSubscribed(5000)**

訂閱者歸零後延遲 5 秒才停止上游。目的是撐過螢幕旋轉、切換 App 等短暫的設定變更，避免重新訂閱資料庫或重發網路請求。5 秒是社群慣例值，不是魔法數字。

**6. StateFlow 為什麼還要 repeatOnLifecycle**

`StateFlow` 本身不感知 lifecycle。在 `lifecycleScope.launch` 裡直接 collect，App 進背景後 collect 仍在執行，持續消耗上游資源、更新看不見的 UI。

`repeatOnLifecycle(STARTED)` 會在進入 STOPPED 時**取消**整個 collect，回到 STARTED 時**重新開始**。這是目前的標準做法。

**示範**

```kotlin
// conflate：每 100ms 發一次，處理要 300ms → 只看到部分值，每個都處理完
flow.conflate().collect { process(it) }

// collectLatest：每次新值到達就取消上一次 → 只有最後一個真的處理完
flow.collectLatest { process(it) }
```

---

### Day 20

**中式英文常見樣態**（回放時對照）

| 常見說法 | 更自然 |
|---|---|
| I think maybe we can... | I'd suggest... ／ My take is... |
| It has a very big improvement | It improved X by 40% |
| I am responsible for the whole project | I owned the X part of the project |
| We discuss and finally decide | We aligned on... ／ We landed on... |
| The performance is not good | Startup time was around 2.8 seconds, which was too slow |
| I will try my best | 直接刪掉，這句沒有資訊 |

**最重要的一條**：把形容詞換成數字。`very big`、`not good`、`a lot` 在 senior 面試裡是空話。

---

### Day 22

**強答案應該涵蓋**

- [釐清] 1對1 還是群組？要已讀回條嗎？訊息量級？歷史訊息保留多久？
- [連線層] WebSocket 維持前景即時性，FCM 負責背景喚醒。**為什麼不能只用 WebSocket**：Android 背景限制會殺掉長連線，且耗電。
- [訊息狀態機] `sending → sent → delivered → read`，加上 `failed`。本地先插入 `sending` 狀態的訊息（樂觀 UI），收到 ack 再更新。
- [本地 ID] 送出前產生 client-side UUID，伺服器回傳時用它對應。這也是**去重**的基礎——重送同一個 UUID 伺服器要 idempotent。
- [離線佇列] 未送出的訊息存 Room，`isPending = true`，重連後依序重送。要保證順序。
- [gap detection] 重連後不是「拉最新 N 則」，而是帶上本地最後一則的 `seq` 或 `serverTimestamp`，讓伺服器補齊中間缺的。這題答不出來會扣很多分。
- [排序] 用伺服器時間，不是本地時間（裝置時鐘可能錯）。但送出中的訊息只有本地時間，要處理這個過渡。
- [省電] 進背景斷開 WebSocket 改用 FCM；前景重連。

**面試官會追問**

> 兩個人同時發訊息，順序怎麼決定？（伺服器接收順序 + 單調遞增 seq）

> 使用者有 10 萬則歷史訊息，本地要全存嗎？（分頁載入、舊訊息可清除、搜尋走伺服器）

---

### Day 23

**LC 33 Search in Rotated Sorted Array**

關鍵：每次二分後，**必有一半是有序的**。判斷 target 在不在那個有序半邊。

```kotlin
fun search(nums: IntArray, target: Int): Int {
    var l = 0; var r = nums.size - 1
    while (l <= r) {
        val m = l + (r - l) / 2
        when {
            nums[m] == target -> return m
            nums[l] <= nums[m] ->                                   // 左半有序
                if (target >= nums[l] && target < nums[m]) r = m - 1 else l = m + 1
            else ->                                                 // 右半有序
                if (target > nums[m] && target <= nums[r]) l = m + 1 else r = m - 1
        }
    }
    return -1
}
```

`nums[l] <= nums[m]` 的等號不能省——當窗口只剩兩個元素時 `m == l`。

**LC 153 Find Minimum in Rotated Sorted Array**

```kotlin
fun findMin(nums: IntArray): Int {
    var l = 0; var r = nums.size - 1
    while (l < r) {
        val m = l + (r - l) / 2
        if (nums[m] > nums[r]) l = m + 1 else r = m
    }
    return nums[l]
}
```

**兩種模板的差別**（把這個寫進你的筆記）

| | `while (l <= r)` | `while (l < r)` |
|---|---|---|
| 用途 | 找確切的值 | 找邊界／最小值 |
| 收縮 | `l = m + 1` / `r = m - 1` | `l = m + 1` / `r = m` |
| 結束 | `l > r`，回傳 -1 | `l == r`，就是答案 |
| 風險 | 無限迴圈風險低 | `r = m` 配 `l <= r` 會無限迴圈 |

用 `l + (r - l) / 2` 而不是 `(l + r) / 2`，避免溢位。Kotlin 的 Int 也會溢位。

---

### Day 24

**過渡語演練檢核**

本週要求是刻意製造停頓並用過渡語接住。回放時確認：

- 停頓前有沒有先給訊號（`Let me think through that`），還是直接沉默
- 停頓長度是否在 3–5 秒（超過 8 秒對方會開始不安）
- 停頓後有沒有接回原本的主線，還是換了話題

**進階：把停頓變成加分**

> Let me think about the failure cases for a second... OK, there are three I'd worry about.

這種講法把停頓轉成「我正在有結構地思考」，比流暢但空洞的回答分數高。

---

### Day 25

**STAR 檢核表（失敗類）**

這是整組故事裡最容易搞砸的一題。

- [ ] 事故的嚴重程度有沒有講清楚（影響多少使用者、多久）
- [ ] 你**當下**做了什麼（止血優先於找原因——這個順序講反會被扣分）
- [ ] 根因是什麼，而且是技術上的根因不是「某人粗心」
- [ ] 事後改了什麼**制度**（監控、流程、測試、灰度發布）
- [ ] 你自己的責任在哪

**紅旗**：整個故事裡你都是救火英雄，沒有任何責任。面試官要的是問責能力，不是英雄事蹟。

**STAR 檢核表（模糊需求類）**

遠端職缺特別看重這題，因為它等同於問「沒人盯著你，你怎麼推進」。

- [ ] 你怎麼把模糊的東西變具體（寫文件？做原型？拆問題？找誰確認？）
- [ ] 你在什麼時候決定「資訊夠了，開始做」——這個判斷點是重點
- [ ] 你怎麼讓別人知道你的進度（**非同步溝通的證據**）
- [ ] 中途發現方向錯了怎麼辦

---

### Day 26

**Repo 檢核表**

- [ ] Room migration 有寫，而且有 migration 測試（`MigrationTestHelper`）
- [ ] Repository 回傳 Flow，不是 suspend 一次性
- [ ] ViewModel 測試用 `TestDispatcher` + `runTest`，不是 `Thread.sleep`
- [ ] 有一個 fake 資料源，測試不碰真的網路或資料庫
- [ ] CI 會在 PR 上跑 `./gradlew lint test`
- [ ] 測試名稱看得懂（`emits error state when network fails` 而不是 `test3`）

**take-home 評分的真相**：功能做完只是門檻。真正拉開差距的是測試品質和 README 裡的設計說明。很多人把 80% 時間花在功能、20% 在其他，正確的比例大概是反過來的 60/40。

---

### Day 27

**反問的品質標準**

好的反問會暴露你懂遠端協作。對照你準備的 8 個：

**強反問**

> How does the team handle decisions when half the people are asleep?
> What does a typical code review turnaround look like across time zones?
> Is the role a direct hire through an EOR, or a contractor arrangement? Are there any restrictions for Taiwan?
> What's the biggest source of technical debt in the Android codebase right now?
> How do you measure whether an engineer is doing well here, given no one sees them working?

**弱反問**（不要問）

- What's the company culture like?（太空泛，對方只能給罐頭答案）
- Do you offer training?（顯得被動）
- 任何 Google 一分鐘能查到的（公司規模、產品線、融資）
- 只問福利不問工作內容

**EOR 那題一定要問**，而且要早問。等到 offer 階段才發現對方不能聘台灣人，你會浪費好幾週。

---

### Day 29

**強答案應該涵蓋**

- [釐清] 檔案多大？要背景上傳嗎？App 被殺掉要續傳嗎？同時幾個？這四題決定架構。
- [架構選擇] WorkManager 適合「可以延遲、需要跨進程存活」的上傳；Foreground Service 適合「使用者正在等、需要即時進度」的上傳。**兩者的分界線是使用者感知**，這是面試官想聽的判斷依據。
- [分塊] chunk 大小通常 1–5MB。太小則 overhead 高，太大則重試成本高。要說出這個 trade-off。
- [續傳記錄] 已上傳的 chunk index 存 Room。伺服器要支援 resumable upload（回傳已收到的 offset）。純 client 記錄不夠——伺服器可能沒真的收到。
- [重試] 指數退避 + jitter。**jitter 的理由**：避免大量裝置同時重試造成雪崩。
- [錯誤分類] 4xx 不重試（除了 408、429），5xx 和網路錯誤重試，401 觸發 token 刷新後重試一次。
- [約束] `NetworkType.UNMETERED`、`requiresBatteryNotLow`。但要說出代價：使用者可能等很久，要給明確的 UI 提示。
- [進度回報] WorkManager 的 `setProgress`，或存 Room 讓 UI 觀察。

**面試官會追問**

> 使用者在上傳中途把 App 滑掉了，會怎樣？

> 同時上傳 20 個檔案，你怎麼控制併發？（Semaphore 限流，WorkManager 的 unique work chain）

---

### Day 30

**LC 150 Evaluate Reverse Polish Notation**

```kotlin
fun evalRPN(tokens: Array<String>): Int {
    val st = ArrayDeque<Int>()
    for (t in tokens) when (t) {
        "+" -> st.addLast(st.removeLast() + st.removeLast())
        "*" -> st.addLast(st.removeLast() * st.removeLast())
        "-" -> { val b = st.removeLast(); st.addLast(st.removeLast() - b) }
        "/" -> { val b = st.removeLast(); st.addLast(st.removeLast() / b) }
        else -> st.addLast(t.toInt())
    }
    return st.last()
}
```

`-` 和 `/` 不可交換，所以要先取出右運算元。這是最常見的 bug。

**LC 739 Daily Temperatures（monotonic stack）**

```kotlin
fun dailyTemperatures(t: IntArray): IntArray {
    val res = IntArray(t.size)
    val st = ArrayDeque<Int>()                    // 存索引，對應溫度遞減
    for (i in t.indices) {
        while (st.isNotEmpty() && t[i] > t[st.last()]) {
            val j = st.removeLast()
            res[j] = i - j
        }
        st.addLast(i)
    }
    return res
}
```

時間 O(n)——每個索引最多進出堆疊一次。

**Monotonic stack 辨識訊號**：「下一個更大／更小的元素」「往左／往右第一個滿足條件的」「柱狀圖面積」。看到這類敘述，先想單調堆疊。

堆疊裡存**索引**不存值，因為通常需要距離。

---

### Day 31

**檢核**

本週要求：全程不看架構圖。

如果做不到，問題不在英文，在你對這題的結構還不夠熟。回去重畫一次架構圖，畫的時候只用關鍵字不寫句子——這會逼你記住結構而不是文字。

**五題都講完了，做一次橫向檢查**

- 五題裡你最有把握哪一題？最沒把握哪一題？
- 有沒有哪一個環節五題都會出現（快取、重試、離線、狀態同步）？那些是**通用模組**，值得單獨練熟，之後遇到新題可以直接套。

---

### Day 32

**STAR 檢核表（效能類）**

這題沒有數字就等於沒答。

- [ ] 你怎麼**發現**問題的？（使用者回報？監控？自己發現？）
- [ ] 用什麼工具測量？（Perfetto、Android Studio Profiler、Firebase Performance、Macrobenchmark）
- [ ] 基準數字是多少，改善後是多少
- [ ] 你怎麼確認是那個改動造成的改善（不是同期其他變動）
- [ ] 有沒有回歸防護（Macrobenchmark 進 CI、監控告警）

**遠端動機的檢核表**

這題答不好會被當紅旗。

- [ ] 有沒有講出你**已經具備**的遠端工作能力（不是「我想要」，是「我可以」）
- [ ] 有沒有提到非同步溝通的具體習慣（寫文件、留下決策紀錄、主動更新進度）
- [ ] 時區問題你打算怎麼處理（你願意配合到什麼程度，要誠實）
- [ ] 離職原因有沒有變成抱怨前公司

**強答案的形態**

> I've been effectively working async for the last two years — my team is split across two offices, so most decisions happen in docs rather than meetings. I'm used to writing things down and making progress without real-time input. Remote isn't a change of mode for me, it's just a change of location.

把「我想要遠端」轉成「我已經在遠端模式工作」。

---

### Day 33

**1. recomposition 觸發條件**

composable 讀取的 snapshot state 改變時，**讀取它的最小 scope** 重組。

關鍵技巧是延遲讀取（defer read）：傳 lambda 而非值，把讀取推到更下層。

```kotlin
// 每次 scroll 都重組整個 Header
Header(offset = scrollState.value)

// 只有真正繪製時才讀，Header 不重組
Header(offset = { scrollState.value })
```

**2. skippable / restartable**

- **restartable**：這個 composable 可以獨立被重新執行，是重組的最小單位
- **skippable**：所有參數都 stable 且值相同時，可以完全跳過

用 Compose compiler metrics 檢查：

```
kotlinOptions.freeCompilerArgs += [
  "-P", "plugin:androidx.compose.compiler.plugins.kotlin:reportsDestination=..."
]
```

產出的 `.txt` 會標出每個 composable 是不是 skippable、每個參數是 stable 還 unstable。

**3. @Stable vs @Immutable**

- `@Stable`：承諾 equals 行為一致，且 public property 改變時會通知 composition（例如內部用 `mutableStateOf`）
- `@Immutable`：更強的承諾——建立後所有 property 永不改變

`List<T>` unstable 的原因：它是介面，編譯器無法保證實作不可變（可能是 `ArrayList`）。解法：用 `kotlinx.collections.immutable` 的 `ImmutableList`，或把 list 包進標了 `@Immutable` 的 data class。

**4. remember vs derivedStateOf**

- `remember`：跨重組保留計算結果
- `derivedStateOf`：從**高頻變動**的 state 推導出**低頻變動**的值

```kotlin
// 正確：scrollState 每幀變，但 showButton 只在跨過閾值時變
val showButton by remember { derivedStateOf { scrollState.firstVisibleItemIndex > 0 } }
```

誤用（推導結果跟來源一樣頻繁變動）反而增加開銷，因為多了一層訂閱。

**5. LaunchedEffect key / rememberUpdatedState**

key 改變時 effect 會被**取消並重啟**。所以 key 要選「effect 需要重跑」的那個值。

`rememberUpdatedState` 解決：effect 要長期執行（不想重啟），但內部要讀到最新的 lambda。

```kotlin
val currentOnTimeout by rememberUpdatedState(onTimeout)
LaunchedEffect(Unit) {          // 只啟動一次
    delay(5000)
    currentOnTimeout()          // 但呼叫的是最新的那個
}
```

**6. lazy list 不設 key**

預設用 index 當 identity。在頂端插入一筆時，所有 item 的 index 都變了 → Compose 認為每個 item 都換了內容 → 整批重組、動畫錯亂、item 內部的 `remember` 狀態錯置（例如展開狀態跑到別的 item 上）。

```kotlin
LazyColumn {
    items(posts, key = { it.id }) { PostItem(it) }
}
```

---

### Day 34

**模擬檢討表**

看錄影，分別給自己打 1–5 分：

| 項目 | 分數 | 說明 |
|---|---|---|
| 自我介紹在 2–3 分鐘內講完 | | 超時是最常見問題 |
| STAR 有具體數字 | | 沒數字直接 2 分以下 |
| System design 有先澄清需求 | | 直接跳架構扣分 |
| 講到取捨時說出「為什麼不選另一個」 | | senior 的核心指標 |
| 全程沒有超過 8 秒的空白沉默 | | |
| 停頓時用了過渡語 | | |
| 有出現複雜句而非只有短句 | | |
| 反問有實質內容 | | |
| 看起來像在對話而非背稿 | | 看錄影的表情和眼神 |

**別急著修所有問題。** 挑分數最低的兩項當第二階段的重點就好。一次修九件事等於一件都修不好。

---

### Day 35

**這天沒有答案，只有一個問題要回答**

把 Day 3 和 Day 34 的錄音放在一起聽完後，回答：

**我現在卡的是英文，還是內容不熟？**

判斷方法：挑你講得最卡的那一段，改用中文講一次。

- 中文講得很順 → 卡的是英文，第二階段加重口說輸出的比例
- 中文也講不順 → 卡的是內容，第二階段要先補技術深度，英文自然會跟上

**這個判斷會決定第二階段的權重，不要跳過。**
