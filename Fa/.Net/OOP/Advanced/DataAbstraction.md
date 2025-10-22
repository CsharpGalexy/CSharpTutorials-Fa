
## فهرست مطالب

1. [مقدمه‌ای بر Data Abstraction](#1-مقدمهای-بر-data-abstraction)
2. [انواع داده در C#: Value Types vs Reference Types](#2-انواع-داده-در-c-value-types-vs-reference-types)
3. [چرا فقط Reference Types برای ابستراکشن و تخصصی‌سازی مناسب‌اند؟](#3-چرا-فقط-reference-types-برای-ابستراکشن-و-تخصصیسازی-مناسباند؟)
4. [انواع ارجاعی قابل ابستراکشن در C#](#4-انواع-ارجاعی-قابل-ابستراکشن-در-c)
   - [4.1 کلاس‌ها (Classes)](#41-کلاسها-classes)
   - [4.2 کلاس‌های انتزاعی (Abstract Classes)](#42-کلاسهای-انتزاعی-abstract-classes)
   - [4.3 رابط‌ها (Interfaces)](#43-رابطها-interfaces)
5. [تخصصی‌سازی (Specialization) چیست؟](#5-تخصصیسازی-specialization-چیست؟)
6. [مقایسه Abstract Class و Interface](#6-مقایسه-abstract-class-و-interface)
7. [الگوهای رایج در ترکیب ابستراکشن و تخصصی‌سازی](#7-الگوهای-رایج-در-ترکیب-ابستراکشن-و-تخصصیسازی)
8. [جمع‌بندی](#8-جمعبندی)
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه‌ای بر Data Abstraction

**ابستراکشن داده** (Data Abstraction) یکی از چهار اصل اصلی برنامه‌نویسی شیء‌گرا (OOP) است که به ما اجازه می‌دهد **جزئیات پیاده‌سازی** را از **استفاده‌کنندگان** (کاربران کلاس) پنهان کنیم. هدف اصلی آن **ساده‌سازی تعامل با سیستم‌های پیچیده** است.

در C#، این مفهوم معمولاً از طریق **انواع ارجاعی** (Reference Types) پیاده‌سازی می‌شود، زیرا این انواع امکان **وراثت** (Inheritance)، **پلی‌مورفیسم** (Polymorphism) و **پنهان‌سازی جزئیات** را فراهم می‌کنند.

---

## 2. انواع داده در C#: Value Types vs Reference Types

در C# دو دسته کلی نوع داده وجود دارد:

| نوع داده | ذخیره‌سازی | قابلیت وراثت | قابلیت ابستراکشن |
|----------|-------------|----------------|---------------------|
| **Value Types** | در Stack | ❌ خیر (به جز از طریق `interface`) | ❌ محدود |
| **Reference Types** | در Heap | ✅ بله | ✅ کامل |

> 💡 **نکته**: فقط **Reference Types** می‌توانند به طور کامل در چارچوب OOP برای ابستراکشن و تخصصی‌سازی استفاده شوند.

---

## 3. چرا فقط Reference Types برای ابستراکشن و تخصصی‌سازی مناسب‌اند؟

- **وراثت** (Inheritance): فقط کلاس‌ها (که Reference Type هستند) می‌توانند از یکدیگر ارث‌بری کنند.
- **پلی‌مورفیسم**: امکان استفاده از یک شیء به عنوان نوع پایه (Base Type) و فراخوانی روش‌های Overridden تنها در Reference Types ممکن است.
- **Heap Allocation**: مدیریت حافظه برای اشیاء پیچیده و قابل گسترش فقط در Heap معنی دارد.

---

## 4. انواع ارجاعی قابل ابستراکشن در C#

### 4.1 کلاس‌ها (Classes)

کلاس‌ها واحد اصلی ساختاری در C# برای تعریف اشیاء هستند. هر کلاس یک **Reference Type** است.

```csharp
public class Animal
{
    public virtual void MakeSound() => Console.WriteLine("Some sound");
}

public class Dog : Animal
{
    public override void MakeSound() => Console.WriteLine("Woof!");
}
```

> ✅ کلاس‌ها می‌توانند **داده‌ها** (فیلدها) و **رفتار** (متدها) را تعریف کنند و پایه‌ای برای تخصصی‌سازی هستند.

---

### 4.2 کلاس‌های انتزاعی (Abstract Classes)

کلاس‌های انتزاعی نمی‌توانند مستقیماً نمونه‌سازی شوند و برای تعریف **طرح کلی** (Blueprint) برای کلاس‌های فرزند استفاده می‌شوند.

```csharp
public abstract class Shape
{
    public abstract double Area { get; }
    public void Display() => Console.WriteLine($"Area: {Area}");
}

public class Circle : Shape
{
    private double radius;
    public Circle(double r) => radius = r;
    public override double Area => Math.PI * radius * radius;
}
```

> ✅ **مزیت**: می‌توانند **اعضای پیاده‌سازی‌شده** (مثل `Display`) و **اعضای انتزاعی** (مثل `Area`) را ترکیب کنند.

---

### 4.3 رابط‌ها (Interfaces)

رابط‌ها قراردادهایی هستند که کلاس‌ها باید پیاده‌سازی کنند. از C# 8.0 به بعد، رابط‌ها می‌توانند **اعضای پیش‌فرض** (Default Implementations) داشته باشند.

```csharp
public interface IDrawable
{
    void Draw();
    void Render() => Console.WriteLine("Rendering..."); // Default implementation
}

public class Rectangle : IDrawable
{
    public void Draw() => Console.WriteLine("Drawing rectangle");
}
```

> ✅ **مزیت**: چندین رابط را می‌توان پیاده‌سازی کرد (چندین وراثت رفتاری).

---

## 5. تخصصی‌سازی (Specialization) چیست؟

**تخصصی‌سازی** فرآیند ایجاد کلاس‌های فرزند است که **رفتار یا ویژگی‌های خاص‌تری** نسبت به کلاس پایه دارند. این کار با **وراثت** انجام می‌شود.

مثال:

```csharp
Animal → Mammal → Dog → PersianDog
```

هر سطح، جزئیات بیشتری از رفتار یا ویژگی‌ها را تعریف می‌کند.

> 🔑 **اصل**: "هر PersianDog یک Dog است، هر Dog یک Mammal است، و هر Mammal یک Animal است."

---

## 6. مقایسه Abstract Class و Interface

| ویژگی | Abstract Class | Interface |
|--------|----------------|-----------|
| وراثت چندگانه | ❌ خیر | ✅ بله |
| اعضای پیاده‌سازی‌شده | ✅ بله | ✅ (از C# 8.0) |
| فیلدها و سازنده‌ها | ✅ بله | ❌ خیر |
| تغییر در آینده | ریسک شکستن کد فرزند | انعطاف‌پذیرتر |
| استفاده برای "is-a" | ✅ بله | ✅ بله (اما برای "can-do") |

> 📌 **راهنمایی طراحی**:
> - اگر رابطه **"is-a"** دارید → از **Abstract Class** استفاده کنید.
> - اگر رابطه **"can-do"** دارید → از **Interface** استفاده کنید.

---

## 7. الگوهای رایج در ترکیب ابستراکشن و تخصصی‌سازی

### الگوی Template Method

```csharp
public abstract class DataProcessor
{
    public void Process()
    {
        LoadData();
        Validate();
        Save();
    }

    protected abstract void LoadData();
    protected abstract void Validate();
    protected virtual void Save() => Console.WriteLine("Saved.");
}
```

کلاس‌های فرزند فقط بخش‌های خاص را پیاده‌سازی می‌کنند.

### الگوی Strategy با Interface

```csharp
public interface ICompressionStrategy
{
    byte[] Compress(byte[] data);
}

public class ZipCompression : ICompressionStrategy { ... }
public class RarCompression : ICompressionStrategy { ... }
```

---

## 8. جمع‌بندی

- **Data Abstraction** در C# عمدتاً از طریق **Reference Types** پیاده‌سازی می‌شود.
- **کلاس‌ها**، **کلاس‌های انتزاعی** و **رابط‌ها** سه ابزار اصلی برای ایجاد ابستراکشن هستند.
- **تخصصی‌سازی** با وراثت انجام می‌شود و به ما اجازه می‌دهد رفتارهای خاص‌تری تعریف کنیم.
- انتخاب بین `abstract class` و `interface` بستگی به رابطه منطقی ("is-a" vs "can-do") و نیازهای طراحی دارد.

---

## 9. منابع معتبر

1. **Microsoft Learn – C# Programming Guide**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)

2. **Microsoft – Abstract and Sealed Classes and Class Members**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)

3. **Microsoft – Interfaces (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface)

4. **C# in Depth – Jon Skeet** (کتاب معتبر)  
   📘 ISBN: 978-1617294437

5. **CLR via C# – Jeffrey Richter**  
   📘 ISBN: 978-0735667457

6. **Object-Oriented Programming in C# – Pluralsight Course**  
   🔗 [https://www.pluralsight.com/courses/object-oriented-programming-csharp](https://www.pluralsight.com/courses/object-oriented-programming-csharp)

---
