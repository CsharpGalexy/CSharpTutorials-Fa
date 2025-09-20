
## 📖 فهرست مطالب

1. [مقدمه: چرا این مفاهیم مهم هستند؟](#مقدمه-چرا-این-مفاهیم-مهم-هستند)
2. [مفهوم Dynamic Binding (ارتباط پویا)](#مفهوم-dynamic-binding-ارتباط-پویا)
3. [Method Overriding (بازنویسی متد) در زمان اجرا](#method-overriding-بازنویسی-متد-در-زمان-اجرا)
4. [Abstract Methods و کلاس‌های مجرد](#abstract-methods-و-کلاس‌های-مجرد)
5. [چگونه این سه مفهوم با هم کار می‌کنند؟](#چگونه-این-سه-مفهوم-با-هم-کار-میکنند)
6. [مثال‌های عملی و کاربردی](#مثالهای-عملی-و-کاربردی)
7. [تفاوت‌های کلیدی و نکات مهم](#تفاوتهای-کلیدی-و-نکات-مهم)
8. [جمع‌بندی و کاربردهای واقعی](#جمعبندی-و-کاربردهای-واقعی)
9. [منابع معتبر](#منابع-معتبر)

---

## 1. مقدمه: چرا این مفاهیم مهم هستند؟

در برنامه‌نویسی شیءگرا (OOP)، یکی از اهداف اصلی **انعطاف‌پذیری** و **قابلیت گسترش** کد است.  
مفاهیمی مثل **Method Overriding**، **Abstract Methods** و **Dynamic Binding** به شما اجازه می‌دهند تا:

- رفتار کلاس‌های فرزند را متناسب با نیاز خود تغییر دهید.
- از یک رابط مشترک برای کلاس‌های مختلف استفاده کنید.
- تصمیم‌گیری درباره اینکه کدام متد اجرا شود را به **زمان اجرا** (Run-time) موکول کنید — نه زمان کامپایل.

این مفاهیم پایه‌های **چندریختی (Polymorphism)** هستند و بدون آن‌ها، OOP معنای کامل خود را ندارد.

---

## 2. مفهوم Dynamic Binding (ارتباط پویا)

### 🔹 تعریف:
**Dynamic Binding** (یا Late Binding) به این معنی است که تصمیم‌گیری درباره اینکه **کدام نسخه از یک متد** باید اجرا شود، در **زمان اجرا (Run-time)** و نه زمان کامپایل انجام می‌شود.

### 🔹 چرا مهم است؟
فرض کنید یک متغیر از نوع `Animal` دارید، اما در واقعیت به یک شیء از نوع `Dog` یا `Cat` اشاره می‌کند. شما می‌خواهید متد `MakeSound()` روی آن فراخوانی کنید — اما نمی‌دانید دقیقاً چه حیوانی است تا زمان اجرا!

با Dynamic Binding، CLR (Common Language Runtime) به صورت خودکار تشخیص می‌دهد که کدام متد باید اجرا شود.

### 🔹 شرط لازم:
برای Dynamic Binding در C#، متد باید:

- `virtual` باشد (در کلاس پایه)
- `override` شود (در کلاس فرزند)

مثال:

```csharp
class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Some generic animal sound");
    }
}

class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

class Program
{
    static void Main()
    {
        Animal myPet = new Dog(); // متغیر از نوع Animal، اما اشاره‌گر به Dog
        myPet.MakeSound(); // خروجی: "Woof!" — تصمیم در زمان اجرا گرفته شد!
    }
}
```

> ✅ اینجا `MakeSound()` به صورت پویا (Dynamic) بایند شده است.

---

## 3. Method Overriding (بازنویسی متد) در زمان اجرا

### 🔹 تعریف:
**Method Overriding** یعنی تعریف مجدد یک متد در کلاس فرزند که قبلاً در کلاس پایه به صورت `virtual` یا `abstract` تعریف شده است.

### 🔹 قوانین:
- متد در کلاس پایه باید `virtual`، `abstract` یا `override` باشد.
- متد در کلاس فرزند باید `override` باشد.
- امضای متد (نام، پارامترها، نوع بازگشتی) باید یکسان باشد.
- سطح دسترسی نمی‌تواند محدودتر شود (مثلاً از `public` به `private`).

### 🔹 هدف:
تغییر رفتار یک متد در کلاس‌های فرزند بدون تغییر کد کلاس پایه.

### 🔹 مثال:

```csharp
class Shape
{
    public virtual double CalculateArea()
    {
        return 0;
    }
}

class Circle : Shape
{
    public double Radius { get; set; }

    public override double CalculateArea()
    {
        return Math.PI * Radius * Radius;
    }
}

class Rectangle : Shape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public override double CalculateArea()
    {
        return Width * Height;
    }
}
```

حالا می‌توانیم از یک لیست از `Shape`ها استفاده کنیم و بدون دانستن نوع دقیق هر شکل، مساحت آن را محاسبه کنیم — چون متد `CalculateArea()` در زمان اجرا تعیین می‌شود که کدام نسخه اجرا شود.

```csharp
List<Shape> shapes = new List<Shape>
{
    new Circle { Radius = 5 },
    new Rectangle { Width = 4, Height = 6 }
};

foreach (var shape in shapes)
{
    Console.WriteLine($"Area: {shape.CalculateArea()}");
}
// خروجی:
// Area: 78.53981633974483
// Area: 24
```

---

## 4. Abstract Methods و کلاس‌های مجرد

### 🔹 تعریف:
- **کلاس مجرد (Abstract Class)**: کلاسی که نمی‌توان از آن شیء ساخت (غیر قابل نمونه‌سازی). فقط برای ارث‌بری طراحی شده است.
- **متد مجرد (Abstract Method)**: متدی که **بدون پیاده‌سازی** در کلاس پایه تعریف می‌شود و **اجباراً** در کلاس‌های فرزند باید پیاده‌سازی شود.

### 🔹 چرا استفاده کنیم؟
وقتی می‌خواهید یک **قرارداد (Contract)** برای کلاس‌های فرزند تعریف کنید — یعنی مطمئن شوید همه فرزندان یک متد خاص را پیاده‌سازی کرده‌اند.

### 🔹 مثال:

```csharp
abstract class Animal
{
    public abstract void MakeSound(); // بدون بدنه — فقط امضا
}

class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}

// ❌ اشتباه: نمی‌توانید از Animal شیء بسازید
// Animal a = new Animal(); // خطای کامپایل!

// ✅ صحیح:
Animal myDog = new Dog();
myDog.MakeSound(); // Woof!
```

### 🔹 نکات:
- کلاس‌های `abstract` می‌توانند شامل متدهای `virtual`، `normal` و `abstract` باشند.
- کلاس فرزند یک کلاس `abstract`، خودش نیز `abstract` است — مگر اینکه تمام متدهای `abstract` را پیاده‌سازی کند.
- `abstract` ≠ `interface` — در ادامه تفاوت‌ها را می‌بینیم.

---

## 5. چگونه این سه مفهوم با هم کار می‌کنند؟

این سه مفهوم مثل سه دنگِ یک ارکستر هستند که با هم موسیقی **چندریختی (Polymorphism)** را می‌سازند:

1. **Abstract Method** → قرارداد می‌دهد: "هر فرزند باید این متد را داشته باشد."
2. **Method Overriding** → فرزندان متد را با رفتار خودشان پیاده‌سازی می‌کنند.
3. **Dynamic Binding** → در زمان اجرا، CLR تصمیم می‌گیرد کدام نسخه از متد اجرا شود — بر اساس نوع واقعی شیء.

### 🔹 مثال ترکیبی:

```csharp
abstract class Vehicle
{
    public abstract void Start(); // Abstract Method — قرارداد
}

class Car : Vehicle
{
    public override void Start() // Method Overriding
    {
        Console.WriteLine("Car engine started with a key.");
    }
}

class ElectricCar : Vehicle
{
    public override void Start() // Method Overriding
    {
        Console.WriteLine("Electric car started silently.");
    }
}

class Program
{
    static void Main()
    {
        List<Vehicle> vehicles = new List<Vehicle>
        {
            new Car(),
            new ElectricCar()
        };

        foreach (Vehicle v in vehicles)
        {
            v.Start(); // Dynamic Binding — در زمان اجرا تصمیم گرفته می‌شود
        }
    }
}
```

**خروجی:**
```
Car engine started with a key.
Electric car started silently.
```

✅ تمام سه مفهوم در این مثال حضور دارند.

---

## 6. مثال‌های عملی و کاربردی

### 🎯 مثال ۱: سیستم پرداخت

```csharp
abstract class PaymentMethod
{
    public abstract void ProcessPayment(decimal amount);
}

class CreditCardPayment : PaymentMethod
{
    public override void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing ${amount} via Credit Card.");
    }
}

class PayPalPayment : PaymentMethod
{
    public override void ProcessPayment(decimal amount)
    {
        Console.WriteLine($"Processing ${amount} via PayPal.");
    }
}

// استفاده:
var payments = new List<PaymentMethod>
{
    new CreditCardPayment(),
    new PayPalPayment()
};

foreach (var payment in payments)
{
    payment.ProcessPayment(100m); // Dynamic Binding!
}
```

### 🎯 مثال ۲: سیستم اطلاعات کارمندان

```csharp
abstract class Employee
{
    public string Name { get; set; }
    public abstract decimal CalculateSalary();
}

class FullTimeEmployee : Employee
{
    public decimal MonthlySalary { get; set; }

    public override decimal CalculateSalary()
    {
        return MonthlySalary;
    }
}

class PartTimeEmployee : Employee
{
    public decimal HourlyRate { get; set; }
    public int HoursWorked { get; set; }

    public override decimal CalculateSalary()
    {
        return HourlyRate * HoursWorked;
    }
}

// استفاده:
var employees = new List<Employee>
{
    new FullTimeEmployee { Name = "Ali", MonthlySalary = 5000 },
    new PartTimeEmployee { Name = "Sara", HourlyRate = 20, HoursWorked = 100 }
};

foreach (var emp in employees)
{
    Console.WriteLine($"{emp.Name}: ${emp.CalculateSalary()}");
}
```

---

## 7. تفاوت‌های کلیدی و نکات مهم

| ویژگی | `virtual` | `abstract` | `override` |
|-------|-----------|------------|------------|
| نیاز به پیاده‌سازی در کلاس پایه | ✅ دارد | ❌ ندارد | — |
| می‌تواند در کلاس غیر مجرد باشد | ✅ | ❌ فقط در کلاس `abstract` | ✅ |
| اجباری برای override در فرزند | ❌ | ✅ | — |
| می‌تواند sealed شود | ✅ | ✅ | ✅ |

### ⚠️ نکات مهم:

- **هر متد `abstract`، ضمناً `virtual` هم هست** — یعنی قابل override است.
- **کلاس `abstract` می‌تواند شامل فیلدها، سازنده‌ها و متدهای معمولی هم باشد**.
- **Dynamic Binding فقط برای متدهای `virtual` یا `abstract` اتفاق می‌افتد** — متدهای عادی (non-virtual) Static Binding دارند.
- **از `new` برای hiding استفاده نکنید مگر اضطراری — چون Polymorphism را خراب می‌کند**.

مثال خطرناک با `new`:

```csharp
class Base
{
    public void Show() { Console.WriteLine("Base"); }
}

class Derived : Base
{
    public new void Show() { Console.WriteLine("Derived"); }
}

Base obj = new Derived();
obj.Show(); // خروجی: "Base" — چون متد virtual نیست و hiding اتفاق افتاده!
```

---

## 8. جمع‌بندی و کاربردهای واقعی

### ✅ کاربردهای واقعی:

- **سیستم‌های پرداخت**: هر روش پرداخت یک رفتار متفاوت دارد.
- **سیستم‌های گرافیکی**: هر شکل یک روش رسم متفاوت دارد.
- **سیستم‌های بازی**: هر موجودیت (Player, Enemy, NPC) یک رفتار حرکتی/حمله متفاوت دارد.
- **سیستم‌های لاگ**: هر نوع لاگ (فایل، دیتابیس، کنسول) یک روش نوشتن متفاوت دارد.

### ✅ چرا این مفاهیم را یاد بگیریم؟

- کد شما **قابل گسترش** می‌شود — می‌توانید کلاس جدید اضافه کنید بدون تغییر کد قبلی.
- کد شما **قابل نگهداری** می‌شود — منطق هر کلاس در خودش متمرکز است.
- از **اصل Open/Closed** (باز برای گسترش، بسته برای تغییر) پیروی می‌کنید.

---

## 9. منابع معتبر

1. **Microsoft Docs (رسمی و معتبرترین منبع)**  
   - [Inheritance - C# Guide](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance)  
   - [Polymorphism - C# Guide](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism)  
   - [Abstract and Sealed Classes and Class Members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members)

2. **C# in Depth — by Jon Skeet (کتاب)**  
   - فصل‌های مربوط به OOP و Polymorphism

3. **CLR via C# — by Jeffrey Richter (کتاب پیشرفته)**  
   - فصل 4: Inheritance and Virtual Methods — جزئیات داخلی CLR و نحوه بایندینگ

4. **Pluralsight / Udemy — دوره‌های C# OOP**  
   - دوره "C# Advanced Topics: Prepare for Technical Interviews" — Zoran Horvat  
   - دوره "C# Intermediate: Classes and OOP" — Mosh Hamedani

5. **Stack Overflow — برای سوالات کاربردی و رایج**  
   - مثلاً: [What is the difference between method hiding and method overriding?](https://stackoverflow.com/questions/3838553/overriding-vs-hiding)

