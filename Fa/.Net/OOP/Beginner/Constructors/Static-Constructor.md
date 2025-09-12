## 📌 فهرست مطالب (به‌روزشده)

1. [مقدمه](#1-مقدمه)  
2. [تعریف Static Constructor](#2-تعریف-static-constructor)  
3. [ویژگی‌های کلیدی](#3-ویژگیهای-کلیدی)  
4. [چه زمانی اجرا می‌شود؟](#4-چه-زمانی-اجرا-میشود)  
5. [تفاوت با Instance Constructor](#5-تفاوت-با-instance-constructor)  
6. [قوانین و محدودیت‌ها](#6-قوانین-و-محدودیتها-) ← **پررنگ‌تر شده**  
7. [مثال‌های عملی](#7-مثالهای-عملی)  
8. [استفاده‌های متداول](#8-استفادههای-متداول)  
9. [Thread Safety و سناریوی واقعی](#9-thread-safety-و-سناریوی-واقعی) ← **تکمیل شده با مثال چندنخی**  
10. [Static Constructor و Inheritence](#10-static-constructor-و-inheritence) ← **اضافه شده**  
11. [قبل از beforefieldinit — مفهوم پیشرفته CLR](#11-قبل-از-beforefieldinit--مفهوم-پیشرفته-clr) ← **اضافه شده**  
12. [بهینه‌سازی و Lazy Initialization](#12-بهینهسازی-و-lazy-initialization)  
13. [اشتباهات رایج](#13-اشتباهات-رایج)  
14. [جمع‌بندی](#14-جمعبندی)  
15. [منابع](#15-منابع)

---

## 1. مقدمه

در C#، `Static Constructor` یک ابزار قدرتمند برای مقداردهی اولیهٔ اعضای استاتیک کلاس است که توسط CLR به‌صورت **خودکار، یک‌باره و thread-safe** اجرا می‌شود. این مقاله به همراه بخش‌های جدیدی در مورد `beforefieldinit`، `وراثت` و `امنیت چندنخی`، یک راهنمای کامل برای توسعه‌دهندگان در هر سطحی است.

---

## 2. تعریف Static Constructor

```csharp
static ClassName()
{
    // کد مقداردهی اولیهٔ اعضای استاتیک
}
```

- فقط یک بار در طول عمر برنامه اجرا می‌شود.
- بدون دسترسی مستقیم توسط کاربر فراخوانی می‌شود.
- برای مقداردهی اولیهٔ `static fields` و تنظیمات سراسری کلاس استفاده می‌شود.

---

## 3. ویژگی‌های کلیدی

| ویژگی | توضیح |
|-------|------|
| 🔹 اجرای یک‌باره | فقط یک بار در طول اجرای برنامه |
| 🔹 بدون پارامتر | نمی‌تواند آرگومان داشته باشد |
| 🔹 بدون access modifier | نمی‌توان `public`, `private` و ... داشت |
| 🔹 غیرقابل فراخوانی مستقیم | توسط CLR فراخوانی می‌شود |
| 🔹 Thread-Safe | توسط CLR تضمین شده |
| 🔹 قبل از اولین دسترسی | قبل از اولین استفاده از کلاس اجرا می‌شود |

---

## 4. چه زمانی اجرا می‌شود؟

سازندهٔ استاتیک قبل از اولین دسترسی به کلاس اجرا می‌شود:

- ایجاد نمونه (`new MyClass()`)
- فراخوانی متد استاتیک
- دسترسی به فیلد استاتیک
- استفاده در Reflection

اما:  
> ⚠️ **زمان دقیق اجرا توسط CLR تعیین می‌شود و غیرقطعی (non-deterministic) است** — مگر تحت شرایط خاصی که در بخش `beforefieldinit` توضیح داده می‌شود.

---



## 5. تفاوت با Instance Constructor

| ویژگی | Static Constructor | Instance Constructor |
|--------|---------------------|------------------------|
| ✅ اجرا | یک بار در کل برنامه | هر بار که `new` صدا زده شود |
| ✅ پارامتر | ندارد | می‌تواند پارامتر داشته باشد |
| ✅ Access Modifier | ندارد | می‌تواند `public`, `private`, ... باشد |
| ✅ فراخوانی | توسط CLR | توسط کاربر (با `new`) |
| ✅ اعضای قابل دسترسی | فقط اعضای استاتیک | همه اعضا (استاتیک و نمونه‌ای) |
| ✅ Thread Safety | توسط CLR تضمین شده | باید دستی مدیریت شود |


---

## 6. قوانین و محدودیت‌ها ✅



| ❗ قانون | ❌ مجاز نیست | ✅ مجاز است |
|--------|-------------|------------|
| **۱. پارامتر داشتن** | `static MyClass(int x)` | فقط `static MyClass()` |
| **۲. Access Modifier** | `public static MyClass()` | بدون access modifier |
| **۳. Overload/Override** | چندین static ctor | فقط یکی مجاز است |
| **۴. فراخوانی صریح** | `MyClass()` | فقط توسط CLR |
| **۵. اجرا بیش از یک بار** | ❌ | فقط یک بار |
| **۶. ارجاع به this** | ❌ | فقط اعضای استاتیک قابل دسترسی |

### 💡 نکته مهم:
اگر `static constructor` وجود نداشته باشد، کامپایلر ممکن است پرچم `beforefieldinit` را تنظیم کند که رفتار بارگذاری کلاس را تغییر دهد (در بخش ۱۱ توضیح داده شده).

---

## 7. مثال‌های عملی


### مثال ۱: مقداردهی اولیهٔ فیلدهای استاتیک

```csharp
public class Logger
{
    private static readonly string LogPath;
    private static int EntryCount;

    // Static Constructor
    static Logger()
    {
        LogPath = Path.Combine(Environment.CurrentDirectory, "logs.txt");
        EntryCount = 0;
        File.WriteAllText(LogPath, "Log file created.\n");
    }

    public static void Log(string message)
    {
        var entry = $"[{DateTime.Now}] {message}\n";
        File.AppendAllText(LogPath, entry);
        EntryCount++;
    }
}
```

✅ استفاده:
```csharp
Logger.Log("Application started."); // Static ctor اول اجرا می‌شود
```

---

### مثال ۲: تنظیمات اولیهٔ کلاس

```csharp
public class AppConfig
{
    public static readonly string ApiUrl;
    public static readonly int TimeoutSeconds;

    static AppConfig()
    {
        // مقدار از appsettings.json یا محیط بارگذاری می‌شود
        ApiUrl = Environment.GetEnvironmentVariable("API_URL") 
                 ?? "https://api.example.com";
        TimeoutSeconds = int.Parse(Environment.GetEnvironmentVariable("TIMEOUT") 
                          ?? "30");
    }
}
```

---

### مثال ۳: ثبت ماژول‌ها یا خدمات

```csharp
public class PluginManager
{
    private static readonly List<IPlugin> Plugins = new();

    static PluginManager()
    {
        Plugins.Add(new EmailPlugin());
        Plugins.Add(new SmsPlugin());
        Console.WriteLine("Plugins registered.");
    }

    public static void ExecuteAll()
    {
        foreach (var plugin in Plugins)
            plugin.Execute();
    }
}
```

---

---

## 8. استفاده‌های متداول

| سناریو | توضیح |
|--------|------|
| 🔧 تنظیمات اولیه | مقداردهی متغیرهای استاتیک از config, environment variables |
| 📦 بارگذاری منابع | بارگذاری فایل‌ها، تنظیمات، dictionaryها |
| 🔄 ثبت سرویس‌ها | در DI Container یا Event Bus |
| 🛡️ Thread-Safe Initialization | وقتی نیاز به init امن در چندنخی داریم |
| 🗃️ Singleton Pattern | در بعضی پیاده‌سازی‌های ساده از static ctor برای lazy-init استفاده می‌شود |


---

## 9. Thread Safety و سناریوی واقعی

### 🔧 مشکل: Race Condition در Multi-threading

فرض کنید می‌خواهیم یک متغیر استاتیک را فقط یک بار مقداردهی کنیم، اما چندین نخ به آن دسترسی دارند.

### ❌ روش ناامن (بدون static constructor):

```csharp
public class UnsafeInitializer
{
    private static Service _service;

    public static Service GetService()
    {
        if (_service == null)
            _service = new Service(); // ❌ Race Condition!
        return _service;
    }
}
```

دو نخ ممکن است همزمان شرط را ببینند `null` باشد و دو شیء ایجاد کنند.

---

### ✅ روش امن با Static Constructor

```csharp
public class SafeInitializer
{
    private static readonly Service _service;

    static SafeInitializer()
    {
        _service = new Service();
        Console.WriteLine($"[Thread {Thread.CurrentThread.ManagedThreadId}] Static ctor executed.");
    }

    public static Service Service => _service;
}
```

### ✅ تست چندنخی:

```csharp
var threads = new List<Thread>();

for (int i = 0; i < 10; i++)
{
    var t = new Thread(() =>
    {
        // اولین دسترسی به کلاس
        var s = SafeInitializer.Service;
    });
    threads.Add(t);
}

// همه نخ‌ها همزمان شروع می‌کنند
foreach (var t in threads) t.Start();
foreach (var t in threads) t.Join();

// خروجی:
// فقط یک خط "[Thread X] Static ctor executed." چاپ می‌شود
```

✅ **نتیجه**: فقط یک نخ static constructor را اجرا می‌کند، بقیه منتظر می‌مانند. بدون lock، بدون race condition.

---

## 10. Static Constructor و Inheritence

### ❓ سوال: آیا Static Constructor والد قبل از فرزند اجرا می‌شود؟

❌ **خیر.**  
هر کلاس به صورت **مستقل** دارای نوع خود است و static constructor آن **فقط در زمان اولین دسترسی به خود آن کلاس** اجرا می‌شود.

---

### ✅ مثال:

```csharp
public class BaseClass
{
    public static string Name = "Base";

    static BaseClass()
    {
        Console.WriteLine("BaseClass static ctor executed.");
    }
}

public class DerivedClass : BaseClass
{
    public static string Version = "1.0";

    static DerivedClass()
    {
        Console.WriteLine("DerivedClass static ctor executed.");
    }
}
```

### تست ۱: دسترسی به BaseClass

```csharp
Console.WriteLine(BaseClass.Name); 
// خروجی:
// BaseClass static ctor executed.
```

> ❗ `DerivedClass` static ctor اجرا نمی‌شود.

---

### تست ۲: دسترسی به DerivedClass

```csharp
Console.WriteLine(DerivedClass.Version);
// خروجی:
// DerivedClass static ctor executed.
```

> ❗ `BaseClass` static ctor **اجر نمی‌شود** — مگر اینکه عضوی از BaseClass استفاده شود.

---

### تست ۳: ایجاد نمونه از DerivedClass

```csharp
var obj = new DerivedClass();
```

> ✅ در این حالت:
> - ابتدا `BaseClass` type initialize می‌شود (اگر قبلاً نشده باشد)
> - سپس `DerivedClass` type initialize می‌شود
> - بنابراین static ctor هر دو اجرا می‌شود (اگر قبلاً اجرا نشده باشند)

> ⚠️ توجه: این رفتار به ترتیب وراثت وابسته است، ولی static constructorها **به صورت زنجیره‌ای فراخوانی نمی‌شوند**. هر کلاس وقتی نیاز باشد initialize می‌شود.

---

## 11. قبل از beforefieldinit — مفهوم پیشرفته CLR

این یک نکته پیشرفته است که رفتار دقیق `type initialization` را تحت تأثیر قرار می‌دهد.

### 🔍 قبل از شروع: what is `beforefieldinit`?

در IL (Intermediate Language)، کلاس‌ها می‌توانند دارای پرچم `beforefieldinit` باشند. این پرچم به CLR اجازه می‌دهد که **بارگذاری کلاس و اجرای static constructor** را تا زمان لازم به تأخیر بیندازد — حتی اگر کد به یک عضو استاتیک دسترسی داشته باشد.

---

### ✅ شرایط حذف `beforefieldinit`:

کامپایلر C# به صورت پیش‌فرض پرچم `beforefieldinit` را برای typeها تنظیم می‌کند تا بهینه‌سازی اجرای JIT را ممکن سازد. این پرچم تنها در یک حالت **حذف** می‌شود:

1. کلاس یک **سازنده استاتیک صریح (`static constructor`)** داشته باشد.

> 📌 به عبارت دیگر: صرفاً نوشتن یک `static constructor` در کد، فارغ از محتوای آن (حتی اگر خالی باشد)، کافی است تا کامپایلر پرچم `beforefieldinit` را حذف کند و تضمین کند که مقداردهی اولیه دقیقاً قبل از اولین دسترسی به اعضای کلاس انجام شود.
---

### 🔁 تأثیر بر زمان اجرا:

| وضعیت | رفتار CLR |
|-------|----------|
| ✅ `beforefieldinit` وجود دارد | CLR می‌تواند static constructor را **زودتر** از اولین دسترسی اجرا کند (مثلاً در زمان بارگذاری ensamble) |
| ❌ `beforefieldinit` وجود ندارد | CLR تضمین می‌کند که static constructor **دقیقاً قبل از اولین دسترسی** به کلاس اجرا شود |

---

### ✅ مثال:

```csharp
// Case 1: beforefieldinit وجود دارد
public class SimpleConfig
{
    public static readonly string Path = "config.json";
    // ❌ static ctor نداریم → beforefieldinit تنظیم می‌شود
}

// Case 2: beforefieldinit حذف می‌شود
public class ComplexConfig
{
    public static readonly string Path;

    static ComplexConfig()
    {
        Path = File.ReadAllText("path.txt"); // ✅ منطق داریم
        Console.WriteLine("ComplexConfig initialized.");
    }
    // ✅ beforefieldinit حذف می‌شود
}
```

> 🔎 بررسی IL با ابزار مثل **ILSpy** یا **dotPeek** نشان می‌دهد که `ComplexConfig` فاقد پرچم `beforefieldinit` است.

---

### 🎯 چرا مهم است؟

- اگر نیاز به **اجرای دقیق در زمان اولین دسترسی** دارید (مثلاً برای lazy-load سنگین)، باید static constructor داشته باشید تا `beforefieldinit` غیرفعال شود.
- اگر می‌خواهید عملکرد بهتری داشته باشید و مقداردهی زودهنگام مشکلی نداشته باشد، `beforefieldinit` مفید است.

---

## 12. بهینه‌سازی و Lazy Initialization

### 🔍 قبل از معرفی `Lazy<T>`، از static constructor برای lazy-init استفاده می‌شد.

اما حالا با وجود `Lazy<T>`، می‌توان تأخیر در ایجاد شیء را دقیق‌تر کنترل کرد:

```csharp
public class ExpensiveService
{
    private static readonly Lazy<ExpensiveService> _instance = 
        new Lazy<ExpensiveService>(() => new ExpensiveService());

    public static ExpensiveService Instance => _instance.Value;

    private ExpensiveService()
    {
        // شبیه‌سازی کار سنگین
        Thread.Sleep(1000);
        Console.WriteLine("ExpensiveService created.");
    }
}
```

> ✅ `Lazy<T>` کنترل بهتری روی زمان ایجاد شیء می‌دهد و می‌تواند thread-safe باشد.

اما static constructor همچنان برای مواردی که نیاز به init یک‌باره و ایمن داریم، عالی است.


---

## 13. اشتباهات رایج

### ❌ ۱. فراخوانی صریح static constructor
```csharp
MyClass(); // ❌ امکان‌پذیر نیست
```

### ❌ ۲. استفاده از access modifier
```csharp
private static MyClass() { } // ❌ خطای کامپایل
```

### ❌ ۳. انتظار زمان اجرای دقیق
```csharp
// نمی‌توان تضمین کرد که دقیقاً در این لحظه اجرا می‌شود
Console.WriteLine(MyClass.SomeValue); // ممکن است static ctor قبلاً اجرا شده باشد
```

### ❌ ۴. استفاده برای کارهای سنگین بدون دلیل
اگر static constructor خیلی سنگین باشد، ممکن است باعث تأخیر در شروع کار برنامه شود.



---

## 14. جمع‌بندی

| ویژگی | وضعیت |
|------|-------|
| ✅ اجرای یک‌باره | ✔️ |
| ✅ Thread-Safe | ✔️ |
| ✅ بدون lock | ✔️ |
| ✅ برای init اعضا | ✔️ |
| ⚠️ زمان اجرا غیرقطعی | ⚠️ (تحت beforefieldinit) |
| ❌ قابل فراخوانی | ❌ |
| 🔁 وراثت تأثیری ندارد | ✔️ (هر کلاس مستقل) |

### ✅ بهترین کاربردها:
- مقداردهی اولیهٔ `static readonly`
- تنظیمات محیطی
- ثبت ماژول‌ها
- پیاده‌سازی Singleton امن

### 🔄 جایگزین‌های مدرن:
- `Lazy<T>` برای کنترل دقیق‌تر زمان ایجاد
- Dependency Injection برای مدیریت lifecycle

---

## 15. منابع

- 📘 [Microsoft Docs - Static Constructors](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)
- 📘 [ECMA-335 CLI Specification](https://www.ecma-international.org/publications-and-standards/standards/ecma-335/) – Section II.10.4.3 (Type Initialization)
- 📚 *C# in Depth* by Jon Skeet – Chapter 3 (BeforeFieldInit and Type Initialization)
- 🛠️ [SharpLab.io](https://sharplab.io) – برای مشاهده IL و تشخیص `beforefieldinit`
- 📺 Pluralsight: *Advanced C#* – Type Initialization and Performance

---

## ✅ خلاصه نهایی (Cheat Sheet)

```csharp
public class MyClass
{
    private static readonly object Data;

    // ✅ فقط یک بار، thread-safe، قبل از اولین دسترسی
    static MyClass()
    {
        Data = ExpensiveLoad(); // مثلاً خواندن فایل
        Console.WriteLine("Initialized");
    }

    public static object GetData() => Data;
}
```

> 🔥 نکات طلایی:
> - از static constructor برای init امن و یک‌باره استفاده کنید.
> - اگر منطق پیچیده دارید، `beforefieldinit` غیرفعال می‌شود و زمان اجرا دقیق‌تر است.
> - در وراثت، هر کلاس مستقل است.
> - از کارهای سنگین در static ctor پرهیز کنید — ممکن است startup برنامه را کند کند.

---


