

## 📖 فهرست مطالب

1. [مقدمه: چرا Base و Derived مهم هستند؟](#1-مقدمه-چرا-base-و-derived-مهم-هستند)  
2. [مفهوم وراثت (Inheritance) در C#](#2-مفهوم-وراثت-inheritance-در-c)  
3. [کلاس پایه (Base Class) و کلاس مشتق شده (Derived Class)](3-کلاس-پایه-base-class-و-کلاس-مشتق-شده-derived-class)  
4. [نحوه تعریف کلاس مشتق شده](#4-نحوه-تعریف-کلاس-مشتق-شده)  
5. [دسترسی به اعضای کلاس پایه در کلاس مشتق شده](#5-دسترسی-به-اعضای-کلاس-پایه-در-کلاس-مشتق-شده)  
6. [کلیدواژه `base` — چیست و چه کاربردی دارد؟](#6-کلیدواژه-base--چیست-و-چه-کاربردی-دارد)  
7. [استفاده از `base` برای فراخوانی سازنده کلاس پایه](#7-استفاده-از-base-برای-فراخوانی-سازنده-کلاس-پایه)  
8. [استفاده از `base` برای فراخوانی متدها و ویژگی‌های کلاس پایه](#8-استفاده-از-base-برای-فراخوانی-متدها-و-ویژگی%E2%80%8Cهای-کلاس-پایه)  
9. [Override کردن متدها و استفاده از `base` در آن](#9-override-کردن-متدها-و-استفاده-از-base-در-آن)  
10. [محدودیت‌ها و نکات مهم](#10-محدودیت%E2%80%8Cها-و-نکات-مهم)  
11. [مثال‌های کاربردی](#11-مثال%E2%80%8Cهای-کاربردی)  
12. [جمع‌بندی](#12-جمع%E2%80%8Cبندی)  
13. [منابع معتبر](#13-منابع-معتبر)

---

## 1. مقدمه: چرا Base و Derived مهم هستند؟

در برنامه‌نویسی شیءگرا (OOP)، **وراثت** یکی از ارکان اصلی است که به شما اجازه می‌دهد کد را **قابل استفاده مجدد** و **سازمان‌یافته** بنویسید.  
با استفاده از وراثت، می‌توانید یک کلاس **پایه (Base)** تعریف کنید و کلاس‌های **مشتق شده (Derived)** از آن ایجاد کنید که همه ویژگی‌ها و رفتارهای کلاس پایه را به ارث می‌برند و می‌توانند آن‌ها را گسترش یا تغییر دهند.

این مفاهیم به شما کمک می‌کنند تا:
- کد تکراری ننویسید.
- تغییرات را در یک نقطه اعمال کنید.
- رفتارهای مشترک را در یک کلاس پایه متمرکز کنید.

---

## 2. مفهوم وراثت (Inheritance) در C#

در C#، وراثت به این معنی است که یک کلاس (مشتق شده) می‌تواند از یک کلاس دیگر (پایه) **اعضای public و protected** را به ارث ببرد.

📌 **نکته مهم**:  
C# از **وراثت چندگانه (Multiple Inheritance)** پشتیبانی نمی‌کند. یعنی یک کلاس فقط می‌تواند از **یک کلاس پایه** به ارث ببرد. (اما می‌توانید از چندین Interface به ارث ببرید.)

---

## 3. کلاس پایه (Base Class) و کلاس مشتق شده (Derived Class)

- **کلاس پایه (Base Class)**: کلاسی که ویژگی‌ها و روش‌های عمومی را تعریف می‌کند.
- **کلاس مشتق شده (Derived Class)**: کلاسی که از کلاس پایه به ارث می‌برد و می‌تواند آن را گسترش دهد یا تغییر دهد.

### مثال ساده:

```csharp
// کلاس پایه
public class Animal
{
    public string Name { get; set; }

    public void Eat()
    {
        Console.WriteLine($"{Name} is eating.");
    }
}

// کلاس مشتق شده
public class Dog : Animal  // Dog از Animal به ارث می‌برد
{
    public void Bark()
    {
        Console.WriteLine($"{Name} is barking!");
    }
}
```

---

## 4. نحوه تعریف کلاس مشتق شده

برای تعریف یک کلاس مشتق شده، از علامت `:` بعد از نام کلاس استفاده می‌کنید و نام کلاس پایه را می‌نویسید:

```csharp
public class DerivedClass : BaseClass
{
    // اعضای جدید یا override شده
}
```

---

## 5. دسترسی به اعضای کلاس پایه در کلاس مشتق شده

اعضای کلاس پایه با سطوح دسترسی `public` و `protected` در کلاس مشتق شده قابل دسترسی هستند.

```csharp
public class Animal
{
    public string Name { get; set; }        // public → قابل دسترسی
    protected int Age { get; set; }         // protected → فقط در کلاس‌های مشتق شده قابل دسترسی
    private string Secret { get; set; }      // private → فقط در خود کلاس Animal
}

public class Dog : Animal
{
    public void ShowInfo()
    {
        Console.WriteLine(Name);   // ✅ OK
        Console.WriteLine(Age);    // ✅ OK
        // Console.WriteLine(Secret); // ❌ خطا — private است
    }
}
```

---

## 6. کلیدواژه `base` — چیست و چه کاربردی دارد؟

کلیدواژه `base` در C# به شما اجازه می‌دهد به **اعضای کلاس پایه** از داخل کلاس مشتق شده دسترسی داشته باشید — حتی اگر آن اعضا **override** شده باشند.

### کاربردهای `base`:

1. فراخوانی **سازنده (Constructor)** کلاس پایه.
2. فراخوانی **متدها و ویژگی‌های** کلاس پایه — مخصوصاً وقتی override شده‌اند.
3. دسترسی به اعضای protected کلاس پایه.

---

## 7. استفاده از `base` برای فراخوانی سازنده کلاس پایه

وقتی کلاس مشتق شده سازنده دارد، معمولاً باید سازنده کلاس پایه را فراخوانی کنید — مگر اینکه سازنده پیش‌فرض (بدون پارامتر) وجود داشته باشد.

### مثال:

```csharp
public class Animal
{
    public string Name { get; set; }

    public Animal(string name)
    {
        Name = name;
    }
}

public class Dog : Animal
{
    public string Breed { get; set; }

    // سازنده Dog باید سازنده Animal را فراخوانی کند
    public Dog(string name, string breed) : base(name)  // 👈 base(name)
    {
        Breed = breed;
    }
}
```

📌 **نکته**: اگر کلاس پایه فقط یک سازنده با پارامتر داشته باشد، **اجباری** است که کلاس مشتق شده آن را با `base(...)` فراخوانی کند.

---

## 8. استفاده از `base` برای فراخوانی متدها و ویژگی‌های کلاس پایه

وقتی یک متد در کلاس مشتق شده **override** می‌شود، ممکن است بخواهید رفتار اصلی کلاس پایه را هم حفظ کنید. اینجا `base` به کمک می‌آید.

### مثال:

```csharp
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
        base.MakeSound();  // 👈 فراخوانی متد کلاس پایه
        Console.WriteLine("Woof woof!");
    }
}
```

خروجی فراخوانی `MakeSound()` روی یک شیء `Dog`:

```
Some generic animal sound
Woof woof!
```

---

## 9. Override کردن متدها و استفاده از `base` در آن

برای override کردن یک متد در کلاس مشتق شده:

1. متد در کلاس پایه باید `virtual`، `abstract` یا `override` باشد.
2. در کلاس مشتق شده از کلیدواژه `override` استفاده کنید.
3. می‌توانید با `base.MethodName()` به نسخه پایه دسترسی داشته باشید.

### مثال کامل:

```csharp
public class Shape
{
    public virtual double CalculateArea()
    {
        return 0;
    }
}

public class Circle : Shape
{
    public double Radius { get; set; }

    public Circle(double radius)
    {
        Radius = radius;
    }

    public override double CalculateArea()
    {
        // می‌توانیم محاسبه جدید اضافه کنیم و یا کاملاً جایگزین کنیم
        return Math.PI * Radius * Radius;
    }
}

public class LoggingCircle : Circle
{
    public LoggingCircle(double radius) : base(radius) { }

    public override double CalculateArea()
    {
        double area = base.CalculateArea();  // 👈 استفاده از نسخه پایه
        Console.WriteLine($"Area calculated: {area}");
        return area;
    }
}
```

---

## 10. محدودیت‌ها و نکات مهم

✅ **اجازه دارد**:
- استفاده از `base` در سازنده‌ها و متدها.
- فراخوانی اعضای public/protected کلاس پایه.
- استفاده در کلاس‌های sealed (اما فقط اگر از کلاسی به ارث برده باشند).

❌ **اجازه ندارد**:
- استفاده از `base` در متد‌های static.
- فراخوانی اعضای private کلاس پایه.
- استفاده از `base.base` — فقط یک سطح بالاتر قابل دسترسی است.

⚠️ **نکات مهم**:
- اگر کلاس پایه `abstract` باشد، نمی‌توانید از آن شیء ایجاد کنید — فقط به ارث می‌برید.
- کلاس‌های `sealed` نمی‌توانند به ارث برده شوند.
- `base()` فقط در تعریف سازنده‌ها و قبل از بدنه آن‌ها مجاز است.

---

## 11. مثال‌های کاربردی

### مثال ۱: سلسله مراتب کارمندان

```csharp
public class Employee
{
    public string Name { get; set; }
    public decimal Salary { get; set; }

    public Employee(string name, decimal salary)
    {
        Name = name;
        Salary = salary;
    }

    public virtual void DisplayInfo()
    {
        Console.WriteLine($"Name: {Name}, Salary: {Salary:C}");
    }
}

public class Manager : Employee
{
    public string Department { get; set; }

    public Manager(string name, decimal salary, string department)
        : base(name, salary)
    {
        Department = department;
    }

    public override void DisplayInfo()
    {
        base.DisplayInfo();  // 👈 نمایش اطلاعات پایه
        Console.WriteLine($"Department: {Department}");
    }
}
```

### مثال ۲: کلاس Vehicle و Car

```csharp
public class Vehicle
{
    public string Brand { get; set; }
    public int Year { get; set; }

    public Vehicle(string brand, int year)
    {
        Brand = brand;
        Year = year;
    }

    public virtual void Start()
    {
        Console.WriteLine("Vehicle is starting...");
    }
}

public class Car : Vehicle
{
    public int Doors { get; set; }

    public Car(string brand, int year, int doors) : base(brand, year)
    {
        Doors = doors;
    }

    public override void Start()
    {
        base.Start();  // 👈 فراخوانی Start پایه
        Console.WriteLine($"Car with {Doors} doors is ready to go!");
    }
}
```

---

## 12. جمع‌بندی

🔹 **Base Class**: کلاسی که ویژگی‌های مشترک را تعریف می‌کند.  
🔹 **Derived Class**: کلاسی که از Base به ارث می‌برد و می‌تواند آن را گسترش دهد.  
🔹 **کلیدواژه `base`**: ابزاری برای دسترسی به اعضای کلاس پایه — مخصوصاً در override و سازنده‌ها.  
🔹 **وراثت** باعث کاهش تکرار کد و افزایش قابلیت نگهداری می‌شود.  
🔹 C# فقط از **وراثت تکی** (از یک کلاس پایه) پشتیبانی می‌کند — اما می‌توانید از چندین Interface استفاده کنید.

---

## 13. منابع معتبر

1. 📘 **Microsoft Learn — Inheritance in C#**  
   https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance

2. 📘 **Microsoft Learn — base keyword**  
   https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/base

3. 📘 **C# in Depth — Jon Skeet (Manning Publications)**  
   فصل مربوط به وراثت و چندشکلی

4. 📘 **Pro C# 10 with .NET 6 — Andrew Troelsen & Phil Japikse (Apress)**  
   فصل 7: Object-Oriented Programming with C#

5. 📘 **C# 10.0 and .NET 6 – Modern Cross-Platform Development — Mark J. Price (Packt)**  
   فصل 7: Building Your Own Types with Object-Oriented Programming

6. 📘 **Stack Overflow — Base Keyword Usage**  
   https://stackoverflow.com/questions/1089123/what-does-the-base-keyword-do

---

