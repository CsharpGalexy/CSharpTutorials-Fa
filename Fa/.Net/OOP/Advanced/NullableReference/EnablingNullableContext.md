

## فهرست مطالب

1. [مقدمه: مشکل Null در سی‌شارپ](#1-مقدمه-مشکل-null-در-سیشارپ)  
2. [Nullable Reference Types چیست؟](#2-nullable-reference-types-چیست)  
3. [تفاوت Nullable Value Types و Nullable Reference Types](#3-تفاوت-nullable-value-types-و-nullable-reference-types)  
4. [Enabling Nullable Context چیست؟](#4-enabling-nullable-context-چیست)  
5. [روش‌های فعال‌سازی Nullable Context](#5-روش‌های-فعال‌سازی-nullable-context)  
   - [5.1. در سطح پروژه (Project-level)](#51-در-سطح-پروژه-project-level)  
   - [5.2. در سطح فایل (File-level)](#52-در-سطح-فایل-file-level)  
   - [5.3. در سطح بلاک کد (Code block-level)](#53-در-سطح-بلاک-کد-code-block-level)  
6. [چگونه با Nullable Context کار کنیم؟](#6-چگونه-با-nullable-context-کار-کنیم)  
7. [بهترین روش‌ها (Best Practices)](#7-بهترین-روش‌ها-best-practices)  
8. [جمع‌بندی](#8-جمع‌بندی)  
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه: مشکل Null در سی‌شارپ

در سی‌شارپ، یکی از متداول‌ترین خطاهای زمان اجرا (**Runtime Exception**)، `NullReferenceException` است. این خطا زمانی رخ می‌دهد که برنامه سعی کند به عضوی از یک شیء **null** دسترسی پیدا کند.

```csharp
string name = null;
Console.WriteLine(name.Length); // ⚠️ NullReferenceException!
```

تا قبل از نسخه **C# 8.0**، تمام انواع مرجع (Reference Types) مانند `string`، `object` و کلاس‌ها به‌صورت پیش‌فرض **قابل null بودند**، اما کامپایلر هیچ هشداری درباره احتمال null بودن آن‌ها نمی‌داد.

---

## 2. Nullable Reference Types چیست؟

در **C# 8.0**، مایکروسافت قابلیت جدیدی به نام **Nullable Reference Types** معرفی کرد که به‌صورت **استاتیک** (در زمان کامپایل) به شما کمک می‌کند تا از بروز `NullReferenceException` جلوگیری کنید.

این قابلیت **تفاوت معنایی** بین دو نوع زیر را معرفی می‌کند:

- `string`: یک رشته **غیر-null** (non-nullable reference type)
- `string?`: یک رشته **قابل null** (nullable reference type)

> ⚠️ توجه: این تفاوت **فقط در زمان کامپایل** بررسی می‌شود. در زمان اجرا، هر دو نوع یکسان هستند (مانند قبل از C# 8).

---

## 3. تفاوت Nullable Value Types و Nullable Reference Types

| نوع | مثال | قابل null به‌صورت پیش‌فرض؟ | نحوه تعریف nullable |
|-----|------|----------------------------|---------------------|
| **Value Type** | `int`, `bool`, `DateTime` | ❌ خیر | `int?`, `bool?` |
| **Reference Type** | `string`, `MyClass` | ✅ بله (در C# < 8) | `string?`, `MyClass?` (در C# ≥ 8 با فعال‌سازی) |

تفاوت کلیدی این است که **Nullable Value Types** از نسخه‌های اولیه سی‌شارپ وجود داشتند، اما **Nullable Reference Types** یک افزودنی هوشمند برای کاهش باگ‌های null هستند.

---

## 4. Enabling Nullable Context چیست؟

**Enabling Nullable Context** به معنای **فعال‌کردن حالت بررسی null برای انواع مرجع** در کد شماست. بدون فعال‌سازی این حالت، کامپایلر رفتار قدیمی خود را حفظ می‌کند (یعنی همه reference types به‌صورت پیش‌فرض nullable هستند و هیچ هشداری نمی‌دهد).

با فعال‌سازی این context، کامپایلر:

- هشدار می‌دهد اگر ممکن است یک متغیر غیر-null، مقدار null بگیرد.
- از شما می‌خواهد برای متغیرهایی که ممکن است null باشند، از `?` استفاده کنید.
- هوشمندانه‌تر با flow کد شما رفتار می‌کند (مثلاً بعد از یک چک `if (x != null)`، دیگر x را nullable نمی‌داند).

---

## 5. روش‌های فعال‌سازی Nullable Context

### 5.1. در سطح پروژه (Project-level)

معمول‌ترین و توصیه‌شده‌ترین روش، فعال‌سازی در فایل `.csproj` است:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable> <!-- ✅ فعال‌سازی -->
  </PropertyGroup>
</Project>
```

مقادیر ممکن برای `<Nullable>`:

- `enable` → Nullable context فعال است.
- `disable` → غیرفعال (پیش‌فرض در پروژه‌های قدیمی).
- `safeonly` → فقط هشدارهای ایمن را نشان بده (کمتر استفاده می‌شود).
- `annotations` → فقط از `?` پشتیبانی کن، ولی هشدار نده.

> 💡 توصیه: همیشه از `<Nullable>enable</Nullable>` استفاده کنید.

---

### 5.2. در سطح فایل (File-level)

اگر نمی‌خواهید کل پروژه را تحت تأثیر قرار دهید، می‌توانید در ابتدای یک فایل بنویسید:

```csharp
#nullable enable

string? name = null; // OK
string title = "Hello"; // non-nullable
title = null; // ⚠️ Warning: Cannot convert null to non-nullable string
```

سایر دستورات:

- `#nullable disable` → غیرفعال‌سازی در این فایل
- `#nullable restore` → بازگشت به حالت پیش‌فرض پروژه

---

### 5.3. در سطح بلاک کد (Code block-level)

می‌توانید nullable context را در بخش‌های خاصی از کد فعال یا غیرفعال کنید:

```csharp
#nullable disable
string oldStyle = null; // بدون هشدار
#nullable enable
string? modernStyle = null; // با هشدار کنترل‌شده
```

اما استفاده از این روش‌ها را **به‌ندرت** توصیه می‌کنند، چون خوانایی کد را کاهش می‌دهد.

---

## 6. چگونه با Nullable Context کار کنیم؟

### مثال ۱: تعریف متغیرهای nullable

```csharp
#nullable enable

public class Person
{
    public string FirstName { get; set; }        // ❌ نمی‌تواند null باشد
    public string? MiddleName { get; set; }      // ✅ می‌تواند null باشد
    public string LastName { get; set; }         // ❌ نمی‌تواند null باشد

    public Person(string firstName, string lastName)
    {
        FirstName = firstName ?? throw new ArgumentNullException(nameof(firstName));
        LastName = lastName ?? throw new ArgumentNullException(nameof(lastName));
        // MiddleName می‌تواند null بماند
    }
}
```

### مثال ۲: بررسی null قبل از استفاده

```csharp
void PrintName(Person p)
{
    if (p.MiddleName != null)
    {
        Console.WriteLine($"{p.FirstName} {p.MiddleName} {p.LastName}");
    }
    else
    {
        Console.WriteLine($"{p.FirstName} {p.LastName}");
    }
}
```

در اینجا، کامپایلر می‌داند که داخل `if`، `MiddleName` دیگر null نیست.

---

## 7. بهترین روش‌ها (Best Practices)

✅ **همیشه `<Nullable>enable</Nullable>` را در پروژه‌های جدید فعال کنید.**  
✅ **از `string?` فقط زمانی استفاده کنید که واقعاً null مجاز باشد.**  
✅ **در سازنده‌ها (Constructors)، مطمئن شوید مقادیر غیر-null مقداردهی می‌شوند.**  
✅ **از الگوی `?? throw` برای ورودی‌های null در پارامترهای غیر-null استفاده کنید.**  
❌ **از `#nullable disable` در کل پروژه استفاده نکنید — این بازگشت به عقب است!**

---

## 8. جمع‌بندی

- **Nullable Reference Types** یک ابزار قدرتمند برای جلوگیری از `NullReferenceException` است.
- برای استفاده از آن، باید **Nullable Context** را فعال کنید.
- فعال‌سازی در سطح پروژه (`<Nullable>enable</Nullable>`) بهترین روش است.
- این قابلیت **فقط در زمان کامپایل** تأثیرگذار است و تغییری در رفتار زمان اجرا ایجاد نمی‌کند.
- با رعایت بهترین روش‌ها، کدهای شما ایمن‌تر، خوانا‌تر و حرفه‌ای‌تر خواهند شد.

---

## 9. منابع معتبر

1. **Microsoft Docs – Nullable Reference Types**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references](https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references)

2. **Microsoft Docs – Nullable Context**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-options/language#nullable](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-options/language#nullable)

3. **C# 8.0 and .NET Core 3.0 – Official Book by Mark J. Price**  
   (فصل ۴: Using Nullable Reference Types)

4. **.NET Blog – Introducing Nullable Reference Types**  
   🔗 [https://devblogs.microsoft.com/dotnet/try-out-nullable-reference-types/](https://devblogs.microsoft.com/dotnet/try-out-nullable-reference-types/)

5. **C# Language Proposal (GitHub – dotnet/csharplang)**  
   🔗 [https://github.com/dotnet/csharplang/blob/main/proposals/nullable-reference-types.md](https://github.com/dotnet/csharplang/blob/main/proposals/nullable-reference-types.md)
