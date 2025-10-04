

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [Extension Methods چیست؟](#extension-methods-چیست)  
3. [چرا Extension Methods به صورت Static تعریف می‌شوند؟](#چرا-extension-methods-به-صورت-static-تعریف-می%E2%80%8Cشوند)  
4. [چگونه یک Static Method را به Instance Method تبدیل کنیم؟](#چگونه-یک-static-method-را-به-instance-method-تبدیل-کنیم)  
5. [نکات مهم و محدودیت‌ها](#نکات-مهم-و-محدودیت‌ها)  
6. [مثال‌های کاربردی](#مثال‌های-کاربردی)  
7. [بهترین روش‌ها (Best Practices)](#بهترین-روش‌ها-best-practices)  
8. [جمع‌بندی](#جمع‌بندی)  
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در زبان برنامه‌نویسی C#، **Extension Methods** امکانی قدرتمند برای گسترش کارکرد نوع‌های موجود (مانند کلاس‌ها، اینترفیس‌ها و حتی نوع‌های اولیه مانند `int` یا `string`) بدون تغییر در کد اصلی آن‌ها فراهم می‌کنند. این روش‌ها از نظر فنی **متد استاتیک (Static Method)** هستند، اما به گونه‌ای طراحی شده‌اند که **مانند یک متد نمونه (Instance Method)** فراخوانی می‌شوند.

در این مستند، به‌طور کامل و با تمرکز بر مفاهیم پایه تا متوسط، نحوه تبدیل متدهای استاتیک به متدهای نمونه با استفاده از **Extension Methods** را بررسی می‌کنیم.

---

## Extension Methods چیست؟

**Extension Methods** متدهای استاتیکی هستند که به شما اجازه می‌دهند **بدون ارث‌بری یا تغییر در کد منبع**، عملکرد یک نوع را گسترش دهید. این متدها در یک **کلاس استاتیک** تعریف می‌شوند و اولین پارامتر آن‌ها با کلیدواژه `this` مشخص می‌شود که نوعی را که می‌خواهید گسترش دهید، نشان می‌دهد.

### ساختار کلی:

```csharp
public static class StringExtensions
{
    public static string ToTitleCase(this string input)
    {
        if (string.IsNullOrEmpty(input)) return input;
        return char.ToUpper(input[0]) + input.Substring(1).ToLower();
    }
}
```

حالا می‌توانید این متد را مانند یک متد نمونه فراخوانی کنید:

```csharp
string name = "john";
Console.WriteLine(name.ToTitleCase()); // خروجی: John
```

> 🔍 **نکته:** این متد در واقع یک متد استاتیک است، اما کامپایلر C# آن را به صورت هوشمندانه‌ای به فراخوانی متد نمونه تبدیل می‌کند.

---

## چرا Extension Methods به صورت Static تعریف می‌شوند؟

Extension Methods باید در یک **کلاس استاتیک** تعریف شوند و خود آن‌ها نیز **استاتیک** باشند. دلایل اصلی این موضوع عبارتند از:

1. **عدم نیاز به نمونه‌سازی:** چون Extension Methods برای گسترش نوع‌های موجود طراحی شده‌اند، نیازی به ایجاد نمونه از کلاس حاوی آن‌ها نیست.
2. **قابلیت کامپایل در زمان کامپایل:** کامپایلر C# در زمان کامپایل، فراخوانی‌های Extension Method را به فراخوانی‌های استاتیک تبدیل می‌کند.
3. **جلوگیری از تداخل با متدهای واقعی کلاس:** با این روش، اطمینان حاصل می‌شود که Extension Methodها با متدهای واقعی کلاس تداخل نداشته باشند.

---

## چگونه یک Static Method را به Instance Method تبدیل کنیم؟

برای تبدیل یک متد استاتیک به یک متد نمونه (از دید کاربر)، مراحل زیر را دنبال کنید:

### 1. یک کلاس استاتیک ایجاد کنید

```csharp
public static class MyExtensions
{
    // ...
}
```

### 2. یک متد استاتیک با کلیدواژه `this` برای اولین پارامتر بنویسید

```csharp
public static bool IsNullOrEmpty(this string str)
{
    return string.IsNullOrEmpty(str);
}
```

### 3. فضای نام (namespace) را در فایل مورد نظر `using` کنید

```csharp
using YourNamespace; // حاوی کلاس MyExtensions
```

### 4. متد را مانند یک متد نمونه فراخوانی کنید

```csharp
string text = null;
if (text.IsNullOrEmpty())
{
    Console.WriteLine("متن خالی است.");
}
```

> 💡 **نکته:** این متد در واقع `MyExtensions.IsNullOrEmpty(text)` فراخوانی می‌شود، اما سینتکس C# اجازه می‌دهد آن را به صورت `text.IsNullOrEmpty()` بنویسید.

---

## نکات مهم و محدودیت‌ها

- **Extension Methods فقط زمانی فراخوانی می‌شوند که فضای نام آن‌ها با `using` وارد شده باشد.**
- **اگر یک متد نمونه با همان نام و امضای Extension Method وجود داشته باشد، متد نمونه اولویت دارد.**
- **نمی‌توانید Extension Method برای فیلدها یا خاصیت‌ها تعریف کنید — فقط برای نوع‌ها.**
- **Extension Methods نمی‌توانند به اعضای خصوصی (private) نوع اصلی دسترسی داشته باشند.**
- **Extension Methods باید در کلاس‌های استاتیک تعریف شوند.**

---

## مثال‌های کاربردی

### مثال ۱: گسترش `int`

```csharp
public static class IntegerExtensions
{
    public static bool IsEven(this int number) => number % 2 == 0;
    public static bool IsOdd(this int number) => !number.IsEven();
}
```

استفاده:

```csharp
int num = 4;
Console.WriteLine(num.IsEven()); // True
```

### مثال ۲: گسترش `IEnumerable<T>`

```csharp
public static class EnumerableExtensions
{
    public static void ForEach<T>(this IEnumerable<T> source, Action<T> action)
    {
        foreach (var item in source)
            action(item);
    }
}
```

استفاده:

```csharp
var numbers = new List<int> { 1, 2, 3 };
numbers.ForEach(Console.WriteLine);
```

> ⚠️ توجه: LINQ از همین الگو برای افزودن متدهایی مانند `.Where()`, `.Select()` استفاده می‌کند.

---

## بهترین روش‌ها (Best Practices)

1. **Extension Methods را در فضای نام مناسب قرار دهید** تا از تداخل جلوگیری شود.
2. **از نام‌های معنی‌دار و منحصربه‌فرد استفاده کنید** تا با متدهای داخلی نوع تداخل نداشته باشند.
3. **Extension Methods را برای رابط‌ها (Interfaces) تعریف کنید** تا به تمام کلاس‌های پیاده‌سازی‌کننده آن رابط اعمال شود.
4. **از Extension Methods برای افزودن قابلیت‌های عمومی و مفید استفاده کنید**، نه برای جایگزینی طراحی مناسب کلاس.
5. **مستندسازی (XML Documentation) را فراموش نکنید**:

```csharp
/// <summary>
/// Checks if a string is null or empty.
/// </summary>
/// <param name="str">The string to check.</param>
/// <returns>True if null or empty; otherwise, false.</returns>
public static bool IsNullOrEmpty(this string str) => string.IsNullOrEmpty(str);
```

---

## جمع‌بندی

Extension Methods در C# یک ویژگی قدرتمند هستند که به شما اجازه می‌دهند **متدهای استاتیک را به شکلی که مانند متدهای نمونه به نظر برسند**، پیاده‌سازی کنید. این روش بدون نیاز به تغییر در کد منبع نوع اصلی، امکان گسترش عملکرد آن را فراهم می‌کند. با رعایت اصول بهترین روش‌ها، می‌توانید کدهای خوانا، قابل نگهداری و قابل استفاده مجددی بنویسید.

---

## منابع معتبر

1. **Microsoft Learn – Extension Methods (C# Programming Guide)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)

2. **C# Language Specification – Extension Methods**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#extension-methods](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#extension-methods)

3. **Jon Skeet – C# in Depth (4th Edition), Chapter 7: Extension Methods**  
   Manning Publications, ISBN: 978-1617294938

4. **Microsoft .NET Documentation – LINQ and Extension Methods**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/linq/](https://learn.microsoft.com/en-us/dotnet/csharp/linq/)

5. **C# 10.0 in a Nutshell by Joseph Albahari**  
   O’Reilly Media – Chapter on "Advanced C#"
