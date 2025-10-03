

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [تعریف Abstract Class](#تعریف-abstract-class)  
3. [تعریف Interface](#تعریف-interface)  
4. [مقایسه کلیدی بین Abstract Class و Interface](#مقایسه-کلیدی-بین-abstract-class-و-interface)  
5. [چه زمانی از Abstract Class استفاده کنیم؟](#چه-زمانی-از-abstract-class-استفاده-کنیم)  
6. [چه زمانی از Interface استفاده کنیم؟](#چه-زمانی-از-interface-استفاده-کنیم)  
7. [نکات پیشرفته در C# (از نسخه 8 به بعد)](#نکات-پیشرفته-در-c-از-نسخه-8-به-بعد)  
8. [جمع‌بندی و جدول تصمیم‌گیری](#جمع‌بندی-و-جدول-تصمیم‌گیری)  
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیءگرا (OOP)، **چکیده‌سازی (Abstraction)** یکی از اصول اصلی است. در C#، دو ابزار قدرتمند برای پیاده‌سازی این اصل وجود دارد:  
- **کلاس‌های چکیده (Abstract Classes)**  
- **رابط‌ها (Interfaces)**

هر دو این ابزارها به شما اجازه می‌دهند تا قراردادهایی برای رفتار کلاس‌ها تعریف کنید، اما **کاربردها، محدودیت‌ها و فلسفه‌های طراحی** آن‌ها متفاوت است. انتخاب صحیح بین این دو، کیفیت کد، قابلیت نگهداری و انعطاف‌پذیری سیستم شما را تحت تأثیر قرار می‌دهد.

---

## تعریف Abstract Class

یک **کلاس چکیده** در C# کلاسی است که نمی‌توان از آن نمونه (instance) ساخت و فقط برای ارث‌بری طراحی شده است. این کلاس می‌تواند شامل:

- **اعضای پیاده‌سازی‌شده** (متد، پراپرتی، فیلد)
- **اعضای چکیده** (abstract members) که **باید** در کلاس‌های فرزند پیاده‌سازی شوند.

### مثال:

```csharp
public abstract class Animal
{
    public string Name { get; set; }

    // متد پیاده‌سازی‌شده
    public void Sleep() => Console.WriteLine($"{Name} is sleeping.");

    // متد چکیده (باید در فرزندان پیاده‌سازی شود)
    public abstract void MakeSound();
}
```

---

## تعریف Interface

یک **رابط (Interface)** در C# یک **قرارداد** است که تعریف می‌کند یک کلاس چه رفتارهایی **باید** داشته باشد، اما **هیچ پیاده‌سازی پیش‌فرضی** ندارد (تا قبل از C# 8). از C# 8 به بعد، می‌توان متد پیش‌فرض نیز تعریف کرد.

### مثال:

```csharp
public interface IFlyable
{
    void Fly();
}

// از C# 8 به بعد:
public interface ISwimmable
{
    void Swim();
    
    // متد پیش‌فرض
    void Dive() => Console.WriteLine("Diving into water!");
}
```

---

## مقایسه کلیدی بین Abstract Class و Interface

| ویژگی | Abstract Class | Interface |
|--------|----------------|-----------|
| **وراثت چندگانه** | ❌ خیر (C# از وراثت چندگانه پشتیبانی نمی‌کند) | ✅ بله (یک کلاس می‌تواند چندین Interface را پیاده‌سازی کند) |
| **اعضای پیاده‌سازی‌شده** | ✅ بله | ✅ از C# 8 به بعد |
| **فیلدها و سازنده‌ها** | ✅ بله | ❌ خیر (حتی در C# 8+) |
| **اعضای static** | ✅ بله | ✅ از C# 8 به بعد |
| **تغییر پس از انتشار** | ریسک‌زا (شکستن کلاس‌های فرزند) | انعطاف‌پذیرتر (با متد پیش‌فرض) |
| **معنای طراحی** | "یک نوع از..." (is-a) | "قادر به..." (can-do) |

---

## چه زمانی از Abstract Class استفاده کنیم؟

از **Abstract Class** زمانی استفاده کنید که:

✅ **یک پایه مشترک منطقی** بین کلاس‌ها وجود دارد (مثلاً `Animal` برای `Dog` و `Cat`).  
✅ **رفتار یا داده‌های مشترکی** دارید که می‌خواهید در یک مکان تعریف شوند (مثلاً `Name` یا `Sleep()`).  
✅ **ارتباط "is-a"** بین کلاس‌ها وجود دارد (`Dog` یک `Animal` است).  
✅ **نیاز به سازنده (constructor)** یا **فیلدهای protected/internal** دارید.

> 💡 **مثال کاربردی**:  
> در یک سیستم بانکی، `BankAccount` می‌تواند یک کلاس چکیده باشد که `SavingsAccount` و `CheckingAccount` از آن ارث‌بری می‌کنند.

---

## چه زمانی از Interface استفاده کنیم؟

از **Interface** زمانی استفاده کنید که:

✅ **چندین کلاس غیرمرتبط** نیاز به یک رفتار مشترک دارند (مثلاً `Fly()` برای `Bird` و `Airplane`).  
✅ **نیاز به وراثت چندگانه** دارید.  
✅ **قراردادی برای تست‌پذیری (Testability)** یا **وابستگی معکوس (Dependency Inversion)** نیاز دارید.  
✅ **معماری مبتنی بر قرارداد (Contract-based)** طراحی می‌کنید (مثل سرویس‌ها در برنامه‌نویسی سیستم‌های بزرگ).

> 💡 **مثال کاربردی**:  
> `IDisposable` برای آزاد کردن منابع، `IEnumerable` برای پشتیبانی از حلقه‌های `foreach`.

---

## نکات پیشرفته در C# (از نسخه 8 به بعد)

با معرفی **Default Interface Methods** در C# 8، تفاوت‌ها کمی محو شده است، اما **اصول طراحی همچنان معتبرند**:

- **Interfaceها هنوز نمی‌توانند فیلد داشته باشند** → نمی‌توانند وضعیت (state) نگه دارند.
- **Abstract Classها همچنان تنها راه برای اشتراک‌گذاری وضعیت بین فرزندان** هستند.
- **استفاده از Interface برای قراردادهای عمومی** همچنان بهترین روش است (مثل `IComparable`, `IEquatable`).

> ⚠️ **هشدار**: استفاده از متد پیش‌فرض در Interface برای جایگزینی Abstract Class **توصیه نمی‌شود**، مگر در موارد خاص (مثل افزودن رفتار جدید بدون شکستن کدهای موجود).

---

## جمع‌بندی و جدول تصمیم‌گیری

| شرایط | توصیه |
|--------|--------|
| نیاز به اشتراک‌گذاری **کد پیاده‌سازی** و **داده‌های مشترک** | ✅ **Abstract Class** |
| نیاز به **وراثت چندگانه** | ✅ **Interface** |
| کلاس‌ها **هم‌خانواده** هستند (is-a) | ✅ **Abstract Class** |
| کلاس‌ها **رفتار مشترک** دارند ولی رابطه‌ی ارثی ندارند (can-do) | ✅ **Interface** |
| طراحی برای **تست‌پذیری** یا **DI** | ✅ **Interface** |
| نیاز به **سازنده** یا **فیلد protected** | ✅ **Abstract Class** |

> 📌 **قانون طلایی**:  
> **"Prefer composition over inheritance"** — اما وقتی inheritance لازم است، ابتدا به رابطه‌ی معنایی (is-a vs can-do) فکر کنید، سپس به نیازهای فنی.

---

## منابع معتبر

1. **Microsoft Docs – Abstract and Sealed Classes and Class Members**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)

2. **Microsoft Docs – Interfaces**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface)

3. **Microsoft Docs – Default Interface Methods (C# 8.0)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#default-interface-members](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#default-interface-members)

4. **C# in Depth – Jon Skeet** (فصل مربوط به OOP و رابط‌ها)  
   [https://csharpindepth.com/](https://csharpindepth.com/)

5. **Clean Code – Robert C. Martin** (اصول SOLID، به‌ویژه Dependency Inversion Principle)  
   ISBN: 978-0132350884

6. **Framework Design Guidelines – Microsoft**  
   [https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/)
!
