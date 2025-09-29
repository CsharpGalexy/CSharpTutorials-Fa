
### فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [تعریف کلاس استاتیک](#تعریف-کلاس-استاتیک)  
3. [ویژگی‌های کلاس‌های استاتیک](#ویژگی‌های-کلاس‌های-استاتیک)  
4. [چرا فقط اعضای استاتیک؟](#چرا-فقط-اعضای-استاتیک)  
5. [مثال‌های عملی](#مثال‌های-عملی)  
6. [کاربردهای رایج](#کاربردهای-رایج)  
7. [محدودیت‌ها و نکات مهم](#محدودیت‌ها-و-نکات-مهم)  
8. [تفاوت با کلاس‌های غیراستاتیک](#تفاوت-با-کلاس‌های-غیراستاتیک)  
9. [جمع‌بندی](#جمع‌بندی)  
10. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP) با زبان C#، **کلاس‌های استاتیک** (Static Classes) یکی از مفاهیم مهم و کاربردی هستند که برای گروه‌بندی توابع و داده‌هایی طراحی شده‌اند که **نیازی به ایجاد نمونه (instance) ندارند**. یکی از قوانین اساسی در مورد کلاس‌های استاتیک این است که **فقط می‌توانند شامل اعضای استاتیک باشند**. این مفهوم در این راهنما به‌صورت کامل و با منابع معتبر توضیح داده می‌شود.

---

## تعریف کلاس استاتیک

کلاس استاتیک در C# با کلیدواژه `static` تعریف می‌شود و **فقط می‌تواند شامل اعضای استاتیک** (static members) باشد. این اعضا شامل متدها، ویژگی‌ها (properties)، فیلدها (fields)، رویدادها (events) و سازنده‌های استاتیک (static constructors) می‌شوند.

```csharp
public static class MathHelper
{
    public static double Pi = 3.14159;
    
    public static double CalculateCircleArea(double radius)
    {
        return Pi * radius * radius;
    }
}
```

> ⚠️ **نکته مهم**: کلاس‌های استاتیک **نمی‌توانند نمونه‌سازی شوند** (یعنی نمی‌توانید از آن‌ها شیء بسازید).

---

## ویژگی‌های کلاس‌های استاتیک

- ✅ فقط شامل **اعضای استاتیک** هستند.
- ❌ **قابل نمونه‌سازی نیستند** (نه می‌توانید از آن‌ها شیء بسازید، نه می‌توانید سازنده‌ی عمومی داشته باشند).
- 🚫 **نمی‌توانند از کلاس‌های دیگر ارث‌بری کنند** (inheritance).
- 🚫 **نمی‌توانند پایه‌ی ارث‌بری باشند** (یعنی نمی‌توان از آن‌ها ارث‌بری کرد).
- 🔒 **Thread-safe نیستند به‌صورت خودکار** (مگر اینکه خودتان آن را پیاده‌سازی کنید).
- 🧠 **در زمان بارگذاری برنامه (JIT)** مقداردهی اولیه می‌شوند.

---

## چرا فقط اعضای استاتیک؟

زبان C# به‌صورت **کامپایل‌تایم** (compile-time) این قانون را اجرا می‌کند:

> **"اگر یک کلاس استاتیک باشد، همه اعضای آن باید استاتیک باشند."**

دلیل این محدودیت منطقی است:

- چون **نمی‌توان از یک کلاس استاتیک شیء ساخت**، پس **اعضای غیراستاتیک معنی ندارند**.
- اعضای غیراستاتیک برای **شیءهای خاص** طراحی شده‌اند، نه برای کلاس به‌عنوان یک کل.

اگر سعی کنید عضوی غیراستاتیک در یک کلاس استاتیک تعریف کنید، کامپایلر خطای زیر را نمایش می‌دهد:

```
CS0708: 'MemberName': cannot declare instance members in a static class
```

---

## مثال‌های عملی

### مثال ۱: کلاس ابزاری برای رشته‌ها

```csharp
public static class StringHelper
{
    public static bool IsNullOrEmpty(string input)
    {
        return string.IsNullOrEmpty(input);
    }

    public static string Reverse(string input)
    {
        if (string.IsNullOrEmpty(input)) return input;
        return new string(input.Reverse().ToArray());
    }
}
```

### مثال ۲: استفاده از سازنده‌ی استاتیک

```csharp
public static class Config
{
    public static string AppName { get; private set; }
    public static int MaxRetries { get; private set; }

    // Static constructor
    static Config()
    {
        AppName = "MyApp";
        MaxRetries = 3;
    }
}
```

> 💡 سازنده‌ی استاتیک فقط یک‌بار در طول عمر برنامه اجرا می‌شود.

---

## کاربردهای رایج

- 🧮 **کتابخانه‌های ریاضی** (مثل `System.Math`)
- 🛠️ **کلاس‌های ابزاری** (Utility classes)
- ⚙️ **تنظیمات سراسری** (Global configuration)
- 📦 **Extension Methods** (روش‌های گسترش‌یافته — که باید در کلاس استاتیک تعریف شوند)
- 🔄 **کارخانه‌های ساده** (Simple factories) بدون نیاز به شیء

---

## محدودیت‌ها و نکات مهم

| مورد | توضیح |
|------|--------|
| **عدم امکان ارث‌بری** | نمی‌توانید از `static class` ارث‌بری کنید یا به آن ارث‌بری دهید. |
| **عدم پشتیبانی از رابط‌ها (Interfaces)** | کلاس‌های استاتیک نمی‌توانند `interface` را پیاده‌سازی کنند. |
| **عدم تست‌پذیری آسان** | به دلیل عدم امکان مانی‌پولیشن (mocking)، تست واحد (unit test) آن‌ها سخت‌تر است. |
| **حافظه‌ی استاتیک** | داده‌های استاتیک در طول عمر برنامه در حافظه باقی می‌مانند. |

> ✅ **راهکار برای تست‌پذیری**: استفاده از الگوی **Dependency Injection** یا تبدیل به کلاس غیراستاتیک با رابط.

---

## تفاوت با کلاس‌های غیراستاتیک

| ویژگی | کلاس استاتیک | کلاس معمولی |
|--------|---------------|--------------|
| نمونه‌سازی | ❌ خیر | ✅ بله |
| اعضای غیراستاتیک | ❌ مجاز نیست | ✅ مجاز است |
| ارث‌بری | ❌ خیر | ✅ بله |
| پیاده‌سازی رابط | ❌ خیر | ✅ بله |
| کاربرد | ابزارها، توابع عمومی | مدل‌های داده، رفتارهای شیءمحور |

---

## جمع‌بندی

کلاس‌های استاتیک در C# یک ابزار قدرتمند برای **گروه‌بندی توابع و داده‌هایی هستند که به شیء خاصی وابسته نیستند**. قانون «فقط اعضای استاتیک» نه یک محدودیت دلخواه، بلکه یک **نتیجه‌ی منطقی از طبیعت کلاس‌های استاتیک** است. استفاده‌ی صحیح از آن‌ها می‌تواند کد شما را تمیز‌تر، خوانا‌تر و کارآمدتر کند — به شرطی که در جای مناسب به‌کار روند.

---

## منابع معتبر

1. **Microsoft Learn – Static Classes and Static Class Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)

2. **C# Language Specification (ECMA-334)** – Section 15.2.7: Static classes  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

3. **C# in Depth – Jon Skeet** (Chapter on Static Members)  
   ISBN: 978-1617294532

4. **.NET Documentation – Static Constructors**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-constructors](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)

5. **Stack Overflow – When to Use Static Classes**  
   [https://stackoverflow.com/questions/241339/when-to-use-static-classes-in-c-sharp](https://stackoverflow.com/questions/241339/when-to-use-static-classes-in-c-sharp)
