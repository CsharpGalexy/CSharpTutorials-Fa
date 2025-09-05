
## 📘 فهرست مطالب: همه چیز درباره Object Initializer در C#

1. 🧠 [Object Initializer یعنی چی؟](#object-initializer)
2. ✅ [مثال ساده با Object Initializer](#simple-example)
3. 🔍 [چرا از Object Initializer استفاده کنیم؟](#why-use-object-initializer)
4. 🧩 [مثال: آبجکت تو در تو (Nested Object)](#nested-object-example)
5. 🆕 [ویژگی جدید: init (از C# 9.0 به بعد)](#init-property)
6. 📚 [کاربرد در لیست‌ها (Collection Initializers)](#collection-initializers)
7. ⚠️ [نکات مهم](#important-notes)
8. 🏁 [جمع‌بندی](#summary)


# object-initializer
### 🧠 **Object Initializer یعنی چی؟**

وقتی یه شیء (Object) می‌سازی، معمولاً بعد از ساختش باید ویژگی‌هاش رو مقداردهی کنی:

```csharp
Student student = new Student();
student.Name = "Ali";
student.Age = 22;
student.Grade = "A";
```

اینجا ۴ خط کد برای ساخت و مقداردهی یه شیء نیاز داری.  
اما با **Object Initializer**، می‌تونی همه این کارها رو **در یه خط** و **همزمان با ساخت شیء** انجام بدی:

---

# simple-example
---
### ✅ **مثال ساده با Object Initializer**

```csharp
Student student = new Student 
{
    Name = "Ali",
    Age = 22,
    Grade = "A"
};
```

> 🎯 **نکته طلایی**: دیگه نیازی نیست بعد از `new Student()` بنویسی `student.Name = ...` — همه چیز توی همون لحظه ساخت انجام میشه!

---
# why-use-object-initializer
---
### 🔍 **چرا از Object Initializer استفاده کنیم؟**

- ✅ **کد کوتاه‌تر و تمیزتر**
- ✅ **خوانایی بالاتر**
- ✅ **کاهش خطاهای انسانی** (مثلاً فراموش کردن مقداردهی یه فیلد)
- ✅ **پشتیبانی از آبجکت‌های تو در تو**
- ✅ **کاربرد گسترده در LINQ، DTOها، و مدل‌های داده**

---
# nested-object-example
---
### 🧩 **مثال: آبجکت تو در تو (Nested Object)**

```csharp
Car car = new Car 
{
    Name = "Corvette",
    Color = Color.Red,
    Manufacturer = new CarManufacturer 
    {
        Name = "Chevrolet",
        Country = "USA"
    }
};
```

> 💡 حتی می‌تونی یه شیء جدید رو داخل خاصیت یه شیء دیگه بسازی — بدون دردسر!

---
# init-property
---
### 🆕 **ویژگی جدید: `init` (C# 9.0 به بعد)**

می‌خوای شیئت **تغییرناپذیر (immutable)** باشه؟ یعنی بعد از ساخت نشه ویژگی‌هاش رو عوض کرد؟  
از `init` استفاده کن:

```csharp
public class Student 
{
    public string Name { get; init; }
    public int Age { get; init; }
}
```

حالا این کد **فقط در زمان ساخت** مجازه:

```csharp
var student = new Student 
{
    Name = "Ali",
    Age = 22
}; // ✅ OK

student.Age = 23; // ❌ Error! Age فقط با init قابل تنظیم است!
```

> 🔒 این ویژگی عالیه برای ساخت **داده‌های امن و تغییرناپذیر** (مثل DTOها، Recordها و ...)

---
# collection-initializers
---
### 📚 **کاربرد در لیست‌ها (Collection Initializers)**

می‌تونی از Object Initializer توی لیست‌ها هم استفاده کنی:

```csharp
var students = new List<Student> 
{
    new Student { Name = "Ali", Age = 22 },
    new Student { Name = "Sara", Age = 21 },
    new Student { Name = "Reza", Age = 23 }
};
```

> 🎉 کد خوندنی، تمیز، و حرفه‌ای!

---
# important-notes
---
### ⚠️ **نکات مهم**

| نکته | توضیح |
|------|--------|
| 🔹 فقط روی **Propertyهای public** کار می‌کنه | فیلد‌های خصوصی یا فقط `get` رو نمی‌تونی با Initializer تنظیم کنی |
| 🔹 باید **`set` یا `init`** داشته باشه | بدون این‌ها، کامپایلر اجازه نمی‌ده |
| 🔹 می‌تونی با **سازنده (Constructor)** ترکیب کنی | مثل: `new Cat("Fluffy") { Age = 10 }` |
| 🔹 مناسب برای **کلاس، struct، record** | همه اشکال شیءسازی رو پشتیبانی می‌کنه |

---
# summary
---
### 🏁 جمع‌بندی

> 🧩 **Object Initializer = ساخت + مقداردهی در یه دستور**

✅ کد کمتر  
✅ خوانایی بیشتر  
✅ پشتیبانی از immutable با `init`  
✅ عالی برای لیست‌ها و داده‌های تو در تو

---

💡 **یادت باشه**:  
هر وقت یه شیء می‌سازی و داری بعدش چندتا `obj.Prop = ...` می‌نویسی…  
**وقتشه از Object Initializer استفاده کنی!** 😎

--- 
