
## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [تعریف Anonymous Types](#تعریف-anonymous-types)  
3. [نحوه ایجاد و استفاده](#نحوه-ایجاد-و-استفاده)  
4. [ویژگی‌های کلیدی](#ویژگی‌های-کلیدی)  
5. [محدودیت‌ها](#محدودیت‌ها)  
6. [کاربردهای رایج](#کاربردهای-رایج)  
7. [Anonymous Types در LINQ](#anonymous-types-در-linq)  
8. [تفاوت با Tuple و Record](#تفاوت-با-tuple-و-record)  
9. [جمع‌بندی](#جمع‌بندی)  
10. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در زبان برنامه‌نویسی C#، **Anonymous Types** (انواع ناشناس) یکی از ویژگی‌های قدرتمندی هستند که به شما امکان می‌دهند بدون نیاز به تعریف یک کلاس صریح، یک شیء با ویژگی‌های مشخص ایجاد کنید. این ویژگی به‌ویژه در سناریوهایی مانند پرس‌وجوهای LINQ بسیار مفید است و به کاهش کد اضافی و افزایش خوانایی کمک می‌کند.

در این مستند، به‌صورت گام‌به‌گام از سطح مقدماتی تا متوسط، Anonymous Types را بررسی می‌کنیم.

---

## تعریف Anonymous Types

**Anonymous Type** نوعی کلاس موقت است که توسط کامپایلر C# به‌صورت خودکار ایجاد می‌شود. این کلاس فقط شامل **فیلدهای خواندنی (read-only properties)** است و نیازی به تعریف دستی کلاس نیست.

این نوع فقط در **محدوده‌ی محلی** (local scope) قابل استفاده است و نمی‌توان آن را به‌عنوان نوع بازگشتی یک متد یا پارامتر ورودی تعریف کرد.

---

## نحوه ایجاد و استفاده

برای ایجاد یک Anonymous Type از کلمه کلیدی `new` همراه با مقداردهی خاصی استفاده می‌شود:

```csharp
var person = new { Name = "Ali", Age = 25 };
```

در اینجا:
- `person` یک شیء از یک نوع ناشناس است.
- این شیء دو ویژگی (`Name` و `Age`) دارد که هر دو **فقط خواندنی** هستند.
- نوع واقعی این شیء توسط کامپایلر تولید می‌شود و شما نمی‌توانید به‌صورت صریح به آن ارجاع دهید.

> 💡 نکته: حتماً از `var` برای تعریف متغیر استفاده کنید، چون نوع واقعی آن ناشناخته است.

---

## ویژگی‌های کلیدی

1. **فقط خواندنی (Read-only)**: تمام ویژگی‌های یک Anonymous Type فقط برای خواندن هستند و نمی‌توان مقدار آن‌ها را تغییر داد.
2. **نوع توسط کامپایلر تولید می‌شود**: کامپایلر یک کلاس داخلی با نامی مانند `<>f__AnonymousType0` ایجاد می‌کند.
3. **پشتیبانی از `Equals` و `GetHashCode`**: دو شیء Anonymous Type با همان نام و نوع ویژگی‌ها و مقادیر یکسان، برابر در نظر گرفته می‌شوند.
4. **قابلیت override کردن `ToString()`**: متد `ToString()` به‌صورت خودکار override می‌شود و مقادیر را به‌صورت خوانا نمایش می‌دهد.

مثال:

```csharp
var p1 = new { Name = "Ali", Age = 25 };
var p2 = new { Name = "Ali", Age = 25 };

Console.WriteLine(p1.Equals(p2)); // True
Console.WriteLine(p1.ToString()); // { Name = Ali, Age = 25 }
```

---

## محدودیت‌ها

- **عدم امکان استفاده در سطح public**: نمی‌توانید یک متد با نوع بازگشتی Anonymous Type داشته باشید.
- **عدم امکان ارسال به متد‌های عمومی**: چون نوع آن ناشناخته است، نمی‌توان آن را به‌عنوان پارامتر به متدی با سیگنیچر مشخص فرستاد.
- **عدم پشتیبانی از متد یا رفتار**: فقط شامل داده (properties) است، نه رفتار (methods).
- **عدم پشتیبانی از inheritance**: نمی‌توان از یک Anonymous Type ارث‌بری کرد.

---

## کاربردهای رایج

1. **پرس‌وجوهای LINQ**: برای انتخاب زیرمجموعه‌ای از داده‌ها بدون نیاز به تعریف کلاس جدید.
2. **موقت نگه‌داشتن داده‌ها**: در متد‌های محلی برای سازماندهی موقت اطلاعات.
3. **تست و دیباگ**: برای سریع‌سازی تست‌های واحد بدون ایجاد کلاس‌های کمکی.

---

## Anonymous Types در LINQ

یکی از پرکاربردترین موارد استفاده از Anonymous Types در **LINQ** است:

```csharp
var employees = new[]
{
    new { Name = "Ali", Department = "IT", Salary = 5000 },
    new { Name = "Sara", Department = "HR", Salary = 4500 }
};

var result = from emp in employees
             where emp.Salary > 4000
             select new { emp.Name, emp.Department };

foreach (var r in result)
{
    Console.WriteLine($"{r.Name} - {r.Department}");
}
```

در اینجا، بدون تعریف کلاسی مانند `EmployeeSummary`، می‌توانیم فقط فیلدهای مورد نیاز را انتخاب کنیم.

---

## تفاوت با Tuple و Record

| ویژگی | Anonymous Type | Tuple | Record |
|--------|----------------|-------|--------|
| قابلیت تغییر | ❌ فقط خواندنی | ✅ (در صورت استفاده از `var`) | بستگی به تعریف دارد |
| نام‌گذاری فیلدها | ✅ | ✅ (در C# 7+) | ✅ |
| قابلیت استفاده در signature متد | ❌ | ✅ | ✅ |
| پشتیبانی از `Equals` / `GetHashCode` | ✅ | ✅ | ✅ |
| مناسب برای LINQ | ✅ | محدود | ❌ (کمتر رایج) |
| معرفی شده در | C# 3.0 | C# 7.0 | C# 9.0 |

> 💡 Anonymous Types همچنان گزینه‌ی بهینه‌ای برای سناریوهای **محلی و موقت** هستند، در حالی که Tuple و Record برای سناریوهای عمومی‌تر مناسب‌ترند.

---

## جمع‌بندی

- Anonymous Types ابزاری قدرتمند برای ایجاد انواع موقت بدون نیاز به تعریف کلاس هستند.
- فقط در محدوده‌ی محلی قابل استفاده‌اند و برای پرس‌وجوهای LINQ بسیار مفیدند.
- دارای محدودیت‌هایی در قابلیت انتقال بین متد‌ها هستند.
- جایگزین‌های مدرن‌تری مانند Tuple و Record وجود دارند، اما Anonymous Types همچنان در جای خود کاربرد دارند.

---

## منابع معتبر

1. **Microsoft Learn – Anonymous Types (C# Programming Guide)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/anonymous-types](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/anonymous-types)

2. **C# Language Specification – Anonymous Types**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/expressions#anonymous-objects](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/expressions#anonymous-objects)

3. **Jon Skeet – C# in Depth (4th Edition)** – Chapter on LINQ and Anonymous Types  
   ISBN: 978-1617294938

4. **Stack Overflow – When to use Anonymous Types**  
   [https://stackoverflow.com/questions/1028961/what-are-the-benefits-of-anonymous-types-in-c](https://stackoverflow.com/questions/1028961/what-are-the-benefits-of-anonymous-types-in-c)

5. **Microsoft Docs – LINQ and Anonymous Types**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/linq/](https://learn.microsoft.com/en-us/dotnet/csharp/linq/)

