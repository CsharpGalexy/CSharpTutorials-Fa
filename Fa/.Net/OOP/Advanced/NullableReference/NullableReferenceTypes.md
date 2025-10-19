
## فهرست مطالب

1. [مقدمه: مشکل NullReferenceException](#1-مقدمه-مشکل-nullreferenceexception)
2. [Nullable Reference Types چیست؟](#2-nullable-reference-types-چیست)
3. [تفاوت با Nullable Value Types](#3-تفاوت-با-nullable-value-types)
4. [فعال‌سازی Nullable Reference Types](#4-فعالسازی-nullable-reference-types)
5. [نحوه استفاده در کد](#5-نحوه-استفاده-در-کد)
6. [عملگرهای کمکی](#6-عملگرهای-کمکی)
7. [Attributes برای راهنمایی کامپایلر](#7-attributes-برای-راهنمایی-کامپایلر)
8. [بهترین روش‌ها (Best Practices)](#8-بهترین-روشها-best-practices)
9. [محدودیت‌ها و نکات مهم](#9-محدودیتها-و-نکات-مهم)
10. [جمع‌بندی](#10-جمعبندی)
11. [منابع معتبر](#11-منابع-معتبر)

---

## 1. مقدمه: مشکل `NullReferenceException`

در C#، **تمامی Reference Types** (مانند `string`، `object`، کلاس‌ها و ...) به‌طور پیش‌فرض **قابل ارجاع به `null`** هستند. این موضوع باعث بروز خطاهای رایجی مانند:

```csharp
string name = null;
Console.WriteLine(name.Length); // ⚠️ NullReferenceException در زمان اجرا!
```

این خطا فقط در **زمان اجرا (Runtime)** شناسایی می‌شود و کامپایلر قادر به تشخیص آن نیست. برای حل این مشکل، مایکروسافت در **C# 8.0** ویژگی **Nullable Reference Types** را معرفی کرد.

---

## 2. Nullable Reference Types چیست؟

**Nullable Reference Types** یک ویژگی **کامپایلری** است که به شما کمک می‌کند تا:

- متغیرهایی که **ممکن است `null` باشند** را به‌صورت صریح مشخص کنید.
- کامپایلر **هشدار (Warning)** بدهد اگر از یک متغیر قابل‌نال بدون بررسی استفاده کنید.

این ویژگی **خطای زمان اجرا را به هشدار زمان کامپایل تبدیل می‌کند**.

> 🔹 توجه: این ویژگی **فقط در زمان کامپایل** کار می‌کند و تأثیری در رفتار زمان اجرا ندارد.

---

## 3. تفاوت با Nullable Value Types

| نوع داده | نحوه تعریف | پیش‌فرض |
|----------|------------|--------|
| **Value Type** (مثل `int`) | `int? x = null;` | غیرقابل‌نال (`int` نمی‌تواند `null` باشد) |
| **Reference Type** (مثل `string`) | `string? name = null;` | قابل‌نال (اما با فعال‌سازی NRT، پیش‌فرض غیرقابل‌نال می‌شود) |

در C# 8.0+ با فعال‌کردن **Nullable Reference Types**، تمامی Reference Types به‌صورت پیش‌فرض **غیرقابل‌نال** در نظر گرفته می‌شوند — مگر اینکه صریحاً با `?` مشخص شوند.

---

## 4. فعال‌سازی Nullable Reference Types

این ویژگی **به‌صورت پیش‌فرض غیرفعال** است. برای فعال‌سازی آن، در فایل `.csproj` پروژه خود این خط را اضافه کنید:

```xml
<PropertyGroup>
  <Nullable>enable</Nullable>
</PropertyGroup>
```

یا اگر از Visual Studio استفاده می‌کنید:
- راست‌کلیک روی پروژه → **Properties** → **Build** → تیک گزینه **Nullable reference types**.

همچنین می‌توانید به‌صورت موقت در یک فایل خاص با دستور زیر فعال یا غیرفعال کنید:

```csharp
#nullable enable
// کد شما
#nullable disable
```

---

## 5. نحوه استفاده در کد

### ✅ تعریف متغیرهای غیرقابل‌نال (پیش‌فرض)

```csharp
string name = "Ali"; // OK — name نمی‌تواند null باشد
name = null; // ⚠️ هشدار کامپایلر: Cannot convert null to non-nullable reference
```

### ✅ تعریف متغیرهای قابل‌نال

```csharp
string? maybeName = null; // OK — مجاز است
Console.WriteLine(maybeName.Length); // ⚠️ هشدار: maybeName ممکن است null باشد
```

### ✅ بررسی قبل از استفاده

```csharp
if (maybeName != null)
{
    Console.WriteLine(maybeName.Length); // OK — کامپایلر می‌داند null نیست
}
```

یا با عملگر `?.`:

```csharp
int length = maybeName?.Length ?? 0;
```

---

## 6. عملگرهای کمکی

### 1. **Null-forgiving operator (`!`)**

وقتی مطمئن هستید یک متغیر `null` نیست، اما کامپایلر هشدار می‌دهد:

```csharp
string? input = GetInput();
string message = input!; // "من می‌دانم input null نیست!"
```

> ⚠️ استفاده بیش‌ازحد از `!` می‌تواند باعث بازگشت `NullReferenceException` شود.

### 2. **Null-coalescing operator (`??`)**

```csharp
string displayName = name ?? "Guest";
```

### 3. **Null-conditional operator (`?.`)**

```csharp
int? length = name?.Length;
```

---

## 7. Attributes برای راهنمایی کامپایلر

در متد‌ها، می‌توانید از **Attributeهای خاصی** استفاده کنید تا رفتار `null` را به کامپایلر اطلاع دهید:

| Attribute | کاربرد |
|----------|--------|
| `[NotNull]` | پارامتر/پراپرتی هرگز `null` نخواهد بود (حتی اگر نوع آن `T?` باشد) |
| `[MaybeNull]` | مقدار بازگشتی ممکن است `null` باشد (حتی اگر نوع بازگشتی `T` باشد) |
| `[AllowNull]` | پارامتر می‌تواند `null` باشد، اما بعد از آن `null` نخواهد بود |
| `[DoesNotReturnIf(true)]` | اگر شرط `true` باشد، متد برنمی‌گردد (مثلاً `throw`) |

مثال:

```csharp
public static void Validate([NotNull] string? input)
{
    if (input is null) throw new ArgumentNullException(nameof(input));
    // بعد از این خط، input به‌عنوان non-nullable در نظر گرفته می‌شود
}
```

---

## 8. بهترین روش‌ها (Best Practices)

✅ **فعال کردن NRT در تمام پروژه‌های جدید**  
✅ **هرگز از `!` بدون دلیل استفاده نکنید**  
✅ **همیشه قبل از دسترسی به متغیرهای `T?`، آن‌ها را بررسی کنید**  
✅ **از `??` و `?.` برای کد ایمن‌تر استفاده کنید**  
✅ **در APIهای عمومی، نوع بازگشتی و پارامترها را به‌دقت مشخص کنید**

---

## 9. محدودیت‌ها و نکات مهم

- **Nullable Reference Types فقط یک ویژگی کامپایلری است** — در زمان اجرا هیچ تفاوتی با کد قبلی ندارد.
- **کتابخانه‌های قدیمی** (که NRT را پشتیبانی نمی‌کنند) ممکن است هشدار دهند. برای این موارد می‌توانید از `#nullable disable` یا `!` استفاده کنید.
- **آرایه‌ها و کالکشن‌ها**: اگر `string[]` داشته باشید، عناصر داخل آن ممکن است `null` باشند، حتی اگر آرایه غیرقابل‌نال باشد.

---

## 10. جمع‌بندی

ویژگی **Nullable Reference Types** یکی از مهم‌ترین بهبودهای ایمنی در C# است که:

- خطاهای `NullReferenceException` را به **هشدارهای کامپایلری** تبدیل می‌کند.
- کد شما را **خوانا‌تر، قابل‌اعتماد‌تر و حرفه‌ای‌تر** می‌کند.
- با ترکیب آن با عملگرهای `?.` و `??`، می‌توانید کدهایی **کاملاً مقاوم در برابر null** بنویسید.

این ویژگی به‌ویژه در پروژه‌های **OOP** که از شیء‌گرایی و ارجاع‌ها استفاده می‌کنند، بسیار حیاتی است.

---

## 11. منابع معتبر

1. **مستندات رسمی مایکروسافت (Microsoft Learn)**  
   🔗 [Nullable reference types](https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references)  
   🔗 [Nullable reference types overview](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types)

2. **C# Language Design (GitHub - dotnet/csharplang)**  
   🔗 [Proposal: Nullable Reference Types](https://github.com/dotnet/csharplang/blob/main/proposals/nullable-reference-types.md)

3. **Microsoft C# 8.0 and .NET Core 3.0 – Modern Cross-Platform Development (کتاب معتبر)**  
   - فصل 4: Working with Nullable Reference Types

4. **Channel 9 – Microsoft Developer Videos**  
   🔗 [Understanding Nullable Reference Types](https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Nullable-Reference-Types)

5. **C# 8.0 in a Nutshell (O'Reilly)**  
   - بخش: Nullable Reference Types
