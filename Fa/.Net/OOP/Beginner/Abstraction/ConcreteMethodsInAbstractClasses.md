

## 📖 فهرست مطالب

1. [مقدمه: کلاس چکیده (Abstract Class) چیست؟](#مقدمه-کلاس-چکیده-abstract-class-چیست)
2. [متدهای Concrete چیستند؟](#متدهای-concrete-چیستند)
3. [تفاوت Concrete Method با Abstract Method](#تفاوت-concrete-method-با-abstract-method)
4. [چرا در کلاس چکیده از Concrete Method استفاده کنیم؟](#چرا-در-کلاس-چکیده-از-concrete-method-استفاده-کنیم)
5. [مثال عملی در C#](#مثال-عملی-در-c)
6. [مزایای استفاده از Concrete Methods در کلاس‌های چکیده](#مزایای-استفاده-از-concrete-methods-در-کلاسهای-چکیده)
7. [نکات مهم و بهترین روش‌ها (Best Practices)](#نکات-مهم-و-بهترین-روشها-best-practices)
8. [جمع‌بندی](#جمعبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## 1. مقدمه: کلاس چکیده (Abstract Class) چیست؟

در C#، **کلاس چکیده** (Abstract Class) کلاسی است که نمی‌توان از آن نمونه (Instance) ساخت. هدف اصلی آن این است که به عنوان **پایه‌ای برای کلاس‌های فرزند** عمل کند.

کلاس چکیده می‌تواند شامل:

- **اعضای چکیده (Abstract Members)** — که باید در کلاس‌های فرزند پیاده‌سازی شوند.
- **اعضای Concrete (غیر چکیده)** — که رفتار کامل خود را دارند و در کلاس‌های فرزند **اختیاری** است override شوند.

> 🔹 کلیدواژه: `abstract` برای تعریف کلاس چکیده و متدهای چکیده استفاده می‌شود.

---

## 2. متدهای Concrete چیستند؟

**متدهای Concrete** (یا Non-Abstract Methods) متدهایی هستند که:

- **بدنه (Implementation) دارند.**
- در کلاس چکیده تعریف می‌شوند.
- **نیازی به override کردن در کلاس فرزند ندارند** (مگر اینکه `virtual` باشند و فرزند بخواهد رفتارش را تغییر دهد).

این متدها می‌توانند:

- `public`، `protected`، `private` و ... باشند.
- شامل منطق تجاری مشترک برای تمام فرزندان باشند.

---

## 3. تفاوت Concrete Method با Abstract Method

| ویژگی | Concrete Method | Abstract Method |
|-------|------------------|------------------|
| بدنه دارد؟ | ✅ بله | ❌ خیر |
| الزام override در فرزند | ❌ خیر (مگر virtual باشد) | ✅ بله |
| می‌تواند private باشد؟ | ✅ بله | ❌ خیر (چون باید override شود) |
| کلیدواژه | `virtual` یا بدون کلیدواژه | `abstract` |
| استفاده در کلاس چکیده | ✅ بله | ✅ بله |

> ⚠️ نکته: متد `abstract` فقط در کلاس `abstract` می‌تواند تعریف شود.

---

## 4. چرا در کلاس چکیده از Concrete Method استفاده کنیم؟

### ✅ دلایل اصلی:

1. **کد مشترک بین فرزندان**  
   مثلاً یک متد لاگ‌گیری، محاسبه فرمول ثابت، یا اعتبارسنجی اولیه.

2. **کاهش تکرار کد (DRY Principle)**  
   به جای تکرار کد در هر کلاس فرزند، آن را یک‌جا در کلاس پایه پیاده‌سازی می‌کنیم.

3. **ارائه رفتار پیش‌فرض**  
   بعضی متدها ممکن است در اکثر موارد یک رفتار ثابت داشته باشند و فقط در موارد خاص نیاز به override داشته باشند.

4. **افزایش کنترل و یکپارچگی منطق**  
   مثلاً یک متد `Save()` که ابتدا اعتبارسنجی می‌کند (`Validate()`) و سپس ذخیره می‌کند — `Validate()` می‌تواند Concrete باشد.

---

## 5. مثال عملی در C#

```csharp
using System;

// کلاس چکیده
public abstract class Animal
{
    // Concrete Method — رفتار کامل دارد
    public void Sleep()
    {
        Console.WriteLine("Animal is sleeping... Zzz");
    }

    // Concrete Method با قابلیت Override (virtual)
    public virtual void MakeSound()
    {
        Console.WriteLine("Some generic animal sound...");
    }

    // Abstract Method — باید در فرزندان پیاده‌سازی شود
    public abstract void Move();
}

// کلاس فرزند
public class Dog : Animal
{
    // الزامی: پیاده‌سازی Move
    public override void Move()
    {
        Console.WriteLine("Dog is running on four legs.");
    }

    // اختیاری: override کردن MakeSound
    public override void MakeSound()
    {
        Console.WriteLine("Woof! Woof!");
    }

    // Sleep به صورت پیش‌فرض ارث برده می‌شود — نیازی به override نیست
}

// کلاس فرزند دیگر
public class Bird : Animal
{
    public override void Move()
    {
        Console.WriteLine("Bird is flying.");
    }

    // MakeSound را override نکردیم — از رفتار پیش‌فرض استفاده می‌کند
}

// تست برنامه
class Program
{
    static void Main()
    {
        Dog dog = new Dog();
        dog.Sleep();      // خروجی: Animal is sleeping... Zzz
        dog.MakeSound();  // خروجی: Woof! Woof!
        dog.Move();       // خروجی: Dog is running on four legs.

        Bird bird = new Bird();
        bird.Sleep();     // خروجی: Animal is sleeping... Zzz
        bird.MakeSound(); // خروجی: Some generic animal sound... (پیش‌فرض)
        bird.Move();      // خروجی: Bird is flying.
    }
}
```

---

## 6. مزایای استفاده از Concrete Methods در کلاس‌های چکیده

✅ **کاهش تکرار کد** — منطق مشترک یک‌جا نوشته می‌شود.  
✅ **انعطاف‌پذیری** — فرزندان می‌توانند در صورت نیاز رفتار را تغییر دهند (با `virtual`).  
✅ **قابلیت نگهداری بالاتر** — تغییر در کلاس پایه، تمام فرزندان را تحت تأثیر قرار می‌دهد.  
✅ **پیاده‌سازی Template Method Pattern** — طراحی الگویی که در آن ساختار کلی الگوریتم در کلاس پایه تعریف می‌شود و جزئیات در فرزندان.

---

## 7. نکات مهم و بهترین روش‌ها (Best Practices)

🔹 **از `virtual` برای Concrete Methods استفاده کنید** اگر احتمال دارید فرزندان بخواهند آن را override کنند.  
🔹 **از `protected` برای متدهای داخلی استفاده کنید** — اگر می‌خواهید فقط کلاس‌های فرزند به آن دسترسی داشته باشند.  
🔹 **از `private` در کلاس چکیده خودداری کنید** — مگر برای متدهای کمکی داخلی که هیچ فرزندی نیازی به دسترسی ندارد.  
🔹 **از Concrete Methods برای منطق مشترک و پایدار استفاده کنید** — نه برای چیزهایی که احتمال تغییر بالایی دارند.  
🔹 **از Template Method Pattern استفاده کنید** — برای تعریف ساختار ثابت الگوریتم با امکان تغییر جزئیات در فرزندان.

---

## 8. جمع‌بندی

🔹 کلاس‌های چکیده در C# می‌توانند هم متدهای **چکیده** (نیازمند پیاده‌سازی در فرزند) و هم **Concrete** (با بدنه کامل) داشته باشند.  
🔹 Concrete Methods به شما اجازه می‌دهند **رفتار مشترک** را در کلاس پایه پیاده‌سازی کنید و از تکرار کد جلوگیری کنید.  
🔹 این متدها می‌توانند `virtual` باشند تا فرزندان بتوانند آن‌ها را **در صورت نیاز** بازنویسی کنند.  
🔹 استفاده هوشمندانه از Concrete Methods باعث **کد تمیزتر، قابل نگهداری‌تر و انعطاف‌پذیرتر** می‌شود.

---

## 9. منابع معتبر

1. 📘 **Microsoft Docs - Abstract Classes**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract)

2. 📘 **Microsoft Docs - Inheritance in C#**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance)

3. 📘 **C# in Depth — Jon Skeet** (فصل مربوط به OOP و Abstract Classes)  
   ISBN: 978-1617294532

4. 📘 **Head First Design Patterns — Template Method Pattern**  
   ISBN: 978-0596007126 — برای کاربرد Concrete Methods در طراحی الگوها

5. 📘 **Stack Overflow — Concrete Methods in Abstract Classes**  
   [https://stackoverflow.com/questions/2091766](https://stackoverflow.com/questions/2091766) — بحث‌های کاربردی و مثال‌های واقعی

6. 📘 **Pluralsight — C# Advanced Topics: Abstract Classes vs Interfaces**  
   [https://www.pluralsight.com](https://www.pluralsight.com) — دوره‌های عملی و عمیق

