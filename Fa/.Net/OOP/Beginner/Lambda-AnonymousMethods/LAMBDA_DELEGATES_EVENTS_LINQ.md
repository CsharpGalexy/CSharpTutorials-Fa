
# راهنمای جامع: استفاده از Lambda و Anonymous Methods با LINQ، Events و Delegates در C#

> 📌 **سطح**: مقدماتی تا متوسط  
> 🎯 **هدف**: آموزش کاربردی برای مخزن آموزش OOP در GitHub  
> 📚 **زبان**: C# (.NET 6 و بالاتر)  
> ✅ **منابع**: مستندات رسمی مایکروسافت و کتاب‌های مرجع

---

## فهرست مطالب

1. [مقدمه](#1-مقدمه)  
2. [مرور سریع: Delegate چیست؟](#2-مرور-سریع-delegate-چیست)  
3. [Anonymous Methods (متدهای ناشناس)](#3-anonymous-methods-متدهای-ناشناس)  
4. [Lambda Expressions (عبارات لامبدا)](#4-lambda-expressions-عبارات-لامبدا)  
5. [تفاوت Lambda و Anonymous Method](#5-تفاوت-lambda-و-anonymous-method)  
6. [کاربرد با Delegates](#6-کاربرد-با-delegates)  
7. [کاربرد با Events](#7-کاربرد-با-events)  
8. [کاربرد با LINQ](#8-کاربرد-با-linq)  
9. [جمع‌بندی و بهترین روش‌ها](#9-جمع‌بندی-و-بهترین-روش‌ها)  
10. [منابع معتبر](#10-منابع-معتبر)

---

## 1. مقدمه

در برنامه‌نویسی شیءگرا (OOP) با C#، گاهی نیاز داریم **رفتارها (Behavior)** را به عنوان داده منتقل کنیم — مثلاً یک تابع را به عنوان پارامتر به متد دیگری بدهیم. برای این کار از **Delegate**ها استفاده می‌شود.

اما نوشتن متدهای جداگانه برای هر Delegate خسته‌کننده است. به همین دلیل، C# دو راه‌حل ارائه می‌دهد:

- **Anonymous Methods** (از C# 2.0)
- **Lambda Expressions** (از C# 3.0)

این دو مفهوم به‌ویژه در سه حوزه کلیدی کاربرد دارند:
- **Delegates** → انتقال رفتار به عنوان داده
- **Events** → واکنش به رویدادها
- **LINQ** → پرس‌وجوهای هوشمند روی داده‌ها

در این راهنما، هر مفهوم را از پایه توضیح داده و با مثال‌های واقعی نشان می‌دهیم چگونه در عمل استفاده می‌شوند.

---

## 2. مرور سریع: Delegate چیست؟

**Delegate** نوعی "اشاره‌گر به متد" است که اجازه می‌دهد متدها به عنوان متغیر ذخیره یا به عنوان پارامتر ارسال شوند.

### مثال ساده:
```csharp
// تعریف Delegate
public delegate void MessageHandler(string message);

// متد معمولی
void PrintMessage(string msg) => Console.WriteLine(msg);

// استفاده
MessageHandler handler = PrintMessage;
handler("Hello from delegate!");
```

> 🔔 بدون Delegate، نمی‌توانستیم Lambda یا Anonymous Method را به Event یا LINQ متصل کنیم.

---

## 3. Anonymous Methods (متدهای ناشناس)

متدهایی هستند که **بدون نام** و مستقیماً در جای استفاده تعریف می‌شوند.

### سینتکس:
```csharp
delegate (پارامترها) { بدنه متد }
```

### مثال:
```csharp
// تعریف Delegate
delegate int MathOperation(int a, int b);

// استفاده از Anonymous Method
MathOperation add = delegate (int x, int y) {
    return x + y;
};

Console.WriteLine(add(3, 4)); // خروجی: 7
```

### ویژگی‌ها:
- نیازی به نام متد نیست.
- می‌تواند به متغیرهای خارجی دسترسی داشته باشد (Closure).
- از C# 2.0 پشتیبانی می‌شود.

> ⚠️ امروزه به‌ندرت استفاده می‌شود و جای خود را به Lambda داده است.

---

## 4. Lambda Expressions (عبارات لامبدا)

روشی **فشرده‌تر و خواناتر** برای نوشتن توابع ناشناس.

### سینتکس کلی:
```csharp
(پارامترها) => عبارت یا { بلوک دستوری }
```

### انواع Lambda:

#### الف) Expression Lambda (عبارتی)
```csharp
Func<int, int> square = x => x * x;
```

#### ب) Statement Lambda (دستوری)
```csharp
Action<string> greet = name => {
    Console.WriteLine($"Hello, {name}!");
};
```

### مثال‌های کاربردی:
```csharp
// بدون پارامتر
Action sayHi = () => Console.WriteLine("Hi!");

// یک پارامتر (بدون پرانتز)
Func<int, bool> isEven = n => n % 2 == 0;

// چند پارامتر
Func<string, string, string> concat = (a, b) => a + " " + b;
```

> ✅ **مزیت**: قابلیت استنباط نوع (Type Inference)، خوانایی بالا، پشتیبانی از Expression Trees.

---

## 5. تفاوت Lambda و Anonymous Method

| ویژگی | Anonymous Method | Lambda Expression |
|-------|------------------|-------------------|
| سینتکس | `delegate (...) { ... }` | `(...) => ...` |
| خوانایی | پایین | بالا |
| استنباط نوع | ❌ | ✅ |
| پشتیبانی از Expression Tree | ❌ | ✅ |
| استفاده در LINQ | غیرمستقیم | مستقیم و طبیعی |
| توصیه شده؟ | ❌ | ✅ |

> 💡 **نتیجه**: همیشه از Lambda استفاده کنید مگر در موارد بسیار خاص.

---

## 6. کاربرد با Delegates

Lambda و Anonymous Method می‌توانند مستقیماً به Delegateها اختصاص داده شوند.

### مثال با Delegate سفارشی:
```csharp
public delegate bool Validator(string input);

Validator isNotEmpty = s => !string.IsNullOrEmpty(s);
Console.WriteLine(isNotEmpty("Test")); // True
```

### استفاده از Delegateهای داخلی C#:
C# delegateهای آماده‌ای دارد که نیاز به تعریف دستی نیست:

- `Action<T>` → متد بدون خروجی
- `Func<T, TResult>` → متد با خروجی
- `Predicate<T>` → متدی که `bool` برمی‌گرداند

```csharp
Func<int, string> toText = num => num.ToString();
Action log = () => Console.WriteLine("Logging...");
Predicate<int> isPositive = x => x > 0;
```

> ✅ این delegateها در سراسر کتابخانه‌های .NET استفاده می‌شوند.

---

## 7. کاربرد با Events

Eventها در C# بر پایه Delegateها ساخته شده‌اند. Lambdaها برای **گوش دادن به رویدادها (Event Handling)** بسیار کاربردی هستند.

### مثال:
```csharp
public class Timer
{
    public event Action<string> Tick;

    public void Start()
    {
        for (int i = 1; i <= 3; i++)
        {
            Thread.Sleep(1000);
            Tick?.Invoke($"Second {i}");
        }
    }
}

// استفاده
var timer = new Timer();
timer.Tick += message => Console.WriteLine($"⏰ {message}");
timer.Start();
```

### نکته مهم درباره Unsubscribe:
اگر نیاز دارید بعداً Event را لغو کنید (Unsubscribe)، Lambda مناسب نیست:

```csharp
// ❌ اشتباه: نمی‌توانید Unsubscribe کنید
timer.Tick += msg => Console.WriteLine(msg);

// ✅ درست: ابتدا متد را ذخیره کنید
Action<string> handler = msg => Console.WriteLine(msg);
timer.Tick += handler;
// بعداً:
timer.Tick -= handler;
```

---

## 8. کاربرد با LINQ

**LINQ** (Language Integrated Query) به‌طور گسترده از Lambda Expressions استفاده می‌کند تا پرس‌وجوهایی مانند فیلتر، تبدیل و مرتب‌سازی را امکان‌پذیر کند.

### مثال‌های پرکاربرد:

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8 };

// فیلتر (Where)
var evens = numbers.Where(n => n % 2 == 0);

// تبدیل (Select)
var squares = numbers.Select(n => n * n);

// ترکیب
var result = numbers
    .Where(n => n > 3)
    .Select(n => $"Number: {n}")
    .ToList();

// مرتب‌سازی
var sorted = numbers.OrderBy(n => n);
```

### Lambda در دو سبک LINQ:

| سبک | مثال | توضیح |
|------|------|--------|
| **Method Syntax** | `list.Where(x => x > 5)` | مستقیم از Lambda استفاده می‌کند. **ترجیح داده می‌شود**. |
| **Query Syntax** | `from x in list where x > 5 select x` | شبیه SQL است، اما در پس‌زمینه به Lambda تبدیل می‌شود. |

> ✅ **توصیه**: برای خوانایی و انعطاف‌پذیری بیشتر، از **Method Syntax با Lambda** استفاده کنید.

---

## 9. جمع‌بندی و بهترین روش‌ها

- ✅ **همیشه از Lambda Expressions** به جای Anonymous Methods استفاده کنید.
- ✅ برای Delegateهای ساده، از `Action` و `Func` استفاده کنید.
- ✅ در LINQ، از **Method Syntax** با Lambda استفاده کنید.
- ⚠️ در Eventها، اگر نیاز به **Unsubscribe** دارید، Lambda را در یک متغیر ذخیره کنید.
- 🔒 از **Closure** (دسترسی به متغیرهای خارجی) با احتیاط استفاده کنید — ممکن است باعث memory leak شود.
- 🧪 Lambdaها را در Unit Testها به‌راحتی می‌توان تست کرد.

---

## 10. منابع معتبر

1. **Microsoft Learn – Lambda Expressions**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions)

2. **Microsoft Learn – Anonymous Methods**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods)

3. **Microsoft Learn – Delegates**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

4. **Microsoft Learn – Events**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)

5. **Microsoft Learn – LINQ**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/linq/](https://learn.microsoft.com/en-us/dotnet/csharp/linq/)

6. **C# in Depth – Jon Skeet (4th Edition)**  
   📘 فصل 9: Lambda Expressions، فصل 11: LINQ

7. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   📘 فصل‌های 13 (Delegates & Events) و 15 (LINQ)

