

## فهرست مطالب

1. [مقدمه: Record چیست؟](#1-مقدمه-record-چیست)
2. [ویژگی‌های کلیدی Record Types](#2-ویژگی‌های-کلیدی-record-types)
   - [2.1. Immutability (ثابت‌بودن)](#21-immutability-ثابت‌بودن)
   - [2.2. Value-Based Equality (برابری مبتنی بر مقدار)](#22-value-based-equality-برابری-مبتنی-بر-مقدار)
   - [2.3. کلمه‌کلیدی `with`](#23-کلمه‌کلیدی-with)
3. [تفاوت Record با Class و Struct](#3-تفاوت-record-با-class-و-struct)
4. [نحوه تعریف Record](#4-نحوه-تعریف-record)
   - [4.1. Positional Syntax (سینتکس موقعیتی)](#41-positional-syntax-سینتکس-موقعیتی)
   - [4.2. Nominal Syntax (سینتکس نامی)](#42-nominal-syntax-سینتکس-نامی)
5. [محدودیت‌ها و نکات مهم](#5-محدودیت‌ها-و-نکات-مهم)
6. [استفاده‌های رایج و بهترین شیوه‌ها](#6-استفاده‌های-رایج-و-بهترین-شیوه‌ها)
7. [جمع‌بندی](#7-جمع‌بندی)
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه: Record چیست؟

در C# 9.0 (و بالاتر)، نوع جدیدی به نام **Record** معرفی شد که برای نمایش **داده‌های غیرقابل تغییر (immutable data)** طراحی شده است. Recordها معمولاً برای مدل‌سازی **مقادیر ساده** (مثل `Point`، `Address`، `Person`) استفاده می‌شوند که برابری آن‌ها باید بر اساس **محتوایشان** باشد، نه هویت شیء.

> ✅ **Record یک نوع مرجعی (reference type) است**، اما رفتاری شبیه به نوع مقداری (value type) دارد — یعنی برابری بر اساس مقدار است، نه آدرس حافظه.

---

## 2. ویژگی‌های کلیدی Record Types

### 2.1. Immutability (ثابت‌بودن)

به‌طور پیش‌فرض، Recordها **غیرقابل تغییر (immutable)** هستند. این یعنی پس از ایجاد یک نمونه از Record، نمی‌توان مقادیر فیلدها یا پراپرتی‌های آن را تغییر داد.

```csharp
public record Person(string FirstName, string LastName);

var person = new Person("Ali", "Rezaei");
// person.FirstName = "Ahmad"; // ❌ خطای کامپایل: set accessor ایجاد نمی‌شود
```

> 🔍 **نکته**: در سینتکس موقعیتی (Positional Syntax)، پراپرتی‌ها به‌صورت `init-only` تولید می‌شوند — یعنی فقط در زمان ساخت قابل تنظیم هستند.

اگر نیاز به تغییر دارید، باید از کلمه‌کلیدی `with` استفاده کنید (در بخش 2.3 توضیح داده شده).

---

### 2.2. Value-Based Equality (برابری مبتنی بر مقدار)

Recordها **برابری را بر اساس مقدار** (نه هویت) پیاده‌سازی می‌کنند. این یعنی دو نمونه از یک Record با مقادیر یکسان، با هم **برابر** در نظر گرفته می‌شوند.

```csharp
var p1 = new Person("Ali", "Rezaei");
var p2 = new Person("Ali", "Rezaei");

Console.WriteLine(p1 == p2); // True
Console.WriteLine(p1.Equals(p2)); // True
```

C# به‌طور خودکار متد‌های زیر را برای Recordها override می‌کند:
- `Equals(object)`
- `Equals(T)` (برای Recordهای جنریک)
- `GetHashCode()`
- عملگر `==` و `!=`

> ⚠️ **نکته**: این رفتار با کلاس‌های معمولی متفاوت است که برابری را بر اساس **هویت شیء** (آدرس حافظه) انجام می‌دهند.

---

### 2.3. کلمه‌کلیدی `with`

با استفاده از کلمه‌کلیدی `with` می‌توانید **یک کپی جدید** از یک Record ایجاد کنید و فقط برخی از مقادیر را تغییر دهید — بدون تغییر نمونهٔ اصلی.

```csharp
var person = new Person("Ali", "Rezaei");
var modifiedPerson = person with { FirstName = "Ahmad" };

Console.WriteLine(person.FirstName);      // Ali
Console.WriteLine(modifiedPerson.FirstName); // Ahmad
```

این ویژگی به‌ویژه در برنامه‌نویسی تابعی (Functional Programming) و مدیریت حالت (State Management) بسیار مفید است.

> 💡 **نحوه کار `with`**: کامپایلر یک کپی سطحی (shallow copy) از شیء ایجاد می‌کند و سپس مقادیر مشخص‌شده را جایگزین می‌کند.

---

## 3. تفاوت Record با Class و Struct

| ویژگی | Class | Struct | Record |
|-------|-------|--------|--------|
| نوع | Reference | Value | Reference |
| Immutability (پیش‌فرض) | ❌ | ❌ | ✅ |
| Value Equality | ❌ | ✅ | ✅ |
| پشتیبانی از `with` | ❌ | ❌ | ✅ |
| Override خودکار `Equals`/`GetHashCode` | ❌ | ✅ (اما ممکن است کارایی پایینی داشته باشد) | ✅ (بهینه‌شده) |
| مناسب برای داده‌های ساده | گاهی | بله | **بهترین گزینه** |

> 📌 **جمع‌بندی**: اگر نیاز به یک نوع داده‌ای ساده، غیرقابل تغییر و با برابری مبتنی بر مقدار دارید، **Record گزینه‌ی ایده‌آل** است.

---

## 4. نحوه تعریف Record

### 4.1. Positional Syntax (سینتکس موقعیتی)

ساده‌ترین راه برای تعریف Record. پارامترهای سازنده به‌صورت خودکار به پراپرتی‌های `init` تبدیل می‌شوند.

```csharp
public record Point(int X, int Y);
```

معادل دستی آن:

```csharp
public sealed class Point : IEquatable<Point>
{
    public int X { get; init; }
    public int Y { get; init; }

    public Point(int x, int y) => (X, Y) = (x, y);

    // Equals, GetHashCode, ==, !=, Deconstruct, ToString به‌صورت خودکار پیاده‌سازی می‌شوند
}
```

### 4.2. Nominal Syntax (سینتکس نامی)

اگر نیاز به کنترل بیشتر دارید (مثلاً منطق سفارشی در سازنده):

```csharp
public record Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

> 🔁 هر دو سبک قابل ترکیب هستند و هر دو از ویژگی‌های Record (مثل `with` و Value Equality) پشتیبانی می‌کنند.

---

## 5. محدودیت‌ها و نکات مهم

- Recordها **sealed** هستند (مگر اینکه از `abstract record` استفاده کنید).
- نمی‌توان از Record به‌عنوان پایه‌ی یک کلاس معمولی استفاده کرد.
- Recordها برای **داده‌های ساده و غیرفعال** طراحی شده‌اند — نه برای رفتارهای پیچیده.
- اگر Record حاوی فیلدهای mutable (مثل `List<T>`) باشد، **Immutability سطحی** است — یعنی لیست را نمی‌توان جایگزین کرد، اما محتوای آن قابل تغییر است.

```csharp
public record Student(string Name, List<string> Courses);

var s1 = new Student("Ali", new() { "Math" });
var s2 = s1 with { }; // کپی سطحی
s2.Courses.Add("Physics");
Console.WriteLine(s1.Courses.Count); // 2! چون لیست مشترک است
```

> ✅ **راه‌حل**: برای Immutability کامل، از `ImmutableList<T>` یا کپی عمیق استفاده کنید.

---

## 6. استفاده‌های رایج و بهترین شیوه‌ها

- **مدل‌های داده‌ای** (DTOs, Entities در DDD)
- **پیام‌ها در سیستم‌های مبتنی بر پیام** (Message Passing)
- **State در برنامه‌نویسی ری‌اکتیو** (مثل در Redux یا Flux)
- **جایگزینی Tupleها** برای خوانایی بیشتر

> 🎯 **بهترین شیوه**: از Record برای هر نوعی استفاده کنید که **هویت آن مهم نیست، بلکه محتوایش مهم است**.

---

## 7. جمع‌بندی

Record Types در C# 9+ یک ابزار قدرتمند برای مدل‌سازی داده‌های غیرقابل تغییر با برابری مبتنی بر مقدار هستند. با ویژگی‌هایی مانند:

- Immutability پیش‌فرض
- Value Equality خودکار
- پشتیبانی از `with` برای کپی‌برداری ایمن

Recordها کد شما را **خواناتر، ایمن‌تر و کم‌خطا‌تر** می‌کنند — به‌ویژه در سناریوهای مبتنی بر داده.

---

## 8. منابع معتبر

1. **مستندات رسمی Microsoft**  
   [Records - C# reference | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record)  
   *(شامل جزئیات سینتکس، رفتار و مثال‌های رسمی)*

2. **C# 9.0 Specification (GitHub - dotnet/csharplang)**  
   [Records Proposal](https://github.com/dotnet/csharplang/blob/main/proposals/csharp-9.0/records.md)  
   *(مستند فنی و کامل از طراحی Recordها)*

3. **Microsoft Docs: What's new in C# 9.0**  
   [What's new in C# 9.0](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-9)  
   *(بخش مربوط به Record در معرفی ویژگی‌های جدید)*

4. **Pluralsight / Microsoft Learn Tutorials**  
   [C# Record Types Deep Dive](https://learn.microsoft.com/en-us/shows/csharp-advanced-topics/record-types)  
   *(آموزش‌های ویدیویی و عملی)*

5. **C# in Depth by Jon Skeet (4th Edition)**  
   *(فصل مربوط به Record Types در ویرایش چهارم کتاب معتبر جان اسکیت)*

