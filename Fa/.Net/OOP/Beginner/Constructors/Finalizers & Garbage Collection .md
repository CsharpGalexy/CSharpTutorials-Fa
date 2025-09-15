# راهنمای ساده: فاینالایزرها و جمع‌آوری زباله (Garbage Collection) در C#

## 🎯 هدف
این مقاله برای برنامه‌نویسان تازه‌کار نوشته شده تا با مفاهیم مدیریت حافظه در فریم‌ورک .NET، نقش فاینالایزرها، الگوی Dispose و استفاده از SafeHandle آشنا بشن. هدف اینه که یاد بگیرین چطور منابع رو به شکل ایمن و کارآمد مدیریت کنین.

## 💡 مقدمه: مدیریت حافظه در .NET
در زبان C# و فریم‌ورک .NET، مدیریت حافظه به صورت خودکار انجام می‌شه. یعنی شما لازم نیست مثل زبان‌های C یا C++ به صورت دستی حافظه رو تخصیص بدین یا آزاد کنین. این کار توسط **Garbage Collector (GC)** انجام می‌شه.

GC مرتب بررسی می‌کنه که کدوم اشیاء دیگه در برنامه استفاده نمی‌شن (یعنی هیچ رفرنسی بهشون وجود نداره) و حافظه‌شون رو آزاد می‌کنه. این فرآیند از خطاهایی مثل **memory leak** یا **dangling pointers** جلوگیری می‌کنه.

اما یه نکته مهم وجود داره: GC فقط با **منابع مدیریت‌شده (managed resources)** مثل اشیاء .NET کار می‌کنه. اگر کلاس شما با **منابع غیرمدیریت‌شده (unmanaged resources)** مثل فایل‌ها، اتصالات دیتابیس یا هندل‌های سیستم‌عامل کار کنه، GC نمی‌تونه این منابع رو به تنهایی آزاد کنه. اینجا فاینالایزرها و الگوی Dispose وارد بازی می‌شن.

