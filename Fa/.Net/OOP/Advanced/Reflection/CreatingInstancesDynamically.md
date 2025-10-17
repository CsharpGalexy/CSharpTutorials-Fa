

## فهرست مطالب

1. [مقدمه‌ای بر Reflection در C#](#1-مقدمهای-بر-reflection-در-c)
2. [چرا از Reflection برای ایجاد نمونه استفاده کنیم؟](#2-چرا-از-reflection-برای-ایجاد-نمونه-استفاده-کنیم؟)
3. [روش‌های ایجاد نمونه‌های پویا](#3-روشهای-ایجاد-نمونههای-پویا)
   - [3.1. استفاده از `Activator.CreateInstance`](#31-استفاده-از-activatorcreateinstance)
   - [3.2. استفاده از `Type.GetConstructor` و `ConstructorInfo.Invoke`](#32-استفاده-از-typegetconstructor-و-constructorinfoinvoke)
4. [مقایسه عملکرد و کاربردها](#4-مقایسه-عملکرد-و-کاربردها)
5. [نکات امنیتی و بهترین روش‌ها (Best Practices)](#5-نکات-امنیتی-و-بهترین-روشها-best-practices)
6. [کاربردهای واقعی در دنیای واقعی](#6-کاربردهای-واقعی-در-دنیای-واقعی)
7. [جمع‌بندی](#7-جمعبندی)
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه‌ای بر Reflection در C#

**Reflection** یکی از قابلیت‌های قدرتمند زبان C# است که به برنامه اجازه می‌دهد **در زمان اجرا (Runtime)** اطلاعاتی درباره‌ی **انواع (Types)**، **ویژگی‌ها (Properties)**، **متدها (Methods)** و **سازنده‌ها (Constructors)** را بررسی کند و حتی با آن‌ها تعامل داشته باشد.

این قابلیت در فضای نام `System.Reflection` قرار دارد و برای سناریوهایی مثل:
- ساخت فریم‌ورک‌های عمومی (مانند ORMها)
- سیستم‌های پلاگین
- سریال‌سازی/دسیریال‌سازی
- تست‌نویسی خودکار (Unit Testing)

بسیار کاربردی است.

> 🔍 **نکته**: Reflection در زمان اجرا کار می‌کند، بنابراین خطاهایی که با آن رخ می‌دهد، معمولاً **Compile-Time** نیستند و در **Runtime** ظاهر می‌شوند.

---

## 2. چرا از Reflection برای ایجاد نمونه استفاده کنیم؟

در برنامه‌نویسی شیءگرا، گاهی نیاز داریم **نوع کلاس را در زمان کامپایل ندانیم**، اما بخواهیم در زمان اجرا یک نمونه از آن بسازیم. مثال‌هایی از این سناریوها:

- بارگذاری کلاس‌های پلاگین از فایل‌های DLL
- ساخت شیء بر اساس نام کلاس در فایل پیکربندی (مثل `appsettings.json`)
- فریم‌ورک‌هایی که از Dependency Injection استفاده می‌کنند

در این موارد، **Reflection** تنها راه برای ساخت شیء بدون دانستن نوع دقیق آن در زمان کامپایل است.

---

## 3. روش‌های ایجاد نمونه‌های پویا

### 3.1. استفاده از `Activator.CreateInstance`

ساده‌ترین و رایج‌ترین روش برای ایجاد نمونه پویا است.

#### مثال ۱: ساخت نمونه از کلاس با سازنده پیش‌فرض

```csharp
using System;

public class Person
{
    public string Name { get; set; }
    public Person()
    {
        Name = "Unknown";
    }
}

// در جای دیگری از کد:
Type type = typeof(Person);
object instance = Activator.CreateInstance(type);
Person person = (Person)instance;
Console.WriteLine(person.Name); // خروجی: Unknown
```

#### مثال ۲: ساخت نمونه با پارامترهای سازنده

```csharp
public class Product
{
    public string Name { get; }
    public decimal Price { get; }

    public Product(string name, decimal price)
    {
        Name = name;
        Price = price;
    }
}

// ساخت نمونه با پارامتر
object product = Activator.CreateInstance(typeof(Product), "Laptop", 1200m);
```

> ✅ **مزیت**: ساده و خوانا  
> ⚠️ **نکته**: اگر سازنده‌ای با آن تعداد/نوع پارامتر وجود نداشته باشد، `MissingMethodException` پرتاب می‌شود.

---

### 3.2. استفاده از `Type.GetConstructor` و `ConstructorInfo.Invoke`

این روش دقیق‌تر است و کنترل بیشتری روی سازنده مورد نظر فراهم می‌کند.

#### مثال:

```csharp
using System;
using System.Reflection;

public class Car
{
    public string Model { get; }
    public int Year { get; }

    public Car(string model, int year)
    {
        Model = model;
        Year = year;
    }
}

// پیدا کردن سازنده با دو پارامتر (string, int)
Type carType = typeof(Car);
ConstructorInfo ctor = carType.GetConstructor(new Type[] { typeof(string), typeof(int) });

if (ctor != null)
{
    object car = ctor.Invoke(new object[] { "Tesla Model 3", 2023 });
    Console.WriteLine(((Car)car).Model); // خروجی: Tesla Model 3
}
else
{
    Console.WriteLine("Constructor not found!");
}
```

> ✅ **مزیت**: کنترل کامل روی سازنده، امکان بررسی وجود سازنده قبل از فراخوانی  
> ❌ **عیب**: کد طولانی‌تر و پیچیده‌تر

---

## 4. مقایسه عملکرد و کاربردها

| روش | سادگی | انعطاف‌پذیری | عملکرد | کاربرد مناسب |
|------|--------|----------------|----------|----------------|
| `Activator.CreateInstance` | ⭐⭐⭐⭐ | ⭐⭐⭐ | متوسط | سناریوهای عمومی، کدنویسی سریع |
| `ConstructorInfo.Invoke` | ⭐⭐ | ⭐⭐⭐⭐ | کمی بهتر (با بررسی قبلی) | نیاز به کنترل دقیق، فریم‌ورک‌نویسی |

> 📊 **نکته عملکردی**: Reflection به‌طور کلی **کندتر** از کد مستقیم است. اگر عملیات تکراری است، بهتر است از **کش کردن** `ConstructorInfo` یا استفاده از **Expression Trees** یا **Source Generators** (در .NET 5+) استفاده کنید.

---

## 5. نکات امنیتی و بهترین روش‌ها (Best Practices)

1. **هرگز از ورودی کاربر مستقیماً برای ساخت نوع استفاده نکنید**  
   → ممکن است به حمله‌های امنیتی (مانند RCE) منجر شود.

2. **همیشه خطاها را مدیریت کنید**  
   → استفاده از `try-catch` برای `MissingMethodException`، `TargetException` و غیره.

3. **در صورت امکان از رابط‌ها (Interfaces) یا کلاس‌های پایه استفاده کنید**  
   → تا بتوانید بدون نیاز به casting مکرر، با شیء کار کنید.

   ```csharp
   IPlugin plugin = (IPlugin)Activator.CreateInstance(pluginType);
   plugin.Execute();
   ```

4. **در .NET 6+ از `Activator.CreateInstance<T>()` برای انواع شناخته‌شده استفاده کنید**  
   → این متد generic است و type-safe است.

---

## 6. کاربردهای واقعی در دنیای واقعی

- **ASP.NET Core**: سیستم Dependency Injection از Reflection برای ساخت سرویس‌ها استفاده می‌کند.
- **Entity Framework Core**: برای نگاشت رکوردهای دیتابیس به کلاس‌های Entity.
- **Newtonsoft.Json / System.Text.Json**: برای دسیریال‌سازی به اشیاء.
- **Unit Testing Frameworks** (مثل xUnit, NUnit): برای کشف و اجرای تست‌ها.

---

## 7. جمع‌بندی

- **Reflection** ابزاری قدرتمند برای کار با انواع در زمان اجراست.
- برای **ایجاد نمونه پویا**، دو روش اصلی وجود دارد: `Activator.CreateInstance` (ساده‌تر) و `ConstructorInfo.Invoke` (دقیق‌تر).
- همیشه **خطاها را مدیریت کنید** و از **ورودی‌های نامعتبر** جلوگیری نمایید.
- در پروژه‌های حرفه‌ای، این تکنیک‌ها پایه‌ی بسیاری از فریم‌ورک‌های مدرن هستند.

---

## 8. منابع معتبر

1. **Microsoft Learn – Reflection**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection)

2. **Microsoft Learn – Activator.CreateInstance Method**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.activator.createinstance](https://learn.microsoft.com/en-us/dotnet/api/system.activator.createinstance)

3. **Microsoft Learn – ConstructorInfo Class**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.reflection.constructorinfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.constructorinfo)

4. **C# in Depth (Jon Skeet)** – فصل Reflection  
   📘 ناشر: Manning Publications

5. **.NET Documentation – Best Practices for Reflection**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/security-considerations-for-reflection](https://learn.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/security-considerations-for-reflection)

6. **Stack Overflow – Performance of Activator.CreateInstance vs ConstructorInfo.Invoke**  
   🔗 [https://stackoverflow.com/questions/3330745/performance-of-activator-createinstance](https://stackoverflow.com/questions/3330745/performance-of-activator-createinstance)

