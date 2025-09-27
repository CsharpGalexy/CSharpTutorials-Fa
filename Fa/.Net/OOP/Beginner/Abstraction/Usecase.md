

## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [کلاس انتزاعی چیست؟ (یادآوری سریع)](#کلاس-انتزاعی-چیست-یادآوری-سریع)
3. [چرا از کلاس انتزاعی استفاده کنیم؟](#چرا-از-کلاس-انتزاعی-استفاده-کنیم)
4. [کاربردهای رایج (Use Cases)](#کاربردهای-رایج-use-cases)
   - [4.1. تعریف یک پایه مشترک با رفتارهای اجباری](#41-تعریف-یک-پایه-مشترک-با-رفتارهای-اجباری)
   - [4.2. پیاده‌سازی الگوی Template Method](#42-پیاده‌سازی-الگوی-template-method)
   - [4.3. جلوگیری از نمونه‌سازی مستقیم (Preventing Instantiation)](#43-جلوگیری-از-نمونه‌سازی-مستقیم-preventing-instantiation)
   - [4.4. اشتراک‌گذاری کد بین کلاس‌های فرزند (Code Reuse)](#44-اشتراک‌گذاری-کد-بین-کلاس‌های-فرزند-code-reuse)
   - [4.5. ساخت سلسله مراتب معنادار در طراحی سیستم](#45-ساخت-سلسله-مراتب-معنادار-در-طراحی-سیستم)
5. [چه زمانی از کلاس انتزاعی استفاده نکنیم؟](##چه-زمانی-از-کلاس-انتزاعی-استفاده-نکنیم)
6. [جمع‌بندی](#جمع‌بندی)
7. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیءگرا (OOP)، **کلاس‌های انتزاعی** (Abstract Classes) یکی از ابزارهای کلیدی برای طراحی سیستم‌های انعطاف‌پذیر و قابل گسترش هستند. اما بسیاری از مبتدیان فقط نحوه تعریف آن‌ها را یاد می‌گیرند، بدون اینکه بدانند **چه زمانی و چرا** باید از آن‌ها استفاده کنند.

این مستند به‌طور خاص روی **کاربردهای واقعی** (Use Cases) کلاس‌های انتزاعی در C# تمرکز دارد — نه صرفاً نحوه نوشتن آن‌ها، بلکه **چرا** و **در چه شرایطی** باید از آن‌ها استفاده کرد.

---

## کلاس انتزاعی چیست؟ (یادآوری سریع)

در C#، یک کلاس انتزاعی با کلیدواژه `abstract` تعریف می‌شود:

```csharp
public abstract class Vehicle
{
    public string Brand { get; set; }
    public abstract void Start(); // بدون بدنه
    public void Stop() => Console.WriteLine("Vehicle stopped."); // با بدنه
}
```

ویژگی‌های کلیدی:
- ❌ **نمی‌توان از آن نمونه‌سازی کرد** (`new Vehicle()` خطای کامپایل است).
- ✅ می‌تواند **روش‌های معمولی** (با بدنه) و **روش‌های انتزاعی** (بدون بدنه) داشته باشد.
- ✅ کلاس‌های فرزند **باید** تمام روش‌های انتزاعی را پیاده‌سازی کنند.

---

## چرا از کلاس انتزاعی استفاده کنیم؟

کلاس انتزاعی زمانی مفید است که:
- چندین کلاس **رفتار مشترکی** دارند.
- می‌خواهید **اجبار کنید** که کلاس‌های فرزند رفتار خاصی را پیاده‌سازی کنند.
- می‌خواهید **کد تکراری** را در یک مکان مشترک قرار دهید.

> 💡 **نکته طلایی**: اگر کلاسی **هیچ رفتار مشترکی** ندارد و فقط یک "قرارداد" است، بهتر است از **اینترفیس** استفاده کنید.

---

## کاربردهای رایج (Use Cases)

### 4.1. تعریف یک پایه مشترک با رفتارهای اجباری

**استفاده**: وقتی می‌خواهید تمام زیرمجموعه‌ها یک روش خاص را پیاده‌سازی کنند.

**مثال**: سیستم پردازش پرداخت

```csharp
public abstract class PaymentProcessor
{
    public abstract bool ProcessPayment(decimal amount);
}

public class CreditCardProcessor : PaymentProcessor
{
    public override bool ProcessPayment(decimal amount)
    {
        // پیاده‌سازی پرداخت با کارت اعتباری
        return true;
    }
}

public class PayPalProcessor : PaymentProcessor
{
    public override bool ProcessPayment(decimal amount)
    {
        // پیاده‌سازی پرداخت با PayPal
        return true;
    }
}
```

✅ **مزیت**: هر پردازنده جدید **حتماً** باید `ProcessPayment` را پیاده‌سازی کند.

---

### 4.2. پیاده‌سازی الگوی Template Method

**استفاده**: وقتی ساختار کلی یک الگوریتم ثابت است، اما جزئیات آن بین زیرمجموعه‌ها متفاوت است.

**مثال**: سیستم گزارش‌گیری

```csharp
public abstract class ReportGenerator
{
    // الگوریتم کلی (قالب ثابت)
    public void GenerateReport()
    {
        FetchData();
        ProcessData();
        RenderReport();
    }

    protected abstract void FetchData();
    protected abstract void ProcessData();
    protected abstract void RenderReport();
}

public class PdfReportGenerator : ReportGenerator
{
    protected override void FetchData() => Console.WriteLine("Fetching data for PDF...");
    protected override void ProcessData() => Console.WriteLine("Processing data...");
    protected override void RenderReport() => Console.WriteLine("Rendering PDF report.");
}
```

✅ **مزیت**: کنترل کامل روی **ترتیب اجرا** دارید، اما جزئیات را به فرزندان واگذار می‌کنید.

> 🔗 این یکی از معروف‌ترین **الگوهای طراحی** (Design Patterns) است: [Template Method Pattern](https://refactoring.guru/design-patterns/template-method)

---

### 4.3. جلوگیری از نمونه‌سازی مستقیم (Preventing Instantiation)

**استفاده**: وقتی یک کلاس **مفهومی کلی** است و نمی‌تواند به تنهایی وجود داشته باشد.

**مثال**: `Animal` یک مفهوم کلی است — نمی‌توانید یک "حیوان" عمومی داشته باشید، بلکه باید `Dog` یا `Cat` باشد.

```csharp
public abstract class Animal
{
    public string Name { get; set; }
    public abstract void Speak();
}

// ❌ این خط خطا می‌دهد:
// var animal = new Animal(); // Compile-time error!
```

✅ **مزیت**: جلوگیری از استفاده نادرست از کلاس در زمان کامپایل.

---

### 4.4. اشتراک‌گذاری کد بین کلاس‌های فرزند (Code Reuse)

**استفاده**: وقتی چندین کلاس فرزند **کد مشترکی** دارند.

**مثال**: سرویس‌های لاگین

```csharp
public abstract class AuthService
{
    protected void ValidateInput(string username, string password)
    {
        if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            throw new ArgumentException("Username and password are required.");
    }

    public abstract bool Login(string username, string password);
}

public class GoogleAuthService : AuthService
{
    public override bool Login(string username, string password)
    {
        ValidateInput(username, password);
        // لاگین با Google
        return true;
    }
}
```

✅ **مزیت**: کاهش تکرار کد (DRY Principle) و نگهداری آسان‌تر.

---

### 4.5. ساخت سلسله مراتب معنادار در طراحی سیستم

**استفاده**: در سیستم‌های بزرگ، کلاس‌های انتزاعی به شما کمک می‌کنند تا یک **سلسله مراتب منطقی** ایجاد کنید.

**مثال**: سیستم مدیریت کارمندان

```csharp
public abstract class Employee
{
    public string Name { get; set; }
    public decimal BaseSalary { get; set; }
    public abstract decimal CalculateBonus();
}

public class Manager : Employee
{
    public override decimal CalculateBonus() => BaseSalary * 0.2m;
}

public class Developer : Employee
{
    public override decimal CalculateBonus() => BaseSalary * 0.1m;
}
```

✅ **مزیت**: کد خوانا، قابل تست و قابل گسترش.

---

## چه زمانی از کلاس انتزاعی استفاده نکنیم؟

- اگر **هیچ رفتار مشترکی** وجود ندارد → از **اینترفیس** استفاده کنید.
- اگر نیاز به **وراثت چندگانه** دارید → فقط **اینترفیس** پشتیبانی می‌شود.
- اگر کلاس شما **کاملاً قراردادی** است (مثل `IDisposable`) → اینترفیس مناسب‌تر است.

> ⚠️ **هشدار**: استفاده بی‌رویه از کلاس‌های انتزاعی می‌تواند سیستم را **سخت‌تر** و **غیرانعطاف‌پذیر** کند.

---

## جمع‌بندی

| Use Case | زمان استفاده |
|--------|-------------|
| رفتارهای اجباری | وقتی همه فرزندان باید یک روش خاص را پیاده‌سازی کنند |
| Template Method | وقتی الگوریتم کلی ثابت است، اما جزئیات متغیر |
| جلوگیری از نمونه‌سازی | وقتی کلاس یک مفهوم کلی است |
| اشتراک‌گذاری کد | وقتی کد تکراری بین فرزندان وجود دارد |
| سلسله مراتب معنادار | در سیستم‌های بزرگ با روابط منطقی |

> ✅ **قانون طلایی**:  
> **کلاس انتزاعی = "یک چیز است"** (is-a)  
> **اینترفیس = "می‌تواند انجام دهد"** (can-do)

---

## منابع معتبر

1. **Microsoft Learn – Abstract Classes**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)

2. **C# in Depth – Jon Skeet**  
   Manning Publications, Chapter 4: "Inheritance and abstraction"

3. **Design Patterns: Elements of Reusable Object-Oriented Software**  
   Gamma, Helm, Johnson, Vlissides (GoF) – Template Method Pattern

4. **Refactoring.Guru – Template Method**  
   [https://refactoring.guru/design-patterns/template-method](https://refactoring.guru/design-patterns/template-method)

5. **Clean Code – Robert C. Martin**  
   Chapter 9: "Unit Tests" and "Classes" – discusses abstraction principles

6. **C# 10 and .NET 6 – Mark J. Price**  
   Packt Publishing, Chapter 7: "Inheritance and Polymorphism"
   
