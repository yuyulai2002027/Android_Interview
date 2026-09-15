# Senior Android 遠端面試課表

21 週的每日訓練課表，可勾選、會記住進度。

- `curriculum.md` — 課表內容
- `answers.md` — 對照答案，用 `### Day N` 對應
- `index.html` — 網頁介面，會讀取上面兩份 md

## 本機預覽

不能直接雙擊 `index.html`（瀏覽器不允許 `file://` 讀取本地檔案）。用：

```
python3 -m http.server 8000
```

然後開 http://localhost:8000

## 修改課表

直接改 `curriculum.md`，推上去即可。網頁會自動跟著變。

打勾紀錄用「該項目的文字」當 key 存在瀏覽器裡，所以調整順序、增減日子都不會弄丟進度；
但如果改掉某一項的文字內容，那一項的勾會消失。
