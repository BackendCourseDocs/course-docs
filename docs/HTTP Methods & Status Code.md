
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