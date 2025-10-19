

## 📚 فهرست مطالب

1. [مقدمه‌ای بر Nullable Reference Types](#1-مقدمهای-بر-nullable-reference-types)  
2. [چرا این قابلیت اضافه شد؟](#2-چرا-این-قابلیت-اضافه-شد)  
3. [فعال‌سازی Nullable Reference Types](#3-فعالسازی-nullable-reference-types)  
4. [تفاوت بین Reference Types معمولی و Nullable](#4-تفاوت-بین-reference-types-معمولی-و-nullable)  
5. [Best Practices](#5-best-practices)  
   - [5.1. همیشه از مقادیر غیر-null در سازنده‌ها استفاده کنید](#51-همیشه-از-مقادیر-غیر-null-در-سازندهها-استفاده-کنید)  
   - [5.2. از `?` برای نشان دادن قابلیت null بودن استفاده کنید](#52-از--برای-نشان-دادن-قابلیت-null-بودن-استفاده-کنید)  
   - [5.3. از `!` (Null-Forgiving Operator) با احتیاط استفاده کنید](#53-از--null-forgiving-operator-با-احتیاط-استفاده-کنید)  
   - [5.4. از `Debug.Assert` یا `Contract` برای تضمین عدم null بودن استفاده کنید](#54-از-debugassert-یا-contract-برای-تضمین-عدم-null-بودن-استفاده-کنید)  
   - [5.5. از `string.Empty` به جای `null` برای رشته‌ها استفاده کنید](#55-از-stringempty-به-جای-null-برای-رشتهها-استفاده-کنید)  
   - [5.6. از `ArgumentNullException` برای اعتبارسنجی ورودی‌ها استفاده کنید](#56-از-argumentnullexception-برای-اعتبارسنجی-ورودیها-استفاده-کنید)  
6. [نکات پیشرفته (اختیاری برای سطح متوسط)](#6-نکات-پیشرفته-اختیاری-برای-سطح-متوسط)  
7. [جمع‌بندی](#7-جمعبندی)  
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه‌ای بر Nullable Reference Types

در C# تا نسخه 7.3، تمام **Reference Types** (مثل `string`، `object`، کلاس‌ها) به‌طور پیش‌فرض **قابلیت null بودن** داشتند، اما این موضوع منجر به خطاهای رایجی مانند `NullReferenceException` می‌شد.  
از **C# 8.0** به بعد، مایکروسافت قابلیت **Nullable Reference Types (NRT)** را معرفی کرد تا توسعه‌دهندگان بتوانند در زمان کامپایل، خطاهای مربوط به null را شناسایی کنند.

> ⚠️ این قابلیت **تحلیل زمان کامپایل** است و روی رفتار زمان اجرا (Runtime) تأثیری ندارد.

---

## 2. چرا این قابلیت اضافه شد؟

- جلوگیری از **NullReferenceException** که یکی از رایج‌ترین خطاها در برنامه‌های .NET است.
- بهبود **قابلیت خواندن کد** و **مستندسازی ضمنی**: وقتی یک متغیر `string?` است، مشخص است که می‌تواند null باشد.
- کاهش نیاز به بررسی‌های دستی `if (x != null)` در کل کد.

---

## 3. فعال‌سازی Nullable Reference Types

در فایل `.csproj` پروژه خود، این تنظیم را اضافه کنید:

```xml
<PropertyGroup>
  <Nullable>enable</Nullable>
</PropertyGroup>
```

یا در هر فایل به‌صورت جداگانه:

```csharp
#nullable enable
```

> 🔔 بدون این فعال‌سازی، C# رفتار قدیمی (همه reference types nullable) را دنبال می‌کند.

---

## 4. تفاوت بین Reference Types معمولی و Nullable

| نوع | مثال | معنی |
|------|------|------|
| غیر-null | `string name` | هرگز نباید null باشد (در صورت null بودن، هشدار کامپایلر داده می‌شود) |
| nullable | `string? name` | می‌تواند null باشد |

---

## 5. Best Practices

### 5.1. همیشه از مقادیر غیر-null در سازنده‌ها استفاده کنید

اگر یک فیلد نباید null باشد، آن را در سازنده مقداردهی کنید:

```csharp
public class Person
{
    public string Name { get; }

    public Person(string name)
    {
        Name = name ?? throw new ArgumentNullException(nameof(name));
    }
}
```

✅ این کار تضمین می‌کند که `Name` همیشه مقدار معتبری دارد.

---

### 5.2. از `?` برای نشان دادن قابلیت null بودن استفاده کنید

اگر یک متغیر ممکن است null باشد، حتماً از `?` استفاده کنید:

```csharp
public string? MiddleName { get; set; }
```

این کار به خواننده کد (و کامپایلر) می‌گوید: «این مقدار ممکن است null باشد.»

---

### 5.3. از `!` (Null-Forgiving Operator) با احتیاط استفاده کنید

گاهی می‌دانید که یک مقدار null نیست، اما کامپایلر نمی‌داند. در این صورت می‌توانید از `!` استفاده کنید:

```csharp
string? input = GetInput();
string validInput = input!; // فقط اگر مطمئن باشید!
```

⚠️ **هشدار**: سوءاستفاده از `!` می‌تواند منجر به `NullReferenceException` شود. فقط در مواردی استفاده کنید که **قطعی** می‌دانید مقدار null نیست (مثلاً پس از بررسی).

---

### 5.4. از `Debug.Assert` یا `Contract` برای تضمین عدم null بودن استفاده کنید

در متد‌های کمکی یا private، می‌توانید از `Debug.Assert` برای مستند کردن فرضیات استفاده کنید:

```csharp
public void Process(string input)
{
    Debug.Assert(input != null);
    // ... rest of code
}
```

این کار به کامپایلر کمک می‌کند تا بداند `input` در ادامه null نیست.

---

### 5.5. از `string.Empty` به جای `null` برای رشته‌ها استفاده کنید

اگر یک رشته می‌تواند "خالی" باشد، اما نباید null باشد، از `string.Empty` استفاده کنید:

```csharp
public string DisplayName => FirstName ?? string.Empty;
```

یا بهتر است در سازنده مقدار پیش‌فرض بدهید.

---

### 5.6. از `ArgumentNullException` برای اعتبارسنجی ورودی‌ها استفاده کنید

در متد‌های public، همیشه ورودی‌های nullable را بررسی کنید:

```csharp
public void SetName(string name)
{
    if (name is null)
        throw new ArgumentNullException(nameof(name));
    _name = name;
}
```

این کار هم برای ایمنی و هم برای مستندسازی مفید است.

---

## 6. نکات پیشرفته (اختیاری برای سطح متوسط)

- استفاده از **Attributes** مانند `[NotNullWhen(true)]` برای توابعی که null بودن را بر اساس خروجی تعیین می‌کنند.
- استفاده از `#nullable disable` در بخش‌های قدیمی کد برای انتقال تدریجی.
- ترکیب NRT با **Pattern Matching** برای بررسی هوشمند null.

> این بخش‌ها را می‌توانید در آینده به عنوان «پیشرفته» به ریپوزیتوری اضافه کنید.

---

## 7. جمع‌بندی

- **Nullable Reference Types** یک ابزار قدرتمند برای جلوگیری از خطا در زمان کامپایل است.
- همیشه مشخص کنید کدام متغیرها می‌توانند null باشند (`?`) و کدام‌ها نه.
- از `!` فقط در موارد ضروری و با دلیل استفاده کنید.
- ورودی‌های public را همیشه اعتبارسنجی کنید.
- این قابلیت با فلسفه **Fail Fast** و **Defensive Programming** هماهنگ است.

---

## 8. منابع معتبر

1. **Microsoft Docs – Nullable Reference Types**  
   🔗 https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references

2. **Microsoft C# Guide – Nullable Reference Types Best Practices**  
   🔗 https://learn.microsoft.com/en-us/dotnet/csharp/nullable-best-practices

3. **C# 8.0 and .NET Core 3.0 – Mark J. Price (کتاب)**  
   📘 فصل 8: Advanced C# Features

4. **.NET Blog – Introducing Nullable Reference Types**  
   🔗 https://devblogs.microsoft.com/dotnet/try-out-nullable-reference-types/

5. **C# Language Proposal (GitHub)**  
   🔗 https://github.com/dotnet/csharplang/blob/main/proposals/nullable-reference-types.md

