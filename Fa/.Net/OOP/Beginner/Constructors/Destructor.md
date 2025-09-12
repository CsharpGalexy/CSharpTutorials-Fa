## 📚 فهرست مطالب

- [🔰 مقدمه](#-مقدمه)
- [📘 تعریف Destructor](#-تعریف-destructor)
  - [ویژگی‌های کلیدی Destructor](#-ویژگیهای-کلیدی-destructor)
- [🛠 نحوه تعریف Destructor](#-نحوه-تعریف-destructor)
- [⏱ چه زمانی Destructor فراخوانی می‌شود؟](#-چه-زمانی-destructor-فراخوانی-میشود)
- [🔄 تفاوت Constructor و Destructor](#-تفاوت-constructor-و-destructor)
- [🚫 محدودیت‌های Destructor](#-محدودیتهای-destructor)
- [💡 Destructor چگونه کار می‌کند؟ (سطح پیشرفته)](#-destructor-چگونه-کار-می‌کند-سطح-پیشرفته)
- [🎯 چرا نباید Destructor برای مدیریت حافظه استفاده کرد؟](#-چرا-نباید-destructor-برای-مدیریت-حافظه-استفاده-کرد)
- [🧩 الگوی Dispose + Destructor (Dispose Pattern)](#-الگوی-dispose--destructor-dispose-pattern)
  - [مثال: پیاده‌سازی صحیح Dispose Pattern](#-مثال-پیادهسازی-صحیح-dispose-pattern)
- [🧪 نحوه استفاده از کلاس بالا](#-نحوه-استفاده-از-کلاس-بالا)
- [🧠 بهترین شیوه‌های استفاده (Best Practices)](#-بهترین-شیوههای-استفاده-best-practices)
- [🧪 تست Destructor (برای آزمایش)](#-تست-destructor-برای-آزمایش)
- [📚 منابع معتبر](#-منابع-معتبر)
- [📝 جمع‌بندی](#-جمعبندی)
- [🙏 نتیجه‌گیری](#-نتیجهگیری)

---


# 🧨 Destructor در C# — از مقدماتی تا پیشرفته

> 🔹 نویسنده: [abolfazl shabani]  
> 📚 منبع اصلی: Microsoft Docs, C# Language Specification, Pro C# by Andrew Troelsen  
> 🗂️ بخش: OOP in C#

---

## 🔰 مقدمه

در برنامه‌نویسی شیءگرا (OOP)، مدیریت منابع (Resources) یکی از مهم‌ترین جنبه‌هاست. در زبان **C#**، به‌عنوان یک زبان تحت **CLR (Common Language Runtime)** و با سیستم **Garbage Collector (جمع‌آوری دوره‌ای حافظه)**، مدیریت حافظه به صورت خودکار انجام می‌شود. اما همیشه تمام منابع فقط شامل حافظه نیستند — مثلاً فایل‌ها، شبکه، اتصالات پایگاه داده، هندل‌های سیستمی و غیره نیاز به **تخریب صریح** دارند.

برای این منظور، C# از **Destructor** (همچنین به نام Finalizer شناخته می‌شود) پشتیبانی می‌کند تا بتوان قبل از حذف یک شیء از حافظه، عملیات پاک‌سازی را انجام داد.

---

## 📘 تعریف Destructor

**Destructor** در C# یک متد عضو خاص در یک کلاس است که **به صورت خودکار** قبل از آزاد شدن شیء توسط Garbage Collector فراخوانی می‌شود. هدف اصلی آن **پاک‌سازی منابع غیرمدیریت‌شده (unmanaged resources)** است.

### ✅ ویژگی‌های کلیدی Destructor:

- فقط در **کلاس‌ها** مجاز است (نه struct).
- فقط یک Destructor در هر کلاس مجاز است.
- نام آن همیشه با علامت `~` شروع می‌شود: `~ClassName()`.
- **بدون مُد (modifier)** است (مثلاً public, private, static نمی‌تواند باشد).
- **ورودی نمی‌گیرد** و **override نمی‌شود**.
- **به صورت مستقیم قابل فراخوانی نیست**.
- توسط **Garbage Collector** فراخوانی می‌شود.

---

## 🛠 نحوه تعریف Destructor

```csharp
public class MyClass
{
    // Constructor
    public MyClass()
    {
        Console.WriteLine("Constructor called.");
    }

    // Destructor (Finalizer)
    ~MyClass()
    {
        Console.WriteLine("Destructor called.");
    }
}
```

---

## ⏱ چه زمانی Destructor فراخوانی می‌شود؟

Destructor **هنگام جمع‌آوری شیء توسط Garbage Collector** فراخوانی می‌شود. این اتفاق **غیرقابل پیش‌بینی** است و به عوامل زیر بستگی دارد:

- حجم حافظه استفاده‌شده
- فشار حافظه در سیستم
- نوع GC (Concurrent, Background, etc.)
- دستور `GC.Collect()` (که استفاده از آن توصیه نمی‌شود)

> ❌ نمی‌توان تضمین کرد که Destructor دقیقاً وقتی فراخوانی می‌شود که شیء خارج از محدوده (scope) می‌رود.

---

## 🔄 تفاوت Constructor و Destructor

| ویژگی | Constructor | Destructor |
|-------|-------------|------------|
| نام | `ClassName()` | `~ClassName()` |
| فراخوانی | هنگام ایجاد شیء (`new`) | هنگام حذف شیء توسط GC |
| ورودی | می‌گیرد | نمی‌گیرد |
| مُد (modifier) | می‌تواند داشته باشد | نمی‌تواند داشته باشد |
| override | نمی‌تواند باشد | نمی‌تواند باشد |
| فراخوانی مستقیم | بله | ❌ خیر |


---

## 🚫 محدودیت‌های Destructor

1. **عدم قابلیت overload**: فقط یک Destructor در کلاس مجاز است.
2. **عدم دسترسی به static members**: نمی‌توانید اعضای static را در Destructor فراخوانی کنید (ممکن است قبلاً توسط GC تخریب شده باشند).
3. **عدم تضمین زمان اجرا**: نمی‌دانید دقیقاً چه زمانی فراخوانی می‌شود.
4. **عملکرد پایین**: شیء‌های دارای Destructor در صف **Finalization Queue** قرار می‌گیرند و نیاز به دو مرحله جمع‌آوری دارند → کاهش عملکرد.
5. **عدم اطمینان از اجرای کامل**: در صورت crash برنامه، ممکن است Destructor اصلاً اجرا نشود.
6. ** Destructorها نمی‌توانند 'async' باشند.

---

## 💡 Destructor چگونه کار می‌کند؟ (سطح پیشرفته)

وقتی یک شیء با Destructor ایجاد می‌شود:

1. شیء در Heap ایجاد می‌شود.
2. ارجاع آن به **Finalization Queue** اضافه می‌شود.
3. وقتی GC تشخیص دهد شیء unreferenced است، آن را به **FReachable Queue** منتقل می‌کند.
4. یک **thread جداگانه** (Finalizer Thread) Destructor را اجرا می‌کند.
5. در دور بعدی GC، شیء واقعاً از حافظه حذف می‌شود.

> 🔁 این فرآیند باعث **تأخیر در آزادسازی حافظه** و کاهش عملکرد می‌شود.

---

## 🎯 چرا نباید Destructor برای مدیریت حافظه استفاده کرد؟

چون:
- Garbage Collector به‌طور خودکار حافظه managed را پاک می‌کند.
- اجرای Destructor تأخیر دارد.
- افزایش فشار روی GC.
- ریسک memory leak برای منابع unmanaged.

✅ بنابراین، Destructor فقط برای **منابع غیرمدیریت‌شده** (مثل IntPtr، File Handles، COM objects) استفاده می‌شود.

---

## 🧩 الگوی Dispose + Destructor (Dispose Pattern)

برای مدیریت صحیح منابع، .NET از **الگوی Dispose** استفاده می‌کند که ترکیبی از `IDisposable` و Destructor است.

### ✅ مثال: پیاده‌سازی صحیح Dispose Pattern

```csharp
using System;

public class ResourceManager : IDisposable
{
    private IntPtr _handle; // Unmanaged resource
    private bool _disposed = false; // Track disposal

    public ResourceManager()
    {
        _handle = AllocateUnmanagedResource();
        Console.WriteLine("Resource allocated.");
    }

    private IntPtr AllocateUnmanagedResource()
    {
        // Simulate allocation
        return new IntPtr(1);
    }

    private void ReleaseUnmanagedResource()
    {
        if (_handle != IntPtr.Zero)
        {
            // Free the resource
            _handle = IntPtr.Zero;
            Console.WriteLine("Unmanaged resource released.");
        }
    }

    // Dispose method (explicit cleanup)
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this); // Prevent finalizer from running
    }

    // Protected virtual Dispose method
    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                // Clean up managed resources (if any)
            }

            // Always clean up unmanaged resources
            ReleaseUnmanagedResource();

            _disposed = true;
        }
    }

    // Destructor (backup for unmanaged cleanup)
    ~ResourceManager()
    {
        Dispose(false);
        Console.WriteLine("Finalizer executed.");
    }
}
```

### 🔍 توضیحات:

- `Dispose(true)`: تمیزکاری همه منابع (managed + unmanaged).
- `GC.SuppressFinalize(this)`: جلوگیری از اجرای Destructor (چون قبلاً تمیزکاری شده).
- `Dispose(false)` در Destructor: فقط منابع unmanaged را پاک می‌کند (چون ممکن است managed resources قبلاً توسط GC جمع‌آوری شده باشند).

---

## 🧪 نحوه استفاده از کلاس بالا

```csharp
class Program
{
    static void Main()
    {
        using (var resource = new ResourceManager())
        {
            // Use resource
        } // Dispose is called automatically here

        // Force GC to see finalizer behavior (for demo only)
        GC.Collect();
        GC.WaitForPendingFinalizers();
    }
}
```

> ✅ استفاده از `using` ضروری است تا تمیزکاری فوری انجام شود.

---

## 🧠 بهترین شیوه‌های استفاده (Best Practices)

| شیوه | توضیح |
|------|-------|
| ✅ از Destructor فقط برای unmanaged resources استفاده کنید | اگر فقط از managed objects استفاده می‌کنید، Destructor لازم نیست. |
| ✅ از `IDisposable` و `using` استفاده کنید | تمیزکاری فوری و قابل پیش‌بینی. |
| ✅ از `GC.SuppressFinalize(this)` استفاده کنید | پس از `Dispose`، جلوگیری از اجرای اضافی Destructor. |
| ❌ از فراخوانی `GC.Collect()` خودداری کنید | غیرضروری و ممکن است عملکرد را کاهش دهد. |
| ✅ Destructor را protected و virtual تعریف کنید | برای کلاس‌های پایه، اجازه override در فرزندان. |
| ❌ از دسترسی به اشیاء دیگر در Destructor خودداری کنید | ممکن است قبلاً توسط GC تخریب شده باشند. |

---

## 🧪 تست Destructor (برای آزمایش)

برای مشاهده رفتار Destructor در محیط توسعه:

```csharp
class TestFinalizer
{
    public static int Count = 0;
    public int Id;

    public TestFinalizer()
    {
        Id = ++Count;
        Console.WriteLine($"Object {Id} created.");
    }

    ~TestFinalizer()
    {
        Console.WriteLine($"Object {Id} finalized.");
    }
}

// Usage
static void Main()
{
    new TestFinalizer();
    new TestFinalizer();

    GC.Collect();
    GC.WaitForPendingFinalizers();
}
```

> خروجی مثال:
```
Object 1 created.
Object 2 created.
Object 2 finalized.
Object 1 finalized.
```

> ⚠️ این روش فقط برای تست است. در محیط تولید نباید `GC.Collect()` فراخوانی شود.

---

## 📚 منابع معتبر

1. **Microsoft Docs - Finalizers**  
   👉 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers)

2. **Microsoft Docs - Implementing Dispose Pattern**  
   👉 [https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose)

3. **C# Language Specification (ECMA-334)**  
   👉 [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

4. **Pro C# 10 with .NET 6** – Andrew Troelsen & Philip Japikse  
   Chapter 7: "Garbage Collection and Resource Management"

5. **CLR via C#** – Jeffrey Richter  
   Chapter 21: "Managed Heap and Garbage Collection"

6. **Stack Overflow - When is a C# destructor called?**  
   👉 [https://stackoverflow.com/questions/455789](https://stackoverflow.com/questions/455789)

---

## 📝 جمع‌بندی

| موضوع | نتیجه |
|------|-------|
| آیا Destructor برای همه کلاس‌ها لازم است؟ | ❌ فقط برای کلاس‌هایی که unmanaged resource دارند. |
| آیا Destructor جایگزین Dispose است؟ | ❌ خیر، فقط پشتیبان است. |
| آیا Destructor زمان‌بندی‌شده است؟ | ❌ غیرقابل پیش‌بینی. |
| بهترین جایگزین Destructor چیست؟ | ✅ `IDisposable` + `using` statement |

---






## 🙏 نتیجه‌گیری

Destructor ابزاری قدرتمند اما خطرناک است. استفاده نادرست از آن می‌تواند منجر به **نشت حافظه، کاهش عملکرد و رفتارهای غیرقابل پیش‌بینی** شود. بنابراین، تنها زمانی از آن استفاده کنید که **منابع غیرمدیریت‌شده** مدیریت می‌کنید و همیشه آن را در کنار **الگوی Dispose** پیاده‌سازی کنید.

> ✅ توصیه نهایی: **هرگز از Destructor به عنوان جایگزین Dispose استفاده نکنید.**

---
