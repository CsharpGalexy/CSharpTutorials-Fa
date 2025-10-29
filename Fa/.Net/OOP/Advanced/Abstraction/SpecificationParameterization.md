### USER
---

### فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [ابستراکشن (Abstraction) چیست؟](#ابستراکشن-abstraction-چیست)  
3. [تخصصی‌سازی (Specialization) چیست؟](#تخصصی‌سازی-specialization-چیست)  
4. [Specification در چارچوب OOP](#specification-در-چارچوب-oop)  
5. [Parameterization در چارچوب OOP](#parameterization-در-چارچوب-oop)  
6. [تفاوت و ارتباط Specification و Parameterization](#تفاوت-و-ارتباط-specification-و-parameterization)  
7. [پیاده‌سازی در C#](#پیاده‌سازی-در-c)  
   - [7.1. Specification با استفاده از اینترفیس و کلاس‌های ابسترکت](#71-specification-با-استفاده-از-اینترفیس-و-کلاس‌های-ابسترکت)  
   - [7.2. Parameterization با Generics](#72-parameterization-با-generics)  
8. [جمع‌بندی](#جمع‌بندی)  
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیءگرا (OOP)، **ابستراکشن** (چکیده‌سازی) و **تخصصی‌سازی** (Specialization) دو مفهوم بنیادین هستند که به توسعه‌دهندگان کمک می‌کنند تا کدهایی انعطاف‌پذیر، قابل نگهداری و قابل استفاده مجدد بنویسند. دو مفهوم **Specification** (مشخصه‌سازی) و **Parameterization** (پارامتری‌سازی) در این زمینه نقش کلیدی ایفا می‌کنند.

در این سند، به‌صورت مستند و با استناد به منابع معتبر، این مفاهیم را در زمینه‌ی زبان C# توضیح می‌دهیم — از سطح مقدماتی تا متوسط — تا برای استفاده در یک ریپوزیتوری آموزشی OOP مناسب باشد.

---

## ابستراکشن (Abstraction) چیست؟

**ابستراکشن** به معنای پنهان کردن جزئیات پیاده‌سازی و نمایش فقط ویژگی‌ها یا رفتارهای ضروری یک شیء است. این مفهوم به ما اجازه می‌دهد تا با مدل‌های ساده‌تری از سیستم‌های پیچیده کار کنیم.

در C#، ابستراکشن معمولاً با استفاده از:
- **کلاس‌های ابسترکت** (`abstract class`)
- **اینترفیس‌ها** (`interface`)

پیاده‌سازی می‌شود.

> 🔹 **مثال ساده**: یک اینترفیس `IDrawable` فقط می‌گوید که "باید بتوانی رسم کنی"، اما چگونگی رسم را مشخص نمی‌کند.

---

## تخصصی‌سازی (Specialization) چیست؟

**تخصصی‌سازی** فرآیندی است که در آن یک کلاس فرزند، رفتار یا ویژگی‌های یک کلاس پدر را **گسترش** یا **تغییر** می‌دهد. این کار معمولاً از طریق **وراثت** (Inheritance) انجام می‌شود.

> 🔹 **مثال**: کلاس `Circle` یک تخصص از کلاس `Shape` است که نحوه‌ی محاسبه‌ی مساحت را به‌صورت خاص پیاده‌سازی می‌کند.

---

## Specification در چارچوب OOP

**Specification** به معنای **تعریف دقیق رفتار یا قراردادی** است که یک کلاس یا نوع باید رعایت کند — بدون اینکه نحوه‌ی پیاده‌سازی آن مشخص باشد.

در واقع، Specification **قرارداد رفتاری** را تعیین می‌کند، نه پیاده‌سازی.

### در C#:
- **اینترفیس‌ها** (`interface`) بهترین ابزار برای Specification هستند.
- **کلاس‌های ابسترکت** نیز می‌توانند هم Specification و هم پیاده‌سازی پیش‌فرض را فراهم کنند.

> ✅ **نکته**: Specification مربوط به "چه کاری باید انجام شود"، نه "چگونه انجام شود".

---

## Parameterization در چارچوب OOP

**Parameterization** (پارامتری‌سازی) به معنای طراحی کلاس‌ها یا متد‌هایی است که **انعطاف‌پذیری نوع داده‌ای** (type flexibility) داشته باشند. به عبارت دیگر، نوع داده‌ای که با آن کار می‌کنند، به‌صورت پارامتر تعیین می‌شود.

در C#، این مفهوم از طریق **Generics** پیاده‌سازی می‌شود.

> 🔹 **مثال**: `List<T>` یک کلاس پارامتری است که می‌تواند لیستی از هر نوعی (`int`, `string`, `Customer` و ...) باشد.

---

## تفاوت و ارتباط Specification و Parameterization

| ویژگی | Specification | Parameterization |
|--------|----------------|------------------|
| هدف | تعریف قرارداد رفتاری | ایجاد انعطاف‌پذیری نوع داده |
| ابزار در C# | `interface`, `abstract class` | `Generics` (`<T>`) |
| مربوط به | رفتار (Behavior) | نوع داده (Type) |
| مثال | `IEnumerable` | `List<T>` |
| ارتباط | می‌توانند ترکیب شوند: `IRepository<T>` |

> 💡 **ترکیب هوشمندانه**: می‌توان یک اینترفیس را هم **Specified** و هم **Parameterized** کرد:  
> ```csharp
> public interface IRepository<T>
> {
>     T GetById(int id);
>     void Add(T entity);
> }
> ```

---

## پیاده‌سازی در C#

### 7.1. Specification با استفاده از اینترفیس و کلاس‌های ابسترکت

```csharp
// Specification: فقط قرارداد رفتاری
public interface IDrawable
{
    void Draw();
}

// تخصصی‌سازی (Specialization) با پیاده‌سازی
public class Circle : IDrawable
{
    public void Draw() => Console.WriteLine("Drawing a circle");
}

public class Rectangle : IDrawable
{
    public void Draw() => Console.WriteLine("Drawing a rectangle");
}
```

### 7.2. Parameterization با Generics

```csharp
// Parameterization: انعطاف‌پذیری نوع
public class Box<T>
{
    public T Content { get; set; }

    public void Print() => Console.WriteLine($"Box contains: {Content}");
}

// استفاده
var stringBox = new Box<string> { Content = "Hello" };
var intBox = new Box<int> { Content = 42 };
```

### ترکیب Specification + Parameterization

```csharp
public interface IValidator<T>
{
    bool IsValid(T item);
}

public class EmailValidator : IValidator<string>
{
    public bool IsValid(string email) => email.Contains("@");
}
```

---

## جمع‌بندی

- **Specification** = تعریف "چه کاری باید انجام شود" → با `interface` یا `abstract class`.
- **Parameterization** = تعریف "با چه نوعی کار می‌کنیم" → با `Generics`.
- **Abstraction** از طریق Specification پیاده‌سازی می‌شود.
- **Specialization** زمانی رخ می‌دهد که یک کلاس Specification را پیاده‌سازی یا گسترش می‌دهد.
- در C#، ترکیب این مفاهیم منجر به کدهایی **قابل استفاده مجدد، قابل تست و انعطاف‌پذیر** می‌شود.

---

## منابع معتبر

1. **Microsoft Learn – C# Programming Guide**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)  
   - بخش‌های مربوط به: Interfaces, Abstract Classes, Generics

2. **"C# in Depth" by Jon Skeet** (4th Edition, Manning Publications)  
   - فصل‌های 3 (Types), 4 (Generics), و 6 (Delegates and Lambdas — برای پیشرفته‌ترها)

3. **"Object-Oriented Software Construction" by Bertrand Meyer**  
   - مفاهیم بنیادین Abstraction, Specification و Inheritance (اگرچه زبان خاصی ندارد، اما پایه‌های نظری را توضیح می‌دهد)

4. **ECMA-334: C# Language Specification**  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)  
   - بخش‌های 10 (Classes), 14 (Interfaces), 24 (Generics)

5. **Microsoft Docs – Generics (C#)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/generics](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/generics)

6. **"Design Patterns: Elements of Reusable Object-Oriented Software" by Gamma et al.**  
   - الگوهایی مانند Strategy و Template Method که از Specification و Specialization استفاده می‌کنند.

