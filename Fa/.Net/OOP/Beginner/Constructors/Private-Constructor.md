## 📌 فهرست مطالب (به‌روزشده)

1. [مقدمه](#1-مقدمه)  
2. [تعریف Private Constructor](#2-تعریف-private-constructor)  
3. [چرا از Private Constructor استفاده کنیم؟](#3-چرا-از-private-constructor-استفاده-کنیم)  
4. [تفاوت با Public Constructor](#4-تفاوت-با-public-constructor)  
5. [الگوهای طراحی (Design Patterns)](#5-الگوهای-طراحی-design-patterns)  
6. [استفاده در Utility / Helper Classes](#6-utility--helper-classes)  
7. [کلاس‌های Static vs کلاس با Private Constructor](#7-کلاسهای-static-vs-کلاس-با-private-constructor)  
8. [Generic و Private Constructor](#8-generic-و-private-constructor)  
9. [Reflection و دسترسی به Private Constructor](#9-reflection-و-دسترسی-به-private-constructor)  
10. [محدودیت‌ها و اشتباهات رایج](#10-محدودیتها-و-اشتباهات-رایج)  
11. [سناریوهای پیشرفته](#11-سناریوهای-پیشرفته)  
12. [Private Constructor در Record و سریالایز کردن](#12-private-constructor-در-record-و-سریالایز-کردن) ← **اضافه شده**
12. [جمع‌بندی](#13-جمعبندی)  
13. [منابع](#14-منابع)  

---




---

# Private Constructor در زبان C#  
## 📘 راهنمای جامع از مقدماتی تا پیشرفته

> ✅ نسخهٔ نهایی — به‌روز با C# 12 و .NET 8  
> 🔐 شامل: الگوهای طراحی، Record Types، سریالایز کردن، Reflection، DI و سناریوهای واقعی

---

## 📌 فهرست مطالب

1. [مقدمه](#1-مقدمه)  
2. [تعریف Private Constructor](#2-تعریف-private-constructor)  
3. [چرا از Private Constructor استفاده کنیم؟](#3-چرا-از-private-constructor-استفاده-کنیم)  
4. [تفاوت با Public Constructor](#4-تفاوت-با-public-constructor)  
5. [الگوهای طراحی (Design Patterns)](#5-الگوهای-طراحی-design-patterns)  
6. [Utility / Helper Classes](#6-utility--helper-classes)  
7. [کلاس‌های Static vs کلاس با Private Constructor](#7-کلاس‌های-static-vs-کلاس-با-private-constructor)  
8. [Generic و Private Constructor](#8-generic-و-private-constructor)  
9. [Reflection و دسترسی به Private Constructor](#9-reflection-و-دسترسی-به-private-constructor)  
10. [محدودیت‌ها و اشتباهات رایج](#10-محدودیت‌ها-و-اشتباهات-رایج)  
11. [سناریوهای پیشرفته](#11-سناریوهای-پیشرفته)  
12. [Private Constructor در Record و سریالایز کردن](#12-private-constructor-در-record-و-سریالایز-کردن) ← **بخش جدید و تخصصی**  
13. [جمع‌بندی](#13-جمع‌بندی)  
14. [منابع](#14-منابع)

---

## 1. مقدمه

در C#، **Constructor** نقطهٔ شروع ایجاد شیء از یک کلاس است. دسترسی به آن با **Access Modifiers** کنترل می‌شود. وقتی یک سازنده را `private` تعریف می‌کنیم:

> 🔐 فقط کد داخل خود کلاس می‌تواند از آن استفاده کند.

این ویژگی، ابزار قدرتمندی برای **کنترل ایجاد شیء**، پیاده‌سازی **الگوهای طراحی** و افزایش **امنیت و انکپولاسیون** است.

این سند تمام جنبه‌های `Private Constructor` — از مفاهیم پایه تا سناریوهای پیشرفته شامل `record`ها، سریالایز کردن و DI — را پوشش می‌دهد.

---

## 2. تعریف Private Constructor

### نحو تعریف:
```csharp
public class MyClass
{
    private MyClass()
    {
        // کد اولیه‌سازی
    }
}
```

- فقط درون کلاس قابل فراخوانی است.
- از بیرون با `new` غیرقابل دسترسی است.
- می‌تواند همراه با سازنده‌های دیگر (مثل public) وجود داشته باشد.

### مثال ترکیبی:
```csharp
public class Person
{
    public string Name { get; set; }

    // فقط برای استفاده داخلی
    private Person() { }

    // تنها راه مجاز برای کاربران کلاس
    public Person(string name)
    {
        Name = name;
    }
}
```

✅ مجاز:
```csharp
var p = new Person("Ali");
```

❌ غیرمجاز:
```csharp
var p = new Person(); // ❌ خطای کامپایل
```

---

## 3. چرا از Private Constructor استفاده کنیم؟

| دلیل | توضیح |
|------|-------|
| 🔒 کنترل ایجاد شیء | جلوگیری از ساخت نمونه توسط کاربران کلاس |
| 🧩 پیاده‌سازی الگوهای طراحی | مثل Singleton، Factory، Builder |
| 🛠️ Utility Classes | کلاس‌هایی با متدهای استاتیک |
| 🔄 Initializer Methods | ایجاد متدهای استاتیک برای ساخت شیء (مثل `Create()`) |
| 📦 Encapsulation | مخفی کردن نحوه ساخت شیء از کاربر |

---

## 4. تفاوت با Public Constructor

| ویژگی | Private Constructor | Public Constructor |
|--------|----------------------|--------------------|
| ✅ دسترسی | فقط درون کلاس | از هرجا |
| ✅ `new` از بیرون | ❌ غیرممکن | ✅ ممکن |
| ✅ استفاده در الگوها | ✅ عالی | ⚠️ محدود |
| ✅ انعطاف در ایجاد شیء | ✅ با Factory Method | ❌ مستقیم |
| ✅ Thread-Safe Initialization | ✅ در Singleton | ❌ نیاز به مدیریت |

---

## 5. الگوهای طراحی (Design Patterns)

### ۱. Singleton Pattern

هدف: اطمینان از اینکه فقط **یک نمونه** از کلاس وجود دارد.

```csharp
public sealed class Logger
{
    private static readonly Logger _instance = new Logger();

    // جلوگیری از ساخت نمونه توسط بیرونی‌ها
    private Logger() { }

    public static Logger Instance => _instance;

    public void Log(string message)
    {
        Console.WriteLine($"[LOG] {message}");
    }
}
```

✅ استفاده:
```csharp
Logger.Instance.Log("App started.");
```

❌ امکان ایجاد نمونه جدید:
```csharp
var log = new Logger(); // ❌ خطای کامپایل
```

> 💡 نکته: استفاده از `sealed` برای جلوگیری از وراثت ضروری است.

---

### ۲. Factory Pattern

وقتی می‌خواهیم نحوه ایجاد شیء را کنترل کنیم:

```csharp
public class DatabaseConnection
{
    public string ConnectionString { get; private set; }

    private DatabaseConnection(string connStr)
    {
        ConnectionString = connStr;
    }

    // Factory Method
    public static DatabaseConnection Create(string type)
    {
        return type.ToLower() switch
        {
            "sql" => new DatabaseConnection("Server=.;Database=SQL"),
            "mongo" => new DatabaseConnection("mongodb://localhost"),
            _ => throw new ArgumentException("Invalid type")
        };
    }
}
```

✅ استفاده:
```csharp
var conn = DatabaseConnection.Create("sql");
Console.WriteLine(conn.ConnectionString);
```

---

## 6. Utility / Helper Classes

کلاس‌هایی که فقط شامل **متدهای استاتیک** هستند (مثل `Math`, `File`, `Path`) باید از ایجاد نمونه جلوگیری کنند.

### مثال: کلاس کمکی برای رشته‌ها

```csharp
public class StringUtils
{
    // جلوگیری از new StringUtils()
    private StringUtils() { }

    public static bool IsPalindrome(string text)
    {
        var cleaned = text.Replace(" ", "").ToLower();
        return cleaned == new string(cleaned.Reverse().ToArray());
    }

    public static string Reverse(string text) =>
        new string(text.Reverse().ToArray());
}
```

✅ استفاده:
```csharp
bool result = StringUtils.IsPalindrome("radar");
```

❌ غیرمجاز:
```csharp
var util = new StringUtils(); // ❌ خطای کامپایل
```

---

## 7. کلاس‌های Static vs کلاس با Private Constructor

| معیار | کلاس Static | کلاس با Private Constructor |
|-------|-------------|-------------------------------|
| 🔹 `static class` | ✅ | ❌ |
| 🔹 فقط اعضای استاتیک | ✅ | ❌ می‌تواند عضو نمونه‌ای داشته باشد |
| 🔹 قابل ارث‌بری | ❌ | ✅ (مگر `sealed`) |
| 🔹 قابل پیاده‌سازی Interface | ❌ | ✅ (اما اعضای استاتیک نه!) |
| 🔹 نمونه‌سازی | ❌ غیرممکن | ✅ فقط از طریق Factory |
| 🔹 Flexibility | پایین | بالا |

### وقتی از کدام استفاده کنیم؟

| سناریو | پیشنهاد |
|-------|--------|
| فقط متدهای استاتیک داری | ✅ `static class` |
| نیاز به ایجاد نمونه کنترل‌شده داری | ✅ کلاس با private ctor |
| می‌خواهی از Interface استفاده کنی | ✅ کلاس با private ctor |
| می‌خواهی کلاس را تست‌پذیر کنی | ✅ کلاس با private ctor + DI |

---

## 8. Generic و Private Constructor

وقتی با `Activator.CreateInstance<T>()` یا Reflection کار می‌کنید، وجود private constructor مهم می‌شود.

### مشکل:
```csharp
var obj = Activator.CreateInstance<MyClass>(); // ❌ اگر private ctor باشد، Exception!
```

### راه‌حل: استفاده از overload مناسب

```csharp
var obj = Activator.CreateInstance(typeof(MyClass), nonPublic: true);
```

> ⚠️ `nonPublic: true` اجازه می‌دهد سازنده‌های غیرعمومی فراخوانی شوند.

### مثال:

```csharp
public class Service
{
    private Service() => Console.WriteLine("Private ctor called.");
}

// در کد دیگر:
var svc = Activator.CreateInstance(typeof(Service), nonPublic: true);
// خروجی: Private ctor called.
```

> 💡 مفید در Dependency Injection Containerها.

---

## 9. Reflection و دسترسی به Private Constructor

با Reflection می‌توان به private constructor دسترسی داشت.

### مثال:

```csharp
var type = typeof(Logger);
var ctor = type.GetConstructor(
    BindingFlags.NonPublic | BindingFlags.Instance,
    null,
    Type.EmptyTypes,
    null);

if (ctor != null)
{
    var instance = ctor.Invoke(null); // ایجاد نمونه
    Console.WriteLine("Instance created via Reflection!");
}
```

> 🔐 این قابلیت ممکن است **امنیتی** را تهدید کند. در محیط‌های امنیتی بالا (مثل Sandboxing)، این کار مسدود می‌شود.

---

## 10. محدودیت‌ها و اشتباهات رایج

### ❌ ۱. فراموش کردن سازنده عمومی لازم

```csharp
public class User
{
    private User() { } // تنها سازنده
}

// new User() → ❌ خطای کامپایل
```

✅ راه‌حل: اضافه کردن سازنده public یا factory method.

---

### ❌ ۲. استفاده از static class وقتی نیاز به نمونه‌سازی داری

```csharp
static class Calculator
{
    public int State { get; set; } // ❌ خطای منطقی
}
```

✅ راه‌حل: از کلاس معمولی با private constructor استفاده کن.

---

### ❌ ۳. تصور اینکه private constructor کلاس را immutable می‌کند

```csharp
public class Config
{
    public string ApiKey { get; set; }
    private Config() { }
}
```

> ❌ این کلاس immutable نیست!

✅ برای immutable بودن، از `init` یا constructor با پارامتر استفاده کن.

---

## 11. سناریوهای پیشرفته

### ۱. کلاس‌های Nested با Private Constructor

```csharp
public class Outer
{
    private Outer() { }

    public class Inner
    {
        public string Data { get; set; }
    }
}

// استفاده:
var inner = new Outer.Inner(); // ✅ OK
// var outer = new Outer(); // ❌ Error
```

مفید در DSLها یا Builderها.

---

### ۲. Dependency Injection و Private Constructor

بعضی DI Containerها (مثل Autofac, Unity) از private constructor پشتیبانی می‌کنند:

```csharp
public class EmailService
{
    private EmailService() { } // فقط توسط Container فراخوانی شود

    public EmailService(IConfiguration config) { /* ... */ }
}
```

اما ASP.NET Core DI (IServiceProvider) فقط سازندهٔ public را می‌شناسد.

---

### ۳. Private Constructor در Enumerations Domain-Driven Design (DDD)

```csharp
public class OrderStatus
{
    public static readonly OrderStatus Pending = new OrderStatus(1, "Pending");
    public static readonly OrderStatus Shipped = new OrderStatus(2, "Shipped");

    public int Id { get; }
    public string Name { get; }

    private OrderStatus(int id, string name)
    {
        Id = id;
        Name = name;
    }
}
```

✅ مزایا:
- مقادیر ثابت
- عدم ایجاد نمونه دلخواه
- قابلیت تست و سریالایز

---

## 12. Private Constructor در Record و سریالایز کردن

### 📌 مقدمه

در C# 9+، `record`ها نوعی از کلاس‌های **immutable** و **value-based** هستند. به صورت پیش‌فرض یک سازندهٔ عمومی دارند، اما در عمل ممکن است نیاز به `private constructor` داشته باشیم — به خصوص برای **سریالایز کردن**.

---

### ✅ مثال: نیاز به private parameterless constructor

```csharp
public record Person(string FirstName, string LastName)
{
    // برای سازگاری با JsonSerializer در .NET 6-
    private Person() : this(null!, null!) { }

    public string FullName => $"{FirstName} {LastName}";
}
```

> ❗ بدون این، `System.Text.Json` در .NET 6 و پایین‌تر خطای زیر می‌دهد:
> ```
> Deserialization of types without a parameterless constructor is not supported.
> ```

---

### ⚠️ System.Text.Json و private constructor

- در **.NET 6 و پایین‌تر**: فقط سازنده‌های `public` پشتیبانی می‌شوند.
- `[JsonConstructor]` روی private constructor **کار نمی‌کند**.

#### ❌ این کد در .NET 6 شکست می‌خورد:

```csharp
[JsonConstructor]
private Person(string firstName, string lastName) 
    : this(firstName, lastName) { } // ignored
```

---

### ✅ راه‌حل در .NET 7+ با Source Generators

در **.NET 7 و بالاتر**، با `JsonSerializerContext` و source generator می‌توان private constructor را مدیریت کرد.

#### مثال:

```csharp
[JsonSerializable(typeof(Person))]
internal partial class AppJsonContext : JsonSerializerContext
{
}

// استفاده:
var json = """{"FirstName":"Ali","LastName":"Reza"}""";
var person = AppJsonContext.Default.Person.Deserialize(json);
```

✅ generator کد را تولید می‌کند و نیازی به سازندهٔ بدون پارامتر نیست — حتی اگر تمام سازنده‌ها private باشند!

---

### ✅ Newtonsoft.Json: پشتیبانی از private constructor

برخلاف `System.Text.Json`، **Newtonsoft.Json** به‌راحتی از سازنده‌های خصوصی پشتیبانی می‌کند.

#### ✅ مثال:

```csharp
public record Person(string FirstName, string LastName)
{
    [JsonConstructor]
    private Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

✅ استفاده:
```csharp
var person = JsonConvert.DeserializeObject<Person>(json);
```

✔️ موفقیت‌آمیز — حتی اگر سازنده private باشد.

---

### 🔁 مقایسه سریالایزرها

| ویژگی | Newtonsoft.Json | System.Text.Json (.NET 6) | System.Text.Json (.NET 7+) |
|-------|------------------|----------------------------|-----------------------------|
| ✅ private constructor | ✔️ با `[JsonConstructor]` | ❌ فقط public | ✔️ با Source Generator |
| ✅ نیاز به parameterless ctor | ❌ | ✔️ | ❌ با generator |
| ✅ performance | متوسط | بالا | بسیار بالا |
| ✅ customize عمیق | ✔️ | محدود | ✔️ با generator |

---

### ✅ بهترین روش‌ها

| سناریو | پیشنهاد |
|-------|--------|
| Newtonsoft.Json | ✅ استفاده از `[JsonConstructor]` روی private ctor |
| System.Text.Json (.NET 6) | ✅ اضافه کردن `private parameterless ctor` |
| System.Text.Json (.NET 7+) | ✅ استفاده از `JsonSerializerContext` |
| Immutable + Control | ✅ ترکیب `private ctor` + factory method |
| DI و Testing | ✅ از public ctor برای DI استفاده کن |

---

## 13. جمع‌بندی

| ویژگی | وضعیت |
|------|-------|
| ✅ ایجاد نمونه از بیرون | ❌ غیرممکن |
| ✅ کنترل ایجاد شیء | ✔️ عالی |
| ✅ پشتیبانی از Factory/Singleton | ✔️ |
| ✅ با Reflection قابل دور زدن | ⚠️ (در محیط‌های باز) |
| ✅ انعطاف بیشتر از static class | ✔️ |
| ✅ کاربرد در الگوهای طراحی | ✔️ |

### ✅ بهترین کاربردها:
- کلاس‌های Utility
- الگوی Singleton
- الگوی Factory
- Enumeration Classes (DDD)
- کلاس‌هایی با Initializerهای پیچیده

### ❌ زمانی که نباید استفاده کرد:
- وقتی نیاز به `new` آزاد داری
- وقتی تمام اعضا استاتیک هستند → از `static class` استفاده کن

---

## 14. منابع

- 📘 [Microsoft Docs - Constructors](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constructors)
- 📘 [C# Language Specification - Constructors](https://github.com/dotnet/csharplang/blob/main/spec/classes.md#constructors)
- 📚 *Head First Design Patterns* – فصل Singleton و Factory
- 📚 *Domain-Driven Design* by Eric Evans – Value Objects
- 🛠️ [SharpLab.io](https://sharplab.io) – بررسی IL و دسترسی به private members
- 📺 Pluralsight: *C# Best Practices*, *Design Patterns in C#*
- 📦 [System.Text.Json Documentation](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-source-generation)

---

## ✅ خلاصه نهایی (Cheat Sheet)

```csharp
public record Order(Guid Id, string Product, decimal Price)
{
    private Order() : this(Guid.Empty, null!, 0) { } // for JSON

    [JsonConstructor] // for Newtonsoft.Json
    private Order(Guid id, string product, decimal price)
        : this(id, product, price) { }

    public static Order Create(string product, decimal price)
    {
        if (string.IsNullOrWhiteSpace(product)) 
            throw new ArgumentException("Product is required.");
        return new Order(Guid.NewGuid(), product, price);
    }
}
```

> 🔥 نکات طلایی:
> - از `private constructor` برای **کنترل ایجاد شیء** استفاده کن.
> - در کلاس‌های helper، یا از `static class` استفاده کن یا private constructor داشته باش.
> - در Singleton، حتماً کلاس را `sealed` کن.
> - در recordها، با توجه به سریالایزر خود، از `parameterless ctor` یا `source generator` استفاده کن.

---
