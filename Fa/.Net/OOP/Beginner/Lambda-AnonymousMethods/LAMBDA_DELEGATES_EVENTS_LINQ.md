

## فهرست مطالب

1. [مقدمه](#1-مقدمه)  
2. [Anonymous Methods (متدهای ناشناس)](#2-anonymous-methods-متدهای-ناشناس)  
3. [Lambda Expressions (عبارات لامبدا)](#3-lambda-expressions-عبارات-لامبدا)  
4. [تفاوت Lambda و Anonymous Method](#4-تفاوت-lambda-و-anonymous-method)  
5. [کاربرد با Delegates](#5-کاربرد-با-delegates)  
6. [کاربرد با Events](#6-کاربرد-با-events)  
7. [کاربرد با LINQ](#7-کاربرد-با-linq)  
8. [جمع‌بندی و بهترین روش‌ها (Best Practices)](#8-جمع‌بندی-و-بهترین-روش‌ها-best-practices)  
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه

در زبان C#، **Delegate**ها نوعی "اشاره‌گر به متد" هستند که اجازه می‌دهند متدها به عنوان پارامتر ارسال شوند. برای ساده‌سازی کار با Delegateها، دو مفهوم **Anonymous Methods** و **Lambda Expressions** معرفی شده‌اند. این مفاهیم به‌ویژه در کار با **LINQ**، **Events** و برنامه‌نویسی تابعی (Functional Programming) بسیار کاربردی هستند.

در این راهنما، از ساده‌ترین مفاهیم شروع کرده و به کاربردهای عملی در LINQ، Event Handling و Delegateها می‌پردازیم.

---

## 2. Anonymous Methods (متدهای ناشناس)

### تعریف
متدهایی هستند که **بدون نام** تعریف می‌شوند و مستقیماً به عنوان مقدار یک Delegate استفاده می‌شوند.

### سینتکس
```csharp
delegate void MyDelegate(string msg);

// استفاده از Anonymous Method
MyDelegate del = delegate (string s) {
    Console.WriteLine(s);
};
del("Hello from anonymous method!");
```

### ویژگی‌ها
- نیازی به تعریف متد جداگانه نیست.
- می‌توانند به متغیرهای محلی (Local Variables) دسترسی داشته باشند (Closure).
- از C# 2.0 پشتیبانی می‌شوند.

> ⚠️ امروزه به‌ندرت استفاده می‌شوند و جای خود را به Lambda Expressions داده‌اند.

---

## 3. Lambda Expressions (عبارات لامبدا)

### تعریف
روشی فشرده و خوانا برای نوشتن توابع ناشناس. از علامت `=>` (arrow operator) استفاده می‌کند.

### سینتکس
```csharp
// سینتکس کلی:
(input-parameters) => expression-or-statement-block
```

### مثال‌های ساده
```csharp
// 1. بدون پارامتر
Action greet = () => Console.WriteLine("Hello!");

// 2. یک پارامتر
Func<int, int> square = x => x * x;

// 3. چند پارامتر
Func<int, int, int> add = (x, y) => x + y;

// 4. بدنه چندخطی
Func<int, string> describe = (num) => {
    if (num > 0) return "Positive";
    else if (num < 0) return "Negative";
    return "Zero";
};
```

### انواع Lambda
- **Expression Lambda**: بدنه یک عبارت (Expression) است.  
  مثال: `x => x * x`
- **Statement Lambda**: بدنه یک بلوک دستوری `{ }` است.  
  مثال: `x => { return x * x; }`

---

## 4. تفاوت Lambda و Anonymous Method

| ویژگی | Anonymous Method | Lambda Expression |
|--------|------------------|-------------------|
| سینتکس | طولانی‌تر (`delegate (...) { }`) | فشرده‌تر (`(...) => ...`) |
| قابلیت استنباط نوع | ❌ | ✅ (Type inference) |
| پشتیبانی از Expression Trees | ❌ | ✅ |
| خوانایی | پایین‌تر | بالاتر |
| استفاده در LINQ | ❌ (مستقیم) | ✅ |

> ✅ **توصیه**: همیشه از Lambda استفاده کنید مگر در موارد بسیار خاص.

---

## 5. کاربرد با Delegates

Delegateها می‌توانند با Lambda یا Anonymous Method مقداردهی شوند.

### مثال عملی
```csharp
// تعریف Delegate
public delegate bool FilterDelegate(int number);

// استفاده با Lambda
FilterDelegate isEven = n => n % 2 == 0;

// استفاده
Console.WriteLine(isEven(4)); // True
```

### Delegateهای داخلی C#
C# delegateهای آماده‌ای دارد که نیاز به تعریف دستی نیست:

- `Action<T>`: برای متدهایی بدون خروجی.
- `Func<T, TResult>`: برای متدهایی با خروجی.
- `Predicate<T>`: برای متدهایی که `bool` برمی‌گردانند.

```csharp
Func<string, int> getLength = s => s.Length;
Action<string> print = s => Console.WriteLine(s);
Predicate<int> isPositive = x => x > 0;
```

---

## 6. کاربرد با Events

Eventها در C# بر پایه Delegateها ساخته شده‌اند. Lambdaها برای **اشتراک‌گذاری رویدادها (Event Subscription)** بسیار مفید هستند.

### مثال
```csharp
public class Button
{
    public event Action Clicked;
    
    public void SimulateClick() => Clicked?.Invoke();
}

// استفاده
var btn = new Button();
btn.Clicked += () => Console.WriteLine("Button clicked!");

btn.SimulateClick(); // خروجی: Button clicked!
```

> 💡 نکته: استفاده از Lambda برای Eventها در صورتی که نیاز به **Unsubscribe** ندارید مناسب است. در غیر این صورت، بهتر است از متد نام‌دار استفاده کنید.

---

## 7. کاربرد با LINQ

LINQ (Language Integrated Query) به‌طور گسترده از Lambda Expressions استفاده می‌کند.

### مثال‌های رایج
```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5, 6 };

// فیلتر کردن
var evens = numbers.Where(n => n % 2 == 0);

// تبدیل
var squares = numbers.Select(n => n * n);

// ترکیب
var result = numbers
    .Where(n => n > 2)
    .Select(n => n.ToString())
    .ToList();
```

### Lambda در Query Syntax vs Method Syntax
- **Method Syntax**: مستقیماً از Lambda استفاده می‌کند.
- **Query Syntax**: از کلمات کلیدی مانند `from`, `where`, `select` استفاده می‌کند (اما در پس‌زمینه به Lambda تبدیل می‌شود).

```csharp
// Method Syntax (ترجیح داده می‌شود)
var query1 = numbers.Where(x => x > 3);

// Query Syntax
var query2 = from x in numbers where x > 3 select x;
```

> ✅ **توصیه**: برای خوانایی و قدرت بیشتر، از **Method Syntax** با Lambda استفاده کنید.

---

## 8. جمع‌بندی و بهترین روش‌ها (Best Practices)

- ✅ همیشه از **Lambda Expressions** به جای Anonymous Methods استفاده کنید.
- ✅ برای Eventها از Lambda استفاده کنید، مگر اینکه نیاز به Unsubscribe داشته باشید.
- ✅ در LINQ، از Method Syntax با Lambda برای خوانایی بیشتر استفاده کنید.
- ⚠️ از Capture کردن متغیرهای محلی در Lambdaها با احتیاط استفاده کنید (Closure).
- ✅ برای Delegateهای ساده، از `Action` و `Func` استفاده کنید، نه تعریف دستی Delegate.

---

## 9. منابع معتبر

1. **Microsoft Learn – Lambda Expressions (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions)

2. **Microsoft Learn – Anonymous Methods (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods)

3. **Microsoft Learn – Delegates**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

4. **Microsoft Learn – LINQ**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/linq/](https://learn.microsoft.com/en-us/dotnet/csharp/linq/)

5. **C# in Depth – Jon Skeet (4th Edition)**  
   📘 فصل 9: Lambda Expressions و فصل 11: LINQ

6. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   📘 فصل‌های مربوط به Delegates، Events و LINQ

---

> ✨ این راهنما برای استفاده در مخزن GitHub شما آماده شده است. می‌توانید آن را به‌عنوان `LAMBDA_ANONYMOUS_METHODS.md` در پوشه‌ی آموزشی OOP قرار دهید.

اگر نیاز به نسخه‌ی فارسی‌تر یا مثال‌های بیشتر داشتید، خوشحال می‌شوم کمک کنم!

### USER
از مقدماتی تا متوط هر چی در مورد  Use with LINQ, Events, Delegates در Lambda & Anonymous Methods فقط در مورد این بگو میدونی در زبان سیشارپ بگو به صورت مستند میخوام



یه ریپوزیتوری دارم دارم آموزش oop میزارم توش  برای اون میخوام



لطفا از منابع معتبر جمع اوری کن



منابع هم بگو در اخر

دارای فهرست مطالب و لینک دهی معتبر  باشه

یه جوری باشه خوانایی  خوبی داشته باشه برای کسی که مبتدی است

### ASSISTANT
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

