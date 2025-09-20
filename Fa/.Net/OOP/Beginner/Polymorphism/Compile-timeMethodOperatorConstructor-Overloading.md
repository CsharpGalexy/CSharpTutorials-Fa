

## 📖 فهرست مطالب

1. [مقدمه: Overloading چیست؟](#1-مقدمه-overloading-چیست)  
2. [Method Overloading (بارگذاری متدها)](#2-method-overloading-بارگذاری-متدها)  
3. [Constructor Overloading (بارگذاری سازنده‌ها)](#3-constructor-overloading-بارگذاری-سازندهها)  
4. [Operator Overloading (بارگذاری عملگرها)](#4-operator-overloading-بارگذاری-عملگرها)  
5. [قوانین و محدودیت‌های Overloading](#5-قوانین-و-محدودیتهای-overloading)  
6. [تفاوت Overloading با Overriding](#6-تفاوت-overloading-با-overriding)  
7. [بهترین روش‌ها و نکات طلایی](#7-بهترین-روشها-و-نکات-طلایی)  
8. [جمع‌بندی](#8-جمعبندی)  
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه: Overloading چیست؟

در برنامه‌نویسی شیءگرا (OOP)، **Overloading** به معنی تعریف چندین عضو (متد، سازنده یا عملگر) با **همان نام** اما **امضای متفاوت** است. این کار به شما اجازه می‌دهد که یک عملکرد را با ورودی‌های مختلف پیاده‌سازی کنید — بدون نیاز به تغییر نام!

🔹 **Compile-time Polymorphism**: Overloading در زمان کامپایل تصمیم‌گیری می‌شود. یعنی کامپایلر بر اساس تعداد، نوع و ترتیب پارامترها، متد/سازنده/عملگر مناسب را انتخاب می‌کند.

---

## 2. Method Overloading (بارگذاری متدها)

### 🔹 تعریف
شما می‌توانید چندین متد با **همان نام** اما **پارامترهای متفاوت** در یک کلاس تعریف کنید.

### 🔹 شرایط Overloading
- تفاوت در **تعداد پارامترها**
- تفاوت در **نوع پارامترها**
- تفاوت در **ترتیب پارامترها**

> ⚠️ تفاوت فقط در نوع بازگشتی (return type) کافی نیست و باعث خطا می‌شود.

### 🔹 مثال ساده

```csharp
public class Calculator
{
    // جمع دو عدد صحیح
    public int Add(int a, int b)
    {
        return a + b;
    }

    // جمع سه عدد صحیح
    public int Add(int a, int b, int c)
    {
        return a + b + c;
    }

    // جمع دو عدد اعشاری
    public double Add(double a, double b)
    {
        return a + b;
    }

    // جمع دو رشته (الحاق)
    public string Add(string a, string b)
    {
        return a + b;
    }
}
```

### 🔹 نحوه استفاده

```csharp
Calculator calc = new Calculator();
Console.WriteLine(calc.Add(2, 3));           // خروجی: 5
Console.WriteLine(calc.Add(2, 3, 4));       // خروجی: 9
Console.WriteLine(calc.Add(2.5, 3.7));      // خروجی: 6.2
Console.WriteLine(calc.Add("Hello ", "C#")); // خروجی: Hello C#
```

---

## 3. Constructor Overloading (بارگذاری سازنده‌ها)

### 🔹 تعریف
شما می‌توانید چندین سازنده (Constructor) با **پارامترهای متفاوت** در یک کلاس تعریف کنید تا اشیاء را به روش‌های مختلف ایجاد کنید.

### 🔹 مثال

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    // سازنده پیش‌فرض
    public Person()
    {
        Name = "Unknown";
        Age = 0;
    }

    // سازنده با نام
    public Person(string name)
    {
        Name = name;
        Age = 0;
    }

    // سازنده با نام و سن
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

### 🔹 نحوه استفاده

```csharp
Person p1 = new Person();               // Name: "Unknown", Age: 0
Person p2 = new Person("Ali");          // Name: "Ali", Age: 0
Person p3 = new Person("Sara", 25);     // Name: "Sara", Age: 25
```

### 🔹 استفاده از `this` برای جلوگیری از تکرار کد

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Person() : this("Unknown", 0) { }

    public Person(string name) : this(name, 0) { }

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

> ✅ این کار باعث کاهش تکرار کد و افزایش خوانایی می‌شود.

---

## 4. Operator Overloading (بارگذاری عملگرها)

### 🔹 تعریف
شما می‌توانید رفتار عملگرهایی مثل `+`, `-`, `==`, `!=` و ... را برای کلاس‌های خودتان تعریف کنید.

### 🔹 قوانین مهم
- عملگرها باید به صورت **static** و **public** تعریف شوند.
- حداقل یکی از عملوندها باید از نوع کلاس شما باشد.
- برخی عملگرها **جفتی** هستند (مثل `==` و `!=`) — اگر یکی را overload کنید، باید دیگری را هم overload کنید.

### 🔹 مثال: بارگذاری عملگر `+` برای جمع دو نقطه

```csharp
public class Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    // بارگذاری عملگر +
    public static Point operator +(Point p1, Point p2)
    {
        return new Point(p1.X + p2.X, p1.Y + p2.Y);
    }

    // بارگذاری عملگر ==
    public static bool operator ==(Point p1, Point p2)
    {
        if (ReferenceEquals(p1, p2)) return true;
        if (p1 is null || p2 is null) return false;
        return p1.X == p2.X && p1.Y == p2.Y;
    }

    // بارگذاری عملگر !=
    public static bool operator !=(Point p1, Point p2)
    {
        return !(p1 == p2);
    }

    // override Equals و GetHashCode برای حفظ انسجام
    public override bool Equals(object obj)
    {
        return obj is Point point && this == point;
    }

    public override int GetHashCode()
    {
        return HashCode.Combine(X, Y);
    }
}
```

### 🔹 نحوه استفاده

```csharp
Point p1 = new Point(1, 2);
Point p2 = new Point(3, 4);
Point p3 = p1 + p2; // Point(4, 6)

Console.WriteLine($"({p3.X}, {p3.Y})"); // خروجی: (4, 6)

if (p1 == new Point(1, 2))
    Console.WriteLine("Points are equal!"); // این خط چاپ می‌شود
```

---

## 5. قوانین و محدودیت‌های Overloading

| نوع Overloading       | قوانین مهم                                                                 |
|------------------------|----------------------------------------------------------------------------|
| **Method**             | - تفاوت در پارامترها (تعداد، نوع، ترتیب) الزامی است. <br> - نوع بازگشتی مهم نیست. |
| **Constructor**        | - مانند Method Overloading اما بدون نام بازگشتی. <br> - می‌توان با `this` به هم ارجاع داد. |
| **Operator**           | - باید static و public باشد. <br> - حداقل یک عملوند از نوع کلاس فعلی. <br> - بعضی عملگرها جفتی هستند. |

### 🔹 عملگرهای قابل بارگذاری در C#

✅ قابل Overload:
- عملگرهای دوتایی: `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, `<=`
- عملگرهای یکتایی: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`
- عملگرهای تبدیل: `(T)`

❌ غیرقابل Overload:
- `=`, `.`, `?:`, `??`, `??=`, `->`, `=>`, `sizeof`, `typeof`, `as`, `is`, `new`, `await`

> 📌 منبع: [Microsoft Docs - Operator overloading](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/operator-overloading)

---

## 6. تفاوت Overloading با Overriding

| ویژگی             | Overloading                            | Overriding                              |
|-------------------|----------------------------------------|------------------------------------------|
| زمان تصمیم‌گیری   | Compile-time (ایستا)                   | Runtime (پویا)                           |
| مکان              | در یک کلاس                             | بین کلاس پایه و کلاس مشتق               |
| امضا              | باید متفاوت باشد                       | باید دقیقاً یکسان باشد                   |
| کلمه کلیدی        | نیازی نیست                             | نیاز به `virtual` در پایه و `override` در مشتق |
| هدف               | راحتی استفاده با پارامترهای مختلف     | تغییر رفتار متد در کلاس‌های فرزند       |

### 🔹 مثال Overriding

```csharp
public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal speaks");
    }
}

public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Dog barks");
    }
}
```

---

## 7. بهترین روش‌ها و نکات طلایی

✅ **استفاده از پارامترهای اختیاری به جای Overloading بیش از حد**  
اگر تفاوت‌ها جزئی است، از پارامترهای پیش‌فرض استفاده کنید:

```csharp
public void Log(string message, LogLevel level = LogLevel.Info)
{
    // ...
}
```

✅ **مستندسازی با XML Comments**  
برای کمک به توسعه‌دهندگان دیگر، هر overload را مستند کنید:

```csharp
/// <summary>
/// Adds two integers.
/// </summary>
public int Add(int a, int b) { ... }

/// <summary>
/// Adds two doubles.
/// </summary>
public double Add(double a, double b) { ... }
```

✅ **اجتناب از Overloading گمراه‌کننده**  
دو متد با تفاوت جزئی در نوع پارامتر (مثل `int` و `long`) ممکن است باعث سردرگمی شود.

✅ **همسازی با عملگرهای استاندارد**  
وقتی عملگر `==` را overload می‌کنید، حتماً `Equals` و `GetHashCode` را هم override کنید.

✅ **استفاده از `this` در Constructorها**  
برای جلوگیری از تکرار کد و افزایش خوانایی.

---

## 8. جمع‌بندی

🔹 **Overloading** یکی از ابزارهای قدرتمند OOP در C# است که به شما اجازه می‌دهد با نام‌های یکسان، رفتارهای متفاوتی بر اساس ورودی‌ها ایجاد کنید.

🔹 سه نوع اصلی:
- **Method Overloading** → برای متدها
- **Constructor Overloading** → برای سازنده‌ها
- **Operator Overloading** → برای تعریف عملگرهای سفارشی

🔹 همه این‌ها در **Compile-time** حل می‌شوند → سریع و ایمن.

🔹 همیشه قوانین زبان و اصول طراحی را رعایت کنید تا کد شما تمیز، خوانا و قابل نگهداری باشد.

---

## 9. منابع معتبر

1. 📘 **Microsoft Learn - C# Programming Guide**  
   → [Method Overloading](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods#method-overloading)  
   → [Constructors](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constructors)  
   → [Operator Overloading](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/operator-overloading)

2. 📗 **C# in Depth — Jon Skeet** (ویرایش چهارم)  
   فصل 2 و 4 — توضیح عمیق روی Overloading و Polymorphism

3. 📙 **CLR via C# — Jeffrey Richter**  
   فصل 6 — نحوه کار کامپایلر و انتخاب overload در زمان کامپایل

4. 📕 **The C# Player’s Guide — RB Whitaker**  
   بخش OOP — آموزش ساده و کاربردی برای مبتدیان

5. 📒 **Stack Overflow — C# Overloading Best Practices**  
   → https://stackoverflow.com/questions/1005313/method-overloading-best-practices

6. 🎓 **Pluralsight / Udemy — C# Advanced Topics**  
   دوره‌هایی مانند “C# Advanced” توسط Mosh Hamedani یا John Sonmez
