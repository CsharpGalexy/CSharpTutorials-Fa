

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [کلاس `sealed`](#کلاس-sealed)  
   - [تعریف](#تعریف)  
   - [کاربرد و مزایا](#کاربرد-و-مزایا)  
   - [مثال کد](#مثال-کد)  
   - [محدودیت‌ها](#محدودیت‌ها)  
3. [کلاس `partial`](#کلاس-partial)  
   - [تعریف](#تعریف-1)  
   - [کاربرد و مزایا](#کاربرد-و-مزایا-1)  
   - [مثال کد](#مثال-کد-1)  
   - [قوانین مهم](#قوانین-مهم)  
4. [کلاس `static`](#کلاس-static)  
   - [تعریف](#تعریف-2)  
   - [کاربرد و مزایا](#کاربرد-و-مزایا-2)  
   - [مثال کد](#مثال-کد-2)  
   - [محدودیت‌ها](#محدودیت‌ها-1)  
5. [جمع‌بندی و مقایسه](#جمع‌بندی-و-مقایسه)  
6. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در زبان C#، علاوه بر کلاس‌های معمولی، سه نوع کلاس خاص وجود دارند که با کلمات کلیدی `sealed`، `partial` و `static` تعریف می‌شوند. هر کدام از این کلاس‌ها هدف و کاربرد خاصی دارند و در طراحی نرم‌افزارهای شیءگرا (OOP) نقش مهمی ایفا می‌کنند. درک صحیح این مفاهیم به بهبود کیفیت کد، قابلیت نگهداری و کارایی برنامه کمک می‌کند.

---

## کلاس `sealed`

### تعریف

کلاس `sealed` کلاسی است که **نمی‌توان از آن ارث‌بری کرد**. به عبارت دیگر، هیچ کلاسی نمی‌تواند از یک کلاس `sealed` مشتق شود.

### کاربرد و مزایا

- **جلوگیری از ارث‌بری ناخواسته**: در مواردی که می‌خواهید کلاس شما تغییرناپذیر یا نهایی باشد.
- **بهبود عملکرد**: کامپایلر می‌تواند برخی بهینه‌سازی‌ها را انجام دهد، چون می‌داند که متدی virtual نیست.
- **امنیت**: جلوگیری از تغییر رفتار کلاس توسط کلاس‌های فرزند.

### مثال کد

```csharp
sealed class Calculator
{
    public int Add(int a, int b) => a + b;
}

// این خط خطا می‌دهد:
// class ScientificCalculator : Calculator { } // ❌ خطای کامپایل
```

### محدودیت‌ها

- نمی‌توان از یک کلاس `sealed` ارث‌بری کرد.
- کلاس‌های `sealed` می‌توانند از یک کلاس پایه (غیر sealed) ارث‌بری کنند، اما خودشان قابل ارث‌بری نیستند.

---

## کلاس `partial`

### تعریف

کلاس `partial` اجازه می‌دهد تعریف یک کلاس در **چندین فایل** یا بخش مختلف از یک فایل تقسیم شود. در زمان کامپایل، تمام بخش‌ها به یک کلاس کامل ترکیب می‌شوند.

### کاربرد و مزایا

- **همکاری چندنفره**: چند توسعه‌دهنده می‌توانند روی بخش‌های مختلف یک کلاس کار کنند.
- **جداکردن منطق**: مثلاً یک بخش برای UI و یک بخش برای منطق کسب‌وکار.
- **استفاده در کدنویسی خودکار** (مثل Windows Forms یا Entity Framework): کامپایلر یا ابزارها کدی تولید می‌کنند که با کد دستی شما ترکیب می‌شود.

### مثال کد

**File1.cs**
```csharp
partial class Person
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

**File2.cs**
```csharp
partial class Person
{
    public string GetFullName() => $"{FirstName} {LastName}";
}
```

در زمان اجرا، کلاس `Person` هر دو خاصیت و متد را دارد.

### قوانین مهم

- تمام بخش‌ها باید کلمه کلیدی `partial` داشته باشند.
- تمام بخش‌ها باید در **یک اسمبلی** (project) باشند.
- اگر کلاس از یک پایه ارث‌بری می‌کند، فقط یکی از بخش‌ها نیاز به ذکر `: BaseClass` دارد.

---

## کلاس `static`

### تعریف

کلاس `static` کلاسی است که **نمی‌توان از آن نمونه (instance) ساخت** و فقط شامل **اعضای static** است. این کلاس‌ها به طور خودکار sealed هستند.

### کاربرد و مزایا

- **گروه‌بندی توابع کمکی** (Utility Methods): مثل `Math`, `Console`.
- **عدم نیاز به نمونه‌سازی**: دسترسی مستقیم از طریق نام کلاس.
- **صرفه‌جویی در حافظه**: چون نیازی به ایجاد شیء نیست.

### مثال کد

```csharp
static class MathUtils
{
    public static double Square(double x) => x * x;
    public static double PI = 3.14159;
}

// استفاده:
double result = MathUtils.Square(5); // بدون نیاز به new
```

### محدودیت‌ها

- نمی‌توان از کلاس `static` شیء ساخت (`new MathUtils()` ❌).
- نمی‌توان از آن ارث‌بری کرد (به طور ضمنی `sealed` است).
- فقط می‌تواند شامل اعضای `static` باشد (فیلد، متد، خاصیت و ...).
- نمی‌تواند constructor نمونه داشته باشد (اما می‌تواند static constructor داشته باشد).

---

## جمع‌بندی و مقایسه

| ویژگی            | `sealed`               | `partial`               | `static`                |
|-------------------|------------------------|--------------------------|--------------------------|
| امکان ارث‌بری     | ❌ خیر                 | ✅ بله                   | ❌ خیر (implicit sealed)|
| امکان نمونه‌سازی  | ✅ بله                 | ✅ بله                   | ❌ خیر                   |
| تقسیم در چند فایل| ❌ خیر                 | ✅ بله                   | ❌ خیر                   |
| فقط اعضای static | ❌ خیر                 | ❌ خیر                   | ✅ بله                   |
| کاربرد اصلی       | جلوگیری از ارث‌بری    | سازماندهی کد / کدنویسی خودکار | توابع کمکی / ابزاری |

---

## منابع معتبر

1. **Microsoft Learn – C# Programming Guide**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)

2. **Sealed Classes and Methods**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/sealed](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/sealed)

3. **Partial Classes and Methods**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)

4. **Static Classes and Static Class Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)

5. **C# Language Specification (ECMA-334)**  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

