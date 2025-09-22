

## 📖 فهرست مطالب

1. [مقدمه: چرا چند ریختی مهم است؟](#مقدمه-چرا-چند-ریختی-مهم-است)
2. [چند ریختی زیرنوع (Subtype Polymorphism)](#چند-ریختی-زیرنوع-subtype-polymorphism)
3. [چند ریختی پارامتریک (Parametric Polymorphism / Generics)](#چند-ریختی-پارامتریک-parametric-polymorphism--generics)
4. [چند ریختی به صورت Ad-hoc (Method Overloading و Operator Overloading)](#چند-ریختی-به-صورت-ad-hoc-method-overloading-و-operator-overloading)
5. [جمع‌بندی و مقایسه انواع چند ریختی](#جمع‌بندی-و-مقایسه-انواع-چند-ریختی)
6. [منابع معتبر](#منابع-معتبر)

---

## 1. مقدمه: چرا چند ریختی مهم است؟

چند ریختی (Polymorphism) یکی از **چهار اصل اصلی OOP** (شیءگرایی) است که به معنای **"چند شکلی"** است. یعنی یک عمل می‌تواند به شکل‌های مختلفی اجرا شود — بسته به نوع داده یا کلاسی که با آن کار می‌کنیم.

در C#، چند ریختی به سه شکل اصلی پیاده‌سازی می‌شود:

- **Subtype Polymorphism** → ارث‌بری و override
- **Parametric Polymorphism** → Generics (مثل `List<T>`)
- **Ad-hoc Polymorphism** → Overloading (متد و عملگر)

هر کدام کاربرد خاص خود را دارند و در طراحی نرم‌افزارهای انعطاف‌پذیر و قابل گسترش بسیار کاربردی هستند.

---

## 2. چند ریختی زیرنوع (Subtype Polymorphism)

### 🎯 تعریف:
این نوع چند ریختی زمانی رخ می‌دهد که یک کلاس فرزند، رفتار کلاس پدر را **تغییر دهد (override)** و هنگام اجرا، متد مناسب بر اساس نوع واقعی شیء فراخوانی شود.

### ✅ مثال ساده:

```csharp
public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Some generic animal sound");
    }
}

public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Meow!");
    }
}

// استفاده:
Animal myPet = new Dog();
myPet.Speak(); // خروجی: Woof!

myPet = new Cat();
myPet.Speak(); // خروجی: Meow!
```

### 🔍 نکات کلیدی:

- از کلمه کلیدی `virtual` در کلاس پایه و `override` در کلاس فرزند استفاده می‌شود.
- متدها باید **قابل override کردن** باشند.
- نوع متغیر می‌تواند پایه (مثلاً `Animal`) باشد، اما شیء واقعی می‌تواند فرزند (مثلاً `Dog`) باشد → **چند ریختی در زمان اجرا (Runtime Polymorphism)**.

### 💡 کاربرد:
- طراحی سیستم‌های قابل گسترش (Extensible)
- الگوهای طراحی مثل Strategy, Factory, Command

---

## 3. چند ریختی پارامتریک (Parametric Polymorphism / Generics)

### 🎯 تعریف:
این نوع چند ریختی به شما اجازه می‌دهد **کدهای عمومی** بنویسید که بدون وابستگی به نوع خاصی از داده، کار کنند. در C# با **Generics** پیاده‌سازی می‌شود.

### ✅ مثال ساده:

```csharp
public class Box<T>
{
    public T Content { get; set; }

    public void ShowContent()
    {
        Console.WriteLine($"Content: {Content}");
    }
}

// استفاده:
var stringBox = new Box<string>();
stringBox.Content = "Hello Generics!";
stringBox.ShowContent();

var intBox = new Box<int>();
intBox.Content = 42;
intBox.ShowContent();
```

### 🔍 نکات کلیدی:

- `T` یک **پارامتر نوع (Type Parameter)** است.
- در زمان کامپایل، نوع `T` جایگزین می‌شود → **Type Safety** کامل.
- از Generics در کلاس‌ها، متد‌ها، اینترفیس‌ها و delegateها استفاده می‌شود.

### ✅ مثال پیشرفته‌تر — متد عمومی:

```csharp
public static T Max<T>(T a, T b) where T : IComparable<T>
{
    return a.CompareTo(b) > 0 ? a : b;
}

// استفاده:
Console.WriteLine(Max(5, 10));        // 10
Console.WriteLine(Max("apple", "zebra")); // zebra
```

### 💡 کاربرد:
- کاهش تکرار کد
- افزایش ایمنی نوع (Type Safety)
- کار با کلاس‌های کلکسیون مانند `List<T>`, `Dictionary<TKey, TValue>`

---

## 4. چند ریختی به صورت Ad-hoc (Method Overloading و Operator Overloading)

### 🎯 تعریف:
در این نوع، یک **نام** (مثلاً نام متد یا عملگر) برای **چندین پیاده‌سازی مختلف** استفاده می‌شود — بسته به نوع یا تعداد پارامترها.

دو نوع اصلی در C#:

#### 4.1 Method Overloading

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
    public string Add(string a, string b) => a + b;
}

// استفاده:
var calc = new Calculator();
Console.WriteLine(calc.Add(2, 3));        // 5
Console.WriteLine(calc.Add(2.5, 3.1));    // 5.6
Console.WriteLine(calc.Add("Hello", " World")); // Hello World
```

#### 4.2 Operator Overloading

```csharp
public class Vector
{
    public int X { get; set; }
    public int Y { get; set; }

    public Vector(int x, int y)
    {
        X = x;
        Y = y;
    }

    // Overload عملگر +
    public static Vector operator +(Vector a, Vector b)
    {
        return new Vector(a.X + b.X, a.Y + b.Y);
    }

    public override string ToString() => $"({X}, {Y})";
}

// استفاده:
var v1 = new Vector(1, 2);
var v2 = new Vector(3, 4);
var v3 = v1 + v2;
Console.WriteLine(v3); // خروجی: (4, 6)
```

### 🔍 نکات کلیدی:

- **Compile-time Polymorphism**: تصمیم‌گیری در زمان کامپایل.
- فقط متد‌ها و عملگرها قابل overload هستند — نه متغیرها یا خصوصیات.
- نباید منطق را تغییر دهد — فقط نحوه استفاده را ساده می‌کند.

### 💡 کاربرد:
- APIهای کاربرپسند
- کلاس‌های ریاضی (مثل Vector, Matrix)
- بهبود خوانایی کد

---

## 5. جمع‌بندی و مقایسه انواع چند ریختی

| نوع چند ریختی         | نحوه پیاده‌سازی در C#         | زمان تصمیم‌گیری | کاربرد اصلی                     |
|------------------------|-------------------------------|------------------|----------------------------------|
| Subtype                | ارث‌بری + override            | Runtime          | سیستم‌های قابل گسترش، الگوهای طراحی |
| Parametric (Generics)  | Generics (`<T>`)              | Compile-time     | کدهای عمومی، کلکسیون‌ها          |
| Ad-hoc                 | Overloading (متد/عملگر)       | Compile-time     | APIهای ساده، کلاس‌های ریاضی      |

> 🎯 نکته: هر سه نوع در C# به خوبی پشتیبانی می‌شوند و مکمل یکدیگرند — نه رقیب!

---

## 6. منابع معتبر

1. **Microsoft Docs — C# Programming Guide**  
   📌 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/)  
   → شامل بخش‌های Polymorphism, Inheritance, Generics, Overloading

2. **C# in Depth — by Jon Skeet (Manning Publications)**  
   📌 کتاب مرجع برای درک عمیق C# — فصل‌های Generics و Polymorphism عالی هستند.

3. **CLR via C# — by Jeffrey Richter**  
   📌 برای درک نحوه اجرای چند ریختی در سطح CLR و زمان اجرا.

4. **Stack Overflow — C# Polymorphism Tag**  
   📌 [https://stackoverflow.com/questions/tagged/c%23+polymorphism](https://stackoverflow.com/questions/tagged/c%23+polymorphism)  
   → سوالات واقعی و پاسخ‌های متخصصان

5. **Pluralsight / Udemy — دوره‌های C# OOP**  
   📌 دوره‌های "C# Advanced Topics" یا "Object-Oriented Programming in C#"

6. **GitHub — Microsoft/dotnet/samples**  
   📌 [https://github.com/dotnet/samples](https://github.com/dotnet/samples)  
   → مثال‌های عملی از Generics, Inheritance, Overloading

