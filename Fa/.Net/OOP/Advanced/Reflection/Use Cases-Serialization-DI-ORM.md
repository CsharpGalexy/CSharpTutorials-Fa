

## فهرست مطالب

1. [مقدمه‌ای بر Reflection در C#](#1-مقدمهای-بر-reflection-در-c)
2. [کاربرد اول: Serialization با Reflection](#2-کاربرد-اول-serialization-با-reflection)
3. [کاربرد دوم: Dependency Injection (DI) با Reflection](#3-کاربرد-دوم-dependency-injection-di-با-reflection)
4. [کاربرد سوم: ORM و Reflection](#4-کاربرد-سوم-orm-و-reflection)
5. [جمع‌بندی و بهترین روش‌ها (Best Practices)](#5-جمعبندی-و-بهترین-روشها-best-practices)
6. [منابع معتبر](#6-منابع-معتبر)

---

## 1. مقدمه‌ای بر Reflection در C#

**Reflection** در C# امکان **بررسی و دستکاری متادیتا** (اطلاعات در مورد نوع‌ها، خواص، متدها و ...) در زمان اجرا (Runtime) را فراهم می‌کند. این ویژگی از طریق فضای نام `System.Reflection` پیاده‌سازی شده است.

### چرا Reflection مهم است؟
- امکان نوشتن کد **عمومی** (Generic) و **انعطاف‌پذیر**.
- پایه‌ی بسیاری از فریم‌ورک‌های مدرن مثل ASP.NET Core، Entity Framework و سریالایزرها.
- امکان **ایجاد اشیاء، فراخوانی متدها و دسترسی به خواص بدون دانستن نوع دقیق در زمان کامپایل**.

### مثال ساده:
```csharp
Type type = typeof(Person);
PropertyInfo nameProp = type.GetProperty("Name");
object person = Activator.CreateInstance(type);
nameProp.SetValue(person, "Ali");
Console.WriteLine(nameProp.GetValue(person)); // خروجی: Ali
```

> 🔍 **نکته**: Reflection هزینه‌ی عملکردی (Performance Overhead) دارد. در کاربردهای حساس به عملکرد، از کش کردن یا جایگزین‌هایی مثل **Source Generators** (در .NET 5+) استفاده کنید.

---

## 2. کاربرد اول: Serialization با Reflection

### چه چیزی را سریالایز می‌کنیم؟
تبدیل یک شیء به فرمت قابل ذخیره یا انتقال (مثل JSON یا XML).

### چرا Reflection نیاز است؟
چون سریالایزر نمی‌داند شیء چه خواصی دارد — مگر اینکه در زمان اجرا آن‌ها را بررسی کند.

### مثال عملی با `System.Text.Json`:
```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}

var person = new Person { Name = "Ali", Age = 30 };
string json = JsonSerializer.Serialize(person); // Reflection در پس‌زمینه استفاده می‌شود
```

### چگونه Reflection کار می‌کند؟
- `JsonSerializer` از `Type.GetProperties()` برای پیدا کردن تمام خواص عمومی استفاده می‌کند.
- سپس با `PropertyInfo.GetValue()` مقدار هر خاصیت را می‌خواند.
- در دی‌سریالایز، از `Activator.CreateInstance()` برای ساخت شیء و `SetValue()` برای پر کردن خواص استفاده می‌شود.

> ⚠️ **هشدار عملکردی**: برای بهینه‌سازی، فریم‌ورک‌های مدرن از **کش کردن** `PropertyInfo` یا حتی **کد تولید شده در زمان کامپایل** (مثل `JsonSourceGenerationContext` در .NET 6+) استفاده می‌کنند.

---

## 3. کاربرد دوم: Dependency Injection (DI) با Reflection

### DI چیست؟
الگویی برای کاهش وابستگی‌های سخت‌کد شده بین کلاس‌ها با تزریق وابستگی‌ها از خارج.

### چرا Reflection در DI Containerها ضروری است؟
وقتی یک سرویس را ثبت می‌کنید (مثلاً `services.AddScoped<ILogger, ConsoleLogger>()`)، کانتینر DI باید:
- در زمان اجرا، نوع درخواستی را تشخیص دهد.
- سازنده (Constructor) آن را بررسی کند.
- وابستگی‌های مورد نیاز را پیدا و تزریق کند.

### مثال ساده از DI با Reflection:
```csharp
public class EmailService
{
    private readonly ILogger _logger;
    public EmailService(ILogger logger) => _logger = logger;
}

// در کانتینر DI (مثل Microsoft.Extensions.DependencyInjection):
var ctor = typeof(EmailService).GetConstructors().First();
var parameters = ctor.GetParameters();
var args = parameters.Select(p => serviceProvider.GetService(p.ParameterType)).ToArray();
var instance = Activator.CreateInstance(typeof(EmailService), args);
```

### چه کتابخانه‌هایی از این استفاده می‌کنند؟
- **Microsoft.Extensions.DependencyInjection** (استاندارد در ASP.NET Core)
- **Autofac**
- **Ninject**

> 💡 **نکته**: کانتینر‌های DI معمولاً در اولین درخواست، اطلاعات را **کش** می‌کنند تا از هزینه‌ی مکرر Reflection جلوگیری شود.

---

## 4. کاربرد سوم: ORM و Reflection

### ORM چیست؟
Object-Relational Mapper — ابزاری که داده‌های رابطه‌ای (مثل جداول دیتابیس) را به اشیاء C# تبدیل می‌کند.

### چرا Reflection در ORM ضروری است؟
- ORM باید بداند کدام خاصیت به کدام ستون مربوط می‌شود.
- هنگام خواندن رکورد از دیتابیس، باید شیء را بسازد و خواص آن را پر کند — بدون اینکه در زمان کامپایل نوع دقیق را بداند.

### مثال با Entity Framework Core:
```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
}

var products = context.Products.ToList(); // EF Core از Reflection برای پر کردن لیست استفاده می‌کند
```

### چگونه کار می‌کند؟
1. EF Core با `Type.GetProperties()` خواص کلاس را بررسی می‌کند.
2. با استفاده از ویژگی‌ها (Attributes) یا Fluent API، نگاشت ستون‌ها را تشخیص می‌دهد.
3. در زمان اجرای کوئری، برای هر سطر دیتابیس یک شیء ایجاد می‌کند و خواص را با `PropertyInfo.SetValue()` پر می‌کند.

> 🚀 **بهینه‌سازی**: EF Core از **Expression Trees** و **کامپایل شده‌ی داینامیک** برای کاهش هزینه‌ی Reflection استفاده می‌کند.

---

## 5. جمع‌بندی و بهترین روش‌ها (Best Practices)

| کاربرد | چرا Reflection؟ | بهترین روش‌ها |
|--------|------------------|----------------|
| **Serialization** | ناشناخته بودن ساختار شیء در زمان کامپایل | استفاده از `JsonSourceGenerationContext` در .NET 6+ |
| **DI** | ساخت اشیاء با وابستگی‌های ناشناخته | کش کردن اطلاعات سازنده‌ها |
| **ORM** | نگاشت داینامیک بین ستون‌ها و خواص | استفاده از Expression Trees یا کتابخانه‌های بهینه‌شده مثل EF Core |

### نکات کلیدی:
- Reflection **قدرتمند** است، اما **کند** است.
- همیشه به دنبال **کش کردن** اطلاعات متادیتا باشید.
- در .NET 5+، از **Source Generators** برای جایگزینی Reflection در موارد تکراری استفاده کنید.
- برای کاربردهای عملی، از فریم‌ورک‌های اثبات‌شده (مثل EF Core یا System.Text.Json) استفاده کنید — نه پیاده‌سازی دستی Reflection.

---

## 6. منابع معتبر

1. **Microsoft Learn – Reflection (C#)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection)

2. **System.Text.Json Source Generation**  
   [https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/source-generation](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/source-generation)

3. **Dependency Injection in .NET** – Microsoft Documentation  
   [https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection)

4. **Entity Framework Core Documentation**  
   [https://learn.microsoft.com/en-us/ef/core/](https://learn.microsoft.com/en-us/ef/core/)

5. **C# in Depth – Jon Skeet** (فصل Reflection)  
   ISBN: 978-1617294938 – Manning Publications

6. **.NET Performance Best Practices – Reflection**  
   [https://learn.microsoft.com/en-us/dotnet/fundamentals/performance/best-practices#reflection](https://learn.microsoft.com/en-us/dotnet/fundamentals/performance/best-practices#reflection)

