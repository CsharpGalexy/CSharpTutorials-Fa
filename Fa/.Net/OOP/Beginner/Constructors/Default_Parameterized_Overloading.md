<div dir="rtl">



## 📚 فهرست مطالب

1. [🔹 ۱. Default Constructor (سازنده پیش‌فرض)](#-۱-default-constructor-سازنده-پیشفرض)
   - [🟢 سطح مقدماتی](#-سطح-مقدماتی-1)
   - [🟡 سطح متوسط](#-سطح-متوسط-1)
   - [🔴 سطح حرفه‌ای](#-سطح-حرفهای-1)

2. [🔹 ۲. Parameterized Constructor (سازنده پارامتردار)](#-۲-parameterized-constructor-سازنده-پارامتردار)
   - [🟢 سطح مقدماتی](#-سطح-مقدماتی-2)
   - [🟡 سطح متوسط](#-سطح-متوسط-2)
   - [🔴 سطح حرفه‌ای](#-سطح-حرفهای-2)

3. [🔹 ۳. Constructor Overloading (بازتعریف سازنده)](#-۳-constructor-overloading-بازتعریف-سازنده)
   - [🟢 سطح مقدماتی](#-سطح-مقدماتی-3)
   - [🟡 سطح متوسط](#-سطح-متوسط-3)
   - [🔴 سطح حرفه‌ای](#-سطح-حرفهای-3)

4. [🔚 جمع‌بندی نهایی](#-جمعبندی-نهایی-فقط-این-سه-مورد)

5. [📋 چک‌لیست عملی: بهترین روش‌ها](#-چکلیست-بهترین-روشها-برای-سازندهها-فقط-default-parameterized-overloading)
   - [🟦 ۱. وقتی از Default Constructor استفاده کنید](#-۱-وقتی-از-default-constructor-استفاده-کنید)
   - [🟨 ۲. وقتی از Parameterized Constructor استفاده کنید](#-۲-وقتی-از-parameterized-constructor-استفاده-کنید)
   - [🟥 ۳. وقتی از Constructor Overloading استفاده کنید](#-۳-وقتی-از-constructor-overloading-استفاده-کنید)
   - [🔧 چک‌لیست فنی قبل از کامیت کد!](#-چکلیست-فنی-قبل-از-کامیت-کد)
   - [💡 جمع‌بندی نهایی (همه چیز در یک خط!)](#-جمعبندی-نهایی-همه-چیز-در-یک-خط)

</div>


---

## 🔹 ۱. Default Constructor (سازنده پیش‌فرض)

### 🟢 سطح مقدماتی 1:
- سازنده‌ای است که **بدون پارامتر** است.
- وقتی شما **هیچ سازنده‌ای ننویسید**، کامپایلر به صورت خودکار یک سازنده پیش‌فرض اضافه می‌کند.
- این سازنده، تمام متغیرها را به مقدار پیش‌فرض آن نوع مقداردهی می‌کند (مثلاً `0` برای `int`، `null` برای رشته).

#### مثال:
```csharp
public class Car
{
    public string Model;
    public int Year;
}

// استفاده:
Car myCar = new Car(); // Default Constructor فراخوانی می‌شود
```

در اینجا:
- `Model = null`
- `Year = 0`

> ⚠️ اگر تو کلاس **هر سازنده دیگری بنویسی** (مثل پارامتردار)، کامپایلر دیگر **سازنده پیش‌فرض را اضافه نمی‌کند**.

---

### 🟡 سطح متوسط 1:
شما می‌تونید **دستی** یک سازنده پیش‌فرض بنویسید — حتی اگر سازنده‌های دیگری هم داشته باشید.

#### مثال:
```csharp
public class Car
{
    public string Model;
    public int Year;

    // سازنده پیش‌فرض دستی
    public Car()
    {
        Model = "Unknown";
        Year = 2000;
    }

    public Car(string model, int year)
    {
        Model = model;
        Year = year;
    }
}
```

حالا می‌تونی هم `new Car()` و هم `new Car("BMW", 2023)` بنویسی.

---

### 🔴 سطح حرفه‌ای 1:
استفاده از سازنده پیش‌فرض در کلاس‌هایی که قراره **سریالایز (Serialize)** بشن (مثل JSON، XML) مهم است.

#### مثال: Newtonsoft.Json
برخی کتابخانه‌های سریالایز کردن، نیاز دارند که کلاس یک **سازنده پیش‌فرض عمومی (public default constructor)** داشته باشد تا بتوانند شیء را بسازند.

```csharp
public class User
{
    public string Name { get; set; }
    public int Age { get; set; }

    // بدون این، بعضی سریالایزرها مشکل می‌کنند
    public User() { } 

    public User(string name, int age)
    {
        Name = name;
        Age = age;
    }
}
```

> 💡 نکته: در **.NET 6+** و با `System.Text.Json`، گاهی می‌شه بدون سازنده پیش‌فرض کار کرد (با reflection)، ولی وجود آن **امن‌تر و قابل اعتمادتر** است.

---

## 🔹 ۲. Parameterized Constructor (سازنده پارامتردار)

### 🟢 سطح مقدماتی 2:
- سازنده‌ای که **پارامتر دارد**.
- برای مقداردهی اولیه شیء با مقادیر مشخص استفاده می‌شود.

#### مثال:
```csharp
public class Student
{
    public string Name;
    public int Id;

    public Student(string name, int id)
    {
        Name = name;
        Id = id;
    }
}

// استفاده:
Student s = new Student("Ali", 123);
```

---

### 🟡 سطح متوسط 2:
می‌تونی از **مقدار پیش‌فرض برای پارامترها** استفاده کنی (در C#).

#### مثال:
```csharp
public Student(string name, int id, string major = "General")
{
    Name = name;
    Id = id;
    Major = major;
}
```

حالا:
```csharp
var s1 = new Student("Ali", 123);           // major = "General"
var s2 = new Student("Reza", 456, "CS");    // major = "CS"
```

> ⚠️ این قابلیت در Java **وجود ندارد**، فقط در C# داریم.

---

### 🔴 سطح حرفه‌ای 2:
در سازنده پارامتردار، **اعتبارسنجی (Validation)** حتماً لازم است.

#### مثال حرفه‌ای:
```csharp
public class BankAccount
{
    public string AccountNumber { get; }
    public decimal Balance { get; private set; }

    public BankAccount(string accountNumber, decimal initialBalance)
    {
        if (string.IsNullOrWhiteSpace(accountNumber))
            throw new ArgumentException("Account number is required.");

        if (accountNumber.Length != 10)
            throw new ArgumentException("Account number must be 10 digits.");

        if (initialBalance < 0)
            throw new ArgumentException("Initial balance cannot be negative.");

        AccountNumber = accountNumber;
        Balance = initialBalance;
    }
}
```

✅ این کار اطمینان می‌دهد که شیء **همیشه در حالت معتبر** ساخته می‌شود.

---

## 🔹 ۳. Constructor Overloading (بازتعریف سازنده)

### 🟢 سطح مقدماتی 3:
- به معنی داشتن **چندین سازنده** در یک کلاس با **تعداد یا نوع پارامترهای مختلف** است.
- کامپایلر بر اساس آرگومان‌هایی که می‌دهی، تشخیص می‌دهد کدام سازنده فراخوانی شود.

#### مثال:
```csharp
public class Rectangle
{
    public double Width;
    public double Height;

    public Rectangle()
    {
        Width = 1;
        Height = 1;
    }

    public Rectangle(double side)
    {
        Width = side;
        Height = side;
    }

    public Rectangle(double width, double height)
    {
        Width = width;
        Height = height;
    }
}
```

استفاده:
```csharp
var r1 = new Rectangle();         // 1x1
var r2 = new Rectangle(5);        // 5x5 (مربع)
var r3 = new Rectangle(3, 4);     // 3x4
```

---

### 🟡 سطح متوسط 3:
استفاده از `this()` برای **فراخوانی یک سازنده از سازنده دیگر** — جلوگیری از تکرار کد.

#### مثال:
```csharp
public class Rectangle
{
    public double Width;
    public double Height;

    public Rectangle() : this(1, 1) { }

    public Rectangle(double side) : this(side, side) { }

    public Rectangle(double width, double height)
    {
        Width = width;
        Height = height;
    }
}
```

✅ تمام سازنده‌ها به آخرین سازنده منتقل می‌شوند.  
✅ اگر منطق مقداردهی عوض شد، فقط یک جا نیاز به تغییر داری.

> 🔔 قانون: `this(...)` باید اولین دستور در بدنه سازنده باشد.

---

### 🔴 سطح حرفه‌ای 3:
در مواقعی که **منطق ساخت پیچیده** است، overloading + `this()` + validation ترکیب قدرتمندی می‌سازد.

#### مثال پیشرفته:
```csharp
public class Email
{
    public string To { get; }
    public string Subject { get; }
    public string Body { get; }
    public DateTime SentAt { get; }

    // پیش‌فرض
    public Email() : this("no-reply@site.com", "No Subject", "Empty") { }

    // فقط گیرنده
    public Email(string to) : this(to, "No Subject", "Empty") { }

    // گیرنده و موضوع
    public Email(string to, string subject) : this(to, subject, "Empty") { }

    // کامل
    public Email(string to, string subject, string body)
    {
        if (!IsValidEmail(to))
            throw new ArgumentException("Invalid email address.");

        To = to;
        Subject = subject;
        Body = body;
        SentAt = DateTime.Now;
    }

    private bool IsValidEmail(string email)
    {
        try { return email.Contains("@") && email.Contains("."); }
        catch { return false; }
    }
}
```

حالا می‌تونی:
```csharp
var e1 = new Email();
var e2 = new Email("ali@gmail.com");
var e3 = new Email("ali@gmail.com", "Hello!");
var e4 = new Email("ali@gmail.com", "Hi", "How are you?");
```

همه حالات پوشش داده شده، بدون تکرار کد، و با اعتبارسنجی.

---

## 🔚 جمع‌بندی نهایی (فقط این سه مورد)

| مفهوم | هدف اصلی | نکته مهم |
|-------|----------|---------|
| **Default Constructor** | ساخت شیء بدون آرگومان | اگر سازنده دیگری بنویسی، کامپایلر خودش اضافه نمی‌کنه |
| **Parameterized Constructor** | مقداردهی اولیه با ورودی | برای اعتبارسنجی و کنترل بهتر وضعیت اولیه |
| **Constructor Overloading** | انعطاف در ساخت شیء | با `this()` از تکرار کد جلوگیری کن |

---

💡 **قاعده طلایی**:  
> "هر شیء باید در لحظه تولد، در **حالت معتبر و کامل** باشد."  
> سازنده‌ها ابزار اصلی برای رسیدن به این هدف هستند.


🔹 **Default Constructor**  
🔹 **Parameterized Constructor**  
🔹 **Constructor Overloading**  

آورده‌ام — دقیقاً مثل یک راهنمای سریع و قابل استفاده در پروژه‌های واقعی.

---

# ✅ چک‌لیست: بهترین روش‌ها برای سازنده‌ها (فقط Default, Parameterized, Overloading)

## 🟦 ۱. وقتی از **Default Constructor** استفاده کنید

| ✅ باید | ❌ نباید |
|--------|--------|
| وقتی می‌خواهید شیء با مقادیر پیش‌فرض بساخته شود. | اگر کلاس نیاز به داده ضروری دارد (مثل ID، Name)، فقط به default constructor اکتفا نکنید. |
| وقتی کلاس قرار است سریالایز شود (JSON, XML, Database). | فرض نکنید کامپایلر همیشه آن را اضافه می‌کند — اگر سازنده دیگری دارید، خودتان بنویسید. |
| برای کلاس‌های DTO، Model، یا Entity در فریم‌ورک‌ها (مثل ASP.NET, EF). | از آن برای ایجاد شیء نامعتبر استفاده نکنید (مثلاً `User` بدون `Username`). |

> 💡 توصیه: اگر از overloading استفاده می‌کنید و می‌خواهید `new MyClass()` مجاز باشد، **حتماً دستی یک `public MyClass()` بنویسید**.

---

## 🟨 ۲. وقتی از **Parameterized Constructor** استفاده کنید

| ✅ باید | ❌ نباید |
|--------|--------|
| برای مقداردهی اجباری فیلدهای ضروری استفاده کنید. | بدون اعتبارسنجی (Validation) مقدار را قبول نکنید. |
| از `this.Name = name;` یا `Name = name;` برای اختصاص مقدار استفاده کنید. | پارامترها را بدون تغییر نام، با فیلدها هم‌نام کنید (مگر با `this.` یا `_`). |
| از پارامترهای با مقدار پیش‌فرض (در C#) برای انعطاف بیشتر استفاده کنید. | منطق پیچیده (مثل ذخیره در دیتابیس) در سازنده اجرا نکنید. |

### ✅ مثال خوب:
```csharp
public class Product
{
    public string Name { get; }
    public decimal Price { get; }

    public Product(string name, decimal price)
    {
        if (string.IsNullOrWhiteSpace(name))
            throw new ArgumentException("Name is required.");

        if (price < 0)
            throw new ArgumentOutOfRangeException("Price cannot be negative.");

        Name = name;
        Price = price;
    }
}
```

---

## 🟥 ۳. وقتی از **Constructor Overloading** استفاده کنید

| ✅ باید | ❌ نباید |
|--------|--------|
| وقتی می‌خواهید کاربر کلاس شما انعطاف در ساخت شیء داشته باشد. | تمام منطق را در چند سازنده تکرار نکنید. |
| حتماً از `this(...)` برای فراخوانی سازنده اصلی استفاده کنید. | بیش از ۴ سازنده ننویسید — نشانه‌ی نیاز به بازطراحی است. |
| سازنده‌های ساده‌تر را به سازنده کامل منتقل کنید. | تفاوت منطقی واضح بین سازنده‌ها نباشد (مثلاً یکی ثبت کند، یکی ذخیره!). |

### ✅ مثال ایده‌آل:
```csharp
public class Logger
{
    public string Source { get; }
    public LogLevel Level { get; }
    public bool IsEnabled { get; }

    // پیش‌فرض
    public Logger() : this("App") { }

    // فقط منبع
    public Logger(string source) : this(source, LogLevel.Info) { }

    // منبع و سطح
    public Logger(string source, LogLevel level) : this(source, level, true) { }

    // کامل
    public Logger(string source, LogLevel level, bool isEnabled)
    {
        Source = source ?? "Unknown";
        Level = level;
        IsEnabled = isEnabled;
    }
}
```

---

## 🔧 چک‌لیست فنی (قبل از کامیت کد!)

✅ قبل از استفاده از سازنده‌ها، این موارد را چک کن:

| مورد | وضعیت ✔️ / ❌ |
|------|----------------|
| آیا حداقل یک سازنده `public` دارم؟ | ☐ |
| اگر سازنده پارامتردار دارم، آیا `default constructor` هم لازمه؟ اگر بله، نوشته‌ام؟ | ☐ |
| آیا تمام سازنده‌های overload شده از `this(...)` استفاده می‌کنند؟ | ☐ |
| آیا در سازنده‌ها Validation انجام می‌دهم؟ (null check, range, etc.) | ☐ |
| آیا منطق پیچیده (مثل DB call, I/O) در سازنده ندارم؟ | ☐ |
| آیا بیش از ۳-۴ سازنده ندارم؟ اگر دارم، آیا به Factory نیاز دارم؟ | ☐ |

> ⚠️ اگر بیش از ۴ سازنده دارید → **الگوی Factory یا Builder** در نظر بگیرید.

---

## 💡 جمع‌بندی نهایی (همه چیز در یک خط!)

| مفهوم | قاعده طلایی |
|-------|-------------|
| **Default Constructor** | فقط وقتی لازم است که بخواهی بدون آرگومان شیء بسازی — و اگر سازنده دیگری داری، خودت بنویس. |
| **Parameterized Constructor** | برای ورودی‌های ضروری + اعتبارسنجی — همیشه شیء را معتبر بساز. |
| **Constructor Overloading** | با `this()` تکرار کد را حذف کن و انعطاف کاربر کلاس را بالا ببر. |

---
