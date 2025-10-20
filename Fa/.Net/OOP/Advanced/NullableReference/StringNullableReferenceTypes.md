
## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [چرا Nullable Reference Types معرفی شد؟](#چرا-nullable-reference-types-معرفی-شد؟)
3. [تفاوت `string` و `string?`](#تفاوت-string-و-string)
4. [چگونه Nullable Reference Types کار می‌کند؟](#چگونه-nullable-reference-types-کار-میکند؟)
5. [فعال‌سازی Nullable Reference Types](#فعالسازی-nullable-reference-types)
6. [مثال‌های کاربردی](#مثالهای-کاربردی)
7. [اپراتورهای کمکی](#اپراتورهای-کمکی)
8. [بهترین روش‌ها (Best Practices)](#بهترین-روشها-best-practices)
9. [جمع‌بندی](#جمعبندی)
10. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در زبان C#، **رشته‌ها (`string`)** یکی از پرکاربردترین **انواع مرجعی (Reference Types)** هستند. از آنجایی که انواع مرجعی می‌توانند مقدار `null` داشته باشند، اغلب با خطاهایی مانند **`NullReferenceException`** مواجه می‌شویم. برای حل این مشکل، مایکروسافت در **C# 8.0** (در سال 2019) ویژگی جدیدی به نام **Nullable Reference Types (NRT)** را معرفی کرد.

در این راهنما، به‌صورت مبتدی‌پسند و مستند، تفاوت بین `string` و `string?` را بررسی می‌کنیم و نحوه استفاده صحیح از آن‌ها را در پروژه‌های شما (به‌ویژه برای آموزش OOP) توضیح می‌دهیم.

---

## چرا Nullable Reference Types معرفی شد؟

قبل از C# 8.0، تمام انواع مرجعی (مثل `string`, `object`, `List<T>`) **به‌طور پیش‌فرض قابل null بودند**، اما کامپایلر هیچ هشداری در مورد استفاده از آن‌ها بدون بررسی `null` نمی‌داد. این امر منجر به **خطاهای زمان اجرا (Runtime Exceptions)** می‌شد.

هدف اصلی NRT:
- **جلوگیری از `NullReferenceException`**
- **افزایش ایمنی کد در زمان کامپایل**
- **مستندسازی صریحِ قابلیت null بودن متغیرها**

> 💡 NRT یک **ویژگی اختیاری** است و بر رفتار زمان اجرا (Runtime) تأثیری ندارد، بلکه فقط در زمان کامپایل هشدار می‌دهد.

---

## تفاوت `string` و `string?`

| نوع | معنی | مقدار پیش‌فرض | مجاز به `null`؟ |
|-----|------|----------------|------------------|
| `string` | رشته‌ای که **نباید** `null` باشد | باید به مقدار غیرنال مقداردهی شود | ❌ خیر (کامپایلر هشدار می‌دهد) |
| `string?` | رشته‌ای که **ممکن است** `null` باشد | `null` | ✅ بله |

> 🔍 توجه: در واقعیت، `string` همچنان می‌تواند `null` باشد (چون در Runtime تفاوتی ندارد)، اما کامپایلر اگر NRT فعال باشد، **هشدار** می‌دهد.

---

## چگونه Nullable Reference Types کار می‌کند؟

وقتی NRT فعال باشد:

- `string` = **غیرقابل null** (non-nullable reference type)
- `string?` = **قابل null** (nullable reference type)

کامپایلر:
- اگر متغیری از نوع `string` را بدون مقداردهی استفاده کنید → **هشدار CS8600**
- اگر مقدار `null` را به `string` نسبت دهید → **هشدار CS8601**
- اگر از `string?` بدون بررسی `null` استفاده کنید → **هشدار CS8602**

این هشدارها به شما کمک می‌کنند **قبل از اجرا** مشکل را پیدا کنید.

---

## فعال‌سازی Nullable Reference Types

در پروژه‌های جدید (.NET 6 به بعد)، NRT به‌صورت پیش‌فرض **فعال** است. اما اگر پروژه قدیمی دارید یا می‌خواهید مطمئن شوید:

### روش 1: در فایل `.csproj`
```xml
<PropertyGroup>
  <Nullable>enable</Nullable>
</PropertyGroup>
```

### روش 2: در هر فایل به‌صورت جداگانه
```csharp
#nullable enable
// کد شما
#nullable disable
```

---

## مثال‌های کاربردی

### مثال 1: تفاوت در اعلان
```csharp
#nullable enable

string name = "Ali";      // OK
string? maybeName = null; // OK

string invalid = null;    // ⚠️ هشدار: Cannot convert null to non-nullable reference type
```

### مثال 2: متد با پارامتر nullable
```csharp
public void Greet(string? name)
{
    if (name is null)
        Console.WriteLine("Hello, anonymous!");
    else
        Console.WriteLine($"Hello, {name}!");
}
```

### مثال 3: خطا در استفاده بدون بررسی
```csharp
string? input = GetInput(); // ممکن است null برگرداند
Console.WriteLine(input.Length); // ⚠️ هشدار: Dereference of a possibly null reference
```

راه‌حل ایمن:
```csharp
if (input != null)
    Console.WriteLine(input.Length);
// یا
Console.WriteLine(input?.Length ?? 0);
```

---

## اپراتورهای کمکی

### 1. **Null-forgiving operator (`!`)**
وقتی مطمئن هستید مقدار `null` نیست:
```csharp
string message = GetMessage()!; // به کامپایلر می‌گوییم: "این null نیست!"
```

> ⚠️ استفاده بیش از حد از `!` می‌تواند امنیت کد را کاهش دهد.

### 2. **Null-coalescing operator (`??`)**
```csharp
string displayName = userInput ?? "Guest";
```

### 3. **Null-conditional operator (`?.`)**
```csharp
int length = text?.Length ?? 0;
```

---

## بهترین روش‌ها (Best Practices)

1. **همیشه NRT را فعال کنید** (به‌ویژه در پروژه‌های جدید).
2. از `string?` فقط زمانی استفاده کنید که واقعاً ممکن است مقدار `null` داشته باشید.
3. قبل از دسترسی به عضوهای یک `string?`، آن را بررسی کنید.
4. در امضای متد، صراحتاً مشخص کنید کدام پارامترها می‌توانند `null` باشند.
5. از `!` فقط در مواردی استفاده کنید که **کاملاً مطمئن** هستید مقدار `null` نیست (مثلاً پس از اعتبارسنجی).

---

## جمع‌بندی

- `string` = رشته‌ای که **نباید** `null` باشد (در حالت ایده‌آل).
- `string?` = رشته‌ای که **ممکن است** `null` باشد.
- NRT یک **ابزار کامپایلری** برای جلوگیری از خطا است، نه تغییر در رفتار زمان اجرا.
- فعال‌سازی NRT کد شما را **خوانا‌تر، ایمن‌تر و مستندتر** می‌کند.
- برای آموزش OOP، استفاده از NRT نشان‌دهنده‌ی **کدنویسی مدرن و حرفه‌ای** است.

---

## منابع معتبر

1. **مستندات رسمی مایکروسافت (Microsoft Docs)**  
   📌 [Nullable reference types](https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references)  
   📌 [Nullable reference types overview](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types)

2. **C# Language Proposal (GitHub - dotnet/csharplang)**  
   📌 [Nullable Reference Types Specification](https://github.com/dotnet/csharplang/blob/main/proposals/csharp-8.0/nullable-reference-types.md)

3. **کتاب "C# 10 in a Nutshell" – Joseph Albahari**  
   فصل 4: "Creating Types in C#" و بخش Nullable Reference Types

4. **.NET Blog – مایکروسافت**  
   📌 [Introducing Nullable Reference Types in C#](https://devblogs.microsoft.com/dotnet/nullable-reference-types-in-csharp/)

5. **Pluralsight / Microsoft Learn**  
   دوره‌های رسمی آموزش C# 8+ و .NET 6+

---
