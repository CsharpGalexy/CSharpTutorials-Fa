# Method Overriding در C#: virtual، override و sealed override

## فهرست مطالب
- [Method Overriding چیست و چرا مهم است؟](#method-overriding-چیست-و-چرا-مهم-است)
- [کلیدواژه‌های اصلی: virtual، override و sealed](#کلیدواژههای-اصلی-virtual-override-و-sealed)
- [مثال ۱: استفاده از virtual و override برای تغییر رفتار متد](#مثال-۱-استفاده-از-virtual-و-override-برای-تغییر-رفتار-متد)
- [مثال ۲: جلوگیری از بازنویسی بیشتر با sealed override](#مثال-۲-جلوگیری-از-بازنویسی-بیشتر-با-sealed-override)
- [تفاوت override با new (مخفی‌سازی متد)](#تفاوت-override-با-new-مخفی‌سازی-متد)
- [محدودیت‌ها و نکات طراحی](#محدودیتها-و-نکات-طراحی)
- [خلاصه و نتیجه‌گیری](#خلاصه-و-نتیجهگیری)
- [منابع معتبر](#منابع-معتبر)

---
## Method Overriding چیست و چرا مهم است؟
Method Overriding، که به آن بازنویسی متد نیز گفته می‌شود، یکی از اصول اساسی چندشکلی (Polymorphism) در برنامه‌نویسی شیءگرا است. این ویژگی به کلاس‌های مشتق‌شده (derived classes) اجازه می‌دهد تا پیاده‌سازی یک متد را که از کلاس پایه (base class) به ارث برده‌اند، تغییر دهند، در حالی که امضای متد (نام، پارامترها و نوع بازگشتی) دقیقاً یکسان باقی می‌ماند.

این مکانیزم اهمیت زیادی دارد زیرا امکان ایجاد سیستم‌های انعطاف‌پذیر و قابل گسترش را فراهم می‌کند. برای مثال، تصور کنید یک کلاس پایه به نام `Shape` دارید که متدی برای محاسبه مساحت تعریف کرده است. کلاس‌های مشتق‌شده مانند `Circle` یا `Rectangle` می‌توانند این متد را بازنویسی کنند تا محاسبه مساحت خاص خود را انجام دهند. در زمان اجرا، وقتی از یک اشاره‌گر به کلاس پایه استفاده می‌کنید، سیستم به طور خودکار پیاده‌سازی مناسب را بر اساس نوع واقعی شیء فراخوانی می‌کند. این رفتار، که به آن چندشکلی در زمان اجرا (runtime polymorphism) گفته می‌شود، کد را تمیزتر و نگهداری‌پذیرتر می‌کند و از تکرار کد جلوگیری می‌نماید.

---

## کلیدواژه‌های اصلی: virtual، override و sealed
در زبان C#، سه کلیدواژه کلیدی برای مدیریت بازنویسی متدها وجود دارد:

- **virtual**: این کلیدواژه در کلاس پایه استفاده می‌شود تا یک متد، پراپرتی، ایندکسر یا رویداد را به عنوان قابل بازنویسی مشخص کند. بدون آن، متد به طور پیش‌فرض غیرمجازی است و نمی‌توان آن را بازنویسی کرد.  
- **override**: در کلاس مشتق‌شده، این کلیدواژه برای ارائه پیاده‌سازی جدید یک متد virtual، یا `abstract` از کلاس پایه به کار می‌رود. این کار تضمین می‌کند که رفتار متد بر اساس نوع واقعی شیء در زمان اجرا تعیین شود.  
- **sealed override**: ترکیب `sealed` با `override`، پیاده‌سازی متد را در کلاس فعلی نهایی می‌کند و از بازنویسی بیشتر آن در کلاس‌های مشتق‌شده بعدی جلوگیری می‌نماید. این گزینه برای حفظ ثبات و جلوگیری از تغییرات ناخواسته مفید است.

توجه داشته باشید که تنها متدهای virtual، (`virtual`)، `abstract` یا `override`‌شده را می‌توان بازنویسی کرد. متدهای معمولی (non-virtual) قابل override نیستند و در صورت تلاش برای این کار، کامپایلر خطا می‌دهد.

---
## مثال ۱: استفاده از virtual و override برای تغییر رفتار متد
این مثال نشان می‌دهد چگونه یک متد مجازی در کلاس پایه تعریف کنیم و سپس آن را در کلاس مشتق‌شده بازنویسی نماییم تا رفتار متفاوتی ارائه دهد.  
**مسئله‌ای که حل می‌کند:** اجازه دادن به کلاس‌های مشتق‌شده برای سفارشی‌سازی رفتار بدون تغییر در رابط کاربری کلاس پایه.

```csharp
using System;

public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal makes a sound.");
    }

    public void Eat()
    {
        Console.WriteLine("Animal is eating.");
    }
}

public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Dog barks.");
    }
}

class Program
{
    static void Main()
    {
        Animal animal = new Animal();
        animal.Speak(); // خروجی: Animal makes a sound.

        Animal dog = new Dog(); // اشاره‌گر از نوع Animal، شیء از نوع Dog
        dog.Speak();    // خروجی: Dog barks. (چندشکلی در عمل)
        dog.Eat();      // خروجی: Animal is eating. (متد غیرمجازی)
    }
}
```

### تحلیل کد
متد `Speak` در کلاس `Animal` با `virtual` تعریف شده، بنابراین کلاس `Dog` می‌تواند آن را با `override` تغییر دهد. وقتی شیء `Dog` از طریق اشاره‌گر `Animal` فراخوانی می‌شود، سیستم نوع واقعی شیء را بررسی کرده و پیاده‌سازی `Dog` را اجرا می‌کند – این هسته چندشکلی است. در مقابل، متد `Eat` غیر virtual، است، پس همیشه نسخه کلاس پایه اجرا می‌شود، حتی اگر شیء مشتق‌شده باشد.

---
## مثال ۲: جلوگیری از بازنویسی بیشتر با sealed override
این مثال نشان می‌دهد چگونه از `sealed override` برای مسدود کردن تغییرات بیشتر در سلسله مراتب ارث‌برداری استفاده کنیم.  
**مسئله‌ای که حل می‌کند:** حفاظت از پیاده‌سازی خاص در برابر تغییرات ناخواسته در کلاس‌های پایین‌تر، که می‌تواند ثبات سیستم را حفظ کند.

```csharp
using System;

public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal makes a sound.");
    }
}

public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Dog barks.");
    }
}

public class Puppy : Dog
{
    public sealed override void Speak()
    {
        Console.WriteLine("Puppy softly barks.");
    }
}

public class BabyPuppy : Puppy
{
    // خطای کامپایل: نمی‌توان Speak را override کرد زیرا sealed است
    // public override void Speak() { Console.WriteLine("Baby puppy whimpers."); }
}

class Program
{
    static void Main()
    {
        BabyPuppy baby = new BabyPuppy();
        baby.Speak(); // خروجی: Puppy softly barks.
    }
}
```

### تحلیل کد
کلاس `Puppy` متد `Speak` را با `sealed override` بازنویسی می‌کند، که پیاده‌سازی آن را نهایی می‌نماید. این کار تضمین می‌کند که رفتار `Speak` در تمام کلاس‌های مشتق‌شده از `Puppy` تغییرناپذیر بماند. اگر سعی کنید در `BabyPuppy` این متد را override کنید، کامپایلر خطای **CS0239** تولید می‌کند.

---

## تفاوت override با new (مخفی‌سازی متد)
گاهی اوقات ممکن است بخواهید یک متد غیرمجازی را در کلاس مشتق‌شده با همان نام تعریف کنید. در این حالت، C# از مکانیزم مخفی‌سازی (method hiding) با کلیدواژه `new` استفاده می‌کند، نه بازنویسی.

```csharp
using System;

public class BaseClass
{
    public void Show()
    {
        Console.WriteLine("BaseClass Show");
    }
}

public class DerivedClass : BaseClass
{
    public new void Show()
    {
        Console.WriteLine("DerivedClass Show");
    }
}

class Program
{
    static void Main()
    {
        BaseClass baseObj = new DerivedClass();
        baseObj.Show(); // خروجی: BaseClass Show (بر اساس نوع اشاره‌گر)

        DerivedClass derivedObj = new DerivedClass();
        derivedObj.Show(); // خروجی: DerivedClass Show
    }
}
```

### تحلیل کد
با `override`، رفتار بر اساس نوع واقعی شیء در زمان اجرا تعیین می‌شود (چندشکلی).  
اما با `new`، رفتار بر اساس نوع اشاره‌گر در زمان کامپایل مشخص می‌گردد. بنابراین، وقتی از اشاره‌گر `BaseClass` استفاده می‌کنید، نسخه پایه فراخوانی می‌شود.  
`new` برای مخفی کردن متدهای غیرمجازی مفید است، اما جایگزین `override` نیست و چندشکلی واقعی ایجاد نمی‌کند.

---
## محدودیت‌ها و نکات طراحی
Method Overriding قدرتمند است، اما محدودیت‌هایی دارد که باید در طراحی کلاس‌ها مد نظر قرار گیرند:

1. **فقط متدهای مجازی یا abstract قابل override هستند**: متدهای `static`، `private` یا غیرمجازی را نمی‌توان بازنویسی کرد.  
2. **امضا باید دقیقاً یکسان باشد**: نام، پارامترها و نوع بازگشتی باید مطابقت کامل داشته باشند (با پشتیبانی از covariant return types در نسخه‌های جدید C#).  
3. **سطح دسترسی تغییرناپذیر است**: اگر متد پایه `public` باشد، متد override نیز باید `public` باشد؛ نمی‌توانید آن را به `protected` یا `private` تغییر دهید.  
4. **sealed فقط با override کار می‌کند**: نمی‌توانید یک متد را مستقیماً `sealed` کنید مگر اینکه آن را override کرده باشید.  
5. **نکات طراحی**: همه متدها را `virtual` نکنید، زیرا این کار کد را آسیب‌پذیر می‌کند. فقط متدهایی را مجازی کنید که واقعاً نیاز به سفارشی‌سازی دارند. در غیر این صورت، از ترکیب (composition) یا اینترفیس‌ها استفاده کنید.

### 🚨 نکته مهم: تفاوت با Method Overloading
> **Method Overriding را با Method Overloading اشتباه نگیرید.**  
> - در **Overloading** چند متد با نام یکسان ولی امضاهای متفاوت در یک کلاس تعریف می‌شوند و انتخاب متد در **زمان کامپایل** انجام می‌شود (Compile-time Polymorphism).  
> - در مقابل، **Overriding** به کلاس‌های مشتق‌شده اجازه می‌دهد پیاده‌سازی متدهای مجازی کلاس پایه را تغییر دهند و انتخاب متد در **زمان اجرا** انجام می‌شود (Runtime Polymorphism).  

---