## 🧹 فاینالایزر چیست؟
فاینالایزر (که در C# بهش **destructor** هم می‌گن) یه متد خاصه که قبل از اینکه GC یه شیء رو کاملاً از حافظه پاک کنه، اجرا می‌شه. این متد فرصتیه برای آزاد کردن منابع غیرمدیریت‌شده، مثل بستن فایل یا آزاد کردن هندل‌های سیستم.

### 🔑 ویژگی‌های فاینالایزر:
- توسط GC فراخوانی می‌شه (برنامه‌نویس نمی‌تونه مستقیماً صداش کنه).
- زمان اجرای اون **غیرقابل پیش‌بینیه** (ممکنه دیر اجرا بشه یا حتی موقع خروج برنامه اجرا نشه).
- نباید به منابع مدیریت‌شده وابسته باشه (چون ممکنه این منابع قبلاً توسط GC پاک شده باشن).
- وجود فاینالایزر باعث می‌شه شیء به **صف فاینالایزیشن (finalization queue)** اضافه بشه و فرآیند جمع‌آوری حافظه کندتر بشه.

📌 **نتیجه**: فاینالایزر فقط یه **شبکه ایمنی (safety net)** برای مواقع خاصه و نباید بهش به عنوان روش اصلی پاکسازی تکیه کنین.

## ⚖️ تفاوت Dispose و Finalizer
| ویژگی               | Dispose                            | Finalizer                          |
|---------------------|------------------------------------|------------------------------------|
| **فراخوانی**       | دستی (توسط برنامه‌نویس یا `using`) | خودکار (توسط GC)                  |
| **زمان اجرا**      | قابل پیش‌بینی (بلافاصله)          | غیرقابل پیش‌بینی                 |
| **هدف**            | پاکسازی منابع مدیریت‌شده و غیرمدیریت‌شده | فقط منابع غیرمدیریت‌شده          |
| **بهترین کاربرد**  | روش اصلی پاکسازی                 | شبکه ایمنی (backup)              |

✅ **همیشه اولویت با Dispose و using است.**

## 🧪 نمونه کد با فاینالایزر و Dispose
بیایم یه مثال واقعی از کلاسی بزنیم که یه فایل رو مدیریت می‌کنه. از `FileStream` استفاده می‌کنیم که یه نمونه واقعی از منبعیه که نیاز به پاکسازی داره.

```csharp
using System;
using System.IO;

public class FileManager : IDisposable
{
    private readonly string _filePath;
    private FileStream _fileStream;
    private bool _disposed = false;

    public FileManager(string filePath)
    {
        _filePath = filePath;
        _fileStream = new FileStream(_filePath, FileMode.OpenOrCreate);
        Console.WriteLine($"فایل '{_filePath}' باز شد.");
    }

    // فاینالایزر
    ~FileManager()
    {
        Console.WriteLine($"فاینالایزر فراخوانی شد برای '{_filePath}'");
        Dispose(false); // false یعنی توسط GC فراخوانی شده
    }

    // متد پاکسازی اصلی
    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                // آزاد کردن منابع مدیریت‌شده
                _fileStream?.Dispose();
                Console.WriteLine("منابع مدیریت‌شده (مثل FileStream) آزاد شدند.");
            }

            // آزاد کردن منابع غیرمدیریت‌شده (در اینجا FileStream خودش هندل می‌کنه)
            Console.WriteLine($"منابع غیرمدیریت‌شده برای '{_filePath}' آزاد شدند.");
            _disposed = true;
        }
    }

    // پیاده‌سازی IDisposable
    public void Dispose()
    {
        Console.WriteLine($"Dispose فراخوانی شد برای '{_filePath}'");
        Dispose(true);
        GC.SuppressFinalize(this); // جلوگیری از اجرای فاینالایزر
    }

    // متد نمونه برای نوشتن در فایل
    public void Write(string text)
    {
        if (_disposed) throw new ObjectDisposedException(nameof(FileManager));
        using var writer = new StreamWriter(_fileStream, leaveOpen: true);
        writer.WriteLine(text);
        writer.Flush();
    }
}

// تست کلاس
class Program
{
    static void Main()
    {
        using (var fm = new FileManager("test.txt"))
        {
            fm.Write("سلام دنیا!");
            Console.WriteLine("در حال نوشتن در فایل...");
        } // اینجا Dispose به طور خودکار اجرا می‌شه

        // برای تست فاینالایزر (فقط برای آزمایش، در تولید استفاده نکنید!)
        // GC.Collect();
        // GC.WaitForPendingFinalizers();

        Console.WriteLine("پایان برنامه.");
    }
}
```
### 🔍 توضیح کد گام به گام:
1. **سازنده (FileManager)**: فایل رو باز می‌کنه و `FileStream` رو مقداردهی می‌کنه. متغیر `_disposed` روی `false` تنظیم می‌شه تا از پاکسازی تکراری جلوگیری بشه.
2. **فاینالایزر (~FileManager)**: فقط `Dispose(false)` رو فراخوانی می‌کنه تا منابع غیرمدیریت‌شده آزاد بشن. به منابع مدیریت‌شده دسترسی نداره، چون ممکنه قبلاً پاک شده باشن.
3. **متد Dispose(bool disposing)**: هسته‌ی الگوی Dispose. اگر `disposing` برابر `true` باشه (یعنی از `Dispose()` فراخوانی شده)، منابع مدیریت‌شده مثل `FileStream` رو آزاد می‌کنه. همیشه منابع غیرمدیریت‌شده رو آزاد می‌کنه.
4. **متد Dispose()**: پیاده‌سازی عمومی رابط `IDisposable`. متد `Dispose(true)` رو فراخوانی می‌کنه و `GC.SuppressFinalize` رو اجرا می‌کنه تا فاینالایزر بیهوده اجرا نشه.
5. **تست در Main()**: از `using` استفاده کردیم تا `Dispose` به طور خودکار فراخوانی بشه. اگر `using` رو حذف کنین و `GC.Collect()` رو فعال کنین، فاینالایزر اجرا می‌شه (ولی این کار فقط برای تست مناسبه).

## 🛡️ SafeHandle: روش مدرن و ایمن
مایکروسافت قویاً توصیه می‌کنه به جای نوشتن فاینالایزرهای دستی، از کلاس‌های `SafeHandle` استفاده کنین. `SafeHandle` یه روش امن و thread-safe برای مدیریت منابع غیرمدیریت‌شده ارائه می‌ده و خودش فاینالایزر داخلی داره، بنابراین نیازی به تعریف فاینالایزر در کدتون نیست.

### نمونه کد با SafeHandle:
```csharp
using Microsoft.Win32.SafeHandles;
using System;
using System.IO;

public class MyResource : IDisposable
{
    private SafeFileHandle _handle;
    private bool _disposed;

    public MyResource(string path)
    {
        _handle = File.OpenHandle(path);
        Console.WriteLine($"هندل برای '{path}' باز شد.");
    }

    public void Dispose()
    {
        if (!_disposed)
        {
            _handle?.Dispose(); // آزادسازی امن هندل
            Console.WriteLine("هندل آزاد شد.");
            _disposed = true;
        }
    }
}
```

 📌 **نتیجه**: با `SafeHandle` کد ساده‌تره، ایمن‌تره و نیازی به فاینالایزر نیست.

## 🔄 نگاهی کوتاه به نسل‌های Garbage Collector
GC برای بهینه‌سازی، اشیاء رو در **نسل‌ها (Generations)** دسته‌بندی می‌کنه:
- **Gen 0**: اشیاء تازه‌ساخته‌شده. بیشتر جمع‌آوری‌ها اینجا اتفاق می‌افته چون اشیاء معمولاً کوتاه‌عمرن.
- **Gen 1**: اشیائی که از Gen 0 جون سالم به در بردن.
- **Gen 2**: اشیاء با عمر طولانی، مثل تنظیمات برنامه.

این مدل باعث می‌شه GC سریع‌تر عمل کنه، چون نیازی نیست همیشه کل حافظه رو اسکن کنه.

## ⚠️ نکات مهم
- **ترتیب اجرای فاینالایزرها نامشخصه**: به اشیاء دیگه در فاینالایزر وابسته نباشین، چون ممکنه قبلاً پاک شده باشن.
- **فاینالایزر رو دستی فراخوانی نکنین**: فقط GC باید این کار رو بکنه.
- **فاینالایزرها کندن**: چون اشیاء رو به صف فاینالایزیشن می‌فرستن و GC دو مرحله‌ای می‌شه.
- **در .NET 5 و بالاتر**: فاینالایزرها موقع خروج برنامه اجرا نمی‌شن. برای cleanup نهایی، از رویداد `AppDomain.ProcessExit` یا `IHostApplicationLifetime` استفاده کنین.

## ✅ بهترین روش‌ها (Best Practices)
1. همیشه رابط `IDisposable` رو برای کلاس‌هایی که منابع غیرمدیریت‌شده دارن پیاده‌سازی کنین.
2. از `using` (یا `using declaration` در C# 8+) برای فراخوانی خودکار `Dispose` استفاده کنین.
3. فاینالایزر رو فقط وقتی واقعاً لازمه اضافه کنین (مثلاً برای منابع غیرمدیریت‌شده‌ای که SafeHandle نمی‌تونن پوشش بدن).
4. برای مدیریت منابع غیرمدیریت‌شده، تا حد ممکن از `SafeHandle` استفاده کنین.

### الگوی کامل Dispose:
```csharp
public class MyResourceHolder : IDisposable
{
    private bool _disposed = false;
    private IntPtr _unmanagedHandle; // نمونه‌ای از منبع غیرمدیریت‌شده

    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                // آزاد کردن منابع مدیریت‌شده (مثل اشیاء IDisposable)
            }

            // آزاد کردن منابع غیرمدیریت‌شده (مثل CloseHandle(_unmanagedHandle))
            _disposed = true;
        }
    }

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    ~MyResourceHolder()
    {
        Dispose(false);
    }
}
```

و نحوه استفاده:
```csharp
using (var obj = new MyResourceHolder())
{
    // کار با شیء
} // Dispose به طور خودکار فراخوانی می‌شه
```

## 📚 منابع رسمی
تمام مطالب این مقاله بر اساس مستندات رسمی مایکروسافت و استانداردهای برنامه‌نویسی C# نوشته شده:
- [Implementing a Dispose method](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose)
- [Finalizers (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers)
- [Using objects that implement IDisposable](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/using-objects)
- [C# Language Specification — Destructors](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#destructors)

## ✍️ نتیجه‌گیری
فاینالایزرها یه ابزار کمکی برای پاکسازی منابع غیرمدیریت‌شده هستن، اما به خاطر غیرقابل پیش‌بینی بودن، نباید روشون حساب باز کنین. به جاش، از الگوی Dispose و `using` برای مدیریت دقیق و سریع منابع استفاده کنین. برای منابع غیرمدیریت‌شده، `SafeHandle` راه‌حل مدرن و توصیه‌شده‌ست که کد رو ایمن‌تر و ساده‌تر می‌کنه. با تمرین این مفاهیم و نوشتن کدهای واقعی، به راحتی می‌تونین مدیریت حافظه رو در C# مثل حرفه‌ای‌ها انجام بدین!