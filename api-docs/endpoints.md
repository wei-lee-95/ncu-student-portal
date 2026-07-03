# API 文件

這些端點定義在 `routes/web.php`。此專案使用 Laravel Web routes 與 Session 狀態管理。

## 身分驗證

### 顯示登入頁

```http
GET /welcome
```

回傳登入與註冊頁面。

### 註冊

```http
POST /welcome/register
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `studentid` | string | 作為帳號識別用的學號 |
| `password` | string | 帳號密碼 |

處理流程：

- 建立使用者資料。
- 設定預設顯示名稱與貼圖頭像。
- 將 `user_sid`、`user_name`、`user_sticker` 存入 Session。
- 導向 `/home`。

### 登入

```http
POST /welcome/login
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `studentid` | string | 學號 |
| `password` | string | 密碼 |

處理流程：

- 查詢符合的使用者。
- 將使用者資料存入 Session。
- 成功時導向 `/home`。
- 失敗時導回上一頁並顯示錯誤訊息。

### 登出

```http
GET /logout
```

清除目前 Session，並導向 `/welcome`。

## 使用者資料

### 顯示設定頁

```http
GET /set
```

回傳使用者設定頁面。

### 更新個人資料

```http
POST /set
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `user-name` | string | 選填，新的顯示名稱 |
| `user-img` | string | 選填，新的貼圖頭像檔名 |

處理流程：

- 更新目前使用者的顯示名稱與/或貼圖頭像。
- 更新 Session 中的使用者資料。
- 導向 `/home`。

## 課程

### 課程搜尋頁

```http
GET /course
```

回傳課程搜尋頁面。

### 新增課程

```http
POST /course/add
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `course_no` | string | 課程代碼 |
| `course_semester` | string | 學期，例如 `112-2` |
| `course_category` | string | 使用者選擇的課程分類 |

處理流程：

- 從 Session 讀取目前登入學生的學號。
- 建立一筆綁定該學生的課程紀錄。

### 課程儀表板頁

```http
GET /course/dashboard
```

回傳課程儀表板頁面。

### 課程儀表板資料

```http
GET /course/dashboard/my
GET /home/dashboard
```

回應範例：

```json
[
  {
    "id": 1,
    "user_sid": 123456789,
    "semester": "112-2",
    "courseNo": "CS101",
    "category": "required",
    "created_at": "2024-05-26T00:00:00.000000Z",
    "updated_at": "2024-05-26T00:00:00.000000Z"
  }
]
```

處理流程：

- 回傳目前 Session 使用者擁有的所有課程。

### 修改課程分類

```http
POST /course/change
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `courseNo` | string | 課程代碼 |
| `courseCategory` | string | 新分類 |

成功回應：

```json
{
  "message": "Category updated successfully"
}
```

錯誤回應：

```json
{
  "message": "Course not found"
}
```

### 課表頁

```http
GET /course/table
```

回傳課表頁面。

### 課表資料

```http
GET /course/table/my
```

處理流程：

- 回傳目前 Session 使用者的課程。
- 目前固定篩選學期為 `112-2`。

### 刪除課程

```http
DELETE /course/delete
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `semester` | string | 學期 |
| `courseNo` | string | 課程代碼 |

成功回應：

```json
{
  "status": "Course deleted"
}
```

## 行事曆

### 行事曆頁

```http
GET /calendar
```

回傳行事曆頁面。

### 查詢所有事件

```http
GET /calendar/events
```

回應範例：

```json
[
  {
    "id": 1,
    "events_name": "Midterm",
    "end_time": "2024-06-01",
    "user_sid": 123456789,
    "created_at": "2024-05-26T00:00:00.000000Z",
    "updated_at": "2024-05-26T00:00:00.000000Z"
  }
]
```

### 查詢指定日期事件

```http
POST /home/calendar
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `date` | date | 要查詢的日期 |

處理流程：

- 回傳目前使用者在指定日期的事件。

### 新增事件

```http
POST /calendar/events
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `date` | date | 事件日期 |
| `event` | string | 事件名稱 |

處理流程：

- 建立一筆綁定目前 Session 使用者的事件。
- 以 JSON 回傳建立後的事件資料。

### 刪除事件

```http
DELETE /calendar/events
```

請求欄位：

| 欄位 | 型別 | 說明 |
| --- | --- | --- |
| `date` | date | 事件日期 |
| `event` | string | 事件名稱 |

成功回應：

```json
{
  "status": "Event deleted"
}
```

## 頁面路由

```http
GET /home
GET /home/eatSquirrel
GET /home/russia
```

這些路由會回傳原始專案 UI 使用的 Blade 頁面。
