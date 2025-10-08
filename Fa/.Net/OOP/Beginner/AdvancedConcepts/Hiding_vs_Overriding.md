# تفاوت بین Hiding و Overriding در برنامه‌نویسی شیءگرا (با مثال در C#)

------------------------------------------------------------------------

## فهرست مطالب

-   [مقدمه](#مقدمه)
-   [تعریف Overriding](#تعریف-overriding)
-   [تعریف Hiding (Method Hiding)](#تعریف-hiding-method-hiding)
-   [تفاوت‌های کلیدی: رفتار در زمان اجرا و زمان
    کامپایل](#تفاوت‌های-کلیدی-رفتار-در-زمان-اجرا-و-زمان-کامپایل)
-   [مثال عملی: Overriding در C#](#مثال-عملی-overriding-در-c)
-   [مثال عملی: Hiding در C#](#مثال-عملی-hiding-در-c)
-   [چرا Hiding می‌تواند گمراه‌کننده
    باشد؟](#چرا-hiding-میتواند-گمراهکننده-باشد)
-   [موارد استفاده مناسب برای Hiding و
    Overriding](#موارد-استفاده-مناسب-برای-hiding-و-overriding)
-   [محدودیت‌ها و نکات هشداردهنده](#محدودیتها-و-نکات-هشداردهنده)
-   [مقایسه با زبان‌های دیگر](#مقایسه-با-زبانهای-دیگر)
-   [جمع‌بندی و نتیجه‌گیری](#جمعبندی-و-نتیجهگیری)
-   [منابع معتبر](#منابع-معتبر)

------------------------------------------------------------------------

## مقدمه

در برنامه‌نویسی شیءگرا، ارث‌بری (Inheritance) یکی از مفاهیم بنیادی برای
بازاستفاده و گسترش رفتار کلاس‌هاست. اما وقتی یک کلاس فرزند بخواهد متد
کلاس پایه را تغییر دهد، دو مسیر پیش رو دارد: **Overriding** و
**Hiding**. این دو مکانیزم هرچند مشابه به‌نظر می‌رسند، اما از نظر زمان
اجرا، رفتار برنامه و هدف طراحی تفاوت‌های اساسی دارند. در این مقاله، با
استفاده از مثال‌های واقعی در C#، تفاوت این دو مفهوم را بررسی می‌کنیم و یاد
می‌گیریم چه زمانی از هر کدام استفاده کنیم.

------------------------------------------------------------------------
## تعریف Overriding

Overriding زمانی اتفاق می‌افتد که یک **کلاس فرزند** متدی از **کلاس پایه**
را با کلیدواژه `override` بازنویسی کند. برای این کار، متد پایه باید با
`virtual` یا `abstract` علامت‌گذاری شده باشد.

به‌این‌ترتیب، در زمان **اجرا (Runtime)**، اگر شیء از نوع کلاس فرزند
باشد---even اگر از طریق ارجاع کلاس پایه فراخوانی شود---نسخه‌ی بازنویسی‌شده
اجرا خواهد شد. این رفتار، قلب **پلی‌مورفیسم زمان اجرا (Runtime
Polymorphism)** در C# است.

------------------------------------------------------------------------

## تعریف Hiding (Method Hiding)

Hiding یا **پنهان‌سازی متد** زمانی رخ می‌دهد که یک کلاس فرزند متدی با همان
نام و امضا در کلاس پایه تعریف کند، بدون اینکه متد پایه virtual باشد. در
این حالت، متد پایه پنهان می‌شود و متد جدید جایگزین آن می‌گردد.

اگر از کلیدواژه `new` استفاده کنیم، کامپایلر می‌فهمد که ما **عمداً** قصد
پنهان‌سازی داریم، و نه بازنویسی. اما رفتار این متدها در زمان اجرا به
**نوع ارجاع (Reference Type)** بستگی دارد، نه نوع واقعی شیء.

------------------------------------------------------------------------

## تفاوت‌های کلیدی: رفتار در زمان اجرا و زمان کامپایل

  -----------------------------------------------------------------------
    ویژگی                     Overriding          Hiding
  ------------------------- ------------------- -------------------------
  نیاز به `virtual` در کلاس بله                 خیر
  پایه                                          

  کلیدواژه در کلاس فرزند    `override`          `new` (توصیه می‌شود)

  زمان تصمیم‌گیری            در زمان اجرا        در زمان کامپایل
                            (Runtime)           (Compile-time)

  وابسته به نوع ارجاع؟      خیر                 بله

  پشتیبانی از پلی‌مورفیسم    بله                 خیر
  -----------------------------------------------------------------------

Overriding برای رفتارهای پویا و runtime طراحی شده، در حالی‌که Hiding
بیشتر تصمیمی در سطح compile-time است.

------------------------------------------------------------------------

## مثال عملی: Overriding در C

``` csharp
public class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Some generic animal sound");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}

public class Program
{
    public static void Main()
    {
        Animal myPet = new Dog();
        myPet.MakeSound(); // خروجی: Woof!

        Animal myOtherPet = new Cat();
        myOtherPet.MakeSound(); // خروجی: Meow!
    }
}
```

------------------------------------------------------------------------

## مثال عملی: Hiding در C

``` csharp
public class Vehicle
{
    public void Start()
    {
        Console.WriteLine("Vehicle engine started.");
    }
}

public class ElectricCar : Vehicle
{
    public new void Start()
    {
        Console.WriteLine("Electric motor started silently.");
    }
}

public class Program
{
    public static void Main()
    {
        Vehicle myCar = new ElectricCar();
        myCar.Start(); // خروجی: Vehicle engine started.

        ElectricCar realCar = new ElectricCar();
        realCar.Start(); // خروجی: Electric motor started silently.
    }
}
```

------------------------------------------------------------------------

## جمع‌بندی

Overriding و Hiding هر دو برای تغییر رفتار متدهای ارث‌برده‌شده استفاده
می‌شوند، اما:

-   Overriding رفتار را **در زمان اجرا** تعیین می‌کند و پلی‌مورفیسم را
    ممکن می‌سازد.
-   Hiding رفتار را **در زمان کامپایل** تعیین می‌کند و به نوع ارجاع
    وابسته است.

------------------------------------------------------------------------

## منابع معتبر

-   [Microsoft Learn -- override (C#
    Reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/override)
-   [Microsoft Learn -- new Modifier (C#
    Reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/new-modifier)
-   [Microsoft Learn -- Polymorphism in
    C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism)
-   [C# Language Specification
    (ECMA-334)](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)
-   Jon Skeet -- *C# in Depth*, Chapter on Inheritance and Polymorphism
-   Stack Overflow: [Difference between method overriding and method
    hiding in
    C#](https://stackoverflow.com/questions/588745/difference-between-method-overriding-and-method-hiding-in-c-sharp)


