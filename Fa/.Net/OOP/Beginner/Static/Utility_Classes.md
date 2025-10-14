<div dir="rtl">

# 📘 Utility Classes در C# و ASP.NET Core  
### راهنمای جامع برای توسعه‌دهندگان تازه‌کار

---

## فهرست مطالب
- Utility Classes چیست؟
- ویژگی‌های یک Utility Class خوب
- نمونه کاربردی: ساخت یک Utility Class برای مدیریت رشته‌ها
- نمونه پیشرفته: Utility Class برای مدیریت تاریخ و زمان
- استفاده از Utility Classes در ASP.NET Core
- مقایسه با Extension Methods: چه زمانی از کدام استفاده کنیم؟
- محدودیت‌ها و هشدارهای استفاده از Utility Classes
- بهترین روش‌ها (Best Practices)
- خلاصه و نتیجه‌گیری
- منابع و مراجع

---

## 🔹 Utility Classes چیست؟
در برنامه‌نویسی C#، Utility Class (که گاهی به آن **کلاس کمکی** یا **کلاس کاربردی** هم گفته می‌شود) یک کلاس استاتیک است که مجموعه‌ای از متدهای استاتیک را برای انجام عملیات‌های عمومی، تکرارشونده و مستقل از حالت (*stateless*) فراهم می‌کند.

این کلاس‌ها معمولاً هیچ داده‌ای در خود ذخیره نمی‌کنند و فقط بر اساس ورودی‌هایی که دریافت می‌کنند، خروجی تولید می‌کنند.  
هدف اصلی آن‌ها، ساده‌سازی کد و جلوگیری از تکرار منطق‌های مشابه در نقاط مختلف برنامه است.

در پروژه‌های C# و به‌خصوص **ASP.NET Core**، این کلاس‌ها نقش مهمی ایفا می‌کنند؛ مثلاً در:
- پردازش رشته‌ها  
- تبدیل فرمت‌ها  
- اعتبارسنجی ساده  
- محاسبات عمومی  

استفاده از Utility Classes به توسعه‌دهندگان کمک می‌کند کد تمیزتر، قابل‌تست‌تر و منظم‌تری داشته باشند.

---

## 🔹 ویژگی‌های یک Utility Class خوب
یک Utility Class مؤثر باید بر اساس اصول طراحی خوب (مانند SOLID) ساخته شود.

🔸 **ویژگی‌های کلیدی:**  
- **استاتیک بودن:** کلاس و متدها با `static` تعریف می‌شوند تا بدون نمونه‌سازی قابل استفاده باشند.  
- **غیرقابل ارث‌بری:** با `sealed` از ارث‌بری جلوگیری می‌شود.  
- **فاقد سازنده عمومی:** سازنده‌ی `private` برای جلوگیری از ساخت شیء.  
- **وابستگی کم:** نباید به محیط بیرونی (مثلاً دیتابیس یا HttpContext) وابسته باشد.  
- **تست‌پذیری بالا:** باید قابل تست و مستقل از محیط باشد.  
- **دامنه‌ی محدود:** هر کلاس فقط در یک حوزه خاص (مثلاً مدیریت رشته یا تاریخ) فعالیت کند.  

---

## 🔹 نمونه کاربردی: ساخت یک Utility Class برای مدیریت رشته‌ها

📌 **مسئله:** در برنامه‌ها معمولاً نیاز داریم رشته‌ها را پاک‌سازی یا بررسی کنیم تا مثلاً خالی نباشند یا فاصله‌های اضافی حذف شوند.

📘 **راه‌حل:** ساخت یک کلاس به نام `StringHelper`

```csharp
namespace MyApp.Utilities;

public sealed static class StringHelper
{
    private StringHelper() { } // جلوگیری از ساخت نمونه

    /// بررسی خالی بودن رشته
    public static bool IsNullOrWhiteSpace(string? value)
    {
        return string.IsNullOrWhiteSpace(value);
    }

    /// حذف فاصله‌های اضافی
    public static string? NormalizeWhitespace(string? value)
    {
        if (string.IsNullOrWhiteSpace(value))
            return null;

        return System.Text.RegularExpressions.Regex.Replace(value.Trim(), @"\s+", " ");
    }
}
```

🔍 **تحلیل:**  
- کلاس `sealed static` است → نمونه‌سازی و ارث‌بری ممنوع.  
- متدها مستقل و بدون حالت هستند.  
- قابل استفاده در هر بخش از برنامه بدون نیاز به `DI`.

---

## 🔹 نمونه پیشرفته: Utility Class برای مدیریت تاریخ و زمان

📌 **مسئله:** محاسبه سن، فرمت تاریخ و تبدیل آن در APIها.

