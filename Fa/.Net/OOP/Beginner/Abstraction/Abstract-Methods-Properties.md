

## 📖 فهرست مطالب

1. [مقدمه: OOP و اهمیت انتزاع](#1-مقدمه-oop-و-اهمیت-انتزاع)  
2. [کلمه کلیدی `abstract` چیست؟](#2-کلمه-کلیدی-abstract-چیست)  
3. [کلاس‌های انتزاعی (Abstract Classes)](#3-کلاس‌های-انتزاعی-abstract-classes)  
4. [متدهای انتزاعی (Abstract Methods)](#4-متدهای-انتزاعی-abstract-methods)  
5. [خواص انتزاعی (Abstract Properties)](#5-خواص-انتزاعی-abstract-properties)  
6. [تفاوت با متدها و خواص مجازی (Virtual)](#6-تفاوت-با-متدها-و-خواص-مجازی-virtual)  
7. [مثال‌های عملی و کاربردی](#7-مثال‌های-عملی-و-کاربردی)  
8. [قوانین و محدودیت‌های مهم](#8-قوانین-و-محدودیت‌های-مهم)  
9. [کاربردهای واقعی در طراحی نرم‌افزار](#9-کاربردهای-واقعی-در-طراحی-نرمافزار)  
10. [جمع‌بندی و نکات کلیدی](#10-جمعبندی-و-نکات-کلیدی)  
11. [منابع معتبر](#منابع-معتبر)

---

## 1. مقدمه: OOP و اهمیت انتزاع

در برنامه‌نویسی شیءگرا (OOP)، یکی از اصول مهم **انتزاع (Abstraction)** است. این اصل به ما کمک می‌کند تا جزئیات پیاده‌سازی را پنهان کرده و فقط رابط (Interface) لازم را در اختیار کاربر قرار دهیم.

در سی‌شارپ، این اصل با استفاده از **کلاس‌های انتزاعی** و **اعضای انتزاعی** (متدها و خواص) پیاده‌سازی می‌شود. این اعضا **بدون پیاده‌سازی** تعریف می‌شوند و **اجباراً در کلاس‌های فرزند پیاده‌سازی می‌شوند**.

> ✅ هدف: تعریف یک قرارداد (Contract) که کلاس‌های فرزند باید آن را رعایت کنند.

---

## 2. کلمه کلیدی `abstract` چیست؟

کلمه کلیدی `abstract` در سی‌شارپ برای تعریف:

- کلاس‌هایی که نمی‌توان از آن‌ها نمونه (Instance) ساخت.
- اعضایی (متد، خاصیت) که بدون بدنه (بدون پیاده‌سازی) تعریف می‌شوند و در کلاس‌های مشتق‌شده باید override شوند.

```csharp
public abstract class Animal
{
    public abstract void MakeSound(); // بدون بدنه!
}
```

> ⚠️ نکته: کلاس‌های انتزاعی می‌توانند شامل اعضای عادی (غیرانتزاعی) هم باشند.

---

## 3. کلاس‌های انتزاعی (Abstract Classes)

کلاس انتزاعی:

- نمی‌توان از آن نمونه ساخت (`new Animal()` ❌).
- می‌تواند شامل متدها، خواص، فیلدها و سازنده‌های عادی باشد.
- می‌تواند شامل اعضای انتزاعی باشد که در کلاس‌های فرزند **باید** پیاده‌سازی شوند.
- فقط می‌توان از آن **وراثت** گرفت.

### مثال:

```csharp
public abstract class Animal
{
    public string Name { get; set; }

    public Animal(string name)
    {
        Name = name;
    }

    public abstract void MakeSound(); // اجباری برای override
}
```

---

## 4. متدهای انتزاعی (Abstract Methods)

- بدون بدنه تعریف می‌شوند (فقط امضای متد).
- در کلاس‌های فرزند **باید** با کلمه کلیدی `override` پیاده‌سازی شوند.
- فقط در کلاس‌های انتزاعی می‌توان تعریف کرد.

### مثال:

```csharp
public abstract class Animal
{
    public abstract void MakeSound();
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}
```

> ✅ کامپایلر اطمینان می‌دهد که هر کلاس فرزند، متد `MakeSound` را پیاده‌سازی کرده است.

---

## 5. خواص انتزاعی (Abstract Properties)

دقیقاً مانند متدهای انتزاعی، می‌توان **خواص (Properties)** را نیز انتزاعی تعریف کرد.

- بدون بدنه `get` یا `set`.
- در کلاس فرزند باید `override` شوند.

### مثال:

```csharp
public abstract class Shape
{
    public abstract double Area { get; } // فقط get اجباری است
}

public class Circle : Shape
{
    public double Radius { get; set; }

    public override double Area
    {
        get { return Math.PI * Radius * Radius; }
    }
}

public class Rectangle : Shape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public override double Area
    {
        get { return Width * Height; }
    }
}
```

> 🔁 می‌توان `get` و `set` را جداگانه انتزاعی کرد، اما معمولاً `get` را انتزاعی می‌کنند.

---

## 6. تفاوت با متدها و خواص مجازی (Virtual)

| ویژگی | `abstract` | `virtual` |
|-------|------------|-----------|
| نیاز به پیاده‌سازی در کلاس پایه | ❌ ندارد (بدون بدنه) | ✅ دارد (با بدنه پیش‌فرض) |
| اجبار به override در فرزند | ✅ بله | ❌ خیر (اختیاری) |
| امکان تعریف در کلاس غیرانتزاعی | ❌ خیر | ✅ بله |
| هدف | تعریف قرارداد اجباری | اجازه تغییر رفتار اختیاری |

### مثال مقایسه‌ای:

```csharp
public abstract class Animal
{
    public abstract void MakeSound();       // اجباری
    public virtual void Sleep()             // اختیاری
    {
        Console.WriteLine("Zzz...");
    }
}
```

---

## 7. مثال‌های عملی و کاربردی

### مثال ۱: سیستم پرداخت

```csharp
public abstract class PaymentMethod
{
    public abstract decimal CalculateFee(decimal amount);
    public abstract void ProcessPayment(decimal amount);
}

public class CreditCardPayment : PaymentMethod
{
    public override decimal CalculateFee(decimal amount)
        => amount * 0.02m; // 2% کارمزد

    public override void ProcessPayment(decimal amount)
        => Console.WriteLine($"Paid {amount:C} via Credit Card.");
}

public class PayPalPayment : PaymentMethod
{
    public override decimal CalculateFee(decimal amount)
        => amount * 0.03m + 0.5m; // 3% + هزینه ثابت

    public override void ProcessPayment(decimal amount)
        => Console.WriteLine($"Paid {amount:C} via PayPal.");
}
```

### مثال ۲: بازی — شخصیت‌ها

```csharp
public abstract class Character
{
    public string Name { get; set; }
    public int Health { get; set; }

    public abstract void Attack();
    public abstract void Defend();
}

public class Warrior : Character
{
    public override void Attack() => Console.WriteLine($"{Name} swings sword!");
    public override void Defend() => Console.WriteLine($"{Name} raises shield!");
}

public class Mage : Character
{
    public override void Attack() => Console.WriteLine($"{Name} casts fireball!");
    public override void Defend() => Console.WriteLine($"{Name} casts barrier!");
}
```

---

## 8. قوانین و محدودیت‌های مهم

✅ **اجباری**:

- کلاس حاوی عضو انتزاعی، **باید** انتزاعی باشد.
- کلاس فرزند از کلاس انتزاعی، **باید** تمام اعضای انتزاعی را `override` کند — مگر اینکه خودش انتزاعی باشد.
- اعضای انتزاعی **نمی‌توانند** `private` باشند (چون در فرزند قابل دسترسی نیستند).
- اعضای انتزاعی **نمی‌توانند** `static` یا `virtual` باشند (چون `abstract` خودش یک نوع `virtual` اجباری است).

❌ **غیرمجاز**:

```csharp
public abstract class MyClass
{
    private abstract void MyMethod(); // ❌ خطا: انتزاعی نمی‌تواند private باشد
    static abstract void StaticMethod(); // ❌ خطا
    virtual abstract void VirtualAbstract(); // ❌ خطا — تکراری
}
```

---

## 9. کاربردهای واقعی در طراحی نرم‌افزار

- **الگوی Template Method**: ترکیب متدهای انتزاعی و عادی برای تعریف ساختار کلی الگوریتم.
- **قراردادهای لایه‌های مختلف**: مثلاً Repository Pattern — تعریف اینترفیس عملیات دیتابیس به صورت انتزاعی.
- **پلاگین‌ها و افزونه‌ها**: تعریف یک پایه انتزاعی که هر پلاگین باید آن را پیاده‌سازی کند.
- **تست‌های واحد (Unit Testing)**: استفاده از Mock کردن کلاس‌های انتزاعی برای تست.

---

## 10. جمع‌بندی و نکات کلیدی

🔹 کلاس انتزاعی = قالبی که نمی‌توان از آن نمونه ساخت، اما می‌توان از آن وراثت گرفت.  
🔹 متد/خاصیت انتزاعی = قراردادی که کلاس فرزند **باید** پیاده‌سازی کند.  
🔹 تفاوت با `virtual`: `abstract` اجباری، `virtual` اختیاری.  
🔹 نمی‌توان از کلاس انتزاعی `new` گرفت.  
🔹 کلاس فرزند غیرانتزاعی، باید تمام اعضای انتزاعی را override کند.  
🔹 اعضای انتزاعی نمی‌توانند `private`، `static` یا `virtual` باشند.

> 🎯 نکته طلایی: از انتزاع زمانی استفاده کنید که می‌خواهید **رفتارهای اساسی و مشترک** را در کلاس‌های فرزند **اجباری** کنید.

---

## 11. منابع معتبر

1. **Microsoft Learn (رسمی)**  
   - [Abstract Classes - C# Programming Guide](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)  
   - [Abstract Properties](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties#abstract-properties)

2. **C# in Depth — Jon Skeet**  
   - فصل مربوط به OOP و انتزاع (ویرایش چهارم)

3. **Pro C# 10 with .NET 6 — Andrew Troelsen & Phil Japikse**  
   - فصل ۷: Object-Oriented Programming with C#

4. **Stack Overflow — Best Practices**  
   - [When to use abstract class vs interface](https://stackoverflow.com/questions/15154374/abstract-class-vs-interface)

5. **Pluralsight / Udemy — دوره‌های OOP در C#**  
   - دوره‌های "C# Advanced Topics" یا "Design Patterns in C#"
