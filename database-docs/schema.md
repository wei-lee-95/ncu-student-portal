# 資料庫設計說明

此專案使用 Laravel migrations 定義資料庫結構。主要業務資料表為 `users`、`courses` 與 `events`。

## 實體概覽

```text
users
  studentID
  userName
  password
  sticker_id

courses
  user_sid
  semester
  courseNo
  category

events
  events_name
  end_time
  user_sid
```

## 關聯設計

```text
users.studentID 1 --- n courses.user_sid
users.studentID 1 --- n events.user_sid
```

目前實作中，`studentID` 是系統層級的使用者識別值，登入後會存入 Laravel Session，欄位名稱為 `user_sid`。

## 資料表

### users

儲存學生帳號與個人設定資料。

| 欄位 | 用途 |
| --- | --- |
| `id` | 內部資料 ID |
| `studentID` | 學生帳號識別值 |
| `userName` | 使用者顯示名稱 |
| `password` | 原始課程專案中的密碼欄位 |
| `sticker_id` | 使用者選擇的貼圖頭像檔名 |
| `created_at`, `updated_at` | Laravel timestamps |

### courses

儲存每位學生已加入的課程。

| 欄位 | 用途 |
| --- | --- |
| `id` | 內部資料 ID |
| `user_sid` | Session 中的學生 ID |
| `semester` | 學期 |
| `courseNo` | 課程代碼 |
| `category` | 使用者選擇的課程分類 |
| `created_at`, `updated_at` | Laravel timestamps |

### events

儲存每位學生的行事曆事件。

| 欄位 | 用途 |
| --- | --- |
| `id` | 內部資料 ID |
| `events_name` | 事件名稱 |
| `end_time` | 事件日期 |
| `user_sid` | Session 中的學生 ID |
| `created_at`, `updated_at` | Laravel timestamps |

## 設計重點

- 使用 Session 資料判斷目前登入學生。
- 課程與事件查詢都會透過 `user_sid` 篩選，避免讀取到其他使用者資料。
- 前端透過 JSON 回應取得儀表板、課表與行事曆資料。

## 作品集整理備註

目前 migration 歷史同時包含 Laravel 預設 migrations 與專案自訂 migrations。如果要整理成正式 production 版本，我會將 schema 整併成更乾淨的 migrations，並補上明確的索引與外鍵設計：

```text
users.studentID unique
courses.user_sid indexed
events.user_sid indexed
```

我也會將明文密碼改成 Laravel password hashing，並導入 Laravel 內建的身分驗證工具。
