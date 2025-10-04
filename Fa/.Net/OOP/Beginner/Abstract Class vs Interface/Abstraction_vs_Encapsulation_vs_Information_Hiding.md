# 📘 تفاوت Abstraction، Encapsulation و Information Hiding در برنامه‌نویسی شیءگرا (OOP)

**برای کیست؟**\
این مقاله برای تازه‌واردانی است که قصد دارند پایه‌های محکمی در برنامه‌نویسی
شیءگرا (OOP) بسازند و مفاهیم اصلی آن را به‌خوبی درک کنند.

**چرا مهم است؟**\
این سه مفهوم به‌عنوان ستون‌های اصلی برنامه‌نویسی شیءگرا شناخته می‌شوند و در
طراحی کدهای تمیز، قابل نگهداری و مقیاس‌پذیر اهمیت بسیاری دارند. درک صحیح
این مفاهیم می‌تواند به جلوگیری از مشکلات رایج در نگهداری و گسترش نرم‌افزار
کمک کند.

**منابع معتبر:**

-   مستندات رسمی مایکروسافت
-   مستندات اوراکل
-   مقالات آکادمیک دیوید پارناس
-   مقالات آموزشی Martin Fowler
-   Stack Overflow و منابع معتبر دیگر

------------------------------------------------------------------------

## 🔗 فهرست مطالب

