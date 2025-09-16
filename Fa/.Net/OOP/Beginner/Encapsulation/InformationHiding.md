# 🔒 Information Hiding در C#  
> ✅ *مقدماتی  — مستندی دقیق و عملی برای توسعه‌دهندگان C#*

---

## 📑 فهرست مطالب

1. [مقدمه](#-مقدمه)  
2. [تعریف Information Hiding](#-تعریف-information-hiding)  
3. [چرا Information Hiding مهم است؟](#-چرا-information-hiding-مهم-است)  
4. [اصول بنیادی در C#](#-اصول-بنیادی-information-hiding-در-c)  
  4.1 -[ دسترسی‌های کنترل‌شده با Access Modifiers  ](#۱-دسترسیهای-کنترلشده-با-access-modifiers)
  4.2 -[ پنهان‌سازی داده‌ها (Data Hiding) ](#۲-پنهانسازی-دادهها-data-hiding)
  4.3 -[ پنهان‌سازی پیاده‌سازی (Implementation Hiding)](#۳-پنهانسازی-پیادهسازی-implementation-hiding)
5. [راهکارهای عملی در C#](#-راهکارهای-عملی-در-c)  
6. [مثال کامل: BankAccount](#-مثال-کامل-bankaccount)  
7. [بهترین روش‌ها (Best Practices)](#-بهترین-روشها-best-practices)  
8. [اشتباهات رایج](#-اشتباهات-رایج)  
9. [جمع‌بندی](#-جمعبندی)  
10. [منابع معتبر](#-منابع-معتبر)

---

## 🔹 مقدمه

در دنیای برنامه‌نویسی شیءگرا (OOP)، **Information Hiding** (پنهان‌سازی اطلاعات) یکی از **اصلی‌ترین اصول طراحی نرم‌افزار** است که پایه و اساس کدنویسی تمیز، قابل نگهداری و مقیاس‌پذیر را تشکیل می‌دهد.

در این مستند، **فقط و فقط** به مفهوم **Information Hiding** می‌پردازیم — بدون حاشیه، بدون ترکیب با Encapsulation یا Inheritance — و نشان می‌دهیم که چگونه در زبان **C#** می‌توان این اصل را به صورت عملی و مؤثر پیاده‌سازی کرد.

---

## 🔹 تعریف Information Hiding

> ❝ **Information Hiding** یعنی جزئیات پیاده‌سازی داخلی یک کلاس باید از سایر بخش‌های سیستم پنهان بمانند و تنها راه تعامل از طریق یک رابط (API) مشخص و کنترل‌شده باشد. ❞

این مفهوم اولین بار توسط **David Parnas** در مقاله معروفش در سال ۱۹۷۲ ("On the Criteria To Be Used in Decomposing Systems into Modules") معرفی شد.

📌 هدف اصلی:
- کاهش وابستگی بین ماژول‌ها
- افزایش انعطاف‌پذیری در تغییر کد
- جلوگیری از استفاده نادرست از داده‌ها
- افزایش امنیت و قابلیت نگهداری

---

## 🔹 چرا Information Hiding مهم است؟

### ۱. ✅ کاهش وابستگی (Decoupling)
وقتی یک کلاس نداند چگونه کلاس دیگر کار می‌کند، می‌توانید پیاده‌سازی آن را عوض کنید بدون اینکه کد دیگری بشکند.

### ۲. ✅ امنیت داده
مثلاً نمی‌خواهید کسی سن شخص را به `-10` تغییر دهد. با پنهان کردن فیلد واقعی، می‌توانید اعتبارسنجی انجام دهید.

### ۳. ✅ تغییر بدون شکستن کد
می‌توانید الگوریتم ذخیره‌سازی لاگ را از `List<T>` به `Queue<T>` تغییر دهید، بدون اینکه کاربران کلاس متوجه شوند.

### ۴. ✅ کنترل رفتار
فقط اجازه دهید کارهای "معقول" انجام شود. مثلاً موجودی بانک نتواند مستقیماً کاهش یابد؛ فقط از طریق برداشت.

---

## 🔹 اصول بنیادی Information Hiding در C#

### ۱. دسترسی‌های کنترل‌شده با Access Modifiers

در C#، از **access modifiers** برای تعیین دیده‌شدن اعضا استفاده می‌شود:

| Modifier | دسترسی |
|--------|--------|
| `private` | فقط در همان کلاس (پیش‌فرض فیلدها) ✅ |
| `protected` | کلاس و کلاس‌های فرزند |
| `internal` | فقط در همان ensamble (DLL/EXE) |
| `public` | همه جا — فقط وقتی واقعاً لازم است ⚠️ |

🔹 **قاعده طلایی**:  
> همیشه با **کوچکترین دسترسی لازم** شروع کنید.  
> فقط وقتی `public` باشید که واقعاً نیاز داشته باشید.

---

### ۲. پنهان‌سازی داده‌ها (Data Hiding)

هدف: جلوگیری از دسترسی مستقیم به فیلدها.

❌ ضد الگو (Anti-pattern):
```csharp
public class Person
{
    public string Name;
    public int Age;
}
```
> هر کسی می‌تواند `person.Age = -5;` بنویسد!

✅ راه حل:
```csharp
public class Person
{
    private string _name;
    private int _age;

    public string Name
    {
        get => _name;
        set => _name = value ?? throw new ArgumentNullException();
    }

    public int Age
    {
        get => _age;
        set
        {
            if (value < 0 || value > 150)
                throw new ArgumentOutOfRangeException(nameof(value));
            _age = value;
        }
    }
}
```

✔️ حالا:
- داده پنهان شده
- تغییر فقط از طریق کنترل‌شده انجام می‌شود
- خطاهای معنادار گزارش می‌شود

---

### ۳. پنهان‌سازی پیاده‌سازی (Implementation Hiding)

هدف: پنهان کردن نحوهٔ انجام کارها.

فرض کنید یک کلاس دارد لاگ تراکنش‌ها را نگه می‌دارد:

```csharp
public class TransactionLogger
{
    private List<string> _logs = new();

    // ❌ اشتباه: لو دادن لیست داخلی
    // public List<string> GetLogs() => _logs;

    // ✅ درست: فقط خواندنی و غیرقابل تغییر
    public IReadOnlyCollection<string> GetLogs() => _logs.AsReadOnly();
}
```

📌 **نکته مهم**:  
بازگرداندن `List<T>` یعنی کاربر می‌تواند `_logs.Add("...");` بزند و ساختار داخلی را خراب کند!

✅ استفاده از `IReadOnlyCollection<T>` یا `IEnumerable<T>` → **پیاده‌سازی پنهان می‌ماند**.

---

## 🔹 راهکارهای عملی در C#

### ✅ ۱. همیشه فیلدها را `private` کنید
```csharp
private decimal _balance;
private readonly List<string> _transactionHistory = new();
```

### ✅ ۲. از Properties برای کنترل دسترسی استفاده کنید
```csharp
public decimal Balance => _balance; // فقط خواندنی
```

### ✅ ۳. از `readonly` برای فیلدهای ثابت استفاده کنید
```csharp
private readonly string _accountNumber;
```

### ✅ ۴. از Interface برای جداسازی استفاده کنید
```csharp
public interface IEmailService
{
    void Send(string to, string body);
}

internal class SmtpEmailService : IEmailService { ... } // پنهان
```

### ✅ ۵. از `record` برای داده‌های Immutable استفاده کنید
```csharp
public record CustomerDto(string Name, string Email);
// داده پنهان و غیرقابل تغییر
```

---

## 🔹 مثال کامل: BankAccount

```csharp
public class BankAccount
{
    private decimal _balance;
    private readonly List<string> _transactions = new();
    private readonly string _accountNumber;

    public string AccountNumber => _accountNumber;
    public decimal Balance => _balance;

    public BankAccount(string accountNumber)
    {
        _accountNumber = accountNumber ?? throw new ArgumentNullException();
    }

    public void Deposit(decimal amount)
    {
        ValidatePositiveAmount(amount);
        _balance += amount;
        AddTransaction($"+{amount:C}");
    }

    public void Withdraw(decimal amount)
    {
        ValidatePositiveAmount(amount);
        if (amount > _balance)
            throw new InvalidOperationException("موجودی کافی نیست.");

        _balance -= amount;
        AddTransaction($"-{amount:C}");
    }

    public IReadOnlyCollection<string> GetTransactionHistory() =>
        _transactions.AsReadOnly();

    // --- Methods Hidden from Users ---

    private void ValidatePositiveAmount(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("مبلغ باید بیشتر از صفر باشد.");
    }

    private void AddTransaction(string description)
    {
        _transactions.Add($"{DateTime.Now:yyyy-MM-dd HH:mm} | {description}");
    }
}
```

### 🔍 تحلیل از دید Information Hiding:
- `_balance`, `_transactions`: **پنهان**
- `Balance`: فقط خواندنی
- `Deposit`/`Withdraw`: تنها راه تغییر موجودی
- `GetTransactionHistory`: فقط نسخه غیرقابل تغییر
- `Validate...` و `AddTransaction`: **داخلی و پنهان**

---

## 🔹 بهترین روش‌ها (Best Practices)

| روش | توضیح |
|-----|-------|
| ✅ فیلدها همیشه `private` | مگر دلیل قوی وجود داشته باشد |
| ✅ استفاده از `readonly` | برای فیلدهای ثابت |
| ✅ بازگردانی `IReadOnlyXXX` | نه `List<T>` |
| ✅ اعتبارسنجی در setterها و متدها | قبل از تغییر داده |
| ✅ استفاده از `internal` برای کامپوننت‌های داخلی | نه `public` |
| ✅ عدم نمایش implementation details | مثل نوع داده یا الگوریتم |

---

## 🔹 اشتباهات رایج

| اشتباه | مشکل |
|--------|------|
| ❌ `public int Age;` | دسترسی بدون کنترل |
| ❌ `public List<string> Logs;` | لو دادن کنترل به داده داخلی |
| ❌ تغییر مستقیم فیلدها در کلاس‌های دیگر | شکستن encapsulation |
| ❌ عدم اعتبارسنجی | داده نامعتبر وارد سیستم می‌شود |
| ❌ expose internal logic | مثل `public void SortInternalList()` |

---

## 🔹 جمع‌بندی

- **Information Hiding** یعنی: *"کمترین اطلاعات لازم را نشان بده."*
- در C# با این ابزارها پیاده‌سازی می‌شود:
  - `private` fields
  - properties با اعتبارسنجی
  - بازگردانی انواع غیرقابل تغییر (`IReadOnlyCollection`)
  - استفاده از interface
- این اصل باعث می‌شود کد شما:
  - قابل نگهداری‌تر
  - امن‌تر
  - انعطاف‌پذیرتر
  - قابل تست‌تر
  - کمتر مستعد باگ باشد

📌 **نکته نهایی**:  
> "اگر نیاز نیست کسی چیزی ببیند، آن را پنهان کن."

---

## 🔹 منابع معتبر

1. **Microsoft Learn – C# Access Modifiers**  
   👉 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)

2. **David L. Parnas – "On the Criteria To Be Used in Decomposing Systems into Modules" (1972)**  
   👉 [https://www.cs.umd.edu/class/spring2003/cmsc838p/Design/criteria.pdf](https://www.cs.umd.edu/class/spring2003/cmsc838p/Design/criteria.pdf)  
   *مقاله بنیادین دربارهٔ Information Hiding*

3. **"Clean Code" by Robert C. Martin**  
   فصل ۶: Objects and Data Structures — توضیح دقیق دربارهٔ پنهان‌سازی

4. **"Agile Software Development, Principles, Patterns, and Practices" by Robert C. Martin**  
   شامل مباحث عمیق دربارهٔ طراحی کلاس و Information Hiding

5. **C# Language Specification (ECMA-334)**  
   تعریف رسمی access modifiers و member accessibility

6. **Stack Overflow & .NET Design Guidelines**  
   برای بررسی best practices و موارد عملی

---

