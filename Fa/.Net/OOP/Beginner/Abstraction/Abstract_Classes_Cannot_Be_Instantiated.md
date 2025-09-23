## 📖 فهرست مطالب

1. [مقدمه: چرا فقط روی "Cannot be Instantiated" تمرکز کنیم؟](#مقدمه-چرا-فقط-روی-cannot-be-instantiated-تمرکز-کنیم)
2. [مفهوم دقیق "Cannot be Instantiated" در C#](#مفهوم-دقیق-cannot-be-instantiated-در-c)
3. [چرا کامپایلر C# اجازه نمونه‌سازی از کلاس انتزاعی را نمی‌دهد؟](#چرا-کامپایلر-c-اجازه-نمونهسازی-از-کلاس-انتزاعی-را-نمیدهد)
4. [چه زمانی این خطا رخ می‌دهد؟ (با مثال کد)](#چه-زمانی-این-خطا-رخ-میدهد-با-مثال-کد)
5. [تفاوت با Interface و کلاس معمولی](#تفاوت-با-interface-و-کلاس-معمولی)
6. [راه‌های دور زدن این محدودیت — و چرا نباید!](#راههای-دور-زدن-این-محدودیت--و-چرا-نباید)
7. [خطاهای کامپایلر — کدهای CS0144 و CS0179](#خطاهای-کامپایلر--کدهای-cs0144-و-cs0179)
8. [چگونه از این خطا جلوگیری کنیم؟ (Best Practices)](#چگونه-از-این-خطا-جلوگیری-کنیم-best-practices)
9. [جمع‌بندی — نکات طلایی برای مبتدیان](#جمعبندی--نکات-طلایی-برای-مبتدیان)
10. [منابع معتبر — مستقیم از مایکروسافت و متخصصان C#](#منابع-معتبر--مستقیم-از-مایکروسافت-و-متخصصان-c)

---

## 1. مقدمه: چرا فقط روی "Cannot be Instantiated" تمرکز کنیم؟

در آموزش OOP، یکی از **اولین موانعی که مبتدیان با آن مواجه می‌شوند**، مواجهه با خطای:

> **Cannot create an instance of the abstract class 'ClassName'**

این خطا **عمدی** است — نه یک باگ، نه یک اشتباه کامپایلر. این یک **اصول طراحی OOP در C#** است.

هدف این راهنما:  
🔹 فقط روی **"Cannot be Instantiated"** تمرکز کنیم — نه کل مبحث کلاس انتزاعی.  
🔹 به زبان ساده و مستند، **چرا این اتفاق می‌افتد** و **چطور با آن کار کنیم**.  
🔹 مناسب برای **آموزش در ریپوزیتوری OOP شما** — با مثال‌های قابل اجرا و خطاها و راه‌حل‌ها.

---

## 2. مفهوم دقیق "Cannot be Instantiated" در C#

در C#، عبارت:

> **Cannot be Instantiated**

به معنای دقیق زیر است:

> ❌ **شما نمی‌توانید با استفاده از کلمه کلیدی `new`، یک شیء (Object) از یک کلاس انتزاعی بسازید.**

مثال:

```csharp
public abstract class Animal { }

class Program
{
    static void Main()
    {
        Animal a = new Animal(); // ❌ خطای کامپایلر!
    }
}
```

### 🔍 خطا دقیقاً چیست؟

کامپایلر C# به شما می‌گوید:

> **CS0144: Cannot create an instance of the abstract class or interface 'Animal'**

این یک **خطای کامپایلی (Compile-time Error)** است — یعنی برنامه **اصلاً کامپایل نمی‌شود**.

---

## 3. چرا کامپایلر C# اجازه نمونه‌سازی از کلاس انتزاعی را نمی‌دهد؟

### 🎯 سه دلیل اصلی (مستند از مایکروسافت):

#### ۱. **کلاس انتزاعی، یک "طرح کلی" است — نه یک شیء کامل**

طبق مستندات مایکروسافت:

> “An abstract class is intended only to be a base class of other classes.”  
> — [Microsoft Docs: Abstract Classes](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract)

ترجمه:  
> “کلاس انتزاعی فقط قرار است به عنوان کلاس پایه برای کلاس‌های دیگر استفاده شود.”

#### ۲. **ممکن است شامل اعضای ناتمام (Abstract Members) باشد**

مثال:

```csharp
public abstract class Animal
{
    public abstract void MakeSound(); // ❗ بدون بدنه!
}
```

اگر اجازه نمونه‌سازی داده شود:

```csharp
Animal a = new Animal();
a.MakeSound(); // 🤯 چه اتفاقی می‌افتد؟ متد بدون بدنه فراخوانی شده!
```

→ **رفتار تعریف‌نشده → Crash → خطرناک!**

#### ۳. **اجبار به استفاده صحیح از وراثت**

C# می‌خواهد مطمئن شود شما:

✅ ابتدا یک کلاس مشتق‌شده (فرزند) می‌سازید.  
✅ آن کلاس، تمام متدهای انتزاعی را پیاده‌سازی کرده است.  
✅ سپس از آن کلاس فرزند، نمونه می‌سازید.

این یک **گارانتی کامپایلری** برای جلوگیری از رفتارهای ناقص است.

---

## 4. چه زمانی این خطا رخ می‌دهد؟ (با مثال کد)

### ✅ حالت ۱: کلاس با `abstract` تعریف شده — حتی اگر خالی باشد!

```csharp
public abstract class EmptyClass { }

class Program
{
    static void Main()
    {
        var obj = new EmptyClass(); // ❌ CS0144
    }
}
```

> 🔥 نکته: حتی اگر هیچ عضو انتزاعی نداشته باشد، باز هم **Cannot be Instantiated**!

---

### ✅ حالت ۲: کلاس شامل متد انتزاعی

```csharp
public abstract class Calculator
{
    public abstract int Add(int a, int b);
}

class Program
{
    static void Main()
    {
        Calculator calc = new Calculator(); // ❌ CS0144
    }
}
```

---

### ✅ حالت ۳: کلاس انتزاعی با سازنده (Constructor)

```csharp
public abstract class Person
{
    public Person()
    {
        Console.WriteLine("Person created!");
    }
}

class Program
{
    static void Main()
    {
        Person p = new Person(); // ❌ CS0144 — حتی با وجود سازنده!
    }
}
```

> ⚠️ داشتن سازنده، اجازه نمونه‌سازی نمی‌دهد! فقط برای استفاده در کلاس‌های فرزند است.

---

## 5. تفاوت با Interface و کلاس معمولی

| نوع | می‌توان نمونه ساخت؟ | دلیل |
|-----|----------------------|------|
| **کلاس معمولی** | ✅ بله | کامل است — همه متدها بدنه دارند. |
| **Interface** | ❌ خیر | فقط قرارداد است — بدون بدنه (تا C# 8.0 — البته حتی با بدنه هم نمی‌توان نمونه ساخت!) |
| **کلاس انتزاعی** | ❌ خیر | طرح کلی — ممکن است ناتمام باشد — فقط برای وراثت طراحی شده. |

> 💡 در C# 8.0 به بعد، Interface می‌تواند شامل متد با بدنه باشد — اما باز هم **Cannot be Instantiated**!

مثال:

```csharp
public interface IExample
{
    void Do() => Console.WriteLine("Default impl");
}

class Program
{
    static void Main()
    {
        IExample x = new IExample(); // ❌ CS0144 — همان خطا!
    }
}
```

→ پس **همه چیزهای "قراردادی" یا "ناتمام" در C#، غیرقابل نمونه‌سازی هستند**.

---

## 6. راه‌های دور زدن این محدودیت — و چرا نباید!

⚠️ **هشدار**: این روش‌ها فقط برای اطلاعات هستند — **استفاده از آن‌ها ضد الگوی طراحی OOP است**.

### 🚫 روش ۱: حذف کلمه `abstract`

```csharp
// قبل:
public abstract class Animal { }

// بعد — غیرمنطقی!
public class Animal { }
```

❌ مشکل: دیگر نمی‌توانید متد انتزاعی داشته باشید — و قرارداد شکسته می‌شود.

---

### 🚫 روش ۲: استفاده از Reflection (بدترین راه!)

```csharp
var obj = FormatterServices.GetUninitializedObject(typeof(Animal));
```

❌ مشکلات:  
- شیء بدون مقداردهی اولیه — خطرناک!  
- متد انتزاعی فراخوانی = خطا در زمان اجرا!  
- نقض اصول OOP — کد غیرقابل نگهداری.

> 🚫 هرگز در پروژه‌های واقعی این کار را نکنید!

---

## 7. خطاهای کامپایلر — کدهای CS0144 و CS0179

### ❌ CS0144: Cannot create an instance of the abstract class or interface

```csharp
Animal a = new Animal(); // خطای CS0144
```

✅ راه حل: از کلاس فرزند استفاده کنید:

```csharp
Animal a = new Dog(); // ✅ صحیح
```

---

### ❌ CS0179: Cannot call abstract method

این خطا زمانی می‌آید که **در کلاس فرزند، متد انتزاعی را بدون `override` پیاده‌سازی کنید**:

```csharp
public class Dog : Animal
{
    public void MakeSound() { } // ❌ CS0179 — باید override باشد
}
```

✅ راه حل:

```csharp
public override void MakeSound() { } // ✅
```

---

## 8. چگونه از این خطا جلوگیری کنیم؟ (Best Practices)

### ✅ ۱. همیشه از کلاس‌های مشتق‌شده استفاده کنید

```csharp
Animal a = new Dog(); // ✅
Shape s = new Circle(); // ✅
```

---

### ✅ ۲. کلاس انتزاعی را فقط برای وراثت طراحی کنید

اگر قرار است کلاس شما **مستقیماً مورد استفاده قرار گیرد** → **کلاس معمولی** بسازید.

اگر قرار است **رفتار کلی تعریف کند و توسط فرزندان کامل شود** → **کلاس انتزاعی**.

---

### ✅ ۳. از Factory Pattern برای ایجاد اشیاء استفاده کنید

```csharp
public static class AnimalFactory
{
    public static Animal CreateAnimal(string type)
    {
        return type switch
        {
            "dog" => new Dog(),
            "cat" => new Cat(),
            _ => throw new ArgumentException("Unknown animal")
        };
    }
}

// استفاده:
Animal a = AnimalFactory.CreateAnimal("dog"); // ✅ بدون دغدغه نمونه‌سازی
```

---

## 9. جمع‌بندی — نکات طلایی برای مبتدیان

🔹 **"Cannot be Instantiated" یک خطا نیست — یک ویژگی طراحی است.**  
🔹 **کامپایلر شما را از ساخت شیء ناقص محافظت می‌کند.**  
🔹 **حتی کلاس انتزاعی خالی هم غیرقابل نمونه‌سازی است.**  
🔹 **راه درست: همیشه از کلاس‌های فرزند (غیرانتزاعی) نمونه بسازید.**  
🔹 **هرگز با Reflection یا حذف `abstract` این محدودیت را دور نزنید.**  
🔹 **این قانون، در Interfaceها هم وجود دارد — پس عادت کنید!**

> 🎓 نکته آموزشی: به دانشجویانتان بگویید کلاس انتزاعی مثل "قالب سوال امتحان" است — نمی‌توان با آن نمره گرفت، مگر اینکه پاسخ‌ها (پیاده‌سازی) را داخلش بنویسند!

---

## 10. منابع معتبر — مستقیم از مایکروسافت و متخصصان C#

### 📚 مستندات رسمی مایکروسافت (Microsoft Learn)

1. **Abstract keyword**  
   🔗 https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract  
   > “An abstract class cannot be instantiated.”

2. **CS0144 - Compiler Error**  
   🔗 https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-messages/cs0144  
   > “You cannot create an instance of an abstract class or interface.”

3. **Abstract and Sealed Classes (Programming Guide)**  
   🔗 https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members

---

### 📖 کتاب‌های معتبر

- **"C# in Depth" — Jon Skeet**  
  فصل ۳: نحوه کار کامپایلر با کلاس‌های انتزاعی و محدودیت‌های آن.

- **"CLR via C#" — Jeffrey Richter**  
  فصل ۴: نحوه ایجاد شیء در CLR و چرا اشیاء از کلاس‌های انتزاعی ممنوع هستند.

---

### 🎥 دوره‌های آموزشی

- **Microsoft Learn: Object-Oriented Programming in C#**  
  🔗 https://learn.microsoft.com/en-us/training/paths/csharp-object-oriented-programming/

- **Pluralsight: C# Advanced - Abstract Classes and Members**  
  (نیاز به اشتراک — اما جامع‌ترین آموزش عملی)

