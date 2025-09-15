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