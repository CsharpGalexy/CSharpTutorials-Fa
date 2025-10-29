

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [Procedural Abstraction چیست؟](#procedural-abstraction-چیست)  
3. [انواع ارجاعی در C#](#انواع-ارجاعی-در-c)  
4. [قابلیت ابستراکشن در انواع ارجاعی](#قابلیت-ابستراکشن-در-انواع-ارجاعی)  
5. [تخصصی‌سازی (Specialization) در انواع ارجاعی](#تخصصیسازی-specialization-در-انواع-ارجاعی)  
6. [کاربرد Procedural Abstraction در انواع ارجاعی](#کاربرد-procedural-abstraction-در-انواع-ارجاعی)  
7. [مثال‌های کاربردی در C#](#مثالهای-کاربردی-در-c)  
8. [جمع‌بندی](#جمعبندی)  
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیءگرا (OOP)، **ابستراکشن** (چکیده‌سازی) یکی از اصول چهارگانه است که به ما اجازه می‌دهد جزئیات پیچیده را پنهان کرده و فقط رابط‌های لازم را در اختیار کاربر قرار دهیم. در این متن، به‌طور خاص به **Procedural Abstraction** (ابستراکشن رویه‌ای) و نحوه پیاده‌سازی آن در زبان **C#** با تمرکز بر **انواع ارجاعی** (Reference Types) می‌پردازیم. هدف، آموزش مفاهیم از سطح مقدماتی تا متوسط برای توسعه‌دهندگانی است که در حال یادگیری OOP هستند.

---

## Procedural Abstraction چیست؟

**Procedural Abstraction** به معنای پنهان‌کردن جزئیات پیاده‌سازی یک رفتار (تابع یا متد) و ارائه یک رابط ساده برای استفاده از آن است. به عبارت دیگر، شما فقط **چه کاری انجام می‌شود** را می‌دانید، نه **چگونه انجام می‌شود**.

در C#، این مفهوم معمولاً با استفاده از:

- متدها (Methods)
- رابط‌ها (Interfaces)
- کلاس‌های چکیده (Abstract Classes)

پیاده‌سازی می‌شود.

> 🔍 **نکته**: Procedural Abstraction در مقابل **Data Abstraction** قرار می‌گیرد که روی داده‌ها و ساختارهای داده متمرکز است.

---

## انواع ارجاعی در C#

در C# دو دسته نوع داده وجود دارد:

1. **انواع مقداری** (Value Types): مثل `int`, `bool`, `struct`
2. **انواع ارجاعی** (Reference Types): مثل `class`, `interface`, `delegate`, `array`

در این متن، فقط به **انواع ارجاعی** می‌پردازیم، چون:

- فقط آن‌ها قابلیت **وراثت** (Inheritance) و **چندریختی** (Polymorphism) را دارند.
- این دو ویژگی برای **ابستراکشن** و **تخصصی‌سازی** ضروری هستند.

---

## قابلیت ابستراکشن در انواع ارجاعی

در C#، انواع ارجاعی می‌توانند:

- از **کلاس‌های چکیده** (abstract class) ارث‌بری کنند.
- **رابط‌ها** (interface) را پیاده‌سازی کنند.
- متدهای **مجازی** (virtual) یا **چکیده** (abstract) داشته باشند که در کلاس‌های فرزند **بازنویسی** (override) شوند.

این ویژگی‌ها به ما امکان می‌دهند تا یک **قرارداد رفتاری** (Behavioral Contract) تعریف کنیم بدون اینکه جزئیات پیاده‌سازی را مشخص کنیم.

### مثال:

```csharp
public abstract class Animal
{
    public abstract void MakeSound(); // ابستراکشن رویه‌ای
}

public class Dog : Animal
{
    public override void MakeSound() => Console.WriteLine("Woof!");
}
```

در اینجا، `MakeSound()` یک **روال چکیده** است که جزئیات پیاده‌سازی آن در کلاس‌های فرزند تعیین می‌شود.

---

## تخصصی‌سازی (Specialization) در انواع ارجاعی

**تخصصی‌سازی** به معنای ایجاد یک کلاس فرزند است که رفتار یک کلاس پایه را **تکمیل یا تغییر** می‌دهد. این کار با **وراثت** (Inheritance) انجام می‌شود.

در C#:

- کلاس‌های فرزند می‌توانند متدهای `virtual` یا `abstract` را `override` کنند.
- می‌توانند ویژگی‌های جدید یا متدهای جدید اضافه کنند.
- می‌توانند از چندین رابط (interface) پشتیبانی کنند.

### مثال:

```csharp
public interface IFlyable
{
    void Fly();
}

public class Bird : Animal, IFlyable
{
    public override void MakeSound() => Console.WriteLine("Tweet!");
    public void Fly() => Console.WriteLine("Flying...");
}
```

در اینجا، `Bird` هم **تخصصی‌سازی** از `Animal` است و هم **قابلیت پرواز** را از طریق رابط `IFlyable` اضافه کرده است.

---

## کاربرد Procedural Abstraction در انواع ارجاعی

Procedural Abstraction در انواع ارجاعی به شما اجازه می‌دهد:

- **کد را قابل نگهداری‌تر** کنید.
- **وابستگی‌ها را کاهش** دهید (Dependency Inversion).
- **تست‌پذیری** را افزایش دهید (مثلاً با Mock کردن رابط‌ها).
- **کدهای تکراری** را حذف کنید.

### الگوهای رایج:

- **استراتژی** (Strategy Pattern): با استفاده از رابط‌ها، رفتارهای مختلف را قابل تعویض می‌کند.
- **الگوی قالب** (Template Method): با استفاده از کلاس‌های چکیده، یک الگوی کلی برای الگوریتم تعریف می‌کند.

---

## مثال‌های کاربردی در C#

### مثال ۱: استفاده از رابط برای Procedural Abstraction

```csharp
public interface ICalculator
{
    int Calculate(int a, int b);
}

public class AddCalculator : ICalculator
{
    public int Calculate(int a, int b) => a + b;
}

public class MultiplyCalculator : ICalculator
{
    public int Calculate(int a, int b) => a * b;
}

// استفاده
ICalculator calc = new AddCalculator();
Console.WriteLine(calc.Calculate(2, 3)); // خروجی: 5
```

### مثال ۲: کلاس چکیده + تخصصی‌سازی

```csharp
public abstract class Shape
{
    public abstract double Area();
}

public class Circle : Shape
{
    private double radius;
    public Circle(double r) => radius = r;
    public override double Area() => Math.PI * radius * radius;
}
```

---

## جمع‌بندی

- **Procedural Abstraction** در C# با استفاده از **انواع ارجاعی** (کلاس‌ها، رابط‌ها) پیاده‌سازی می‌شود.
- **انواع ارجاعی** تنها دسته‌ای از داده‌ها هستند که امکان **وراثت** و **چندریختی** را فراهم می‌کنند.
- **ابستراکشن** به ما اجازه می‌دهد رفتارهای کلی را تعریف کنیم بدون جزئیات پیاده‌سازی.
- **تخصصی‌سازی** این امکان را می‌دهد که رفتارهای کلی را در کلاس‌های فرزند پیاده‌سازی یا تغییر دهیم.
- این مفاهیم پایه‌های **برنامه‌نویسی شیءگرا** و **طراحی نرم‌افزارهای انعطاف‌پذیر** هستند.

---

## منابع معتبر

1. **Microsoft Learn – C# Programming Guide**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)

2. **C# in Depth – Jon Skeet** (Manning Publications)  
   [https://www.manning.com/books/c-sharp-in-depth-fourth-edition](https://www.manning.com/books/c-sharp-in-depth-fourth-edition)

3. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse** (Apress)  
   [https://link.springer.com/book/10.1007/978-1-4842-7950-6](https://link.springer.com/book/10.1007/978-1-4842-7950-6)

4. **Object-Oriented Programming in C# – Microsoft Learn**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/tutorials/oop](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/tutorials/oop)

5. **Design Patterns: Elements of Reusable Object-Oriented Software – GoF**  
   (برای درک عمیق‌تر از کاربردهای ابستراکشن در الگوهای طراحی)
.
