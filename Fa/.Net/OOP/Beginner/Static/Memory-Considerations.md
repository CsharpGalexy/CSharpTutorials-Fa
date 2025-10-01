
## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [چه زمانی از اعضای استاتیک استفاده می‌شود؟](#چه-زمانی-از-اعضای-استاتیک-استفاده-میشود)
3. [ذخیره‌سازی حافظه برای اعضای استاتیک](#ذخیرهسازی-حافظه-برای-اعضای-استاتیک)
4. [چرخه حیات اعضای استاتیک](#چرخه-حیات-اعضای-استاتیک)
5. [مقایسه حافظه‌ای با اعضای نمونه (Instance Members)](#مقایسه-حافظهای-با-اعضای-نمونه-instance-members)
6. [نکات عملکردی و بهینه‌سازی](#نکات-عملکردی-و-بهینهسازی)
7. [هشدارها و بهترین روش‌ها (Best Practices)](#هشدارها-و-بهترین-روشها-best-practices)
8. [جمع‌بندی](#جمعبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در زبان برنامه‌نویسی C#، **اعضای استاتیک** (Static Members) شامل فیلدها، متد‌ها، پراپرتی‌ها و کلاس‌هایی هستند که به **کلاس** تعلق دارند، نه به **نمونه‌های** (Instance) آن کلاس. این ویژگی باعث می‌شود تنها **یک نسخه** از آن عضو در کل برنامه وجود داشته باشد، صرف‌نظر از تعداد اشیایی که از آن کلاس ساخته می‌شود.

درک **ملاحظات حافظه‌ای** (Memory Considerations) این اعضا برای توسعه‌دهندگان C# بسیار مهم است، چرا که استفاده نادرست از آن‌ها می‌تواند منجر به **مصرف بی‌رویه حافظه**، **مشکلات عملکردی** یا حتی **نشت حافظه** (Memory Leak) شود.

---

## چه زمانی از اعضای استاتیک استفاده می‌شود؟

اعضای استاتیک معمولاً در موارد زیر به کار می‌روند:

- زمانی که داده یا رفتاری **مستقل از هر نمونه خاص** باشد (مثلاً یک شمارنده کلی).
- برای پیاده‌سازی **الگوهای طراحی** مانند Singleton.
- برای تعریف **ثوابت** یا **ابزارهای کمکی** (Utility Methods) که نیازی به نمونه‌سازی ندارند.

مثال ساده:

```csharp
public class Counter
{
    public static int TotalCount = 0; // یک فیلد استاتیک
    public Counter()
    {
        TotalCount++;
    }
}
```

در اینجا `TotalCount` برای **همه نمونه‌ها مشترک** است.

---

## ذخیره‌سازی حافظه برای اعضای استاتیک

### محل ذخیره‌سازی

- اعضای استاتیک در **حافظه مدیریت‌شده** (Managed Heap) ذخیره می‌شوند، اما **نه در هیپ معمولی** بلکه در یک ناحیه خاص به نام **High Frequency Heap** (در CLR قدیمی) یا **Loader Heap** در .NET Framework و .NET Core.
- این ناحیه توسط **CLR** (Common Language Runtime) مدیریت می‌شود و **هرگز توسط Garbage Collector (GC)** آزاد نمی‌شود — مگر اینکه **AppDomain** مربوطه آزاد شود (در .NET Framework) یا **کل برنامه متوقف شود** (در .NET 5+).

> 🔍 **نکته مهم**: اعضای استاتیک **همیشه در حافظه باقی می‌مانند** تا زمانی که برنامه اجراست.

### مصرف حافظه

- هر عضو استاتیک **فقط یک بار** فضا اشغال می‌کند.
- اگر یک فیلد استاتیک یک **شیء بزرگ** (مثل یک لیست یا آرایه بزرگ) باشد، آن شیء **همیشه در حافظه** خواهد ماند.

مثال خطرناک:

```csharp
public class DataCache
{
    public static List<string> Cache = new List<string>();
}
```

اگر این لیست پر شود و هرگز پاک نشود، **حافظه به طور دائمی اشغال** می‌شود.

---

## چرخه حیات اعضای استاتیک

- اعضای استاتیک **هنگام اولین استفاده** از کلاس مقداردهی اولیه می‌شوند (Lazy Initialization).
- سازنده استاتیک (`static constructor`) فقط **یک بار** و **قبل از اولین دسترسی** به کلاس اجرا می‌شود.
- پس از مقداردهی، این اعضا **تا پایان عمر برنامه** زنده می‌مانند.

```csharp
public class Logger
{
    static Logger()
    {
        Console.WriteLine("Static constructor called once.");
    }

    public static string LogFile = "app.log";
}
```

> ⚠️ **هشدار**: اگر برنامه شما طولانی‌مدت اجرا شود (مثل سرویس‌ها یا وب‌اپلیکیشن‌ها)، اعضای استاتیک می‌توانند **منبع نشت حافظه** باشند.

---

## مقایسه حافظه‌ای با اعضای نمونه (Instance Members)

| ویژگی | اعضای استاتیک | اعضای نمونه |
|--------|----------------|---------------|
| تعداد نسخه | 1 نسخه در کل برنامه | 1 نسخه برای هر شیء |
| محل ذخیره‌سازی | Loader Heap (غیرقابل جمع‌آوری توسط GC) | Managed Heap (قابل جمع‌آوری توسط GC) |
| چرخه حیات | تا پایان برنامه | تا زمانی که شیء قابل دسترسی باشد |
| مصرف حافظه | ثابت (اما دائمی) | متغیر (بسته به تعداد اشیاء) |

---

## نکات عملکردی و بهینه‌سازی

1. **از اعضای استاتیک برای داده‌های بزرگ خودداری کنید**  
   مگر اینکه واقعاً نیاز به دسترسی سراسری داشته باشید.

2. **در وب‌اپلیکیشن‌ها مراقب باشید**  
   در ASP.NET Core، استفاده از فیلدهای استاتیک برای ذخیره داده‌های کاربر **مشکل‌ساز** است، چون داده‌ها بین کاربران **اشتراکی** می‌شوند.

3. **استفاده از `readonly` برای فیلدهای استاتیک**  
   اگر مقدار فیلد استاتیک تغییر نمی‌کند، آن را `readonly` تعریف کنید:

   ```csharp
   public static readonly DateTime AppStartTime = DateTime.Now;
   ```

4. **پاک‌سازی دستی در صورت نیاز**  
   اگر فیلد استاتیک یک شیء قابل تغییر است، مطمئن شوید که در زمان مناسب `null` یا خالی شود:

   ```csharp
   public static void ClearCache() => DataCache.Cache.Clear();
   ```

---

## هشدارها و بهترین روش‌ها (Best Practices)

✅ **استفاده مناسب**:
- برای ثوابت، ابزارها، یا داده‌های واقعاً سراسری.

❌ **استفاده نادرست**:
- ذخیره‌سازی داده‌های کاربر یا session در فیلدهای استاتیک.
- استفاده از اعضای استاتیک به جای Dependency Injection در برنامه‌های مدرن.

✅ **بهترین روش‌ها**:
- در برنامه‌های مبتنی بر سرویس (مثل ASP.NET Core)، به جای اعضای استاتیک از **Service Lifetimes** (مثل `Singleton`) استفاده کنید.
- از `static readonly` به جای `static` برای مقادیر ثابت استفاده کنید.
- تست‌پذیری کد را در نظر بگیرید — اعضای استاتیک معمولاً **تست واحد** (Unit Testing) را سخت‌تر می‌کنند.

---

## جمع‌بندی

اعضای استاتیک در C# ابزاری قدرتمند هستند، اما **مصرف دائمی حافظه** و **عدم قابلیت جمع‌آوری توسط GC** آن‌ها نیازمند دقت در طراحی است. درک اینکه این اعضا **کجا**، **چگونه** و **تا چه زمانی** در حافظه نگه داشته می‌شوند، به شما کمک می‌کند تا از **نشت حافظه** و **مشکلات همزمانی** (Concurrency Issues) جلوگیری کنید.

در پروژه‌های مدرن، به‌ویژه در معماری‌های مبتنی بر سرویس، **ترجیحاً از الگوهای جایگزین** مانند Dependency Injection با lifetime مناسب استفاده کنید.

---

## منابع معتبر

1. **Microsoft Docs – Static Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)

2. **Microsoft Docs – Memory Management in .NET**  
   [https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals)

3. **CLR via C# – Jeffrey Richter (4th Edition)**  
   فصل 8: Methods, فصل 21: CLR Hosting and AppDomains  
   ISBN: 978-0735667457

4. **.NET Garbage Collection and Statics – MSDN Blog (Archived)**  
   [https://devblogs.microsoft.com/dotnet/](https://devblogs.microsoft.com/dotnet/) (جستجوی "static memory .NET")

5. **Stack Overflow – Do static variables live on the heap or stack?**  
   [https://stackoverflow.com/questions/4373820/do-static-variables-live-on-the-heap-or-stack](https://stackoverflow.com/questions/4373820/do-static-variables-live-on-the-heap-or-stack)

6. **C# in Depth – Jon Skeet**  
   فصل مربوط به Static Members و Memory Model  
   ISBN: 978-1617294532

