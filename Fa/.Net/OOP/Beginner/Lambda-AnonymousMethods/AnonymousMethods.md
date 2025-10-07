

## فهرست مطالب

1. [مقدمه](#1-مقدمه)
2. [Anonymous Methods چیست؟](#2-anonymous-methods-چیست)
   - [تعریف](#تعریف)
   - [نحوه تعریف و استفاده](#نحوه-تعریف-و-استفاده)
   - [مثال عملی](#مثال-عملی)
3. [Lambda Expressions چیست؟](#3-lambda-expressions-چیست)
   - [تعریف](#تعریف-1)
   - [نحوه نوشتن](#نحوه-نوشتن)
   - [مثال‌های کاربردی](#مثالهای-کاربردی)
4. [تفاوت Anonymous Methods و Lambda Expressions](#4-تفاوت-anonymous-methods-و-lambda-expressions)
5. [استفاده در برنامه‌نویسی شیءگرا (OOP)](#5-استفاده-در-برنامه%E2%80%8Cنویسی-شیءگرا-oop)
6. [بهترین شیوه‌ها (Best Practices)](#6-بهترین-شیوه%E2%80%8Cها-best-practices)
7. [جمع‌بندی](#7-جمع%E2%80%8Cبندی)
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه

در زبان برنامه‌نویسی **C#**، گاهی نیاز داریم توابعی را تعریف کنیم که فقط یک‌بار استفاده می‌شوند یا به‌عنوان آرگومان به توابع دیگر داده می‌شوند. در چنین مواردی، نیازی به تعریف یک متد نام‌دار (Named Method) نیست. برای این منظور، دو مفهوم قدرتمند وجود دارند:

- **Anonymous Methods** (متد‌های ناشناس)
- **Lambda Expressions** (عبارات لامبدا)

این دو مفهوم به ما امکان می‌دهند که **توابع بدون نام** را به‌صورت درون‌خط (inline) تعریف کنیم و آن‌ها را به‌راحتی به‌عنوان پارامتر به متد‌های دیگر بفرستیم.

---

## 2. Anonymous Methods چیست؟

### تعریف

**Anonymous Method** یک متد بدون نام است که می‌تواند به‌جای یک دلیگیت (Delegate) استفاده شود. این متد‌ها در زمان کامپایل به‌صورت متد‌های عادی تبدیل می‌شوند، اما نیازی به تعریف جداگانه ندارند.

### نحوه تعریف و استفاده

```csharp
delegate void MyDelegate(string message);

// استفاده از Anonymous Method
MyDelegate del = delegate(string msg)
{
    Console.WriteLine("Anonymous Method: " + msg);
};

del("Hello from anonymous method!");
```

### مثال عملی

```csharp
using System;

class Program
{
    delegate int MathOperation(int a, int b);

    static void Main()
    {
        // تعریف یک Anonymous Method برای جمع
        MathOperation add = delegate(int x, int y)
        {
            return x + y;
        };

        Console.WriteLine(add(5, 3)); // خروجی: 8
    }
}
```

> 💡 **نکته:** Anonymous Methods از C# 2.0 پشتیبانی می‌شوند.

---

## 3. Lambda Expressions چیست؟

### تعریف

**Lambda Expression** یک نحو مختصرتر و خواناتر برای نوشتن Anonymous Methods است. این عبارات با استفاده از عملگر `=>` (عملگر لامبدا) نوشته می‌شوند و بیشتر در LINQ و برنامه‌نویسی تابعی (Functional Programming) کاربرد دارند.

### نحوه نوشتن

ساختار کلی:
```csharp
(input-parameters) => expression-or-statement-block
```

### مثال‌های کاربردی

#### مثال ۱: Lambda ساده
```csharp
Func<int, int, int> multiply = (x, y) => x * y;
Console.WriteLine(multiply(4, 5)); // خروجی: 20
```

#### مثال ۲: Lambda با بدنه بلوکی
```csharp
Action<string> greet = name =>
{
    Console.WriteLine("Hello, " + name);
    Console.WriteLine("Welcome to C#!");
};

greet("Ali");
```

#### مثال ۳: استفاده در LINQ
```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0).ToList();
// evenNumbers شامل [2, 4] است
```

> 💡 **نکته:** Lambda Expressions از C# 3.0 معرفی شدند و امروزه جایگزین رایج‌تری برای Anonymous Methods هستند.

---

## 4. تفاوت Anonymous Methods و Lambda Expressions

| ویژگی | Anonymous Method | Lambda Expression |
|--------|------------------|-------------------|
| **نسخه C#** | از 2.0 | از 3.0 |
| **خوانایی** | کمتر | بیشتر |
| **پشتیبانی از Type Inference** | خیر | بله |
| **قابلیت تبدیل به Expression Tree** | خیر | بله (در صورت استفاده از `Expression<T>`) |
| **استفاده در LINQ** | نامناسب | ایده‌آل |

> ✅ **نتیجه:** در اکثر موارد، استفاده از **Lambda Expressions** توصیه می‌شود.

---

## 5. استفاده در برنامه‌نویسی شیءگرا (OOP)

اگرچه Anonymous Methods و Lambda Expressions بیشتر در **برنامه‌نویسی تابعی** کاربرد دارند، اما در **OOP** نیز کاربردهای مهمی دارند:

- **Event Handling**: تعریف هندلرهای رویداد به‌صورت inline
- **Callback Functions**: ارسال توابع به‌عنوان بازخورد
- **Custom Comparers**: برای مرتب‌سازی یا فیلتر کردن اشیاء
- **Dependency Injection**: تزریق رفتارهای موقت

### مثال: Event Handling با Lambda
```csharp
button.Click += (sender, e) => MessageBox.Show("Button clicked!");
```

### مثال: Custom Sorting
```csharp
List<Person> people = GetPeople();
people.Sort((p1, p2) => p1.Age.CompareTo(p2.Age));
```

---

## 6. بهترین شیوه‌ها (Best Practices)

1. **از Lambda برای کدهای کوتاه استفاده کنید** – برای منطق پیچیده، متد نام‌دار بهتر است.
2. **از Capture متغیرهای خارجی (Closure) با احتیاط استفاده کنید** – ممکن است باعث memory leak یا رفتار غیرمنتظره شود.
3. **در LINQ حتماً از Lambda استفاده کنید** – خوانایی و کارایی بالاتری دارد.
4. **برای افزایش تست‌پذیری، Lambdaهای پیچیده را به متد جداگانه تبدیل کنید.**

---

## 7. جمع‌بندی

- **Anonymous Methods** و **Lambda Expressions** ابزارهای قدرتمندی برای نوشتن توابع درون‌خط در C# هستند.
- Lambda Expressions خواناتر، مختصرتر و قابلیت‌های بیشتری نسبت به Anonymous Methods دارند.
- این مفاهیم در OOP برای مدیریت رویدادها، کال‌بک‌ها و پردازش داده‌ها بسیار مفید هستند.
- همیشه سعی کنید از Lambda Expressions استفاده کنید، مگر در موارد خاصی که Anonymous Method مناسب‌تر باشد.

---

## 8. منابع معتبر

1. **Microsoft Learn – Anonymous Methods (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods)

2. **Microsoft Learn – Lambda Expressions (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions)

3. **C# in Depth – Jon Skeet** (فصل 9: Lambda Expressions)  
   🔗 [https://livebook.manning.com/book/c-sharp-in-depth-fourth-edition](https://livebook.manning.com/book/c-sharp-in-depth-fourth-edition)

4. **C# Language Specification – ECMA-334**  
   🔗 [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

5. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   (فصل 14: Delegates, Events, and Lambda Expressions)