1.  [مقدمه: چرا این سه مفهوم را باید جدا
    کرد؟](#مقدمه-چرا-این-سه-مفهوم-را-باید-جدا-کرد)
2.  [Abstraction (انتزاع)](#abstraction-انتزاع)
3.  [Encapsulation (کپسوله‌سازی)](#encapsulation-کپسوله‌سازی)
4.  [Information Hiding (مخفی‌سازی
    اطلاعات)](#information-hiding-مخفی‌سازی-اطلاعات)
5.  [تفاوت‌های کلیدی در یک نگاه](#تفاوت‌های-کلیدی-در-یک-نگاه)
6.  [محدودیت‌ها و نکات هشداردهنده](#محدودیت‌ها-و-نکات-هشداردهنده)
7.  [جمع‌بندی و نتیجه‌گیری](#جمع‌بندی-و-نتیجه‌گیری)
8.  [منابع معتبر](#منابع-معتبر)

------------------------------------------------------------------------

## مقدمه: چرا این سه مفهوم را باید جدا کرد؟

در برنامه‌نویسی شیءگرا، **Abstraction**، **Encapsulation** و
**Information Hiding** سه اصل بنیادین هستند که اغلب با هم اشتباه گرفته
می‌شوند، اما هرکدام نقش منحصربه‌فردی در طراحی سیستم‌های نرم‌افزاری ایفا
می‌کنند. درک دقیق این تفاوت‌ها نه تنها به نوشتن کدهای بهتر کمک می‌کند بلکه
از مشکلات رایج در نگهداری و گسترش نرم‌افزار جلوگیری می‌نماید.

-   **Abstraction** بر ساده‌سازی تعامل با سیستم تمرکز دارد و جزئیات
    غیرضروری را پنهان می‌کند.\
-   **Encapsulation** داده‌ها و عملیات مرتبط را در یک واحد گردآوری کرده و
    دسترسی به آن‌ها را کنترل می‌کند.\
-   **Information Hiding** بر پنهان کردن تصمیمات طراحی که ممکن است تغییر
    کنند تأکید دارد تا وابستگی بین ماژول‌ها کاهش یابد.

------------------------------------------------------------------------

## 1. Abstraction (انتزاع)

### تعریف دقیق

**Abstraction** به معنای پنهان کردن جزئیات پیاده‌سازی پیچیده و ارائه تنها
ویژگی‌ها و رفتارهای ضروری به کاربر خارجی است. این اصل اجازه می‌دهد تا
کاربران با سیستم تعامل کنند بدون اینکه نیاز به درک مکانیسم‌های داخلی
داشته باشند.

> "Abstraction is the concept of exposing only the required essential
> characteristics and behavior of an object, hiding the unnecessary
> details."\
> --- Microsoft Learn: Object-Oriented Programming Concepts.

### چه مسئله‌ای را حل می‌کند؟

مشکل پیچیدگی سیستم‌های بزرگ. مثلا در سیستم پرداخت، کاربر نباید نگران
رمزنگاری یا ارتباط با بانک باشد.

### مثال عملی در C

``` csharp
public interface IPaymentProcessor
{
    bool ProcessPayment(decimal amount);
}

public class BankPaymentProcessor : IPaymentProcessor
{
    public bool ProcessPayment(decimal amount)
    {
        // جزئیات پیچیده: ارتباط با API بانک، رمزنگاری، ثبت لاگ...
        Console.WriteLine("Connecting to bank API...");
        Console.WriteLine("Encrypting data...");
        return true;
    }
}

class Program
{
    static void Main()
    {
        IPaymentProcessor processor = new BankPaymentProcessor();
        bool success = processor.ProcessPayment(100m);
        Console.WriteLine(success ? "✅ Payment succeeded!" : "❌ Failed!");
    }
}
```

------------------------------------------------------------------------

## 2. Encapsulation (کپسوله‌سازی)

### تعریف دقیق

**Encapsulation** به معنای بسته‌بندی داده‌ها و متدهای مرتبط در یک کلاس و
کنترل دسترسی با **private** و **public** است.

> "Encapsulation is the technique of making the fields in a class
> private and providing access to them via public methods."\
> --- Oracle Java Tutorials: What Is an Object?

### مثال عملی در C

``` csharp
public class BankAccount
{
    private decimal _balance;
    public decimal Balance => _balance;

    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Amount must be positive.");
        _balance += amount;
    }

    public void Withdraw(decimal amount)
    {
        if (amount <= 0 || amount > _balance)
            throw new InvalidOperationException("Invalid withdrawal.");
        _balance -= amount;
    }
}
```

------------------------------------------------------------------------

## 3. Information Hiding (مخفی‌سازی اطلاعات)

### تعریف دقیق

**Information Hiding** یعنی جزئیات پیاده‌سازی یک ماژول از سایر بخش‌ها
پنهان بماند تا تغییرات داخلی باعث وابستگی زیاد نشود.

> "Information hiding is the principle that design decisions likely to
> change should be hidden from other modules."\
> --- David Parnas, 1972

### مثال عملی در C

``` csharp
public interface IHasher
{
    string Hash(string input);
}

internal class SHA256Hasher : IHasher
{
    public string Hash(string input)
    {
        using var sha256 = System.Security.Cryptography.SHA256.Create();
        var bytes = sha256.ComputeHash(System.Text.Encoding.UTF8.GetBytes(input));
        return Convert.ToBase64String(bytes);
    }
}

public class UserService
{
    private readonly IHasher _hasher;
    public UserService(IHasher hasher) => _hasher = hasher;
    public string Register(string password) => _hasher.Hash(password);
}
```

------------------------------------------------------------------------

## تفاوت‌های کلیدی در یک نگاه

  --------------------------------------------------------------------------------------------
  مفهوم               تمرکز اصلی       ابزارهای پیاده‌سازی در C#            مثال
  ------------------- ---------------- ----------------------------------- -------------------
  **Abstraction**     ساده‌سازی تعامل   interface، abstract class           IPaymentProcessor
                      خارجی (What)                                         

  **Encapsulation**   بسته‌بندی داده و  private فیلدها، public متدها        BankAccount
                      عملیات (How to                                       
                      organize)                                            

  **Information       کاهش وابستگی     internal، interface                 SHA256Hasher
  Hiding**            (How to protect)                                     
  --------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## محدودیت‌ها و نکات هشداردهنده

-   **Abstraction** بیش از حد = پیچیدگی در سلسله‌مراتب کلاس‌ها\
-   **Encapsulation** نادرست = سختی در Debugging\
-   **Information Hiding** = دشواری در Unit Test

------------------------------------------------------------------------

## جمع‌بندی و نتیجه‌گیری

این سه اصل مکمل یکدیگر هستند و کمک می‌کنند نرم‌افزارهایی تمیز، ایزوله و
مقاوم در برابر تغییرات بسازیم.

------------------------------------------------------------------------

## منابع معتبر

-   [Microsoft Learn -- Object-Oriented
    Programming](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/)
-   [Oracle Java Tutorials -- What Is an
    Object?](https://docs.oracle.com/javase/tutorial/java/concepts/object.html)
-   [Parnas, D. L. (1972). On the Criteria To Be Used in Decomposing
    Systems into Modules](https://dl.acm.org/doi/10.1145/361598.361623)
-   [Martin Fowler --
    Encapsulation](https://martinfowler.com/bliki/Encapsulation.html)
-   [Stack Overflow -- Difference between Abstraction and
    Encapsulation](https://stackoverflow.com/questions/742341/difference-between-abstraction-and-encapsulation)
-   [GeeksforGeeks -- Difference between Abstraction and
    Encapsulation](https://www.geeksforgeeks.org/difference-between-abstraction-and-encapsulation-in-java-with-examples/)