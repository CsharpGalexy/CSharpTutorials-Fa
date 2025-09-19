## 📖 فهرست مطالب

1. [مقدمه: ارث‌بری چیست؟](#1-مقدمه-ارث%E2%80%8Cبری-چیست)
2. [انواع ارث‌بری در C#](#2-انواع-ارث%E2%80%8Cبری-در-c)
3. [ارث‌بری چندسطحی (Multi-level Inheritance)](#3-ارث%E2%80%8Cبری-چندسطحی-multi-level-inheritance)
4. [ارث‌بری سلسله‌مراتبی (Hierarchical Inheritance)](#4-ارث%E2%80%8Cبری-سلسله%E2%80%8Cمراتبی-hierarchical-inheritance)
5. [تفاوت Multi-level و Hierarchical Inheritance](#5-تفاوت-multi-level-و-hierarchical-inheritance)
6. [نکات مهم و بهترین روش‌ها (Best Practices)](#6-نکات-مهم-و-بهترین-روش%E2%80%8Cها-best-practices)
7. [خطاهای رایج و نحوه جلوگیری از آنها](#7-خطاهای-رایج-و-نحوه-جلوگیری-از-آنها)
8. [جمع‌بندی](#8-جمع%E2%80%8Cبندی)
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه: ارث‌بری چیست؟

ارث‌بری (Inheritance) یکی از چهار اصل اصلی برنامه‌نویسی شیءگرا (OOP) است که به شما اجازه می‌دهد کلاس‌های جدیدی (فرزند) ایجاد کنید که از کلاس‌های موجود (والد) **ویژگی‌ها و رفتارها** (فیلدها و متد‌ها) را به ارث می‌برند.

> ✅ هدف اصلی: **کاهش تکرار کد**، **افزایش قابلیت استفاده مجدد** و **مدل‌سازی روابط واقعی** بین اشیاء.

در C#، ارث‌بری با استفاده از کلمه کلیدی `:` پیاده‌سازی می‌شود:

```csharp
class ChildClass : ParentClass { }
```

---

## 2. انواع ارث‌بری در C#

C# از انواع زیر ارث‌بری پشتیبانی می‌کند:

- **Single Inheritance** (ارث‌بری تک‌سطحی) — یک کلاس فقط از یک کلاس والد ارث می‌برد. ✅ پشتیبانی می‌شود.
- **Multi-level Inheritance** (ارث‌بری چندسطحی) — یک کلاس از کلاسی ارث می‌برد که خودش از کلاس دیگری ارث برده است. ✅ پشتیبانی می‌شود.
- **Hierarchical Inheritance** (ارث‌بری سلسله‌مراتبی) — چند کلاس از یک کلاس پایه ارث می‌برند. ✅ پشتیبانی می‌شود.
- **Multiple Inheritance** (ارث‌بری چندگانه) — یک کلاس از چند کلاس والد ارث می‌برد. ❌ **پشتیبانی نمی‌شود** (اما با استفاده از Interface می‌توان شبیه‌سازی کرد).

---

## 3. ارث‌بری چندسطحی (Multi-level Inheritance)

### 🔹 تعریف:
در این نوع ارث‌بری، یک کلاس از کلاسی ارث می‌برد که خودش از کلاس دیگری ارث برده است. به عبارت دیگر، یک **سلسله ارث‌بری** ایجاد می‌شود.

مثال:  
`Animal` ← `Mammal` ← `Dog`

### 🔹 نحوه پیاده‌سازی:

```csharp
// کلاس پایه
class Animal
{
    public void Eat() => Console.WriteLine("Animal is eating.");
}

// کلاس واسطه — از Animal ارث می‌برد
class Mammal : Animal
{
    public void GiveBirth() => Console.WriteLine("Mammal gives birth to live young.");
}

// کلاس نهایی — از Mammal ارث می‌برد (و به طور غیرمستقیم از Animal)
class Dog : Mammal
{
    public void Bark() => Console.WriteLine("Dog is barking.");
}
```

### 🔹 مثال عملی:

```csharp
class Program
{
    static void Main()
    {
        Dog myDog = new Dog();
        myDog.Eat();        // از Animal به ارث رسیده
        myDog.GiveBirth();  // از Mammal به ارث رسیده
        myDog.Bark();       // متعلق به Dog
    }
}
```

**خروجی:**
```
Animal is eating.
Mammal gives birth to live young.
Dog is barking.
```

### 🔹 کاربردها و مزایا:

- مدل‌سازی روابط واقعی (مثل موجودات زنده، سازمان‌ها، محصولات).
- افزایش قابلیت استفاده مجدد کد.
- ساختار منظم و قابل گسترش.

> ⚠️ هشدار: اگر سلسله ارث‌بری خیلی عمیق شود، کد پیچیده و نگهداری آن دشوار می‌شود. این امر می‌تواند منجر به وابستگی‌های شدید بین کلاس‌ها و شکنندگی کد گردد.

---

## 4. ارث‌بری سلسله‌مراتبی (Hierarchical Inheritance)

### 🔹 تعریف:
در این نوع، **چند کلاس فرزند** از **یک کلاس پایه** ارث می‌برند. این نوع ارث‌بری برای مدل‌سازی موقعیت‌هایی مناسب است که چند موجودیت مشترکاتی دارند اما تفاوت‌های خاص خود را نیز دارند.

مثال:  
`Shape` ← `Circle`, `Rectangle`, `Triangle`

### 🔹 نحوه پیاده‌سازی:

```csharp
// کلاس پایه
class Shape
{
    public virtual void Draw() => Console.WriteLine("Drawing a shape.");
}

// کلاس‌های فرزند
class Circle : Shape
{
    public override void Draw() => Console.WriteLine("Drawing a circle.");
}

class Rectangle : Shape
{
    public override void Draw() => Console.WriteLine("Drawing a rectangle.");
}

class Triangle : Shape
{
    public override void Draw() => Console.WriteLine("Drawing a triangle.");
}
```

### 🔹 مثال عملی:

```csharp
class Program
{
    static void Main()
    {
        Shape[] shapes = { new Circle(), new Rectangle(), new Triangle() };

        foreach (var shape in shapes)
        {
            shape.Draw(); // چندشکلی (Polymorphism) در عمل
        }
    }
}
```

**خروجی:**
```
Drawing a circle.
Drawing a rectangle.
Drawing a triangle.
```

### 🔹 کاربردها و مزایا:

- پیاده‌سازی **چندشکلی** (Polymorphism).
- امکان تعریف رفتار مشترک در کلاس پایه و رفتار خاص در کلاس‌های فرزند.
- مناسب برای سیستم‌هایی با اجزای مشابه اما متفاوت (مثل اشکال، کارمندان، وسایل نقلیه).

---

## 5. تفاوت Multi-level و Hierarchical Inheritance

| ویژگی | Multi-level Inheritance | Hierarchical Inheritance |
|-------|--------------------------|---------------------------|
| ساختار | زنجیره‌ای (A → B → C) | ستاره‌ای (A ← B, C, D) |
| تعداد فرزندان مستقیم | یک فرزند در هر سطح | چند فرزند از یک والد |
| کاربرد رایج | مدل‌سازی سلسله مراتب عمیق | مدل‌سازی انواع مختلف یک مفهوم |
| مثال | انسان ← کارمند ← مدیر | وسیله نقلیه ← ماشین، موتور، کامیون |

---

## 6. نکات مهم و بهترین روش‌ها (Best Practices)

✅ **از کلمه کلیدی `base`** برای دسترسی به اعضای کلاس پایه استفاده کنید:

```csharp
class Dog : Mammal
{
    public override void GiveBirth()
    {
        base.GiveBirth(); // فراخوانی متد والد
        Console.WriteLine("And the dog takes care of puppies.");
    }
}
```

✅ **از `virtual` و `override`** برای چندشکلی استفاده کنید.

✅ **از sealed class** برای جلوگیری از ارث‌بری بیشتر استفاده کنید اگر لازم نیست:

```csharp
sealed class FinalClass { } // دیگر نمی‌توان از این کلاس ارث برد
```

✅ **از abstract class** برای تعریف کلاس‌های پایه‌ای که نباید نمونه‌سازی شوند استفاده کنید:

```csharp
abstract class Animal
{
    public abstract void MakeSound();
}
```

✅ **از Composition به جای Inheritance** استفاده کنید اگر رابطه "دارد" (has-a) منطقی‌تر از "است" (is-a) است.

---

## 7. خطاهای رایج و نحوه جلوگیری از آنها

### ❌ خطای 1: فراموش کردن `override` هنگام override کردن متد

```csharp
class Circle : Shape
{
    public void Draw() { } // ❌ اشتباه — باید override باشد
}
```

✅ راه حل: همیشه از `override` استفاده کنید وقتی متدی را override می‌کنید.

---

### ❌ خطای 2: تلاش برای ارث‌بری از چند کلاس

```csharp
class MyClass : ClassA, ClassB { } // ❌ خطای کامپایل — C# اجازه نمی‌دهد
```

✅ راه حل: از Interface استفاده کنید:

```csharp
interface IA { void MethodA(); }
interface IB { void MethodB(); }

class MyClass : IA, IB { ... }
```

---

### ❌ خطای 3: دسترسی مستقیم به اعضای `private` کلاس پایه

اعضای `private` در کلاس‌های فرزند قابل دسترسی نیستند.

✅ راه حل: از `protected` یا `protected internal` استفاده کنید.

---

## 8. جمع‌بندی

- **ارث‌بری چندسطحی (Multi-level)**: برای مدل‌سازی سلسله‌مراتب عمیق و واقعی مناسب است.
- **ارث‌بری سلسله‌مراتبی (Hierarchical)**: برای تعریف چندین نوع مختلف از یک مفهوم پایه عالی است.
- هر دو نوع ارث‌بری در C# پشتیبانی می‌شوند و در طراحی نرم‌افزارهای واقعی کاربرد فراوان دارند.
- همیشه به **اصل جایگزینی لیسکوف (LSP)** و **اصل تک وظیفگی (SRP)** توجه کنید تا کد شما تمیز و قابل نگهداری بماند.
- از ارث‌بری بیش از حد استفاده نکنید — گاهی **ترکیب (Composition)** بهتر از **ارث‌بری (Inheritance)** است.

> 🎯 نکته طلایی:  
> “Favor composition over inheritance.” — طراحی شیءگرا (اصل ترکیب بر ارث‌بری)

---

## 9. منابع معتبر

📚 کتاب‌ها:

- **C# in Depth** — *Jon Skeet* (Manning Publications)
- **CLR via C#** — *Jeffrey Richter* (Microsoft Press)
- **Head First Design Patterns** — *Eric Freeman & Elisabeth Robson* (برای درک بهتر ارث‌بری و Composition)

🌐 وبسایت‌ها:

- [Microsoft Docs — Inheritance in C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance)
- [GeeksforGeeks — Inheritance in C#](https://www.geeksforgeeks.org/inheritance-in-c-sharp/)
- [TutorialsPoint — C# Inheritance](https://www.tutorialspoint.com/csharp/csharp_inheritance.htm)
- [C# Station — Tutorial 8: Inheritance](http://www.csharp-station.com/Tutorial/CSharp/lesson08)

🎥 دوره‌های آموزشی:

- [Pluralsight — C# Fundamentals by Scott Allen](https://www.pluralsight.com/)
- [Udemy — C# OOP Masterclass by Mosh Hamedani](https://www.udemy.com/course/csharp-oop/)

---

