
## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [تعریف Static Dispatch (Early Binding)](#تعریف-static-dispatch-early-binding)  
3. [تفاوت Static Dispatch و Dynamic Dispatch](#تفاوت-static-dispatch-و-dynamic-dispatch)  
4. [نحوه پیاده‌سازی Static Dispatch در C#](#نحوه-پیاده‌سازی-static-dispatch-در-c)  
5. [مثال‌های کاربردی](#مثال‌های-کاربردی)  
6. [مزایا و معایب Static Dispatch](#مزایا-و-معایب-static-dispatch)  
7. [جمع‌بندی](#جمع‌بندی)  
8. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP)، **Dispatch** به فرآیند تصمیم‌گیری در مورد اینکه کدام متد باید در زمان اجرا یا کامپایل فراخوانی شود، گفته می‌شود. دو نوع اصلی Dispatch وجود دارد:

- **Static Dispatch** (یا Early Binding)
- **Dynamic Dispatch** (یا Late Binding)

در این مستند، به‌طور کاملاً تمرکز‌شده، **Static Dispatch** را در زبان **C#** بررسی می‌کنیم — از مفاهیم پایه تا جزئیات فنی، با مثال‌های عملی و منابع معتبر.

---

## تعریف Static Dispatch (Early Binding)

**Static Dispatch** (یا **Early Binding**) به فرآیندی گفته می‌شود که در آن **تصمیم‌گیری درباره اینکه کدام متد یا تابع باید فراخوانی شود، در زمان کامپایل انجام می‌شود**.

در این روش، کامپایلر C# با توجه به **نوع اعلام‌شده (declared type)** متغیر و **امضا (signature)** متد، دقیقاً می‌داند کدام متد باید فراخوانی شود. این تصمیم‌گیری **قبل از اجرای برنامه** و در طی فرآیند کامپایل صورت می‌گیرد.

> ✅ **نکته کلیدی**: در Static Dispatch، **هیچ نیازی به بررسی نوع واقعی (runtime type)** شیء در زمان اجرا نیست.

---

## تفاوت Static Dispatch و Dynamic Dispatch

| ویژگی | Static Dispatch (Early Binding) | Dynamic Dispatch (Late Binding) |
|--------|-------------------------------|--------------------------------|
| زمان تصمیم‌گیری | زمان کامپایل | زمان اجرا (Runtime) |
| پایداری | بسیار سریع‌تر (بدون overhead) | کمی کندتر (نیاز به lookup جدول مجازی) |
| انعطاف‌پذیری | کمتر (وابسته به نوع اعلام‌شده) | بیشتر (پشتیبانی از چندریختی) |
| استفاده در C# | متد‌های `static`، متد‌های غیر مجازی (`non-virtual`)، و overload resolution | متد‌های `virtual`/`override` و `dynamic` |

---

## نحوه پیاده‌سازی Static Dispatch در C#

در C#، Static Dispatch در موارد زیر رخ می‌دهد:

1. **فراخوانی متد‌های `static`**  
2. **فراخوانی متد‌های غیر مجازی (`non-virtual`)**  
3. **Overload Resolution** (انتخاب متد بر اساس تعداد و نوع پارامترها در زمان کامپایل)

### نکات مهم:
- متد‌هایی که با کلمه کلیدی `virtual` تعریف نشده‌اند، به‌صورت پیش‌فرض **غیر مجازی** هستند و از Static Dispatch استفاده می‌کنند.
- حتی اگر یک شیء از نوع فرزند باشد، ولی متغیر آن از نوع پدر تعریف شده باشد، در صورت غیر مجازی بودن متد، **متد کلاس پدر** فراخوانی می‌شود.

---

## مثال‌های کاربردی

### مثال ۱: فراخوانی متد غیر مجازی (Static Dispatch)

```csharp
class Animal
{
    public void Speak() // غیر مجازی → Static Dispatch
    {
        Console.WriteLine("Animal speaks");
    }
}

class Dog : Animal
{
    public new void Speak() // پنهان‌سازی (hiding) متد پدر
    {
        Console.WriteLine("Dog barks");
    }
}

class Program
{
    static void Main()
    {
        Animal myPet = new Dog();
        myPet.Speak(); // خروجی: "Animal speaks"
    }
}
```

> 📌 **چرا؟** چون `Speak()` در کلاس `Animal` غیر مجازی است، بنابراین تصمیم‌گیری در زمان کامپایل بر اساس **نوع متغیر (`Animal`)** انجام می‌شود، نه نوع واقعی شیء (`Dog`).

---

### مثال ۲: Overload Resolution (Static Dispatch)

```csharp
class Calculator
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
}

class Program
{
    static void Main()
    {
        Calculator calc = new Calculator();
        Console.WriteLine(calc.Add(2, 3));      // int overload
        Console.WriteLine(calc.Add(2.5, 3.1));  // double overload
    }
}
```

> 🔍 کامپایلر در زمان کامپایل بر اساس نوع آرگومان‌ها، متد مناسب را انتخاب می‌کند → **Static Dispatch**.

---

### مثال ۳: متد‌های `static`

```csharp
class MathUtils
{
    public static int Square(int x) => x * x;
}

class Program
{
    static void Main()
    {
        Console.WriteLine(MathUtils.Square(5)); // Static Dispatch
    }
}
```

> ⚙️ متد‌های `static` همیشه از Static Dispatch استفاده می‌کنند، چون به هیچ شیء خاصی وابسته نیستند.

---

## مزایا و معایب Static Dispatch

### ✅ مزایا:
- **سرعت بالا**: هیچ overheadی در زمان اجرا وجود ندارد.
- **قابلیت پیش‌بینی**: رفتار کد در زمان کامپایل مشخص است.
- **بهینه‌سازی بهتر**: کامپایلر می‌تواند کد را بهینه‌تر کند (مثلاً inline کردن متد).

### ❌ معایب:
- **عدم پشتیبانی از چندریختی واقعی**: نمی‌توان رفتار متد را بر اساس نوع واقعی شیء تغییر داد.
- **انعطاف‌پذیری کمتر**: تغییر رفتار در زمان اجرا ممکن نیست.

---

## جمع‌بندی

- **Static Dispatch** در C# زمانی رخ می‌دهد که کامپایلر بتواند در زمان کامپایل تصمیم بگیرد کدام متد فراخوانی شود.
- این روش در متد‌های `static`، متد‌های غیر مجازی (`non-virtual`) و overload resolution استفاده می‌شود.
- برخلاف Dynamic Dispatch، Static Dispatch از **چندریختی واقعی (polymorphism)** پشتیبانی نمی‌کند، اما **سریع‌تر و قابل پیش‌بینی‌تر** است.
- برای استفاده از چندریختی در C#، باید از کلمات کلیدی `virtual` و `override` استفاده کنید که منجر به **Dynamic Dispatch** می‌شود.

---

## منابع معتبر

1. **Microsoft Docs – C# Language Specification**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/)

2. **Microsoft Docs – Virtual, Override, and New Modifiers**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/knowing-when-to-use-override-and-new-keywords](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/knowing-when-to-use-override-and-new-keywords)

3. **C# in Depth – Jon Skeet** (Chapter 5: Methods)  
   ISBN: 978-1617294532  
   [https://csharpindepth.com/](https://csharpindepth.com/)

4. **ECMA-334: C# Language Specification**  
   [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

5. **Stack Overflow – Early vs Late Binding in C#**  
   [https://stackoverflow.com/questions/10080984/early-binding-vs-late-binding-in-c-sharp](https://stackoverflow.com/questions/10080984/early-binding-vs-late-binding-in-c-sharp)

6. **CLR via C# – Jeffrey Richter** (Chapter 10: Methods)  
   ISBN: 978-0735667457!
