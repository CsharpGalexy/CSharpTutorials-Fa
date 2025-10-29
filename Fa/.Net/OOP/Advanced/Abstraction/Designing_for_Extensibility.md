
### فهرست مطالب

1. [مقدمه](#مقدمه)
2. [ابستراکشن چیست؟](#ابستراکشن-چیست؟)
3. [چرا طراحی برای گسترش‌پذیری مهم است؟](#چرا-طراحی-برای-گسترشپذیری-مهم-است؟)
4. [ابزارهای ابستراکشن در سی‌شارپ](#ابزارهای-ابستراکشن-در-سیشارپ)
   - [کلاس‌های ابسترکت (Abstract Classes)](#کلاسهای-ابسترکت-abstract-classes)
   - [اینترفیس‌ها (Interfaces)](#اینترفیسها-interfaces)
5. [الگوهای رایج برای گسترش‌پذیری](#الگوهای-رایج-برای-گسترشپذیری)
   - [الگوی استراتژی (Strategy Pattern)](#الگوی-استراتژی-strategy-pattern)
   - [الگوی تمپلیت متد (Template Method Pattern)](#الگوی-تمپلیت-متد-template-method-pattern)
6. [اصل Open/Closed (OCP) و ارتباط آن با گسترش‌پذیری](#اصل-openclosed-ocp-و-ارتباط-آن-با-گسترشپذیری)
7. [نکات عملی و بهترین روش‌ها](#نکات-عملی-و-بهترین-روشها)
8. [جمع‌بندی](#جمعبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در دنیای توسعه نرم‌افزار، نیاز به تغییر و گسترش کد بدون دگرگونی ساختار اصلی آن، یکی از چالش‌های اساسی است. **طراحی برای گسترش‌پذیری** (Designing for Extensibility) به این معناست که سیستم را به گونه‌ای طراحی کنیم که در آینده بتوان بدون تغییر در کدهای موجود، قابلیت‌های جدیدی به آن اضافه کرد.  
در زبان سی‌شارپ، **ابستراکشن** (چکیده‌سازی) یکی از قدرتمندترین ابزارها برای دستیابی به این هدف است.

---

## ابستراکشن چیست؟

**ابستراکشن** به معنای پنهان کردن جزئیات پیچیده و نمایش فقط آنچه که برای استفاده از یک سیستم ضروری است. در برنامه‌نویسی شیءگرا (OOP)، این مفهوم به ما کمک می‌کند تا کدهایی بنویسیم که:

- انعطاف‌پذیر باشند.
- قابل نگهداری باشند.
- قابل گسترش باشند.

> **مثال ساده**: وقتی از یک کلاس `ILogger` استفاده می‌کنید، نیازی نیست بدانید که لاگ‌ها به فایل ذخیره می‌شوند یا به کنسول یا سرویس ابری. شما فقط با رابط اصلی کار می‌کنید.

---

## چرا طراحی برای گسترش‌پذیری مهم است؟

- **کاهش ریسک خطا**: تغییر در کدهای موجود ممکن است باعث ایجاد باگ در بخش‌های دیگر شود.
- **افزایش قابلیت تست**: کدهای گسترش‌پذیر معمولاً قابل تست‌تر هستند.
- **پشتیبانی از تیم‌های بزرگ**: توسعه‌دهندگان مختلف می‌توانند ماژول‌های جدید را بدون دخالت در کد دیگران اضافه کنند.
- **هماهنگی با اصول SOLID**: به‌ویژه **اصل Open/Closed**.

---

## ابزارهای ابستراکشن در سی‌شارپ

### کلاس‌های ابسترکت (Abstract Classes)

کلاس‌های ابسترکت، کلاس‌هایی هستند که **نمی‌توان از آن‌ها شیء ساخت** و فقط برای ارث‌بری طراحی شده‌اند. این کلاس‌ها می‌توانند شامل:

- متد‌های پیاده‌سازی‌شده (concrete)
- متد‌های ابسترکت (abstract) که باید در کلاس‌های فرزند پیاده‌سازی شوند.

```csharp
public abstract class PaymentProcessor
{
    public void ProcessPayment(decimal amount)
    {
        ValidatePayment(amount);
        ExecutePayment(amount);
        LogTransaction(amount);
    }

    protected abstract void ExecutePayment(decimal amount);

    private void ValidatePayment(decimal amount)
    {
        if (amount <= 0) throw new ArgumentException("Invalid amount");
    }

    private void LogTransaction(decimal amount)
    {
        Console.WriteLine($"Processed payment: {amount}");
    }
}

public class CreditCardProcessor : PaymentProcessor
{
    protected override void ExecutePayment(decimal amount)
    {
        Console.WriteLine("Processing via Credit Card");
    }
}
```

> 🔍 **نکته**: کلاس‌های ابسترکت زمانی مناسب هستند که بین کلاس‌های فرزند **رفتار مشترکی** وجود داشته باشد.

---

### اینترفیس‌ها (Interfaces)

اینترفیس‌ها **قراردادهایی** هستند که کلاس‌ها باید پیاده‌سازی کنند. در سی‌شارپ، یک کلاس می‌تواند چندین اینترفیس را پیاده‌سازی کند.

```csharp
public interface IEmailService
{
    void SendEmail(string to, string subject, string body);
}

public class SmtpEmailService : IEmailService
{
    public void SendEmail(string to, string subject, string body)
    {
        Console.WriteLine($"Sending email to {to} via SMTP");
    }
}

public class MockEmailService : IEmailService
{
    public void SendEmail(string to, string subject, string body)
    {
        Console.WriteLine("Mock: Email not actually sent");
    }
}
```

> ✅ **مزیت**: اینترفیس‌ها به شما امکان **جایگزینی راحت** پیاده‌سازی‌ها را می‌دهند (مثلاً برای تست یا تعویض سرویس).

---

## الگوهای رایج برای گسترش‌پذیری

### الگوی استراتژی (Strategy Pattern)

در این الگو، خانواده‌ای از الگوریتم‌ها تعریف می‌شود که قابل تعویض هستند. این کار با استفاده از اینترفیس‌ها یا کلاس‌های ابسترکت انجام می‌شود.

```csharp
public interface ISortStrategy
{
    void Sort(List<int> list);
}

public class QuickSort : ISortStrategy
{
    public void Sort(List<int> list) => list.Sort(); // ساده‌سازی شده
}

public class BubbleSort : ISortStrategy
{
    public void Sort(List<int> list)
    {
        // پیاده‌سازی Bubble Sort
    }
}

public class Sorter
{
    private ISortStrategy _strategy;
    public void SetStrategy(ISortStrategy strategy) => _strategy = strategy;
    public void Sort(List<int> list) => _strategy.Sort(list);
}
```

> 💡 این الگو به شما اجازه می‌دهد بدون تغییر در کلاس `Sorter`، روش‌های جدید مرتب‌سازی اضافه کنید.

---

### الگوی تمپلیت متد (Template Method Pattern)

این الگو از **کلاس‌های ابسترکت** استفاده می‌کند تا یک الگوریتم کلی را تعریف کند، اما بخش‌های خاص آن را به کلاس‌های فرزند واگذار می‌کند.

(همان مثال `PaymentProcessor` در بالا یک نمونه از این الگوست.)

---

## اصل Open/Closed (OCP) و ارتباط آن با گسترش‌پذیری

**اصل Open/Closed** یکی از پنج اصل SOLID است و می‌گوید:

> **"کلاس‌ها باید برای گسترش باز (Open for extension) و برای تغییر بسته (Closed for modification) باشند."**

این اصل مستقیماً به **طراحی برای گسترش‌پذیری** مرتبط است. با استفاده از ابستراکشن (اینترفیس یا کلاس ابسترکت)، می‌توانیم:

- کلاس‌های جدیدی اضافه کنیم.
- بدون تغییر در کدهای قبلی، رفتار سیستم را گسترش دهیم.

> 📌 **مثال**: اگر بخواهید سرویس پرداخت جدیدی (مثل PayPal) اضافه کنید، فقط کلاس جدیدی از `PaymentProcessor` ارث‌بری می‌کنید — نیازی به تغییر در کد اصلی نیست.

---

## نکات عملی و بهترین روش‌ها

1. **از اینترفیس‌ها برای وابستگی‌ها استفاده کنید** (Dependency Inversion Principle).
2. **از کلاس‌های ابسترکت زمانی استفاده کنید که منطق مشترکی وجود دارد**.
3. **از کلمه کلیدی `virtual` برای متد‌هایی که ممکن است override شوند، استفاده کنید**.
4. **از Composition به جای Inheritance زمانی که منطق مشترک کم است، استفاده کنید**.
5. **از Dependency Injection برای جایگزینی راحت پیاده‌سازی‌ها استفاده کنید**.

---

## جمع‌بندی

طراحی برای گسترش‌پذیری با استفاده از ابستراکشن در سی‌شارپ، به شما کمک می‌کند تا سیستم‌هایی بسازید که:

- در برابر تغییرات آینده مقاوم باشند.
- قابلیت نگهداری و تست بالایی داشته باشند.
- با اصول مهندسی نرم‌افزار (مثل SOLID) هماهنگ باشند.

استفاده هوشمندانه از **اینترفیس‌ها** و **کلاس‌های ابسترکت**، کلید موفقیت در این زمینه است.

---

## منابع معتبر

1. **Microsoft Learn – C# Programming Guide**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)

2. **"Head First Design Patterns" – Eric Freeman & Elisabeth Robson**  
   (فصل‌های Strategy و Template Method)

3. **"Clean Code" – Robert C. Martin**  
   (فصل 10: Classes – بحث درباره اصول OCP و ابستراکشن)

4. **"Agile Principles, Patterns, and Practices in C#" – Robert C. Martin & Micah Martin**  
   (فصل‌های مربوط به SOLID و طراحی برای گسترش‌پذیری)

5. **C# in Depth – Jon Skeet**  
   (بخش‌های مربوط به اینترفیس‌ها و چکیده‌سازی)

6. **Refactoring.Guru – Design Patterns in C#**  
   [https://refactoring.guru/design-patterns/csharp](https://refactoring.guru/design-patterns/csharp)

7. **Microsoft Docs – Abstract and Sealed Classes and Class Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)
