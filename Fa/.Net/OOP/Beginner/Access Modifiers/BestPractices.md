

## 1. مقدمه: چرا به Access Modifiers نیاز داریم؟

در برنامه‌نویسی شیءگرا (OOP)، **کپسوله‌سازی (Encapsulation)** یکی از اصول بنیادین است. این اصل بیان می‌کند که جزئیات پیاده‌سازی داخلی یک کلاس باید از دیگر کلاس‌ها پنهان بماند.  
**Access Modifiers** ابزاری قدرتمند در C# هستند که به ما اجازه می‌دهند تا **سطح دسترسی** به متغیرها، متدها، خصوصیات و کلاس‌ها را دقیقاً کنترل کنیم.

استفاده صحیح از آن‌ها:
- ✅ کد تمیز‌تر و حرفه‌ای‌تر می‌سازد.
- ✅ امنیت و ثبات برنامه را افزایش می‌دهد.
- ✅ از تغییرات ناخواسته در داده‌ها جلوگیری می‌کند.
- ✅ با اصل **کمترین دسترسی (Least Privilege)** سازگار است.

---

## 2. انواع Access Modifiers در C#

طبق [مستندات رسمی Microsoft](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/access-modifiers)، C# از **هفت سطح دسترسی** پشتیبانی می‌کند که با پنج کلمه کلیدی (`public`, `protected`, `internal`, `private`, `file`) تعریف می‌شوند:

| Access Modifier | توضیح |
|------------------|-------|
| `public` | بدون محدودیت؛ قابل دسترسی از هر جایی. |
| `private` | فقط درون همان **کلاس** قابل دسترسی است. |
| `protected` | درون همان کلاس یا **کلاس‌های مشتق شده** (فرزند). |
| `internal` | فقط درون همان **Assembly** (معمولاً یک پروژه DLL یا EXE). |
| `protected internal` | یا درون همان Assembly **یا** در کلاس‌های فرزند (حتی در Assembly دیگر). |
| `private protected` | فقط در کلاس‌های فرزند **درون همان Assembly**. |
| `file` | فقط در همان **فایل منبع (source file)** قابل دسترسی است. |

> 💡 `file` modifier عموماً برای **Source Generators** استفاده می‌شود و در کدنویسی معمولی کمتر دیده می‌شود.

---

## 3. قانون طلایی: اصل کمترین دسترسی (Principle of Least Privilege)

🔐 **همیشه با محدودترین سطح دسترسی شروع کنید.**

- اعضای کلاس را **به صورت پیش‌فرض `private`** تعریف کنید.
- تنها زمانی سطح دسترسی را ارتقاء دهید که واقعاً نیاز داشته باشید.
- این رویکرد، **رابطه عمومی کلاس (Public API)** را تمیز، کنترل‌شده و قابل نگهداری نگه می‌دارد.

### ❌ مثال اشتباه
```csharp
public class PaymentGateway
{
    public void ConnectToDatabase() { /* ... */ } // ❌ عمداً public شده!
    
    public void ProcessPayment()
    {
        ConnectToDatabase();
    }
}
```
> ⚠️ `ConnectToDatabase` یک متد داخلی است، اما به اشتباه public شده و حالا هر کسی می‌تواند آن را فراخوانی کند!

### ✅ مثال درست
```csharp
public class PaymentGateway
{
    private void ConnectToDatabase() { /* ... */ } // ✅ فقط داخلی
    
    public void ProcessPayment()
    {
        ConnectToDatabase();
    }
}
```

---

## 4. بهترین شیوه‌ها: `private` و `public`

### ✅ استفاده از `private`
- **تمام فیلدها** (Fields) را private تعریف کنید.
- **متدهای کمکی** (Helper Methods) را private نگه دارید.
- از دسترسی مستقیم به داده‌ها جلوگیری کنید.

### ✅ استفاده از `public`
- فقط برای **رابطه خارجی کلاس** (Public API).
- برای **خصوصیات (Properties)** که اعتبارسنجی دارند:

```csharp
public class User
{
    private int _age;

    public int Age
    {
        get => _age;
        set => _age = value >= 0 ? value : throw new ArgumentException("Age cannot be negative.");
    }
}
```

> 🛡️ این روش از تغییرات غیرمجاز داده جلوگیری می‌کند.

---

## 5. بهترین شیوه‌ها: `protected` و `internal`

### ✅ `protected`: برای وراثت
- وقتی کلاس‌های فرزند نیاز به دسترسی یا override کردن عضو دارند.
- برای اعضای مشترک در سلسله مراتب وراثت.

```csharp
public class Animal
{
    protected void MakeSound() => Console.WriteLine("Some sound");
}

public class Dog : Animal
{
    public void Bark() => MakeSound(); // ✅ دسترسی مجاز
}
```

### ✅ `internal`: برای پروژه‌محوری
- برای کلاس‌ها یا متدهایی که فقط در یک Assembly استفاده می‌شوند.
- از "نشت انتزاع" (Leaky Abstraction) جلوگیری می‌کند.

```csharp
internal class DatabaseHelper
{
    internal string GetConnectionString() => "...";
}
```

> 🔒 این کلاس در Assembly دیگر قابل دسترسی نیست.

---

## 6. یکپارچه‌سازی با الگوهای طراحی

### ✅ الگوی Helper Class
برای کلاس‌هایی که فقط شامل متدهای static هستند:

```csharp
public static class MathHelper
{
    public static int Square(int x) => x * x;
}
```

یا با سازنده private:

```csharp
public class StringHelper
{
    private StringHelper() { } // ❌ جلوگیری از نمونه‌سازی

    public static string Reverse(string s) => new(s.Reverse().ToArray());
}
```

### ✅ الگوی Singleton
با استفاده از `private constructor`:

```csharp
public sealed class Logger
{
    private static readonly Logger _instance = new();

    private Logger() { } // ✅ فقط داخل کلاس می‌تواند نمونه بسازد

    public static Logger Instance => _instance;
}
```

---

## 7. جمع‌بندی و نکات کلیدی

| Modifier | بهترین کاربرد | نکته مهم |
|--------|---------------|----------|
| `private` | فیلدها، متدهای داخلی | ✅ پیش‌فرض ایمن |
| `public` | Public API، Properties | 🔐 با مراقبت استفاده شود |
| `protected` | وراثت، کلاس‌های پایه | 🧬 برای فرزندان |
| `internal` | پیاده‌سازی داخلی پروژه | 🏗️ از نشت کد جلوگیری می‌کند |
| `protected internal` | ترکیب internal + protected | OR (یا/یا) |
| `private protected` | فرزندان در همان Assembly | AND (و/و) |
| `file` | Source Generators | 📄 فقط در یک فایل |

---

## 8. منابع

- 📘 [Microsoft Docs - Access Modifiers](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/access-modifiers)
- 📚 *C# in Depth* – Jon Skeet (فصل 2)
- 📚 *Pro C# 10 with .NET 6* – Andrew Troelsen & Philip Japikse
- 📜 [ECMA-334: C# Language Specification](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)
- 📖 *Clean Code* – Robert C. Martin (فصل 6: Objects and Data Structures)

---

✅ **نکته نهایی:**  
همیشه با `private` شروع کنید. فقط زمانی که واقعاً نیاز به دسترسی خارجی دارید، سطح دسترسی را افزایش دهید. این ساده‌ترین راه برای نوشتن کدی **امن، تمیز و قابل نگهداری** است.

--- 

