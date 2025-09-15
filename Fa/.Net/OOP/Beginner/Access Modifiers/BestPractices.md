

## ✅ Best Practices برای استفاده از Access Modifiers

استفاده صحیح از دسترسی‌ها (Access Modifiers) یکی از مهم‌ترین جنبه‌های نوشتن کد تمیز، امن و قابل نگهداری در C# است. در ادامه به بهترین روش‌ها (Best Practices) برای استفاده از آن‌ها می‌پردازیم.

---

### 1. کوچک‌ترین دسترسی لازم را انتخاب کنید (Principle of Least Privilege)

> 🔐 **هر عضو فقط به حداقل دسترسی مورد نیاز برای عملکرد صحیح خود دسترسی داشته باشد.**

این اصل امنیتی و طراحی نرم‌افزار است که به کاهش خطا، افزایش امنیت و تسهیل تست کمک می‌کند.

- اگر یک فیلد فقط درون یک کلاس استفاده می‌شود → `private`
- اگر فقط فرزندان کلاس باید به آن دسترسی داشته باشند → `protected`
- اگر فقط درون پروژه استفاده می‌شود → `internal`

❌ اشتباه:
```csharp
public string Email;
```

✅ درست:
```csharp
private string _email;
public string Email 
{
    get => _email;
    private set => _email = value;
}
```

---

### 2. از `public` فقط برای APIهای عمومی استفاده کنید

اعضای `public` بخشی از **قرارداد عمومی کلاس (Public Contract)** هستند.  
هر تغییر در آن‌ها ممکن است باعث شکستن کد مشتریان (کاربران کتابخانه) شود.

#### ✅ زمان‌های مناسب برای `public`:
- Properties و متدهایی که کاربران کلاس باید از آن‌ها استفاده کنند
- کلاس‌های قابل استفاده در پروژه‌های دیگر
- Interfaceها و APIهای کتابخانه

#### ❌ زمان‌های نامناسب:
- فیلدهای داخلی
- متدهای کمکی (Helper)
- تنظیمات موقت یا تست

---

### 3. فیلدها را همیشه `private` یا `private set` تعریف کنید

> 🛡️ **همیشه از کپسول‌سازی (Encapsulation) استفاده کنید.**

مستقیماً فیلدها را `public` نکنید — حتی اگر فکر می‌کنید "فقط یک متغیر ساده است".

❌ ضد الگو:
```csharp
public int Age;
```

✅ الگوی صحیح:
```csharp
private int _age;
public int Age 
{
    get => _age;
    set 
    {
        if (value < 0) throw new ArgumentException("Age cannot be negative.");
        _age = value;
    }
}
```

یا در حالت ساده:
```csharp
public int Age { get; private set; }
```

---

### 4. برای کلاس‌های داخلی از `internal` استفاده کنید

اگر یک کلاس فقط برای استفاده درون یک پروژه (ensamble) طراحی شده، نباید `public` باشد.

```csharp
internal class FileLogger 
{
    public void Log(string message)
    {
        // فقط در این پروژه استفاده می‌شود
    }
}
```

> 💡 این کار از "نشت جزئیات پیاده‌سازی" (Leaky Abstraction) جلوگیری می‌کند.

---

### 5. از `protected` برای اعضای قابل گسترش در فرزندان استفاده کنید

وقتی می‌خواهید کلاس‌های فرزند بتوانند رفتار کلاس پایه را تغییر دهند یا توسعه دهند:

```csharp
public abstract class Vehicle
{
    protected string Brand { get; set; }

    protected abstract void StartEngine();

    protected virtual void NotifyStarted()
    {
        Console.WriteLine($"{Brand} engine started.");
    }
}
```

> ⚠️ از `protected` فقط وقتی استفاده کنید که واقعاً نیاز به دسترسی فرزندان دارید.

---

### 6. از `protected internal` با دقت استفاده کنید

این modifier ترکیبی از دو شرط است:
- یا در کلاس فرزند باشد (در هر پروژه)
- یا در همان ensamble باشد

مناسب برای کتابخانه‌هایی که می‌خواهید فرزندان خارجی هم بتوانند از بعضی اعضا استفاده کنند.

```csharp
protected internal string InternalId;
```

---

### 7. از `private protected` در کتابخانه‌های SDK استفاده کنید

وقتی یک کتابخانه عمومی توسعه می‌دهید و نمی‌خواهید کلاس‌های فرزند در پروژه‌های دیگر به یک عضو دسترسی داشته باشند:

```csharp
private protected void OnAuthenticationFailed();
```

> ✅ فقط فرزندان **در همان پروژه** می‌توانند از آن استفاده کنند.

---

### 8. متدهای کمکی (Helper) را `private` نگه دارید

هر متدی که فقط برای پیاده‌سازی داخلی کلاس استفاده می‌شود، باید `private` باشد.

```csharp
public class OrderProcessor
{
    public void Process(Order order)
    {
        if (Validate(order))
        {
            ApplyDiscounts(order);
            SendConfirmation(order);
        }
    }

    private bool Validate(Order order) { /* ... */ }
    private void ApplyDiscounts(Order order) { /* ... */ }
    private void SendConfirmation(Order order) { /* ... */ }
}
```

> 🧹 این کار کلاس را تمیز و قابل فهم نگه می‌دارد.

---

### 9. از `public` برای ثابت‌ها (Constants) با احتیاط استفاده کنید

اگر یک `const` یا `static readonly` عمومی است، تغییر آن ممکن است باعث شکستن کد مشتریان شود.

✅ مناسب:
```csharp
public const string DateFormat = "yyyy-MM-dd";
```

🚫 غیرضروری:
```csharp
public const string TempPath = "C:\\Temp"; // بهتر است تنظیم شود یا internal باشد
```

---

### 10. در کتابخانه‌های عمومی، دسترسی‌ها را با طرح معماری هماهنگ کنید

- کلاس‌های اصلی: `public`
- پیاده‌سازی‌های داخلی: `internal`
- متدهای پایه برای ارث‌بری: `protected`
- اعضای اختصاصی برای توسعه درونی: `private protected`

---

## ✅ خلاصه دستورالعمل‌ها

| موقعیت | Access Modifier پیشنهادی |
|--------|--------------------------|
| فیلد یا متد داخلی | `private` |
| قابل استفاده توسط فرزندان | `protected` |
| فقط در همان پروژه | `internal` |
| فرزندان یا همان پروژه | `protected internal` |
| فرزندان در همان پروژه | `private protected` |
| API عمومی | `public` (با احتیاط) |

---

✅ **هدف نهایی:**  
نوشتن کدی که:
- امن باشد
- تغییرات آن کمترین تأثیر را روی دیگران بگذارد
- قابل تست و نگهداری باشد
- اصول OOP (به ویژه Encapsulation) را رعایت کند

---
