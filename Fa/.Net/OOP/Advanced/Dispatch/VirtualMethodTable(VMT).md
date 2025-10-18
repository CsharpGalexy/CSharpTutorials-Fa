

### فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [چرا VMT نیاز است؟](#چرا-vmt-نیاز-است؟)  
3. [VMT چیست؟](#vmt-چیست؟)  
4. [نحوه کار VMT در C#](#نحوه-کار-vmt-در-c)  
5. [مثال عملی در C#](#مثال-عملی-در-c)  
6. [تفاوت‌های مهم با سایر زبان‌ها (مثل C++)](#تفاوت‌های-مهم-با-سایر-زبان‌ها-مثل-c)  
7. [تأثیر VMT بر عملکرد (Performance)](#تأثیر-vmt-بر-عملکرد-performance)  
8. [جمع‌بندی](#جمع‌بندی)  
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP)، یکی از قدرتمندترین مفاهیم، **چندریختی (Polymorphism)** است. در C#، چندریختی اغلب از طریق **روش‌های مجازی (virtual methods)** و **روش‌های بازنویسی‌شده (overridden methods)** پیاده‌سازی می‌شود. اما چگونه زمان اجرا (Runtime) تشخیص می‌دهد که کدام نسخه از یک متد باید فراخوانی شود؟ پاسخ این سؤال در ساختاری به نام **Virtual Method Table** یا **VMT** نهفته است.

در این راهنما، به‌صورت گام‌به‌گام و از سطح مقدماتی تا متوسط، مفهوم VMT در C# را بررسی می‌کنیم.

---

## چرا VMT نیاز است؟

در زمان کامپایل، کامپایلر C# نمی‌تواند همیشه بداند که شیء واقعی کدام کلاس است — به‌ویژه وقتی از **اشاره‌گر یا مرجع به کلاس پایه** استفاده می‌شود:

```csharp
Animal a = new Dog();
a.Speak(); // کدام Speak؟ Animal یا Dog؟
```

این تصمیم‌گیری باید **در زمان اجرا (Runtime)** انجام شود. اینجاست که VMT وارد عمل می‌شود.

---

## VMT چیست؟

**Virtual Method Table (VMT)** یا **جدول روش‌های مجازی**، یک ساختار داده داخلی است که توسط CLR (Common Language Runtime) برای پیاده‌سازی **پویا (Dynamic) یا مجازی (Virtual) Dispatch** استفاده می‌شود.

هر کلاسی که حداقل یک متد مجازی (`virtual`) یا بازنویسی‌شده (`override`) داشته باشد، یک VMT برای خود دارد. این جدول شامل **آدرس‌های توابع مجازی** آن کلاس است.

> 💡 **نکته مهم**: در C#، VMT به‌صورت مستقیم قابل دسترسی نیست و بخشی از جزئیات پیاده‌سازی CLR است. اما درک آن برای درک عمیق‌تر OOP ضروری است.

---

## نحوه کار VMT در C#

1. **در زمان کامپایل**:
   - کامپایلر C# برای هر کلاسی که متد مجازی دارد، یک جدول VMT ایجاد می‌کند.
   - هر متد مجازی در این جدول یک شاخص (index) ثابت دارد.

2. **در زمان اجرا**:
   - هر شیء (object) یک **اشاره‌گر به VMT کلاس خود** دارد (معمولاً در ابتدای حافظه شیء ذخیره می‌شود).
   - هنگام فراخوانی یک متد مجازی، CLR:
     - اشاره‌گر VMT شیء را بررسی می‌کند.
     - شاخص متد مورد نظر را در جدول پیدا می‌کند.
     - متد واقعی را از آدرس ذخیره‌شده در جدول فراخوانی می‌کند.

این فرآیند به **Late Binding** یا **Dynamic Dispatch** معروف است.

---

## مثال عملی در C#

```csharp
public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal speaks");
    }
}

public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Dog barks");
    }
}

class Program
{
    static void Main()
    {
        Animal myPet = new Dog();
        myPet.Speak(); // خروجی: "Dog barks"
    }
}
```

### چه اتفاقی می‌افتد؟

- کلاس `Animal` یک VMT دارد که شامل آدرس `Speak()` است.
- کلاس `Dog` یک VMT جداگانه دارد که آدرس `Speak()` را به نسخه خودش اشاره می‌دهد.
- وقتی `myPet.Speak()` فراخوانی می‌شود:
  - CLR به VMT شیء `Dog` نگاه می‌کند (نه `Animal`).
  - متد `Speak` از VMT `Dog` فراخوانی می‌شود.

> ✅ این دقیقاً همان چیزی است که به ما اجازه می‌دهد از چندریختی استفاده کنیم.

---

## تفاوت‌های مهم با سایر زبان‌ها (مثل C++)

| ویژگی | C# | C++ |
|-------|----|-----|
| **مدیریت VMT** | توسط CLR (بخشی از .NET Runtime) | توسط کامپایلر (مثل MSVC یا GCC) |
| **قابلیت دسترسی** | غیرقابل دسترسی مستقیم | گاهی با pointer arithmetic قابل دسترسی |
| **ساختار حافظه** | شیء‌ها همیشه یک اشاره‌گر به Method Table دارند | مشابه، اما جزئیات پیاده‌سازی کامپایلر-وابسته است |
| **قابلیت تغییر VMT** | خیر (امنیت بالا) | در برخی موارد بله (اما خطرناک) |

> 🔒 C# امنیت و استحکام را بر کنترل پایین‌سطحی ترجیح می‌دهد.

---

## تأثیر VMT بر عملکرد (Performance)

- فراخوانی متد مجازی **کمی کندتر** از فراخوانی متد عادی است، چون نیاز به lookup در جدول دارد.
- اما CLR از **بهینه‌سازی‌های پیشرفته** مانند **Method Inlining** و **Virtual Call Devirtualization** استفاده می‌کند.
- در اکثر موارد، تفاوت عملکرد ناچیز است و نباید مانع استفاده از چندریختی شود.

> 📊 طبق تست‌های Microsoft، تفاوت عملکرد بین virtual و non-virtual در موارد معمولی کمتر از 1-2% است.

---

## جمع‌بندی

- VMT سازوکاری است که به CLR اجازه می‌دهد **فراخوانی‌های مجازی** را در زمان اجرا به درستی هدایت کند.
- هر کلاس با متد مجازی، یک VMT دارد.
- هر شیء یک اشاره‌گر به VMT کلاس خود نگه می‌دارد.
- این مکانیزم پایه‌ی **چندریختی پویا** در C# است.
- درک VMT به شما کمک می‌کند تا کد OOP خود را بهینه‌تر و عمیق‌تر بنویسید.

---

## منابع معتبر

1. **Microsoft Docs – C# Language Specification**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#virtual-methods](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#virtual-methods)

2. **ECMA-335 – Common Language Infrastructure (CLI) Standard**  
   Section II.10.1.3: Virtual Methods and VTable  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-335/](https://www.ecma-international.org/publications-and-standards/standards/ecma-335/)

3. **"CLR via C#" by Jeffrey Richter (4th Edition)**  
   Chapter 6: Type Fundamentals & Chapter 10: Methods  
   ISBN: 978-0735667457

4. **.NET Runtime Internals – Virtual Method Dispatch**  
   [https://github.com/dotnet/runtime/blob/main/docs/design/coreclr/botr/virtual-method-dispatch.md](https://github.com/dotnet/runtime/blob/main/docs/design/coreclr/botr/virtual-method-dispatch.md)

5. **Stack Overflow – How does virtual method dispatch work in C#?**  
   [https://stackoverflow.com/questions/11972880/how-does-virtual-method-dispatch-work-in-c](https://stackoverflow.com/questions/11972880/how-does-virtual-method-dispatch-work-in-c)

