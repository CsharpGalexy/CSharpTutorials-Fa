## 📖 فهرست مطالب

1. [مقدمه: چرا Constructor Chaining مهم است؟](#1-مقدمه-چرا-constructor-chaining-مهم-است)  
2. [مفهوم Constructor Chaining چیست؟](#2-مفهوم-constructor-chaining-چیست)  
3. [انواع Constructor Chaining در C#](#3-انواع-constructor-chaining-در-c)  
4. [Chaining با استفاده از `this` — داخل یک کلاس](#4-chaining-با-استفاده-از-this--داخل-یک-کلاس)  
5. [Chaining با استفاده از `base` — به کلاس پایه](#5-chaining-با-استفاده-از-base--به-کلاس-پایه)  
6. [چرا Constructor Chaining مفید است؟](#6-چرا-constructor-chaining-مفید-است)  
7. [قوانین و محدودیت‌های Constructor Chaining](#7-قوانین-و-محدودیت%E2%80%8Cهای-constructor-chaining)  
8. [مثال‌های کاربردی و واقعی](#8-مثال%E2%80%8Cهای-کاربردی-و-واقعی)  
9. [مقایسه با رویکردهای قدیمی (تکرار کد)](#9-مقایسه-با-رویکردهای-قدیمی-تکرار-کد)  
10. [نکات بهینه‌سازی و بهترین روش‌ها (Best Practices)](#10-نکات-بهینه%E2%80%8Cسازی-و-بهترین-روش%E2%80%8Cها-best-practices)  
11. [جمع‌بندی](#11-جمع%E2%80%8Cبندی)  
12. [منابع معتبر](#12-منابع-معتبر)

---

## 1. مقدمه: چرا Constructor Chaining مهم است؟

در برنامه‌نویسی شیءگرا، **سازنده‌ها (Constructors)** نقش حیاتی در مقداردهی اولیه اشیاء دارند. اما وقتی یک کلاس چندین سازنده داشته باشد، ممکن است **تکرار کد** ایجاد شود — چیزی که باعث کاهش خوانایی، نگهداری سخت‌تر و افزایش خطا می‌شود.

🔹 **Constructor Chaining** راه‌حلی هوشمندانه است که به شما اجازه می‌دهد سازنده‌ها را به هم متصل کنید تا از تکرار کد جلوگیری کنید و منطق مقداردهی را متمرکز نگه دارید.

---

## 2. مفهوم Constructor Chaining چیست؟

**Constructor Chaining** به معنی فراخوانی یک سازنده از داخل سازنده دیگر — در **همان کلاس** (`this`) یا در **کلاس پایه** (`base`) — است.

هدف:  
✅ جلوگیری از تکرار کد  
✅ متمرکز کردن منطق مقداردهی  
✅ کاهش احتمال خطا  
✅ افزایش خوانایی و نگهداری کد

---

## 3. انواع Constructor Chaining در C#

در C# دو نوع اصلی Constructor Chaining وجود دارد:

| نوع | کلیدواژه | توضیح |
|------|----------|--------|
| **داخلی (Intra-class)** | `this` | فراخوانی سازنده دیگر در همان کلاس |
| **به کلاس پایه (Base class)** | `base` | فراخوانی سازنده کلاس پایه از کلاس مشتق شده |

---

## 4. Chaining با استفاده از `this` — داخل یک کلاس

وقتی یک کلاس چند سازنده دارد، می‌توانید از `this` برای فراخوانی یکی از سازنده‌های دیگر استفاده کنید.

### 🔹 نحوه نوشتن:

```csharp
public ClassName(parameters) : this(otherParameters)
{
    // کد اضافی (اختیاری)
}
```

📌 **نکته مهم**:  
فراخوانی `this(...)` **باید اولین دستور** در سازنده باشد.

### 🔹 مثال:

```csharp
public class Person
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public int Age { get; set; }

    // سازنده اصلی — تمام فیلدها را مقداردهی می‌کند
    public Person(string firstName, string lastName, int age)
    {
        FirstName = firstName;
        LastName = lastName;
        Age = age;
    }

    // سازنده زنجیره‌ای — فقط نام و نام خانوادگی — سن پیش‌فرض 0
    public Person(string firstName, string lastName) : this(firstName, lastName, 0)
    {
        // نیازی به مقداردهی مجدد نیست — this(...) انجام داده است
    }

    // سازنده پیش‌فرض — همه چیز پیش‌فرض
    public Person() : this("Unknown", "Unknown", 0)
    {
    }
}
```

### 🔹 استفاده:

```csharp
var p1 = new Person();                          // Unknown Unknown, 0
var p2 = new Person("Ali", "Rezaei");           // Ali Rezaei, 0
var p3 = new Person("Sara", "Ahmadi", 25);      // Sara Ahmadi, 25
```

---

## 5. Chaining با استفاده از `base` — به کلاس پایه

وقتی یک کلاس از کلاس دیگری به ارث می‌برد، سازنده کلاس مشتق شده **باید** سازنده کلاس پایه را فراخوانی کند — مگر اینکه کلاس پایه یک سازنده پیش‌فرض داشته باشد.

### 🔹 نحوه نوشتن:

```csharp
public DerivedClass(parameters) : base(baseParameters)
{
    // کد اضافی
}
```

📌 **نکته**:  
اگر کلاس پایه **فقط** سازنده‌های پارامتردار داشته باشد، فراخوانی `base(...)` **اجباری** است.

### 🔹 مثال:

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

    // فراخوانی سازنده کلاس پایه با base
    public Dog(string name, string breed) : base(name)
    {
        Breed = breed;
    }
}
```

---

## 6. چرا Constructor Chaining مفید است؟

✅ **کاهش تکرار کد**: منطق مقداردهی فقط در یک جا نوشته می‌شود.  
✅ **قابلیت نگهداری**: تغییر در یک نقطه، همه موارد را به‌روز می‌کند.  
✅ **کاهش خطا**: احتمال فراموش کردن مقداردهی فیلدها کاهش می‌یابد.  
✅ **خوانایی بهتر**: رابطه بین سازنده‌ها شفاف است.

---

## 7. قوانین و محدودیت‌های Constructor Chaining

✅ **اجازه دارد**:
- استفاده از `this(...)` در سازنده برای فراخوانی سازنده دیگر در همان کلاس.
- استفاده از `base(...)` برای فراخوانی سازنده کلاس پایه.
- ترکیب `this` و `base` در یک زنجیره (غیرمستقیم).

❌ **اجازه ندارد**:
- استفاده از `this(...)` و `base(...)` در یک سازنده — فقط یکی از آن‌ها مجاز است.
- فراخوانی `this(...)` یا `base(...)` جز در ابتدای سازنده.
- استفاده از سینتکس زنجیره‌سازی سازنده (`: this(...)` یا `: base(...)`) در متدهای غیر سازنده.

⚠️ **نکات مهم**:
- سازنده پیش‌فرض (بدون پارامتر) به صورت خودکار ایجاد **نمی‌شود** اگر شما حداقل یک سازنده تعریف کنید.
- اگر `base(...)` فراموش شود و کلاس پایه فقط سازنده پارامتردار داشته باشد → **خطای کامپایل**.

---

## 8. مثال‌های کاربردی و واقعی

### 🔹 مثال ۱: کلاس Rectangle با زنجیره سازنده

```csharp
public class Rectangle
{
    public double Width { get; set; }
    public double Height { get; set; }

    // سازنده اصلی
    public Rectangle(double width, double height)
    {
        Width = width;
        Height = height;
    }

    // مربع — عرض و ارتفاع یکسان
    public Rectangle(double size) : this(size, size)
    {
    }

    // پیش‌فرض — مستطیل واحد
    public Rectangle() : this(1.0, 1.0)
    {
    }
}
```

### 🔹 مثال ۲: وراثت + زنجیره — کلاس Book و EBook

```csharp
public class Book
{
    public string Title { get; set; }
    public string Author { get; set; }

    public Book(string title, string author)
    {
        Title = title;
        Author = author;
    }
}

public class EBook : Book
{
    public string FileFormat { get; set; }  // e.g. PDF, EPUB

    // زنجیره به کلاس پایه + مقداردهی محلی
    public EBook(string title, string author, string format) : base(title, author)
    {
        FileFormat = format;
    }

    // سازنده با فرمت پیش‌فرض
    public EBook(string title, string author) : this(title, author, "PDF")
    {
    }
}
```

### 🔹 مثال ۳: زنجیره ترکیبی (this → this → base)

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
}

public class Car : Vehicle
{
    public int Doors { get; set; }

    public Car(string brand, int year, int doors) : base(brand, year)
    {
        Doors = doors;
    }

    // زنجیره داخلی به سازنده بالاتر در همین کلاس
    public Car(string brand, int doors) : this(brand, 2024, doors)
    {
    }

    // زنجیره به سازنده پیش‌فرض
    public Car() : this("Unknown", 4)
    {
    }
}
```

---

## 9. مقایسه با رویکردهای قدیمی (تکرار کد)

❌ **بدون Constructor Chaining — تکرار کد**:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Person()
    {
        Name = "Unknown";
        Age = 0;
    }

    public Person(string name)
    {
        Name = name;   // 👈 تکرار
        Age = 0;       // 👈 تکرار
    }

    public Person(string name, int age)
    {
        Name = name;   // 👈 تکرار
        Age = age;
    }
}
```

✅ **با Constructor Chaining — بدون تکرار**:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }

    public Person(string name) : this(name, 0) { }
    public Person() : this("Unknown", 0) { }
}
```

➡️ **مزیت**: تغییر در یک نقطه (مثلاً مقدار پیش‌فرض سن) فقط در یک جا انجام می‌شود.

---

## 10. نکات بهینه‌سازی و بهترین روش‌ها (Best Practices)

✅ **یک سازنده اصلی داشته باشید** — که تمام فیلدها را مقداردهی می‌کند — و بقیه سازنده‌ها به آن زنجیره شوند.

✅ **از پارامترهای پیش‌فرض استفاده نکنید در جایی که Constructor Chaining معنادارتر است** — چون زنجیره‌سازی انعطاف بیشتری دارد.

✅ **مستندسازی کنید** — اگر سازنده‌ها زنجیره‌ای هستند، در کامنت یا XML Doc ذکر کنید.

✅ **از زنجیره‌های طولانی خودداری کنید** — بیش از 3-4 لایه ممکن است خوانایی را کاهش دهد.

✅ **در کلاس‌های مشتق شده، سازنده مناسب کلاس پایه را به صورت صریح فراخوانی کنید** — این کار خوانایی را افزایش می‌دهد و از خطاهای احتمالی هنگام عدم وجود سازنده پیش‌فرض در کلاس پایه جلوگیری می‌کند.

---

## 11. جمع‌بندی

🔹 **Constructor Chaining** ابزاری قدرتمند برای جلوگیری از تکرار کد در سازنده‌هاست.  
🔹 با `this(...)` می‌توانید سازنده‌های داخل یک کلاس را به هم متصل کنید.  
🔹 با `base(...)` می‌توانید سازنده کلاس پایه را از کلاس مشتق شده فراخوانی کنید.  
🔹 رعایت قوانین (اولین دستور، عدم ترکیب this/base) ضروری است.  
🔹 استفاده صحیح از آن باعث کد تمیزتر، قابل نگهداری‌تر و حرفه‌ای‌تر می‌شود.

---

## 12. منابع معتبر

1. 📘 **Microsoft Learn — Using Constructors**  
   https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/using-constructors

2. 📘 **Microsoft Learn — Constructor Chaining in C#**  
   https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/instance-constructors#constructor-chaining

3. 📘 **C# in Depth — Jon Skeet (Manning Publications)**  
   فصل 4: Expressing designs in C# — بخش مربوط به سازنده‌ها

4. 📘 **Pro C# 10 with .NET 6 — Andrew Troelsen & Phil Japikse (Apress)**  
   فصل 5: Understanding Encapsulation — Constructor Chaining

5. 📘 **C# 10.0 and .NET 6 – Modern Cross-Platform Development — Mark J. Price (Packt)**  
   فصل 7: Building Your Own Types with Object-Oriented Programming — Constructor Best Practices

6. 📘 **Stack Overflow — What is constructor chaining and how is it done in C#?**  
   https://stackoverflow.com/questions/1814953/what-is-constructor-chaining-and-how-is-it-done-in-c

7. 📘 **C# Station — Tutorial 15: Constructors and Destructors**  
   http://www.csharp-station.com/Tutorial/CSharp/lesson15

