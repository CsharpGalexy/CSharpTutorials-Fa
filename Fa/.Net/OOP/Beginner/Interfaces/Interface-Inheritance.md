
## فهرست مطالب

1. [مقدمه‌ای بر رابط‌ها (Interfaces)](#1-مقدمه‌ای-بر-رابط‌ها-interfaces)
2. [ارث‌بری رابط چیست؟](#2-ارث‌بری-رابط-چیست)
3. [چرا از ارث‌بری رابط استفاده می‌کنیم؟](#3-چرا-از-ارث‌بری-رابط-استفاده-می‌کنیم)
4. [نحوه پیاده‌سازی ارث‌بری رابط در C#](#4-نحوه-پیاده‌سازی-ارث‌بری-رابط-در-c)
5. [مثال‌های عملی](#5-مثال‌های-عملی)
6. [تفاوت ارث‌بری رابط و ارث‌بری کلاس](#6-تفاوت-ارث‌بری-رابط-و-ارث‌بری-کلاس)
7. [نکات مهم و بهترین روش‌ها (Best Practices)](#7-نکات-مهم-و-بهترین-روش‌ها-best-practices)
8. [جمع‌بندی](#8-جمع‌بندی)
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه‌ای بر رابط‌ها (Interfaces)

در برنامه‌نویسی شیءگرا (OOP)، **رابط** (Interface) یک قرارداد است که مشخص می‌کند یک کلاس چه رفتارهایی (متد، خاصیت، رویداد و ...) باید داشته باشد، بدون اینکه نحوه پیاده‌سازی آن‌ها را تعیین کند.

رابط‌ها در C# با کلیدواژه `interface` تعریف می‌شوند و فقط شامل **اعلان** اعضاء هستند (نه پیاده‌سازی).

مثال ساده:

```csharp
public interface IAnimal
{
    void MakeSound();
}
```

هر کلاسی که از `IAnimal` پیاده‌سازی کند، **باید** متد `MakeSound()` را پیاده‌سازی کند.

---

## 2. ارث‌بری رابط چیست؟

در C#، یک رابط می‌تواند از یک یا چند رابط دیگر **ارث‌بری** کند. این کار باعث می‌شود رابط فرزند تمام اعضای رابط‌های والد را **الزامی** کند.

> ⚠️ توجه: ارث‌بری در رابط‌ها **چندگانه** است (یعنی یک رابط می‌تواند از چند رابط دیگر ارث‌بری کند)، در حالی که کلاس‌ها در C# فقط از یک کلاس می‌توانند ارث‌بری کنند.

نحوه تعریف:

```csharp
public interface IParent1 { void Method1(); }
public interface IParent2 { void Method2(); }

public interface IChild : IParent1, IParent2
{
    void Method3();
}
```

هر کلاسی که از `IChild` پیاده‌سازی کند، باید سه متد `Method1()`, `Method2()`, و `Method3()` را پیاده‌سازی کند.

---

## 3. چرا از ارث‌بری رابط استفاده می‌کنیم؟

- **سازمان‌دهی بهتر کدها**: می‌توانید رابط‌های کلی را تعریف کرده و رابط‌های تخصصی‌تر را از آن‌ها ارث‌بری کنید.
- **قابلیت گسترش (Extensibility)**: بدون تغییر رابط‌های موجود، می‌توانید رابط‌های جدیدی با قابلیت‌های بیشتر ایجاد کنید.
- **کاهش تکرار کد**: اگر چندین رابط نیاز به یک مجموعه مشترک از اعضا دارند، می‌توانید آن‌ها را در یک رابط پایه قرار دهید.
- **هماهنگی با اصول SOLID**: به‌ویژه اصل **جایگزینی لیسکوف (Liskov Substitution Principle)** و **جداکردن رابط (Interface Segregation Principle)**.

---

## 4. نحوه پیاده‌سازی ارث‌بری رابط در C#

### سینتکس کلی:

```csharp
public interface IBaseInterface
{
    void BaseMethod();
}

public interface IDerivedInterface : IBaseInterface
{
    void DerivedMethod();
}
```

### نکات مهم:
- رابط فرزند **تمام اعضای رابط‌های والد** را به ارث می‌برد.
- می‌توانید از **چندین رابط** به صورت همزمان ارث‌بری کنید.
- رابط‌ها **نمی‌توانند** از کلاس‌ها ارث‌بری کنند.
- رابط‌ها **فقط** می‌توانند از رابط‌های دیگر ارث‌بری کنند.

---

## 5. مثال‌های عملی

### مثال ۱: سلسله مراتب رابط‌ها

```csharp
public interface IReadable
{
    string Read();
}

public interface IWritable
{
    void Write(string data);
}

public interface IReadWrite : IReadable, IWritable
{
    // این رابط هم خواندن دارد، هم نوشتن
}

// پیاده‌سازی
public class FileHandler : IReadWrite
{
    public string Read() => "Data from file";
    public void Write(string data) => Console.WriteLine($"Writing: {data}");
}
```

### مثال ۲: استفاده در طراحی سیستم‌های واقعی

```csharp
public interface ILoggable
{
    void Log(string message);
}

public interface IValidatable
{
    bool Validate();
}

public interface IService : ILoggable, IValidatable
{
    void Process();
}

public class PaymentService : IService
{
    public void Log(string message) => Console.WriteLine($"LOG: {message}");
    public bool Validate() => true;
    public void Process() => Console.WriteLine("Processing payment...");
}
```

---

## 6. تفاوت ارث‌بری رابط و ارث‌بری کلاس

| ویژگی | ارث‌بری کلاس | ارث‌بری رابط |
|--------|----------------|----------------|
| تعداد والدین | فقط یک کلاس | چندین رابط |
| پیاده‌سازی پیش‌فرض | بله (در کلاس پایه) | خیر (فقط اعلان) |
| اعضای غیرعمومی | مجاز است | فقط اعضای عمومی |
| سازنده (Constructor) | دارد | ندارد |
| فیلدها | دارد | ندارد (اما از C# 8.0 به بعد می‌تواند متد پیش‌فرض داشته باشد) |

> 📌 نکته: از C# 8.0 به بعد، رابط‌ها می‌توانند **متد پیش‌فرض (Default Implementation)** داشته باشند، اما همچنان نمی‌توانند فیلد داشته باشند.

---

## 7. نکات مهم و بهترین روش‌ها (Best Practices)

1. **از نام‌گذاری استاندارد استفاده کنید**: نام رابط‌ها معمولاً با `I` شروع می‌شوند (مثل `IComparable`).
2. **رابط‌ها را کوچک و تخصصی نگه دارید**: از اصل **Interface Segregation Principle** پیروی کنید.
3. **از ارث‌بری رابط برای ترکیب رفتارها استفاده کنید**، نه برای شبیه‌سازی ارث‌بری کلاس.
4. **از تغییر رابط‌های عمومی خودداری کنید**: چون تغییر رابط، تمام کلاس‌های پیاده‌ساز را تحت تأثیر قرار می‌دهد.
5. **در صورت نیاز به رفتار پیش‌فرض، از C# 8+ و Default Interface Methods استفاده کنید** (اما با احتیاط).

---

## 8. جمع‌بندی

ارث‌بری رابط در C# یک ابزار قدرتمند برای طراحی سیستم‌های انعطاف‌پذیر و قابل گسترش است. با استفاده از آن می‌توانید رابط‌های ترکیبی ایجاد کنید که چندین قرارداد را در یک واحد منطقی جمع‌آوری کنند. این ویژگی به‌ویژه در طراحی APIها، سرویس‌ها و سیستم‌های ماژولار بسیار کاربرد دارد.

---

## 9. منابع معتبر

1. **مستندات رسمی مایکروسافت (Microsoft Learn)**  
   - [Interfaces (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/)  
   - [Interface inheritance](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation#interface-inheritance)  
   - [Default Interface Methods (C# 8.0+)](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/default-interface-methods)

2. **کتاب: C# in Depth – نوشته Jon Skeet**  
   - فصل مربوط به رابط‌ها و چندشکلی (Polymorphism)

3. **مستندات رسمی .NET**  
   - [.NET Documentation – Interfaces](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/interfaces)

4. **Principles of OOP – SOLID**  
   - Robert C. Martin (Uncle Bob), *Agile Software Development: Principles, Patterns, and Practices*
