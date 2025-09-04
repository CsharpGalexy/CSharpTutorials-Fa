# کلمه کلیدی `this` در C#  
**آخرین به‌روزرسانی: 11 جولای 2025**

کلمه کلیدی `this` یکی از قدرتمندترین و پرکاربردترین ابزارها در برنامه‌نویسی شیءگرا با C# است. این کلمه به **شیء فعلی (current instance)** کلاس اشاره می‌کند و در موقعیت‌های مختلفی برای نوشتن کد تمیز، خوانا و قابل نگهداری استفاده می‌شود.

## 📚 فهرست مطالب

1. [مقدمه](#کلمه-کلیدی-this-در-c)
2. [معنی `this` چیست؟](#معنی-this-چیست)
3. [کاربردهای رایج `this` در C#](#کاربردهای-رایج-this-در-c)
   - 3.1 [ارجاع به فیلدهای کلاس (حل تداخل نام)](#1-ارجاع-به-فیلدهای-کلاس-حل-تداخل-نام)
   - 3.2 [فراخوانی سازنده دیگر در همان کلاس (`this(...)`)](#2-فراخوانی-سازنده-دیگر-در-همان-کلاس-this)
   - 3.3 [فراخوانی متدهای دیگر کلاس](#3-فراخوانی-متدهای-دیگر-کلاس)
   - 3.4 [ارسال شیء جاری به عنوان پارامتر](#4-ارسال-شیء-جاری-به-عنوان-پارامتر)
   - 3.5 [تعریف ایندکسر با `this`](#5-this-indexer)
4. [کاربرد پیشرفته: متدهای اکستنشن (Extension Methods)](#کاربرد-پیشرفته-استفاده-در-متدهای-اکستنشن-extension-methods)
   - 4.1 [افزودن متد به نوع `int`](#مثال-افزودن-متد-iseven-به-نوع-int)
   - 4.2 [افزودن متد به `DateTime`](#مثال-افزودن-متد-topersiandate-به-datetime)
   - 4.3 [افزودن متد به `string`](#مثال-افزودن-متد-isvalidemail-به-string)
   - 4.4 [نکات مهم در مورد متدهای اکستنشن](#نکات-مهم-در-مورد-متدهای-اکستنشن)
5. [کاربردهای پیشرفته و الگوهای رایج](#کاربردهای-پیشرفته-و-الگوهای-رایج)
   - 5.1 [Method Chaining (فراخوانی زنجیروار)](#method-chaining-فراخوانی-زنجیروار-متدها)
6. [جمع‌بندی: خلاصه کاربردهای `this`](#جمعبندی-خلاصه-کاربردهای-this)
7. [نکات طلایی](#نکات-طلایی)
8. [توصیه عملی](#توصیه-عملی)



## معنی `this` چیست؟

وقتی یک شیء از یک کلاس ایجاد می‌کنید، تمام متدها و سازنده‌های آن کلاس در محدوده آن شیء اجرا می‌شوند.  
کلمه `this` دقیقاً به **همان شیء** اشاره دارد.

### مثال:
```csharp
public class Person
{
    public string Name;

    public void Print()
    {
        Console.WriteLine(this.Name); // 'this' به شیء فعلی اشاره دارد
    }
}

// استفاده
Person p = new Person();
p.Name = "علی";
p.Print(); // خروجی: علی
```

در اینجا، `this` معادل `p` است — چون `p` شیء جاری است.

---

## کاربردهای رایج `this` در C#

### 1. ارجاع به فیلدهای کلاس (حل تداخل نام)

وقتی نام یک پارامتر با نام یک فیلد یا خاصیت کلاس یکی باشد، از `this` برای تمایز بین آن‌ها استفاده می‌کنیم.

```csharp
public class Person
{
    public string Name;

    public void SetName(string Name)
    {
        this.Name = Name; // this.Name → فیلد کلاس | Name → پارامتر متد
    }
}
```

بدون `this`، کامپایلر نمی‌تواند تشخیص دهد که منظورتان کدام `Name` است.

---

### 2. فراخوانی سازنده دیگر در همان کلاس (`this(...)`)

با استفاده از `this()` می‌توانید یک سازنده دیگر از همان کلاس را فراخوانی کنید. این کار در **بازنویسی سازنده (constructor overloading)** بسیار مفید است.

```csharp
public class Geeks
{
    private string title;

    // سازنده اصلی
    public Geeks(string title)
    {
        this.title = title;
        Console.WriteLine($"عنوان تنظیم شد: {title}");
    }

    // سازنده بدون پارامتر که سازنده دیگر را صدا می‌زند
    public Geeks() : this("geeks")
    {
        Console.WriteLine("سازنده بدون پارامتر فراخوانی شد");
    }
}
```

```csharp
Geeks g1 = new Geeks();           // خروجی: عنوان تنظیم شد: geeks → سپس پیام دوم
Geeks g2 = new Geeks("برنامه‌نویس"); // فقط سازنده اصلی اجرا می‌شود
```

> **نکته**: فراخوانی `this(...)` باید در **اولین خط** سازنده باشد.

---

### 3. فراخوانی متدهای دیگر کلاس

می‌توانید از `this` برای صدا زدن دیگر متدهای همان کلاس استفاده کنید. هرچند اختیاری است، اما خوانایی کد را افزایش می‌دهد.

```csharp
public class Calculator
{
    public void Add(int a, int b)
    {
        this.ShowResult(a + b); // فراخوانی متد دیگر با this
    }

    private void ShowResult(int result)
    {
        Console.WriteLine("نتیجه: " + result);
    }
}
```

استفاده:
```csharp
Calculator calc = new Calculator();
calc.Add(3, 5); // خروجی: نتیجه: 8
```

---

### 4. ارسال شیء جاری به عنوان پارامتر

گاهی لازم است شیء فعلی را به یک متد خارجی یا داخلی بفرستید. در اینجا از `this` استفاده می‌کنیم.

```csharp
public class Person
{
    public string Name;

    public void Register()
    {
        PersonManager.Register(this); // ارسال شیء فعلی به یک کلاس دیگر
    }
}

public static class PersonManager
{
    public static void Register(Person person)
    {
        Console.WriteLine($"{person.Name} ثبت‌نام شد.");
    }
}
```

---
### 5 this indexer
### 5. تعریف ایندکسر با this (Indexer)

در کلاس‌هایی که می‌خواهند رفتاری شبیه آرایه داشته باشند، از `this` برای تعریف **ایندکسر** استفاده می‌شود.

```csharp
public class DaysCollection
{
    private string[] days = { "شنبه", "یکشنبه", "دوشنبه", "سه‌شنبه", "چهارشنبه", "پنجشنبه", "جمعه" };

    public string this[int index]
    {
        get 
        { 
            if (index < 0 || index >= days.Length)
                throw new IndexOutOfRangeException();
            return days[index]; 
        }
        set 
        { 
            if (index < 0 || index >= days.Length)
                throw new IndexOutOfRangeException();
            days[index] = value; 
        }
    }
}
```

استفاده:
```csharp
DaysCollection week = new DaysCollection();
Console.WriteLine(week[0]);     // خروجی: شنبه
week[1] = "یکشنبه تعطیل";       // تغییر مقدار
Console.WriteLine(week[1]);     // خروجی: یکشنبه تعطیل
```

---

## کاربرد پیشرفته: استفاده در متدهای اکستنشن (Extension Methods)

یکی از **قدرتمندترین کاربردهای `this`**، استفاده آن در **متدهای اکستنشن (Extension Methods)** است.  
این متدها به شما اجازه می‌دهند **روی انواع موجود (حتی کلاس‌های سیستمی)** متد جدید اضافه کنید، **بدون تغییر کد اصلی**.

### نحوه تعریف متد اکستنشن

- متد باید **static** باشد.
- کلاس حاوی متد باید **static** باشد.
- اولین پارامتر با کلمه کلیدی `this` نوشته می‌شود و نوع آن، نوعی است که می‌خواهید گسترش دهید.

### مثال: افزودن متد `IsEven` به نوع `int`

```csharp
public static class IntExtensions
{
    public static bool IsEven(this int number)
    {
        return number % 2 == 0;
    }
}
```

حالا می‌توانید این متد را **مثل یک متد عادی نمونه‌ای** فراخوانی کنید:

```csharp
int x = 10;
bool result = x.IsEven(); // استفاده زیبا و طبیعی
Console.WriteLine(result); // خروجی: True
```

---

### مثال: افزودن متد `ToPersianDate` به `DateTime`

```csharp
using System.Globalization;

public static class DateTimeExtensions
{
    public static string ToPersianDate(this DateTime date)
    {
        var pc = new PersianCalendar();
        return $"{pc.GetYear(date)}/{pc.GetMonth(date):00}/{pc.GetDayOfMonth(date):00}";
    }
}
```

استفاده:
```csharp
DateTime now = DateTime.Now;
Console.WriteLine(now.ToPersianDate()); // مثلاً: 1403/04/20
```

---

### مثال: افزودن متد `IsValidEmail` به `string`

```csharp
public static class StringExtensions
{
    public static bool IsValidEmail(this string email)
    {
        if (string.IsNullOrWhiteSpace(email))
            return false;

        try
        {
            var addr = new System.Net.Mail.MailAddress(email);
            return addr.Address == email;
        }
        catch
        {
            return false;
        }
    }
}
```

استفاده:
```csharp
string email = "user@example.com";
if (email.IsValidEmail())
{
    Console.WriteLine("ایمیل معتبر است.");
}
```

---

### نکات مهم در مورد متدهای اکستنشن

- فقط در کلاس‌های `static` تعریف می‌شوند
- اولین پارامتر با `this` مشخص می‌شود (نوع شیء هدف)
- فضای نام (namespace) آن‌ها باید با `using` وارد شود تا در IntelliSense نمایش داده شوند
- نمی‌توانند به اعضای `private` یا `protected` دسترسی داشته باشند
- اگر متدی با همان نام در خود نوع تعریف شود، **اولویت با متد اصلی** است

---

## کاربردهای پیشرفته و الگوهای رایج

### Method Chaining (فراخوانی زنجیروار متدها)

با بازگرداندن `this` از یک متد، می‌توانید چندین متد را پشت سر هم فراخوانی کنید.

```csharp
public class StringBuilder
{
    private string content = "";

    public StringBuilder Append(string text)
    {
        content += text;
        return this; // بازگرداندن شیء فعلی
    }

    public StringBuilder ToUpper()
    {
        content = content.ToUpper();
        return this;
    }

    public void Print() => Console.WriteLine(content);
}
```

استفاده:
```csharp
new StringBuilder()
    .Append("سلام ")
    .Append("دنیا!")
    .ToUpper()
    .Print(); // خروجی: سلام دنیا!
```

این الگو در کتابخانه‌هایی مثل **LINQ**، **Entity Framework** و **Fluent APIs** بسیار رایج است.

---

## جمع‌بندی: خلاصه کاربردهای `this`

| کاربرد | مثال |
|--------|-------|
| تمایز فیلد و پارامتر | `this.Name = Name;` |
| فراخوانی سازنده دیگر | `public Person() : this("ناشناس")` |
| فراخوانی متد دیگر | `this.Show();` |
| ارسال شیء به متد | `Service.Process(this);` |
| تعریف ایندکسر | `public string this[int i]` |
| Method Chaining | `return this;` |
| متدهای اکستنشن | `public static bool IsEven(this int n)` |

---

## نکات طلایی

- استفاده از `this` در متدهای instance **اختیاری** است، اما برای **خوانایی** و **شفافیت** کد توصیه می‌شود.
- در سازنده‌ها، `this()` باید در **اولین خط** باشد.
- در متدهای `static`، استفاده از `this` **فقط در متدهای اکستنشن** مجاز است.
- `this` فقط به **نمونه (instance)** کلاس اشاره دارد، نه خود کلاس.
- متدهای اکستنشن، قدرت شگفت‌انگیزی برای **گسترش کلاس‌های موجود بدون ویرایش آن‌ها** فراهم می‌کنند.

---

## توصیه عملی

اگر در تیم توسعه هستید، استفاده ثابت از `this` برای فیلدها و خاصیت‌ها (حتی بدون تداخل نام) می‌تواند به **یکدستی کد** کمک کند. مثلاً:

```csharp
public class User
{
    private string username;
    private int age;

    public void SetInfo(string username, int age)
    {
        this.username = username;
        this.age = age;
    }
}
```

این سبک، کد را خواناتر و قابل فهم‌تر می‌کند — به‌ویژه در کلاس‌های بزرگ.

---

با دانستن این مفاهیم، شما الان می‌توانید از `this` به‌صورت حرفه‌ای در پروژه‌های C# استفاده کنید و کدهای شیءگرای تمیز، قدرتمند و قابل گسترش بنویسید.

**مرحله بعدی**: برای یادگیری عمیق‌تر، الگوهای طراحی مانند **Builder Pattern** یا **Fluent Interface** را بررسی کنید — تمام آن‌ها از `this` برای ساخت APIهای زیبا استفاده می‌کنند.

---