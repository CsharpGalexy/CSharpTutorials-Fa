
## فهرست مطالب

1. [مقدمه‌ای بر اینترفیس در C#](#1-مقدمه%E2%80%8Cای-بر-اینترفیس-در-c)
2. [Definition (تعریف) اینترفیس](#2-definition-تعریف-اینترفیس)
3. [Implementation (پیاده‌سازی) اینترفیس](#پیاده%E2%80%8Cسازی-صریح-explicit-implementation)
4. [تفاوت‌های کلیدی بین کلاس و اینترفیس](#4-تفاوت‌های-کلیدی-بین-کلاس-و-اینترفیس)
5. [قابلیت‌های پیشرفته‌تر اینترفیس در C# 8.0+](#5-قابلیت‌های-پیشرفته‌تر-اینترفیس-در-c-80)
6. [بهترین روش‌ها (Best Practices)](#6-بهترین-روش‌ها-best-practices)
7. [جمع‌بندی](#7-جمع‌بندی)
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه‌ای بر اینترفیس در C#

**اینترفیس (Interface)** در C# یک **قرارداد** (Contract) است که تعریف می‌کند یک کلاس چه رفتارهایی (متد، خاصیت، ایونت یا ایندکسر) باید داشته باشد، اما نحوه‌ی پیاده‌سازی آن‌ها را مشخص نمی‌کند.

اینترفیس‌ها برای **چندریختی (Polymorphism)**، **کاهش وابستگی (Loose Coupling)** و **طراحی مبتنی بر قرارداد** استفاده می‌شوند.

> 💡 **نکته برای مبتدی‌ها**:  
> فکر کنید اینترفیس مثل یک "لیست وظایف" است. هر کلاسی که این لیست را "امضا" کند، موظف است تمام وظایف را انجام دهد — اما نحوه‌ی انجام آن‌ها به خودش بستگی دارد.

---

## 2. Definition (تعریف) اینترفیس

### ساختار کلی تعریف اینترفیس

```csharp
public interface IMyInterface
{
    // اعلان متد (بدون بدنه)
    void DoSomething();

    // اعلان خاصیت
    string Name { get; set; }

    // اعلان ایونت (Event)
    event EventHandler SomethingHappened;
}
```

### ویژگی‌های تعریف اینترفیس:

- **همیشه با پیشوند `I` شروع می‌شود** (قرارداد نام‌گذاری .NET).
- **اعضای آن به‌صورت پیش‌فرض `public` هستند** و نمی‌توان سطح دسترسی دیگری مشخص کرد.
- **بدون بدنه (bodyless)** هستند (تا قبل از C# 8.0).
- **نمی‌توانند فیلد (field) داشته باشند** (اما می‌توانند خاصیت داشته باشند).
- **نمی‌توانند سازنده (constructor) داشته باشند**.

> ⚠️ **نکته مهم**:  
> اینترفیس‌ها **چندین ارث‌بری** را پشتیبانی می‌کنند (یک کلاس می‌تواند چندین اینترفیس را پیاده‌سازی کند)، در حالی که کلاس‌ها فقط از یک کلاس پایه می‌توانند ارث‌بری کنند.

---

## 3. Implementation (پیاده‌سازی) اینترفیس

### نحوه پیاده‌سازی

یک کلاس با استفاده از `:` و نام اینترفیس، آن را پیاده‌سازی می‌کند:

```csharp
public class MyClass : IMyInterface
{
    public string Name { get; set; }

    public void DoSomething()
    {
        Console.WriteLine("Doing something!");
    }

    public event EventHandler SomethingHappened;
}
```

### پیاده‌سازی صریح (Explicit Implementation)

گاهی برای جلوگیری از تداخل نام یا کنترل دسترسی، از پیاده‌سازی صریح استفاده می‌شود:

```csharp
public class MyClass : IMyInterface
{
    void IMyInterface.DoSomething()
    {
        Console.WriteLine("Explicit implementation");
    }

    private string _name;
    string IMyInterface.Name { get => _name; set => _name = value; }

    // تمام اعضای اینترفیس باید پیاده‌سازی شوند
    public event EventHandler SomethingHappened;
}
```

در این حالت، متد فقط از طریق رفرنس اینترفیس قابل دسترسی است:

```csharp
IMyInterface obj = new MyClass();
obj.DoSomething(); // کار می‌کند

MyClass obj2 = new MyClass();
obj2.DoSomething(); // ❌ خطا! چون پیاده‌سازی صریح است
```

---

## 4. تفاوت‌های کلیدی بین کلاس و اینترفیس

| ویژگی | کلاس (`class`) | اینترفیس (`interface`) |
|--------|----------------|------------------------|
| ارث‌بری | فقط یک کلاس پایه | چندین اینترفیس |
| اعضا | می‌تواند بدنه داشته باشد | تا C# 7.0 بدون بدنه |
| فیلد | بله | خیر (فقط خاصیت) |
| سازنده | بله | خیر |
| سطح دسترسی | قابل تنظیم | فقط `public` (در تعریف) |
| استفاده | برای مدل‌سازی موجودیت‌ها | برای تعریف رفتارها |

---

## 5. قابلیت‌های پیشرفته‌تر اینترفیس در C# 8.0+

از نسخه‌ی C# 8.0 به بعد، اینترفیس‌ها قابلیت‌های جدیدی پیدا کرده‌اند:

### 5.1. متد‌های پیش‌فرض (Default Interface Methods)

```csharp
public interface ILogger
{
    void Log(string message);

    // متد پیش‌فرض
    void LogError(string error)
    {
        Log($"[ERROR] {error}");
    }
}
```

کلاس‌های پیاده‌ساز نیازی به پیاده‌سازی `LogError` ندارند، مگر بخواهند رفتار پیش‌فرض را تغییر دهند.

### 5.2. خاصیت‌های پیش‌فرض

```csharp
public interface IConfigurable
{
    string ConfigPath { get; } = "default.json";
}
```

### 5.3. اعضا با سطح دسترسی `private`, `protected`, `internal`

```csharp
public interface IProcessor
{
    void Process();

    // متد خصوصی برای استفاده داخلی
    private void Validate()
    {
        // اعتبارسنجی
    }
}
```

> ⚠️ **نکته**: این قابلیت‌ها برای **پشتیبانی از توسعه‌ی کتابخانه‌ها بدون شکستن کد قدیمی** طراحی شده‌اند و نباید به‌جای کلاس‌های انتزاعی استفاده شوند.

---

## 6. بهترین روش‌ها (Best Practices)

1. **از پیشوند `I` برای نام‌گذاری اینترفیس استفاده کنید** (مثل `IRepository`).
2. **اینترفیس‌ها را کوچک و متمرکز نگه دارید** (اصل ISP — Interface Segregation Principle).
3. **از اینترفیس برای وابستگی‌ها در Dependency Injection استفاده کنید**.
4. **پیاده‌سازی صریح را فقط زمانی استفاده کنید که واقعاً نیاز باشد** (مثلاً برای جلوگیری از تداخل نام).
5. **از متد‌های پیش‌فرض فقط برای backward compatibility استفاده کنید**، نه به‌عنوان جایگزین کلاس انتزاعی.

---

## 7. جمع‌بندی

- **اینترفیس یک قرارداد است** که رفتارهای یک کلاس را تعریف می‌کند.
- **تعریف (Definition)** شامل اعلان اعضا بدون بدنه است (البته از C# 8.0 به بعد می‌توان بدنه هم داشت).
- **پیاده‌سازی (Implementation)** یعنی کلاس تمام اعضا را پیاده‌سازی کند.
- اینترفیس‌ها ابزار قدرتمندی برای **طراحی انعطاف‌پذیر و قابل تست** هستند.
- با نسخه‌های جدید C#، اینترفیس‌ها قابلیت‌های بیشتری پیدا کرده‌اند، اما باید با احتیاط استفاده شوند.

---

## 8. منابع معتبر

1. **Microsoft Learn – Interfaces (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/)

2. **C# Language Specification – Interfaces**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/interfaces](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/interfaces)

3. **Default Interface Methods – C# 8.0**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#default-interface-members](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#default-interface-members)

4. **SOLID Principles – Interface Segregation Principle (ISP)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/architectural-principles#interface-segregation-principle-isp](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/architectural-principles#interface-segregation-principle-isp)

5. **C# in Depth – Jon Skeet** (فصل مربوط به اینترفیس‌ها)  
   🔗 [https://csharpindepth.com/](https://csharpindepth.com/)

6. **.NET Design Guidelines – Naming Interfaces**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/names-of-classes-structs-and-interfaces](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/names-of-classes-structs-and-interfaces)