📘 **راه‌حل:** ساخت کلاس `DateHelper`

```csharp
namespace MyApp.Utilities;

public sealed static class DateHelper
{
    private DateHelper() { }

    public static int CalculateAge(DateTime birthDate, DateTime referenceDate)
    {
        int age = referenceDate.Year - birthDate.Year;
        if (referenceDate.Month < birthDate.Month ||
            (referenceDate.Month == birthDate.Month && referenceDate.Day < birthDate.Day))
            age--;
        return age;
    }

    public static string ToIsoString(DateTime date)
    {
        return date.ToString("yyyy-MM-ddTHH:mm:ssZ");
    }
}
```

🔍 **تحلیل:**  
- دقیق در محاسبه سن.  
- فرمت ISO مناسب برای APIها.  
- بدون وابستگی و کاملاً مستقل.

---

## 🔹 استفاده از Utility Classes در ASP.NET Core

در پروژه‌های **ASP.NET Core**، معمولاً Utilityها در مسیر زیر قرار می‌گیرند:

```
/Infrastructure
    /Utilities
        StringHelper.cs
        DateHelper.cs
```

و سپس در کنترلر استفاده می‌شوند:

```csharp
using MyApp.Utilities;
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class UserController : ControllerBase
{
    [HttpPost("register")]
    public IActionResult Register([FromBody] RegisterDto dto)
    {
        if (StringHelper.IsNullOrWhiteSpace(dto.Email))
            return BadRequest("ایمیل نمی‌تواند خالی باشد.");

        var email = StringHelper.NormalizeWhitespace(dto.Email)?.ToLowerInvariant();

        return Ok($"ثبت‌نام با ایمیل {email} انجام شد.");
    }
}
```

---

## 🔹 مقایسه با Extension Methods

Extension Methods در واقع نسخه‌ای پیشرفته‌تر از Utility هستند که به صورت طبیعی‌تر روی نوع‌ها فراخوانی می‌شوند.

```csharp
public static class StringExtensions
{
    public static string? NormalizeWhitespace(this string? value)
    {
        if (string.IsNullOrWhiteSpace(value)) return null;
        return System.Text.RegularExpressions.Regex.Replace(value.Trim(), @"\s+", " ");
    }
}
```

و استفاده به شکل:
```csharp
string name = "  Ali   Reza  ";
string cleanName = name.NormalizeWhitespace();
```

📖 **توصیه مایکروسافت:**  
برای افزایش خوانایی، در صورت ارتباط مستقیم با یک نوع خاص از Extension استفاده کنید.

---

## 🔹 محدودیت‌ها و هشدارها

⚠️ استفاده بیش‌ازحد از Utilityها ممکن است منجر به **God Class** شود (کلاسی که همه‌چیز در آن است).  
⚠️ اگر متدها حالت یا وابستگی دارند، Utility مناسب نیست.  
⚠️ اگر thread-safety رعایت نشود، در ASP.NET Core مشکلات همزمانی ایجاد می‌شود.

✅ **راه‌حل:**  
- کلاس‌ها را بر اساس دامنه جدا کنید.  
- از سرویس‌ها برای منطق وابسته به محیط استفاده کنید.  
- همیشه Stateless طراحی کنید.

---

## 🔹 بهترین روش‌ها (Best Practices)

- از `sealed static` و سازنده‌ی خصوصی استفاده کنید.  
- فقط برای منطق‌های عمومی و Stateless.  
- نام‌گذاری واضح (مثل `ValidationHelper` به جای `Utils`).  
- مستندسازی کامل با XML Comments.  
- از قابلیت‌های داخلی .NET استفاده کنید.  
- تست واحد (Unit Test) بنویسید.  
- به SRP و SOLID پایبند باشید.  

---

## 🔹 خلاصه و نتیجه‌گیری

Utility Classes ابزاری عالی برای ساده‌سازی کد هستند، به‌شرطی که به درستی استفاده شوند.  
اگر متدی نیاز به وابستگی دارد، بهتر است در سرویس جداگانه قرار گیرد نه Utility.  
Utilityها مکمل اصول **Clean Code** و **SOLID** هستند و در پروژه‌های بزرگ، نظم و تست‌پذیری را افزایش می‌دهند.

---

## 🔹 منابع و مراجع
- [Microsoft Learn – Static Classes and Static Class Members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)  
- [Microsoft Learn – Extension Methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)  
- [Microsoft ASP.NET Core Docs – Fundamentals and Project Structure](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-8.0)  
- *Clean Code – Robert C. Martin*  
- *Framework Design Guidelines – Krzysztof Cwalina & Brad Abrams*  

</div>
