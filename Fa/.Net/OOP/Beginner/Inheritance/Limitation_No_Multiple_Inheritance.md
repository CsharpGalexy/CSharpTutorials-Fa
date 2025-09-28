# عدم پشتیبانی از ارث‌بری چندگانه (Multiple Inheritance) در C#

## فهرست مطالب
- [چرا C# از ارث‌بری چندگانه پشتیبانی نمی‌کند؟](#چرا-c-از-ارث‌بری-چندگانه-پشتیبانی-نمیکند)  
- [ارث‌بری تک‌بازه (Single Inheritance) در عمل](#ارثبری-تکبازه-single-inheritance-در-عمل)  
- [جایگزین ارث‌بری چندگانه: استفاده از اینترفیس‌ها](#جایگزین-ارثبری-چندگانه-استفاده-از-اینترفیسها)  
- [مشکل الماس (Diamond Problem) چیست؟](#مشکل-الماس-diamond-problem-چیست)  
- [محدودیت‌های راه‌حل با اینترفیس‌ها](#محدودیتهای-راهحل-با-اینترفیسها)  
- [الگوهای جایگزین: ترکیب (Composition) به جای ارث‌بری](#الگوهای-جایگزین-ترکیب-composition-به-جای-ارثبری)  
- [خلاصه و نتیجه‌گیری](#خلاصه-و-نتیجهگیری)  
- [منابع معتبر](#منابع-معتبر)  

---

## چرا C# از ارث‌بری چندگانه پشتیبانی نمی‌کند؟
در C# هر کلاس تنها می‌تواند از **یک کلاس پایه** ارث‌بری کند. به این ساختار، *ارث‌بری تک‌بازه* (Single Inheritance) گفته می‌شود.  

طراحان زبان عمداً این تصمیم را گرفته‌اند تا:  
- پیچیدگی کد کاهش یابد،  
- از ابهامات و خطاهای رایج در زبان‌هایی مثل ++C جلوگیری شود،  
- و نگهداری پروژه‌ها ساده‌تر باشد.  

یکی از مهم‌ترین مشکلات ارث‌بری چندگانه، **مشکل الماس (Diamond Problem)** است که در ادامه آن را بررسی می‌کنیم.  

---

## ارث‌بری تک‌بازه (Single Inheritance) در عمل
با ارث‌بری تک‌بازه، یک کلاس فرزند می‌تواند رفتارها و ویژگی‌های کلاس پایه را به ارث ببرد.  

**هدف:** نشان دادن استفاده‌ی ساده از ارث‌بری یک‌پایه.  

```csharp
public class Animal
{
    public void Eat()
    {
        Console.WriteLine("Animal is eating.");
    }
}

public class Dog : Animal
{
    public void Bark()
    {
        Console.WriteLine("Dog is barking.");
    }
}

class Program
{
    static void Main()
    {
        Dog dog = new Dog();
        dog.Eat();  // متد به ارث رسیده از Animal
        dog.Bark(); // متد اختصاصی Dog
    }
}
```

**تحلیل کد:**  
- `Dog` تنها از `Animal` ارث‌بری کرده است.  
- به همین دلیل، هم می‌تواند از متد `Eat()` کلاس پایه استفاده کند و هم متد `Bark()` مخصوص به خودش را داشته باشد.  
- اگر امکان ارث‌بری از چند کلاس وجود داشت، احتمال داشت کلاس فرزند با متدهای هم‌نام یا پیاده‌سازی‌های متناقض روبه‌رو شود.  

---
## جایگزین ارث‌بری چندگانه: استفاده از اینترفیس‌ها
در C# به‌جای ارث‌بری از چندین کلاس، می‌توان از **اینترفیس‌ها (Interfaces)** استفاده کرد.  

اینترفیس‌ها اجازه می‌دهند یک کلاس چندین قرارداد رفتاری را پیاده‌سازی کند، بدون اینکه مشکلات ارث‌بری چندگانه ایجاد شود.  

**هدف:** شبیه‌سازی رفتارهای پرواز و شنا برای یک کلاس.  

```csharp
public interface IFlyable
{
    void Fly();
}

public interface ISwimmable
{
    void Swim();
}

public class Duck : IFlyable, ISwimmable
{
    public void Fly()
    {
        Console.WriteLine("Duck is flying.");
    }

    public void Swim()
    {
        Console.WriteLine("Duck is swimming.");
    }
}

class Program
{
    static void Main()
    {
        Duck duck = new Duck();
        duck.Fly();  
        duck.Swim(); 
    }
}
```

**تحلیل کد:**  
- `Duck` دو اینترفیس `IFlyable` و `ISwimmable` را پیاده‌سازی کرده است.  
- هر اینترفیس فقط تعریف قرارداد (امضای متد) را دارد و `Duck` مسئول پیاده‌سازی آن‌ها است.  
- این روش، رفتار چندگانه را بدون تداخل فراهم می‌کند.  

---
## مشکل الماس (Diamond Problem) چیست؟
فرض کنید زبان C# اجازه می‌داد یک کلاس از چند کلاس ارث‌بری کند:  

```
   A
  /  B   C
  \ /
   D
```

- کلاس `A` متدی به نام `DoSomething()` دارد.  
- کلاس‌های `B` و `C` هر دو از `A` ارث‌بری می‌کنند و این متد را بازنویسی می‌کنند.  
- کلاس `D` همزمان از `B` و `C` ارث‌بری می‌کند.  

اکنون وقتی `D.DoSomething()` فراخوانی شود، مشخص نیست کدام نسخه باید اجرا شود (نسخه `B` یا نسخه `C`). این ابهام همان **مشکل الماس** است.  

C# با ممنوع کردن ارث‌بری چندگانه، از بروز چنین مشکلاتی جلوگیری می‌کند.  

---
## محدودیت‌های راه‌حل با اینترفیس‌ها
اینترفیس‌ها جایگزین قدرتمندی هستند، اما محدودیت‌هایی دارند:  

1. **عدم پشتیبانی از وضعیت (State)**  
   اینترفیس‌ها فیلد یا داده ذخیره نمی‌کنند. بنابراین اشتراک داده بین کلاس‌ها از طریق اینترفیس امکان‌پذیر نیست.  

2. **تکرار کد**  
   اگر چند کلاس نیازمند پیاده‌سازی مشابهی باشند، مجبور به نوشتن کد تکراری خواهید شد. (برای رفع این مشکل معمولاً از کلاس‌های پایه یا *composition* استفاده می‌شود).  

3. **ابهام در متدهای پیش‌فرض (C# 8.0+)**  
   اگر دو اینترفیس متدی با امضای یکسان و پیاده‌سازی پیش‌فرض داشته باشند، کامپایلر خطا می‌دهد و شما باید صریحاً مشخص کنید کدام نسخه استفاده شود:  

   ```csharp
   public interface IA { void DoWork() { Console.WriteLine("A"); } }
   public interface IB { void DoWork() { Console.WriteLine("B"); } }

   public class Test : IA, IB
   {
       void IA.DoWork() => Console.WriteLine("From A");
       void IB.DoWork() => Console.WriteLine("From B");
   }
   ```

   این اجباری بودن انتخاب، عملاً مانع از بروز مشکل الماس می‌شود.  

---
## الگوهای جایگزین: ترکیب (Composition) به جای ارث‌بری
الگوی **ترکیب** (Composition) یکی از بهترین راه‌ها برای پرهیز از پیچیدگی ارث‌بری است.  

به‌جای اینکه بگوییم یک کلاس «است» (is-a) کلاس دیگر، می‌گوییم «دارد» (has-a).  

**هدف:** ترکیب رفتار پرواز و شنا در کلاس Duck.  

```csharp
public class FlyBehavior
{
    public void Fly() => Console.WriteLine("Flying with wings.");
}

public class SwimBehavior
{
    public void Swim() => Console.WriteLine("Swimming in water.");
}

public class Duck
{
    private readonly FlyBehavior _fly = new FlyBehavior();
    private readonly SwimBehavior _swim = new SwimBehavior();

    public void PerformFly() => _fly.Fly();
    public void PerformSwim() => _swim.Swim();
}

class Program
{
    static void Main()
    {
        Duck duck = new Duck();
        duck.PerformFly();
        duck.PerformSwim();
    }
}
```

**تحلیل کد:**  
- کلاس `Duck` نه از چند کلاس ارث‌بری کرده و نه از اینترفیس استفاده کرده است.  
- بلکه با *ترکیب* دو کلاس `FlyBehavior` و `SwimBehavior` رفتارهای مورد نیاز را کسب کرده است.  
- این روش انعطاف‌پذیری بالایی دارد و تغییر یا جایگزینی رفتارها در آینده ساده‌تر خواهد بود.  

---
## خلاصه و نتیجه‌گیری
- C# برای ساده‌سازی و جلوگیری از مشکلاتی مثل **مشکل الماس**، ارث‌بری چندگانه را پشتیبانی نمی‌کند.  
- به جای آن، اینترفیس‌ها و ترکیب (Composition) به عنوان راه‌حل‌های اصلی معرفی شده‌اند.  
- هرچند اینترفیس‌ها محدودیت‌هایی دارند، اما با طراحی درست می‌توان کدی قابل نگهداری، تمیز و مقیاس‌پذیر نوشت.  
- ترکیب در بسیاری از موارد انتخابی بهتر از ارث‌بری است و انعطاف‌پذیری بیشتری ارائه می‌دهد.  

---

## منابع معتبر
- [Microsoft Learn: Inheritance in C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance)  
- [Microsoft Learn: Interfaces](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/interfaces)  
- [Microsoft Learn: Default Interface Methods (C# 8.0+)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-8.0/default-interface-methods)  
- [Microsoft Learn: Composition over Inheritance](https://learn.microsoft.com/en-us/previous-versions/msp-n-p/ff921345(v=pandp.10))  
- [C# Language Specification – Inheritance](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#inheritance)  
