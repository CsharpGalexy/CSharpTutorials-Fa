

## فهرست مطالب

1. [مقدمه: چرا Interface؟](#مقدمه-چرا-interface)
2. [تعریف Interface در C#](#تعریف-interface-در-c)
3. [چرا Multiple Interfaces؟](#چرا-multiple-interfaces)
4. [نحوه پیاده‌سازی چندین Interface در یک کلاس](#نحوه-پیاده‌سازی-چندین-interface-در-یک-کلاس)
5. [قوانین و محدودیت‌ها](#قوانین-و-محدودیت‌ها)
6. [تفاوت با ارث‌بری چندگانه (Multiple Inheritance)](#تفاوت-با-ارث‌بری-چندگانه-multiple-inheritance)
7. [الگوهای رایج استفاده از Multiple Interfaces](#الگوهای-رایج-استفاده-از-multiple-interfaces)
8. [مثال‌های کاربردی](#مثال‌های-کاربردی)
9. [بهترین روش‌ها (Best Practices)](#بهترین-روش‌ها-best-practices)
10. [جمع‌بندی](#جمع‌بندی)
11. [منابع معتبر](#منابع-معتبر)

---

## مقدمه: چرا Interface؟

در برنامه‌نویسی شیءگرا (OOP)، **رابط‌ها (Interfaces)** نقشی کلیدی در تعریف **قراردادهای رفتاری** بین اشیاء دارند. یک Interface مجموعه‌ای از امضاهای متد (Method Signatures) است که کلاس‌ها می‌توانند آن را پیاده‌سازی کنند، بدون اینکه جزئیات پیاده‌سازی را مشخص کنند.

در C#، یک کلاس فقط می‌تواند از **یک کلاس پایه** ارث‌بری کند (Single Inheritance)، اما می‌تواند از **چندین Interface** پیاده‌سازی کند. این قابلیت، **Multiple Interfaces** نام دارد و یکی از قدرتمندترین ویژگی‌های C# برای طراحی سیستم‌های انعطاف‌پذیر و قابل گسترش است.

---

## تعریف Interface در C#

یک Interface در C# با کلمه کلیدی `interface` تعریف می‌شود و فقط شامل امضاهای متد، خاصیت (Property)، رویداد (Event) یا اندیس‌دهنده (Indexer) است — **نه بدنه متد**.

```csharp
public interface IDrawable
{
    void Draw();
}

public interface IResizable
{
    void Resize(double factor);
}
```

---

## چرا Multiple Interfaces؟

چون:
- یک شیء ممکن است **چندین رفتار** متفاوت داشته باشد.
- می‌خواهیم **انعطاف‌پذیری** و **قابلیت تست** (Testability) را افزایش دهیم.
- می‌خواهیم از **وابستگی به کلاس‌های عینی** (Concrete Classes) جلوگیری کنیم (اصل وابستگی معکوس — Dependency Inversion Principle).

مثال: یک شیء `Button` ممکن است هم قابل نمایش باشد (`IDrawable`) و هم قابل تغییر اندازه (`IResizable`) و هم قابل کلیک (`IClickable`).

---

## نحوه پیاده‌سازی چندین Interface در یک کلاس

در C#، برای پیاده‌سازی چندین Interface، آن‌ها را با کاما از هم جدا می‌کنید:

```csharp
public class Circle : IDrawable, IResizable
{
    public void Draw()
    {
        Console.WriteLine("Drawing a circle.");
    }

    public void Resize(double factor)
    {
        Console.WriteLine($"Resizing circle by factor: {factor}");
    }
}
```

> ⚠️ **نکته**: اگر دو Interface متدی با **نام یکسان** داشته باشند، می‌توانید از **Explicit Interface Implementation** استفاده کنید تا تداخل را مدیریت کنید.

```csharp
public interface IShape
{
    void Draw();
}

public interface IImage
{
    void Draw();
}

public class Graphic : IShape, IImage
{
    void IShape.Draw()
    {
        Console.WriteLine("Drawing as a shape.");
    }

    void IImage.Draw()
    {
        Console.WriteLine("Drawing as an image.");
    }
}
```

---

## قوانین و محدودیت‌ها

- یک کلاس می‌تواند **هر تعداد Interface** را پیاده‌سازی کند.
- Interfaceها **نمی‌توانند** شامل فیلدهای نمونه (Instance Fields) باشند، اما از C# 8.0 به بعد می‌توانند اعضای `static` (شامل فیلدها) را تعریف کنند.
- از C# 8.0 به بعد، Interfaceها می‌توانند **متد پیش‌فرض (Default Implementation)** داشته باشند.
- Interfaceها **قابل ارث‌بری** هستند (یک Interface می‌تواند از Interface دیگر ارث‌بری کند).

```csharp
public interface IAdvancedDrawable : IDrawable
{
    void DrawShadow();
}
```

---

## تفاوت با ارث‌بری چندگانه (Multiple Inheritance)

C# از **ارث‌بری چندگانه کلاس‌ها** پشتیبانی **نمی‌کند** (برخلاف C++). اما با استفاده از **چندین Interface**، می‌توان رفتارهای چندگانه را بدون مشکلات ارث‌بری چندگانه (مثل Diamond Problem) پیاده‌سازی کرد.

| ویژگی | ارث‌بری چندگانه کلاس | Multiple Interfaces |
|--------|------------------------|----------------------|
| پشتیبانی در C# | ❌ خیر | ✅ بله |
| شامل بدنه متد | ✅ بله | ❌ خیر (تا C# 7)، ✅ بله (از C# 8 با Default Implementation) |
| Diamond Problem | ممکن است رخ دهد | وجود ندارد |

---

## الگوهای رایج استفاده از Multiple Interfaces

1. **الگوی استراتژی (Strategy Pattern)**: استفاده از Interface برای تغییر رفتار در زمان اجرا.
2. **الگوی وابستگی معکوس (Dependency Inversion)**: کلاس‌ها به Interfaceها وابسته‌اند، نه به کلاس‌های عینی.
3. **الگوی ترکیب (Composition over Inheritance)**: به‌جای ارث‌بری، از ترکیب Interfaceها برای ساخت رفتارهای پیچیده استفاده می‌شود.

---

## مثال‌های کاربردی

### مثال 1: سیستم مدیریت اشیاء گرافیکی

```csharp
public interface IDrawable { void Draw(); }
public interface IMovable { void Move(int x, int y); }
public interface ISelectable { bool IsSelected { get; set; } }

public class TextBox : IDrawable, IMovable, ISelectable
{
    public bool IsSelected { get; set; }

    public void Draw() => Console.WriteLine("Drawing TextBox");
    public void Move(int x, int y) => Console.WriteLine($"Moving to ({x}, {y})");
}
```

### مثال 2: سرویس‌های لاگینگ و ذخیره‌سازی

```csharp
public interface ILoggable { void Log(string message); }
public interface ISavable { void Save(); }

public class UserService : ILoggable, ISavable
{
    public void Log(string message) => Console.WriteLine($"[LOG] {message}");
    public void Save() => Console.WriteLine("User data saved.");
}
```

---

## بهترین روش‌ها (Best Practices)

✅ **هر Interface فقط یک مسئولیت داشته باشد** (اصل تک‌مسئولیتی — SRP).  
✅ از نام‌گذاری استاندارد (`I` در ابتدای نام) استفاده کنید.  
✅ Interfaceهای کوچک و تخصصی (Interface Segregation Principle) ترجیح داده می‌شوند.  
✅ از Explicit Implementation فقط زمانی استفاده کنید که واقعاً نیاز باشد.  
✅ در طراحی APIها، به‌جای کلاس‌های عینی، از Interfaceها استفاده کنید.

---

## جمع‌بندی

- **Multiple Interfaces** امکان پیاده‌سازی چندین رفتار در یک کلاس را فراهم می‌کند.
- این ویژگی، جایگزین امن و انعطاف‌پذیری برای ارث‌بری چندگانه است.
- با رعایت اصول SOLID و بهترین روش‌ها، می‌توان سیستم‌هایی مقیاس‌پذیر و قابل نگهداری ساخت.
- از C# 8.0 به بعد، Interfaceها قدرتمندتر شده‌اند (Default Implementation).

---

## منابع معتبر

1. **Microsoft Learn – Interfaces (C#)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/)

2. **Microsoft Learn – Default Interface Methods (C# 8.0+)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/default-interface-methods](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/default-interface-methods)

3. **C# in Depth – Jon Skeet** (فصل مربوط به Interfaceها)  
   ISBN: 978-1617294532

4. **CLR via C# – Jeffrey Richter** (فصل 8: Interfaces)  
   ISBN: 978-0735667457

5. **SOLID Principles – Martin Fowler**  
   [https://martinfowler.com/bliki/SolidPrinciples.html](https://martinfowler.com/bliki/SolidPrinciples.html)

6. **C# Language Specification – ECMA-334**  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

