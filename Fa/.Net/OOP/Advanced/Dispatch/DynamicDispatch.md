

## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [تعریف Dynamic Dispatch چیست؟](#تعریف-dynamic-dispatch-چیست)
3. [تفاوت Static Dispatch و Dynamic Dispatch](#تفاوت-static-dispatch-و-dynamic-dispatch)
4. [چگونه Dynamic Dispatch در C# پیاده‌سازی می‌شود؟](#چگونه-dynamic-dispatch-در-c-پیادهسازی-میشود)
   - [استفاده از کلمات کلیدی `virtual`، `override` و `base`](#استفاده-از-کلمات-کلیدی-virtual-override-و-base)
   - [نمونه کد ساده](#نمونه-کد-ساده)
5. [چرا Dynamic Dispatch مهم است؟](#چرا-dynamic-dispatch-مهم-است)
6. [محدودیت‌ها و نکات مهم](#محدودیتها-و-نکات-مهم)
7. [مقایسه با زبان‌های دیگر](#مقایسه-با-زبانهای-دیگر)
8. [جمع‌بندی](#جمعبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP)، **Dynamic Dispatch** یا **Late Binding** یکی از مفاهیم کلیدی است که به ما امکان می‌دهد رفتار یک متد را در زمان اجرا (Runtime) تعیین کنیم، نه در زمان کامپایل (Compile-time). این ویژگی پایه‌ی **چندریختی (Polymorphism)** در C# و بسیاری از زبان‌های شیء‌گرا است.

در این مقاله، به‌صورت گام‌به‌گام از سطح مقدماتی شروع کرده و تا سطح متوسط پیش می‌رویم تا شما بتوانید این مفهوم را به‌خوبی درک کنید و در پروژه‌های واقعی به‌کار ببرید.

---

## تعریف Dynamic Dispatch چیست؟

**Dynamic Dispatch** (یا **Late Binding**) به فرآیندی گفته می‌شود که در آن **متدی که باید فراخوانی شود، در زمان اجرا (Runtime) بر اساس نوع واقعی شیء تعیین می‌شود**، نه بر اساس نوع اشاره‌گر یا متغیر.

به عبارت ساده‌تر:  
اگر یک متغیر از نوع پایه (Base) باشد اما به یک شیء از نوع فرزند (Derived) اشاره کند، متدی که فراخوانی می‌شود، مربوط به کلاس فرزند خواهد بود — **البته اگر متد به‌درستی برای چندریختی تنظیم شده باشد**.

---

## تفاوت Static Dispatch و Dynamic Dispatch

| ویژگی | Static Dispatch (Early Binding) | Dynamic Dispatch (Late Binding) |
|--------|-------------------------------|--------------------------------|
| زمان تصمیم‌گیری | زمان کامپایل | زمان اجرا |
| انعطاف‌پذیری | کم | زیاد |
| عملکرد | سریع‌تر | کمی کندتر (به‌دلیل lookup در جدول vtable) |
| مثال در C# | متد‌های عادی (غیر virtual) | متد‌های `virtual` + `override` |

> 💡 **نکته:** در C#، تمام متد‌ها به‌صورت پیش‌فرض **static dispatch** هستند. برای فعال‌سازی dynamic dispatch باید از کلمات کلیدی خاصی استفاده کنید.

---

## چگونه Dynamic Dispatch در C# پیاده‌سازی می‌شود؟

C# از طریق **جدول مجازی (vtable)** و کلمات کلیدی زیر این قابلیت را پیاده‌سازی می‌کند:

- `virtual`: در کلاس پایه، نشان می‌دهد که این متد قابل override شدن است.
- `override`: در کلاس فرزند، نشان می‌دهد که این متد جایگزین متد پایه شده است.
- `base`: برای فراخوانی نسخه پایه متد از داخل کلاس فرزند.

### استفاده از کلمات کلیدی `virtual`، `override` و `base`

- بدون `virtual`، متد‌ها **قابل override نیستند**.
- بدون `override`، متد جدید در کلاس فرزند **پنهان‌سازی (hiding)** می‌شود (با هشدار کامپایلر)، نه override.
- برای پنهان‌سازی عمدی (نه override)، از `new` استفاده می‌شود — اما این **dynamic dispatch را غیرفعال می‌کند**.

### نمونه کد ساده

```csharp
// کلاس پایه
public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("The animal makes a sound.");
    }
}

// کلاس فرزند
public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Woof!");
    }
}

// کلاس فرزند دیگر
public class Cat : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Meow!");
    }
}

// استفاده
class Program
{
    static void Main()
    {
        Animal myPet1 = new Dog();
        Animal myPet2 = new Cat();

        myPet1.Speak(); // خروجی: Woof!
        myPet2.Speak(); // خروجی: Meow!
    }
}
```

در این مثال:
- متغیر `myPet1` از نوع `Animal` است، اما به یک شیء از نوع `Dog` اشاره می‌کند.
- در زمان اجرا، C# تشخیص می‌دهد که شیء واقعی از نوع `Dog` است و متد `Speak()` مربوط به `Dog` را فراخوانی می‌کند.
- این دقیقاً همان **Dynamic Dispatch** است.

---

## چرا Dynamic Dispatch مهم است؟

1. **انعطاف‌پذیری کد**: می‌توانید بدون تغییر کد پایه، رفتارهای جدید اضافه کنید.
2. **چندریختی (Polymorphism)**: یکی از چهار اصل اصلی OOP.
3. **قابلیت گسترش (Extensibility)**: سیستم‌های بزرگ (مثل فریم‌ورک‌ها) به این مکانیزم وابسته‌اند.
4. **الگوهای طراحی (Design Patterns)**: الگوهایی مثل Strategy، Factory، و Template Method به شدت به dynamic dispatch متکی‌اند.

---

## محدودیت‌ها و نکات مهم

- **فقط متد‌های `virtual` یا `abstract` قابل override هستند.**
- اگر در کلاس فرزند از `new` به‌جای `override` استفاده کنید، **dynamic dispatch اتفاق نمی‌افتد**:

```csharp
public class Bird : Animal
{
    public new void Speak() // ❌ این پنهان‌سازی است، نه override
    {
        Console.WriteLine("Tweet!");
    }
}

// استفاده:
Animal bird = new Bird();
bird.Speak(); // خروجی: "The animal makes a sound." (نه Tweet!)
```

- **عملکرد**: dynamic dispatch کمی کندتر از static dispatch است، اما در اکثر موارد تفاوت قابل چشم‌پوشی است.
- **sealed کردن کلاس یا متد**: اگر یک کلاس یا متد را `sealed` کنید، دیگر نمی‌توان آن را override کرد.

---

## مقایسه با زبان‌های دیگر

| زبان | نحوه پیاده‌سازی Dynamic Dispatch |
|------|----------------------------------|
| **Java** | تمام متد‌های غیر-private به‌صورت پیش‌فرض virtual هستند. |
| **C++** | با استفاده از `virtual` در کلاس پایه (مشابه C#). |
| **Python** | به‌صورت پیش‌فرض همه چیز dynamic است (duck typing). |
| **C#** | فقط متد‌های `virtual` یا `abstract` قابل override هستند. |

> ✅ C# رویکردی **صریح و ایمن** دارد: شما باید عمدتاً `virtual` را مشخص کنید تا dynamic dispatch فعال شود.

---

## جمع‌بندی

- **Dynamic Dispatch** = فراخوانی متد بر اساس **نوع واقعی شیء** در زمان اجرا.
- در C# با ترکیب `virtual` (در پایه) و `override` (در فرزند) پیاده‌سازی می‌شود.
- پایه‌ی چندریختی و یکی از ستون‌های برنامه‌نویسی شیء‌گراست.
- بدون آن، کد شما کمتر انعطاف‌پذیر و قابل گسترش خواهد بود.

---

## منابع معتبر

1. **Microsoft Learn – C# Programming Guide**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)  
   *بخش‌های مربوط به Inheritance و Polymorphism*

2. **C# Language Specification (ECMA-334)**  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)  
   *بخش 15.7.3: Virtual and Override Methods*

3. **"C# in Depth" by Jon Skeet** (Manning Publications)  
   *فصل 6: Implementing inheritance*  
   ISBN: 978-1617294532

4. **"CLR via C#" by Jeffrey Richter** (Microsoft Press)  
   *فصل 10: Interfaces و فصل 11: Inheritance*  
   ISBN: 978-0735667457

5. **Stack Overflow – How does virtual method dispatch work in C#?**  
   [https://stackoverflow.com/questions/3912867/how-does-virtual-method-dispatch-work-in-c](https://stackoverflow.com/questions/3912867/how-does-virtual-method-dispatch-work-in-c)

6. **C# Documentation – Polymorphism**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism)

---!
