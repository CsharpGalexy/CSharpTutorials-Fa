

## فهرست مطالب

1. [مقدمه‌ای بر Delegates](#1-مقدمه‌ای-بر-delegates)  
2. [چرا از Delegate استفاده می‌کنیم؟](#2-چرا-از-delegate-استفاده-میکنیم)  
3. [انواع Built-in Delegates در C#](#3-انواع-built-in-delegates-در-c)  
   - [3.1 Action](#31-action)  
   - [3.2 Func](#32-func)  
   - [3.3 Predicate](#33-predicate)  
4. [تفاوت Action، Func و Predicate](#4-تفاوت-action-func-و-predicate)  
5. [کاربرد Delegates در Eventها](#5-کاربرد-delegates-در-eventها)  
6. [نکات کلیدی و Best Practices](#6-نکات-کلیدی-و-best-practices)  
7. [جمع‌بندی](#7-جمعبندی)  
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه‌ای بر Delegates

در زبان C#، **Delegate** یک نوع داده (Type) است که به **ارجاع به یک متد** اجازه می‌دهد — دقیقاً مانند اشاره‌گر به تابع در C/C++.  
Delegate‌ها نوع‌های **نوع‌سازگار (Type-safe)** هستند، یعنی امضای متدی که به آن ارجاع می‌دهند باید با امضای تعریف‌شده برای Delegate مطابقت داشته باشد.

### تعریف سفارشی Delegate:

```csharp
public delegate void MyDelegate(string message);
```

این Delegate فقط می‌تواند به متدی ارجاع دهد که:
- **ورودی**: یک `string`
- **خروجی**: `void`

### استفاده:

```csharp
public void PrintMessage(string msg) => Console.WriteLine(msg);

MyDelegate del = new MyDelegate(PrintMessage);
del("Hello, Delegate!");
```

> 💡 **نکته**: Delegate‌ها در C# از نوع `MulticastDelegate` هستند، یعنی می‌توانند چندین متد را به صورت زنجیره‌ای فراخوانی کنند (با عملگرهای `+` و `-`).

---

## 2. چرا از Delegate استفاده می‌کنیم؟

Delegate‌ها امکانات زیر را فراهم می‌کنند:

- **Callback**: اجرای یک متد پس از اتمام یک عملیات (مثلاً در I/O یا شبکه).
- **Event Handling**: سیستم رویداد محور (Event-driven) در برنامه‌نویسی رابط کاربری.
- **Functional Programming**: امکان ارسال متد به عنوان پارامتر (مثل LINQ).
- **Decoupling**: کاهش وابستگی بین کامپوننت‌ها.

---

## 3. انواع Built-in Delegates در C#

به جای تعریف Delegate دستی، C# چند نوع Delegate عمومی (Generic) از پیش تعریف‌شده دارد:

### 3.1 Action

- **بدون مقدار بازگشتی** (`void`)
- **تا 16 پارامتر ورودی** (Generic)

#### تعریف:

```csharp
public delegate void Action<in T>(T obj);
public delegate void Action<in T1, in T2>(T1 arg1, T2 arg2);
// ... تا Action<T1, ..., T16>
```

#### مثال:

```csharp
Action<string> print = msg => Console.WriteLine(msg);
print("Hello from Action!");
```

---

### 3.2 Func

- **دارای مقدار بازگشتی**
- **آخرین پارامتر نوع بازگشتی است**
- **تا 16 پارامتر ورودی + 1 خروجی**

#### تعریف:

```csharp
public delegate TResult Func<in T, out TResult>(T arg);
public delegate TResult Func<in T1, in T2, out TResult>(T1 arg1, T2 arg2);
// ... تا Func<T1, ..., T16, TResult>
```

#### مثال:

```csharp
Func<int, int, int> add = (a, b) => a + b;
int result = add(3, 4); // 7
```

---

### 3.3 Predicate

- **نوع خاصی از `Func<T, bool>`**
- فقط یک ورودی دارد و **همیشه `bool` برمی‌گرداند**
- معمولاً برای **فیلتر کردن** (مثل در LINQ) استفاده می‌شود

#### تعریف:

```csharp
public delegate bool Predicate<in T>(T obj);
```

#### مثال:

```csharp
Predicate<int> isEven = n => n % 2 == 0;
bool check = isEven(4); // true
```

> 💡 `Predicate<T>` معادل `Func<T, bool>` است، اما نام معنایی‌تری برای شرایط فیلتر دارد.

---

## 4. تفاوت Action، Func و Predicate

| ویژگی         | Action               | Func                          | Predicate             |
|----------------|----------------------|-------------------------------|------------------------|
| **مقدار بازگشتی** | ❌ (`void`)          | ✅ (هر نوع)                   | ✅ (`bool`)           |
| **ورودی**       | 0 تا 16 پارامتر      | 1 تا 16 ورودی + 1 خروجی       | دقیقاً 1 ورودی        |
| **کاربرد رایج** | انجام عمل (side-effect) | محاسبه و بازگرداندن نتیجه   | بررسی شرط (فیلتر)     |
| **معادل**       | —                    | —                             | `Func<T, bool>`       |

---

## 5. کاربرد Delegates در Eventها

در C#، **Eventها بر پایه Delegate هستند**.  
Eventها از نوع خاصی از Delegate به نام `EventHandler` یا `EventHandler<TEventArgs>` استفاده می‌کنند.

### تعریف Event:

```csharp
public class Button
{
    public event EventHandler Click;

    public void OnClick()
    {
        Click?.Invoke(this, EventArgs.Empty);
    }
}
```

### ثبت Event Handler:

```csharp
Button btn = new Button();
btn.Click += (sender, e) => Console.WriteLine("Button clicked!");
btn.OnClick(); // خروجی: Button clicked!
```

> 🔒 **نکته امنیتی**: Eventها فقط از داخل کلاس می‌توانند فراخوانی شوند (`invoke`)، اما از خارج فقط می‌توان به آن‌ها **اشتراک‌گذاری (+=)** یا **لغو اشتراک (-=)** کرد.

---

## 6. نکات کلیدی و Best Practices

- از `Action` و `Func` به جای تعریف Delegate دستی استفاده کنید (مگر نیاز خاصی داشته باشید).
- برای Eventها از `EventHandler<T>` استاندارد استفاده کنید.
- `Predicate<T>` را فقط زمانی استفاده کنید که منظورتان **فیلتر یا شرط** است (خوانایی کد بالاتر می‌رود).
- از Null-conditional operator (`?.`) برای فراخوانی Eventها استفاده کنید تا از `NullReferenceException` جلوگیری شود.
- در برنامه‌نویسی مدرن C#، اغلب از **Lambda Expressions** برای ایجاد Delegate استفاده می‌شود.

---

## 7. جمع‌بندی

Delegate‌ها ستون فقرات **برنامه‌نویسی رویدادمحور** و **کدنویسی تابعی** در C# هستند.  
با استفاده از `Action`، `Func` و `Predicate` می‌توانید بدون تعریف انواع جدید، به راحتی متد را به عنوان داده ارسال کنید.  
Eventها نیز بر اساس Delegate ساخته شده‌اند و امکان طراحی سیستم‌های **Loosely Coupled** را فراهم می‌کنند.

---

## 8. منابع معتبر

1. **Microsoft Learn – Delegates (C# Programming Guide)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

2. **Microsoft Learn – Action Delegate**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.action](https://learn.microsoft.com/en-us/dotnet/api/system.action)

3. **Microsoft Learn – Func Delegate**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.func-2](https://learn.microsoft.com/en-us/dotnet/api/system.func-2)

4. **Microsoft Learn – Predicate Delegate**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.predicate-1](https://learn.microsoft.com/en-us/dotnet/api/system.predicate-1)

5. **Microsoft Learn – Events (C# Programming Guide)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)

6. **C# in Depth – Jon Skeet** (فصل 5: Delegates and Events)  
   📘 ISBN: 978-1617294532

7. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   📘 فصل 15: Understanding Delegates and Events

