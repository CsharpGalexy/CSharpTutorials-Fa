# Public Interface vs Private Implementation: راهنمای جامع برای توسعه‌دهندگان تازه‌کار

## فهرست مطالب
- [مقدمه: چرا این مفهوم مهم است؟](#مقدمه-چرا-این-مفهوم-مهم-است)  
- [تعریف Public Interface و Private Implementation با مثال ساده](#تعریف-public-interface-و-private-implementation-با-مثال-ساده)  
- [چرا باید پیاده‌سازی را مخفی کنیم؟ (Encapsulation در عمل)](#چرا-باید-پیادهسازی-را-مخفی-کنیم-encapsulation-در-عمل)  
- [نمونه کد کامل در C# (Calculator) با توضیح گام‌به‌گام](#نمونه-کد-کامل-در-c-calculator-با-توضیح-گامبهگام)  
- [نکات پیشرفته: Internal، Default Interface Methods و Protected](#نکات-پیشرفته-internal-default-interface-methods-و-protected)  
- [مزایای استفاده از این اصل در طراحی نرم‌افزار](#مزایای-استفاده-از-این-اصل-در-طراحی-نرمافزار)  
- [خلاصه و نتیجه‌گیری](#خلاصه-و-نتیجهگیری)  
- [منابع و رفرنس‌های معتبر](#منابع-و-رفرنسهای-معتبر)  

---

## مقدمه: چرا این مفهوم مهم است؟
وقتی شروع به نوشتن کلاس‌ها و کتابخانه‌ها در C# می‌کنید، ممکن است وسوسه شوید همه‌چیز را در دسترس قرار دهید تا کار ساده‌تر شود. اما در واقعیت، کد شما توسط دیگران (همکاران، کاربران کتابخانه یا حتی خودتان در آینده) استفاده خواهد شد.  

اگر جزئیات داخلی کلاس را عمومی کنید، هر تغییر کوچکی ممکن است به **شکستن سازگاری (Breaking Change)** منجر شود. اینجاست که اصل **Public Interface vs Private Implementation** وارد عمل می‌شود.  

این اصل بخشی از **Encapsulation** (کپسوله‌سازی) در برنامه‌نویسی شی‌گرا است و می‌گوید:  
🔑 «فقط بخش‌های ضروری را عمومی کنید و جزئیات پیاده‌سازی را مخفی نگه دارید.»

---
## تعریف Public Interface و Private Implementation با مثال ساده

### Public Interface (رابط عمومی)
این رابط، قراردادی رسمی بین شما و مصرف‌کننده‌ی کد است. شامل متدها، پراپرتی‌ها و ایونت‌هایی است که قرار است دیگران استفاده کنند.  
مثال در کلاس `List<T>`:
```csharp
var numbers = new List<int>();
numbers.Add(5);   // Public Interface
```
در اینجا متد `Add` یک رابط عمومی است. کاربران فقط آن را صدا می‌زنند، بدون اینکه بدانند پشت صحنه چطور عنصر به آرایه اضافه می‌شود.

---
### Private Implementation (پیاده‌سازی خصوصی)
جزئیاتی است که درون کلاس اتفاق می‌افتد، اما بیرون نباید دیده شود.  
```csharp
private int InternalAdd(int x, int y)
{
    return x + y;
}
```
این متد صرفاً یک جزئیات داخلی است. کاربران خارجی نیازی ندارند آن را ببینند.  

✅ **جمع‌بندی**: رابط عمومی پایدار است و باید تغییراتش با دقت مدیریت شود، اما پیاده‌سازی داخلی می‌تواند آزادانه تغییر کند.

---
## چرا باید پیاده‌سازی را مخفی کنیم؟ (Encapsulation در عمل)
پنهان کردن پیاده‌سازی مزایای کلیدی دارد:

- **کاهش وابستگی (Loose Coupling):** کاربران به قرارداد وابسته می‌شوند، نه به جزئیات.  
- **قابلیت نگهداری:** تغییرات داخلی بدون شکستن کد کاربران امکان‌پذیر است.  
- **امنیت:** داده‌ها و منطق حساس در برابر دسترسی مستقیم محافظت می‌شوند.  
- **سادگی استفاده:** کاربران فقط یک رابط تمیز و شفاف می‌بینند.  

➡️ **طبق [راهنمای طراحی .NET مایکروسافت](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/)، همیشه جزئیات پیاده‌سازی را مخفی کنید و فقط قرارداد لازم را عمومی کنید.**

---
## نمونه کد کامل در C# (Calculator) با توضیح گام‌به‌گام
بیایید یک مثال واقعی بسازیم:

```csharp
// 1. اینترفیس عمومی – قرارداد
public interface ICalculator
{
    int Add(int a, int b);
    int Subtract(int a, int b);
}

// 2. کلاس پیاده‌سازی
public class Calculator : ICalculator
{
    // متدهای عمومی – تنها درگاه کاربران
    public int Add(int a, int b)
    {
        LogOperation("Add", a, b);
        return InternalAdd(a, b);
    }

    public int Subtract(int a, int b)
    {
        LogOperation("Subtract", a, b);
        return InternalSubtract(a, b);
    }

    // پیاده‌سازی خصوصی
    private int InternalAdd(int x, int y) => x + y;
    private int InternalSubtract(int x, int y) => x - y;

    private void LogOperation(string operation, int a, int b)
    {
        Console.WriteLine($"Operation: {operation} | Args: {a}, {b}");
    }
}
```
### توضیح گام‌به‌گام
- **گام ۱:** `ICalculator` قرارداد است. کاربران فقط آن را می‌بینند.  
- **گام ۲:** کلاس `Calculator` منطق داخلی را مدیریت می‌کند.  
- **گام ۳:** متدهای `Add` و `Subtract` عمومی هستند و نقطه‌ی ورود کاربران.  
- **گام ۴:** متدهای `InternalAdd` و `InternalSubtract` خصوصی‌اند؛ کاربران بیرونی حتی وجودشان را نمی‌دانند.  
- **گام ۵:** متد `LogOperation` صرفاً یک جزئیات داخلی است. تغییر آن تاثیری روی مصرف‌کننده ندارد.  

---
## نکات پیشرفته: Internal، Default Interface Methods و Protected

### ۱. internal
برای محدود کردن دسترسی به سطح اسمبلی:
```csharp
internal class InternalHelper { /* ... */ }
```

### ۲. Default Interface Methods (C# 8+)
```csharp
public interface ILogger
{
    void Log(string message);

    void LogError(string message) => Log($"[ERROR] {message}");

    private void Validate(string message)
    {
        if (string.IsNullOrEmpty(message))
            throw new ArgumentException("Message cannot be null or empty.");
    }
}
```

### ۳. protected
در کلاس پایه برای ارث‌بری کنترل‌شده:
```csharp
public abstract class BaseCalculator
{
    public int Add(int a, int b) => PerformAdd(a, b);

    protected abstract int PerformAdd(int a, int b);
}
```

---

## مزایای استفاده از این اصل در طراحی نرم‌افزار
- سادگی در استفاده  
- افزایش قابلیت نگهداری  
- امنیت داده‌ها  
- کاهش بروز باگ  
- هماهنگی بهتر در تیم‌ها  

---

## خلاصه و نتیجه‌گیری
اصل **Public Interface vs Private Implementation** پایه‌ای برای کدنویسی پایدار و حرفه‌ای است.  
- **Public Interface = قرارداد پایدار**  
- **Private Implementation = آزادی عمل داخلی**  

همیشه بپرسید:  
👉 «آیا واقعاً این عضو باید عمومی باشد؟ یا می‌تواند در پشت صحنه باقی بماند؟»  

انتخاب درست، کدی امن‌تر، خواناتر و قابل توسعه‌تر می‌سازد.  

---