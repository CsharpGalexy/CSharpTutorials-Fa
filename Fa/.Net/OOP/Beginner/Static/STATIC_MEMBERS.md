

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [تعریف اعضای استاتیک](#تعریف-اعضای-استاتیک)  
3. [فیلدهای استاتیک (Static Fields)](#فیلدهای-استاتیک-static-fields)  
4. [پراپرتی‌های استاتیک (Static Properties)](#پراپرتی‌های-استاتیک-static-properties)  
5. [متدهای استاتیک (Static Methods)](#متدهای-استاتیک-static-methods)  
6. [تفاوت‌های کلیدی بین اعضای استاتیک و نمونه‌ای (Instance)](#تفاوت‌های-کلیدی-بین-اعضای-استاتیک-و-نمونه‌ای-instance)  
7. [بهترین شیوه‌ها و ملاحظات (Best Practices)](#بهترین-شیوه‌ها-و-ملاحظات-best-practices)  
8. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP) با زبان C#، **اعضای استاتیک (Static Members)** یکی از مفاهیم پایه‌ای و کاربردی هستند که به شما امکان می‌دهند بدون نیاز به ایجاد نمونه (instance) از یک کلاس، به فیلدها، پراپرتی‌ها یا متدهای آن دسترسی داشته باشید. این مفهوم به‌ویژه در مواردی مانند کتابخانه‌های کمکی (Utility Classes)، مدیریت حالت‌های سراسری (Global State) یا پیاده‌سازی الگوهای طراحی مانند **Singleton** کاربرد دارد.

در این راهنما، به‌صورت ساده و مستند، به بررسی **فیلدهای استاتیک**، **پراپرتی‌های استاتیک** و **متدهای استاتیک** در C# می‌پردازیم.

---

## تعریف اعضای استاتیک

اعضای استاتیک به اعضایی گفته می‌شوند که با کلیدواژه `static` تعریف می‌شوند و **متعلق به خود کلاس** هستند، نه به هیچ نمونه خاصی از آن کلاس. این اعضا **در زمان بارگذاری کلاس (Class Loading)** توسط CLR (Common Language Runtime) مقداردهی اولیه می‌شوند و **تنها یک نسخه از آن‌ها در تمام برنامه وجود دارد**.

> 🔹 نکته: اعضای استاتیک با نام کلاس فراخوانی می‌شوند، نه با نام یک شیء.

---

## فیلدهای استاتیک (Static Fields)

فیلد استاتیک، یک متغیر است که به کل کلاس تعلق دارد و بین تمام نمونه‌های آن کلاس **اشتراک‌گذاری می‌شود**.

### مثال:

```csharp
public class Counter
{
    public static int Count = 0; // فیلد استاتیک

    public Counter()
    {
        Count++; // هر بار که شیء جدیدی ساخته شود، Count افزایش می‌یابد
    }
}

// استفاده:
Console.WriteLine(Counter.Count); // خروجی: 0
var c1 = new Counter();
var c2 = new Counter();
Console.WriteLine(Counter.Count); // خروجی: 2
```

### ویژگی‌ها:
- فقط یک نسخه از فیلد در کل برنامه وجود دارد.
- بدون نیاز به `new` قابل دسترسی است.
- مقداردهی اولیه در زمان بارگذاری کلاس انجام می‌شود.

> ⚠️ هشدار: استفاده بی‌رویه از فیلدهای استاتیک می‌تواند منجر به **حالت سراسری (Global State)** شود که تست‌پذیری و نگهداری کد را سخت می‌کند.

---

## پراپرتی‌های استاتیک (Static Properties)

پراپرتی استاتیک، یک رابط استاندارد برای دسترسی به یک فیلد استاتیک (معمولاً خصوصی) فراهم می‌کند و امکان کنترل خواندن/نوشتن را می‌دهد.

### مثال:

```csharp
public class AppConfig
{
    private static string _appName = "MyApp";

    public static string AppName
    {
        get => _appName;
        set => _appName = value ?? "DefaultApp";
    }
}

// استفاده:
Console.WriteLine(AppConfig.AppName); // خروجی: MyApp
AppConfig.AppName = "NewApp";
Console.WriteLine(AppConfig.AppName); // خروجی: NewApp
```

### کاربردها:
- مدیریت تنظیمات سراسری.
- پیاده‌سازی **الگوی Singleton**.
- ارائه دسترسی کنترل‌شده به داده‌های استاتیک.

---

## متدهای استاتیک (Static Methods)

متدهای استاتیک، توابعی هستند که **بدون نیاز به نمونه‌سازی کلاس** قابل فراخوانی‌اند و **نمی‌توانند به اعضای غیراستاتیک (instance members)** دسترسی داشته باشند.

### مثال:

```csharp
public class MathHelper
{
    public static int Add(int a, int b)
    {
        return a + b;
    }

    public static double CalculateCircleArea(double radius)
    {
        return Math.PI * radius * radius;
    }
}

// استفاده:
int sum = MathHelper.Add(5, 3); // بدون نیاز به new
double area = MathHelper.CalculateCircleArea(2.5);
```

### ویژگی‌ها:
- نمی‌توانند از کلمه کلیدی `this` استفاده کنند.
- نمی‌توانند به فیلدها یا متدهای غیراستاتیک دسترسی داشته باشند.
- معمولاً برای عملیات‌های **کمکی** یا **ریاضی** استفاده می‌شوند.

> 💡 نکته: کلاس‌هایی که فقط شامل اعضای استاتیک هستند، معمولاً به‌عنوان `static class` تعریف می‌شوند (مانند `System.Math`).

---

## تفاوت‌های کلیدی بین اعضای استاتیک و نمونه‌ای (Instance)

| ویژگی | عضو استاتیک | عضو نمونه‌ای (Instance) |
|--------|--------------|--------------------------|
| دسترسی | با نام کلاس (`ClassName.Member`) | با نام شیء (`object.Member`) |
| تعداد نسخه | 1 نسخه در کل برنامه | یک نسخه برای هر شیء |
| دسترسی به `this` | ❌ خیر | ✅ بله |
| دسترسی به اعضای دیگر | فقط به اعضای استاتیک | به همه اعضای کلاس |
| زمان مقداردهی | زمان بارگذاری کلاس | زمان ساخت شیء (`new`) |

---

## بهترین شیوه‌ها و ملاحظات (Best Practices)

1. **از کلاس‌های استاتیک برای Utility استفاده کنید**:  
   مانند `FileHelper`, `StringFormatter` و غیره.

2. **از فیلدهای استاتیک برای حالت سراسری خودداری کنید**:  
   این کار می‌تواند کد را غیرقابل تست و پیچیده کند.

3. **کلاس‌های استاتیک را sealed در نظر بگیرید**:  
   در C#، کلاس‌های استاتیک به‌طور خودکار `sealed` هستند و نمی‌توان از آن‌ها ارث‌بری کرد.

4. **استاتیک بودن را برای داده‌های ثابت یا توابع کمکی در نظر بگیرید**:  
   مانند ثابت‌ها (`const`) یا متدهایی که وابسته به حالت شیء نیستند.

5. **در محیط‌های چندنخی (Multithreading) مراقب باشید**:  
   داده‌های استاتیک بین تمام نخ‌ها (Threads) مشترک هستند و نیاز به همگام‌سازی (Synchronization) دارند.

---

## منابع معتبر

1. **Microsoft Learn – Static Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)

2. **C# Language Specification (ECMA-334)**  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

3. **"C# in Depth" by Jon Skeet** – فصل 2 و 3  
   ISBN: 978-1617294532  
   (توضیح عمیق درباره اعضای استاتیک و مکانیزم‌های داخلی CLR)

4. **Stack Overflow – When to use static classes**  
   [https://stackoverflow.com/questions/241339/when-to-use-static-classes-in-c-sharp](https://stackoverflow.com/questions/241339/when-to-use-static-classes-in-c-sharp)

5. **Microsoft .NET Documentation – Classes and Objects**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/)

