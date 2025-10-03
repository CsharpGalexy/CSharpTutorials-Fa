
## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [کلاس‌های استاتیک چیستند؟](#کلاس%E2%80%8Cهای-استاتیک-چیستند)  
3. [Thread Safety چیست؟](#thread-safety-چیست)  
4. [چرا کلاس‌های استاتیک ممکن است Thread-Safe نباشند؟](#چرا-کلاس%E2%80%8Cهای-استاتیک-ممکن-است-thread-safe-نباشند)  
5. [الگوهای رایج برای Thread Safety در کلاس‌های استاتیک](#الگوهای-رایج-برای-thread-safety-در-کلاس‌های-استاتیک)  
   - [استفاده از `lock`](#استفاده-از-lock)  
   - [استفاده از `Lazy<T>` برای Lazy Initialization](#استفاده-از-lazyt-برای-lazy-initialization)  
   - [استفاده از انواع اتمیک مانند `Interlocked`](#استفاده-از-انواع-اتمیک-مانند-interlocked)  
   - [Immutable State](#immutable-state)  
6. [مثال‌های عملی](#مثال‌های-عملی)  
7. [بهترین روش‌ها (Best Practices)](#بهترین-روش‌ها-best-practices)  
8. [جمع‌بندی](#جمع‌بندی)  
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی مدرن، به‌ویژه در محیط‌های چندنخی (Multithreaded)، امنیت نخی (Thread Safety) یکی از مفاهیم حیاتی است. در سی‌شارپ، کلاس‌های استاتیک به دلیل اشتراک‌گذاری حالت (state) بین تمام نخ‌ها، به‌طور پیش‌فرض **Thread-Safe نیستند**. این راهنما به‌صورت گام‌به‌گام و با استناد به منابع معتبر، مفاهیم اساسی تا پیشرفته‌تر را در مورد Thread Safety در کلاس‌های استاتیک سی‌شارپ توضیح می‌دهد.

---

## کلاس‌های استاتیک چیستند؟

کلاس‌های استاتیک در سی‌شارپ (`static class`) کلاس‌هایی هستند که:

- نمی‌توان از آن‌ها نمونه (instance) ساخت.
- فقط شامل اعضای استاتیک (متد، فیلد، خاصیت و ...) هستند.
- به‌طور خودکار sealed هستند.
- در طول عمر برنامه، فقط یک بار مقداردهی می‌شوند (تا زمانی که AppDomain زنده باشد).

مثال ساده:
```csharp
public static class MathHelper
{
    public static int Add(int a, int b) => a + b;
}
```

> ⚠️ **نکته مهم**: اگر یک کلاس استاتیک دارای **فیلدهای استاتیک قابل تغییر** (mutable static fields) باشد، ممکن است در محیط چندنخی مشکل ایجاد کند.

---

## Thread Safety چیست؟

**Thread Safety** به این معناست که یک قطعه کد (مانند یک متد یا کلاس) می‌تواند به‌صورت امن توسط چندین نخ (Thread) به‌طور همزمان فراخوانی یا استفاده شود، بدون اینکه منجر به **وضعیت ناسازگار** (inconsistent state)، **داده‌های خراب‌شده** یا **رفتار غیرقابل پیش‌بینی** شود.

اگر یک کلاس Thread-Safe باشد، نیازی به هماهنگی بیرونی (مانند قفل‌گذاری) برای استفاده از آن در چندنخی نیست.

---

## چرا کلاس‌های استاتیک ممکن است Thread-Safe نباشند؟

کلاس‌های استاتیک **به‌طور ذاتی Thread-Safe نیستند**، زیرا:

- تمام اعضای آن‌ها به‌صورت **اشتراکی** بین تمام نخ‌های برنامه وجود دارند.
- اگر چندین نخ همزمان به یک فیلد استاتیک قابل تغییر دسترسی پیدا کنند، ممکن است **race condition** ایجاد شود.

### مثال ناسالم (غیر Thread-Safe):

```csharp
public static class Counter
{
    private static int _count = 0;

    public static void Increment() => _count++; // ❌ ناسالم در چندنخی
    public static int GetCount() => _count;
}
```

عملیات `_count++` شامل سه مرحله است:
1. خواندن مقدار فعلی
2. افزایش آن
3. ذخیره مقدار جدید

اگر دو نخ همزمان این عملیات را انجام دهند، ممکن است هر دو مقدار یکسانی را بخوانند و سپس هر دو یکی افزایش دهند — در نتیجه فقط یک افزایش واقعی رخ دهد!

---

## الگوهای رایج برای Thread Safety در کلاس‌های استاتیک

### استفاده از `lock`

ساده‌ترین راه برای جلوگیری از race condition، استفاده از `lock` است:

```csharp
public static class SafeCounter
{
    private static readonly object _lock = new object();
    private static int _count = 0;

    public static void Increment()
    {
        lock (_lock)
        {
            _count++;
        }
    }

    public static int GetCount()
    {
        lock (_lock)
        {
            return _count;
        }
    }
}
```

> ✅ **نکته**: همیشه از یک `object` خصوصی و `readonly` برای قفل استفاده کنید تا از تداخل با سایر بخش‌های کد جلوگیری شود.

---

### استفاده از `Lazy<T>` برای Lazy Initialization

اگر کلاس استاتیک شما شامل یک شیء سنگین است که به‌صورت lazy مقداردهی می‌شود، از `Lazy<T>` با حالت Thread-Safe استفاده کنید:

```csharp
public static class ConfigManager
{
    private static readonly Lazy<Dictionary<string, string>> _config = 
        new Lazy<Dictionary<string, string>>(() => LoadConfig(), isThreadSafe: true);

    public static Dictionary<string, string> Config => _config.Value;

    private static Dictionary<string, string> LoadConfig()
    {
        // بارگذاری تنظیمات از فایل یا دیتابیس
        return new Dictionary<string, string>();
    }
}
```

> ✅ `Lazy<T>` با `isThreadSafe: true` (که پیش‌فرض هم هست) به‌صورت کامل Thread-Safe است.

---

### استفاده از انواع اتمیک مانند `Interlocked`

برای عملیات ساده عددی (مانند افزایش/کاهش)، به‌جای `lock` می‌توانید از `Interlocked` استفاده کنید که سریع‌تر و سبک‌تر است:

```csharp
public static class AtomicCounter
{
    private static long _count = 0;

    public static void Increment() => Interlocked.Increment(ref _count);
    public static long GetCount() => Interlocked.Read(ref _count);
}
```

> ✅ `Interlocked` عملیات‌های اتمیک را روی متغیرهای ساده انجام می‌دهد و نیازی به قفل ندارد.

---

### Immutable State

اگر وضعیت (state) کلاس استاتیک **غیرقابل تغییر** (immutable) باشد، به‌طور خودکار Thread-Safe است:

```csharp
public static class Constants
{
    public static readonly string AppName = "MyApp";
    public static readonly DateTime LaunchDate = new DateTime(2024, 1, 1);
}
```

> ✅ داده‌های فقط‌خواندنی (read-only) یا immutable هرگز تغییر نمی‌کنند، پس هیچ race condition‌ای ایجاد نمی‌شود.

---

## مثال‌های عملی

### ❌ مثال ناسالم (غیر Thread-Safe)
```csharp
public static class UnsafeLogger
{
    private static readonly List<string> _logs = new List<string>(); // List Thread-Safe نیست!

    public static void Log(string message)
    {
        _logs.Add(message); // ❌ خطرناک در چندنخی
    }
}
```

### ✅ مثال امن (Thread-Safe)
```csharp
public static class SafeLogger
{
    private static readonly object _lock = new object();
    private static readonly List<string> _logs = new List<string>();

    public static void Log(string message)
    {
        lock (_lock)
        {
            _logs.Add(message);
        }
    }
}
```

یا بهتر: استفاده از `ConcurrentQueue<T>`:
```csharp
public static class ConcurrentSafeLogger
{
    private static readonly ConcurrentQueue<string> _logs = new ConcurrentQueue<string>();

    public static void Log(string message) => _logs.Enqueue(message); // ✅ Thread-Safe
}
```

---

## بهترین روش‌ها (Best Practices)

1. **از فیلدهای استاتیک قابل تغییر (mutable) خودداری کنید** — ترجیحاً فقط از `readonly` یا `const` استفاده کنید.
2. **اگر حتماً نیاز به state دارید، آن را immutable یا synchronized کنید**.
3. **برای ساختارهای داده‌ای مانند لیست یا دیکشنری، از `System.Collections.Concurrent` استفاده کنید** (مثل `ConcurrentDictionary`, `ConcurrentQueue`).
4. **هرگز از `lock(this)` یا `lock(typeof(MyClass))` استفاده نکنید** — همیشه یک `private readonly object` اختصاصی برای قفل تعریف کنید.
5. **در صورت امکان، از `Interlocked` یا `Lazy<T>` به‌جای `lock` استفاده کنید** — عملکرد بهتری دارند.
6. **کلاس‌های استاتیک را stateless نگه دارید** — این ساده‌ترین راه برای Thread Safety است.

---

## جمع‌بندی

- کلاس‌های استاتیک در سی‌شارپ **به‌طور پیش‌فرض Thread-Safe نیستند**.
- وجود **فیلدهای استاتیک قابل تغییر** خطر اصلی ایجاد race condition را افزایش می‌دهد.
- برای Thread Safety می‌توانید از `lock`، `Interlocked`، `Lazy<T>` یا ساختارهای داده‌ی همزمان (`Concurrent*`) استفاده کنید.
- ساده‌ترین راه: **کلاس استاتیک را بدون state نگه دارید** یا state آن را **immutable** کنید.

---

## منابع معتبر

1. **Microsoft Docs – Static Classes and Static Class Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)

2. **Microsoft Docs – Thread-Safe Collections**  
   [https://learn.microsoft.com/en-us/dotnet/standard/collections/thread-safe/](https://learn.microsoft.com/en-us/dotnet/standard/collections/thread-safe/)

3. **Microsoft Docs – Lazy Initialization**  
   [https://learn.microsoft.com/en-us/dotnet/framework/performance/lazy-initialization](https://learn.microsoft.com/en-us/dotnet/framework/performance/lazy-initialization)

4. **Microsoft Docs – Interlocked Class**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.threading.interlocked](https://learn.microsoft.com/en-us/dotnet/api/system.threading.interlocked)

5. **C# in Depth – Jon Skeet (Chapter on Threading)**  
   ISBN: 978-1617294531 — بخش‌های مربوط به Thread Safety و static state

6. **.NET Threading Guidelines – Microsoft .NET Design Guidelines**  
   [https://learn.microsoft.com/en-us/dotnet/standard/threading/](https://learn.microsoft.com/en-us/dotnet/standard/threading/)

7. **Concurrent Programming in C# – Stephen Cleary**  
   [https://stephencleary.com/](https://stephencleary.com/) — مقالات و کتاب‌های معتبر درباره همزمانی
