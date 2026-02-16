
# مستندات HTTP Methods و Status Code در FastAPI


این مستندات برای آشنایی با روش‌های HTTP، کدهای وضعیت و نحوه پیاده‌سازی آن‌ها در **FastAPI** آماده شده است.

---
## 1. HTTP چیست؟

**HTTP** مخفف **HyperText Transfer Protocol** است و پروتکلی برای ارتباط مرورگر (کلاینت) و سرور است.  
وظیفه آن انتقال داده‌ها بین کلاینت و سرور و مشخص کردن نحوه ارسال و دریافت اطلاعات است.

**نکته مهم:**  
HTTP فقط متن منتقل نمی‌کند؛ می‌تواند HTML، JSON، عکس، ویدئو و فایل‌ها را هم منتقل کند.


## 2. HTTP Methods اصلی

### 2.1 GET
**کاربرد:**

-  دریافت داده از سرور
-  idempotent (۱۰ بار اجرا نتیجه یکی دارد)

**مثال FastAPI:**



```python
@app.get("/users")
def get_users():
    return users
```
###  2.2 POST

**کاربرد:**  
- ایجاد داده جدید در سرور  
- معمولا idempotent نیست (چند بار اجرا = چند داده ایجاد می‌شود)

**مثال FastAPI:**

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    name: str
    age: int

users = []

@app.post("/users")
def create_user(user: User):
    users.append(user)
    return {"message": "User created", "user": user}
```
### 2.3 PUT

**کاربرد:**  
- جایگزینی کامل یک منبع موجود  
- idempotent است (چند بار اجرا = نتیجه یکسان)

**مثال FastAPI:**

```python
@app.put("/users/{user_id}")
def update_user(user_id: int, user: User):
    if user_id >= len(users):
        return {"error": "User not found"}
    users[user_id] = user
    return {"message": "User updated", "user": user}
```
### 2.4 DELETE

**کاربرد:**  
- حذف یک منبع  
-  idempotent است (چند بار اجرا = همان خروجی)

**مثال FastAPI:**

```python
@app.delete("/users/{user_id}")
def delete_user(user_id: int):
    if user_id >= len(users):
        return {"error": "User not found"}
    users.pop(user_id)
    return {"message": "User deleted"}
```

---

## 3. HTTP Status Codes

کدهای وضعیت HTTP پاسخ سرور به درخواست کلاینت هستند.  
این کدها مشخص می‌کنند که آیا درخواست موفق بوده یا خطا رخ داده است.

کدها در ۵ دسته اصلی قرار می‌گیرند:

- 1xx → Informational  
- 2xx → Success  
- 3xx → Redirection  
- 4xx → Client Errors  
- 5xx → Server Errors  

---

### 3.1 1xx — Informational

این کدها نشان می‌دهند که درخواست دریافت شده و پردازش ادامه دارد.


| Status Code | نام | توضیح |
|------------|------|--------|
| 100 | Continue | سرور هدرها را دریافت کرده و کلاینت می‌تواند ادامه درخواست را ارسال کند |
| 101 | Switching Protocols | تغییر پروتکل (مثلا HTTP به WebSocket) |
| 102 | Processing | سرور در حال پردازش درخواست است |




---

### 3.2 2xx — Success

نشان‌دهنده موفقیت‌آمیز بودن درخواست است.


| Status Code | نام | توضیح |
|------------|----|-------|
| 200 | OK | پاسخ موفق GET |
| 201 | Created | بعد از POST |
| 202 | Accepted | پردازش‌های async |
| 204 | No Content | عملیات موفق بود ولی پاسخی برنمی‌گرداند |


---

### 3.3 3xx — Redirection


نشان می‌دهد کلاینت باید کار دیگری انجام دهد (مثلا ریدایرکت شود).

| Status Code | نام | توضیح |
|------------|------|--------|
| 301 | Moved Permanently | آدرس برای همیشه تغییر کرده |
| 302 | Found | ریدایرکت موقت |
| 304 | Not Modified | داده تغییری نکرده (برای cache) |


---

### 3.4 4xx — Client Errors

نشان می‌دهد خطا از سمت کلاینت بوده است.


| Status Code | نام | توضیح |
|------------|------|--------|
| 400 | Bad Request | داده اشتباه |
| 401 | Unauthorized | توکن ندارد |
| 403 | Forbidden | مجوز ندارد |
| 404 | Not Found | آدرس اشتباه |
| 405 | Method Not Allowed | مثلا POST به GET |
| 409 | Conflict | تکراری بودن |
| 422 | Unprocessable Entity | خطای ولیدیشن FastAPI |


---

### 3.5 5xx — Server Errors

نشان‌دهنده خطا از سمت سرور است.


| Status Code | نام | توضیح |
|------------|------|--------|
| 500 | Internal Server Error | خطای داخلی سرور |
| 502 | Bad Gateway | پاسخ نامعتبر از سرور دیگر |
| 503 | Service Unavailable | سرور در دسترس نیست |
| 504 | Gateway Timeout | تایم‌اوت در پاسخ سرور دیگر |


---
