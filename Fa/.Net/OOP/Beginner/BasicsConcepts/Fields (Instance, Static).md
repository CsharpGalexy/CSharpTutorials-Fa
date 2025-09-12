# 📘 Fields در C#

## تعریف
**Field**‌ها متغیرهایی هستن که مستقیماً داخل یک **class** یا **struct** تعریف می‌شن و بخشی از اعضای اون نوع محسوب می‌شن.  
برخلاف متغیرهای محلی (local variables)، طول عمر فیلدها به طول عمر **شیء** یا **کلاس** بستگی داره و می‌تونن توسط چندین متد داخل کلاس مورد استفاده قرار بگیرن.

Fieldها به دو دسته‌ی اصلی تقسیم می‌شن:
- **Instance Fields** (وابسته به هر شیء)
- **Static Fields** (وابسته به خود کلاس و مشترک بین همه‌ی شیءها)

---

## 🟦 Instance Fields

### تعریف
Instance Fields به هر نمونه (instance) از کلاس تعلق دارن. یعنی برای هر شیء ساخته‌شده، یک کپی جدا از این فیلدها وجود داره. تغییر مقدار فیلد در یک شیء، روی شیء دیگه اثری نداره.  

### نحوه کار
- وقتی یک شیء جدید ساخته می‌شه، حافظه‌ی جداگانه‌ای برای فیلدهای instance اون اختصاص داده می‌شه.  
- این فیلدها **قبل از اجرای constructor** مقداردهی اولیه می‌شن.  
- مقداردهی اولیه‌ی مستقیم فقط می‌تونه با مقادیر ثابت یا literal انجام بشه (نه فیلدهای دیگر).  

### زمان استفاده
وقتی داده‌های هر شیء باید مستقل از شیءهای دیگر باشه.  
مثلاً تاریخ تولد هر شخص در یک کلاس `Person`.

### نمونه کد
```csharp
public class CalendarEntry
{
    // فیلد instance خصوصی (backing store برای پراپرتی Date)
    private DateTime _date;

    // پراپرتی عمومی که _date رو کنترل می‌کنه
    public DateTime Date
    {
        get { return _date; }
        set
        {
            if (value.Year > 1900 && value.Year <= DateTime.Today.Year)
                _date = value;
            else
                throw new ArgumentOutOfRangeException("Date");
        }
    }

    // فیلد instance عمومی (توصیه نمی‌شه بدون کنترل دسترسی)
    public string? Day;
}
```

---

## 🟩 Static Fields

### تعریف
Static Fields به خود کلاس تعلق دارن، نه به شیء خاص.  
فقط یک کپی از این فیلد برای کل برنامه وجود داره و همه‌ی شیءها اون رو به اشتراک می‌ذارن.

### نحوه کار
- حافظه برای static field فقط یک بار اختصاص داده می‌شه (حتی اگر هیچ شیئی ساخته نشه).  
- برای دسترسی از نام کلاس استفاده می‌کنیم، نه از `this`.  
- اگر کلاس generic باشه، هر نوع بسته‌شده (closed generic type) یک کپی جدا از static field خواهد داشت.  

### زمان استفاده
وقتی داده‌ای باید بین همه‌ی شیءها مشترک باشه. مثل شمارنده‌ی تعداد کل کارمندان یا تنظیمات سراسری.

### نمونه کد
```csharp
public class Employee
{
    public string Id;   // فیلد instance
    public string Name; // فیلد instance

    public static int EmployeeCounter; // فیلد static

    public static int AddEmployee()
    {
        return ++EmployeeCounter; // افزایش فیلد static
    }

    public Employee(string name, string id)
    {
        Name = name;
        Id = id;
    }
}

class Program
{
    static void Main()
    {
        Employee e = new Employee("Ali", "1001");

        Employee.EmployeeCounter = 10; // دسترسی به فیلد static از طریق کلاس
        Employee.AddEmployee();

        Console.WriteLine($"Name: {e.Name}");
        Console.WriteLine($"ID:   {e.Id}");
        Console.WriteLine($"New Number of Employees: {Employee.EmployeeCounter}");
    }
}
```

---

## 🟨 مقداردهی اولیه فیلدها

فیلدها می‌تونن:

- مستقیماً موقع تعریف مقداردهی بشن:
```csharp
public class MyClass
{
    private int _x = 10;                     // مقداردهی اولیه instance
    public static string Greeting = "Hello"; // مقداردهی اولیه static
}
```

- در constructor یا static constructor مقداردهی بشن (برای مقادیر پیچیده‌تر).

---

## 🟥 const و readonly Fields

علاوه بر instance و static، فیلدها می‌تونن به صورت `const` یا `readonly` تعریف بشن:

### const
- مقدارش در زمان کامپایل مشخص می‌شه.  
- به طور پیش‌فرض static هست.  
- قابل تغییر نیست.  

### readonly
- مقدارش فقط در هنگام تعریف یا داخل constructor قابل تنظیمه.  
- بعد از اون تغییرناپذیره.  

### نمونه کد
```csharp
public class Circle
{
    public const double Pi = 3.14159; // ثابت
    public readonly int Radius;       // فقط در constructor مقداردهی می‌شه

    public Circle(int radius)
    {
        Radius = radius;
    }
}
```

---

## 🔑 تفاوت‌های کلیدی

| ویژگی              | Instance Fields           | Static Fields                      |
|---------------------|---------------------------|-------------------------------------|
| **مالکیت**         | متعلق به هر شیء           | متعلق به کلاس                      |
| **تعداد کپی**       | یک کپی برای هر شیء        | یک کپی مشترک برای کل کلاس          |
| **دسترسی**         | از طریق شیء (`obj.field`) | از طریق کلاس (`Class.field`)       |
| **اشتراک‌گذاری**   | داده‌ها مستقل             | داده‌ها مشترک بین همه شیءها        |
| **طول عمر**        | تا زمانی که شیء وجود دارد | تا پایان اجرای برنامه              |
| **مقداردهی اولیه** | قبل از اجرای constructor | قبل از اولین استفاده یا در static constructor |

---

## ✅ بهترین شیوه‌ها (Best Practices)

- فیلدها رو `private` یا `protected` تعریف کن و از **property** برای کنترل دسترسی استفاده کن.  
- از static constructor برای مقداردهی پیچیده‌ی فیلدهای static استفاده کن.  
- در برنامه‌های چندنخی (multi-threaded) برای فیلدهای static به **thread safety** توجه کن (مثلاً از `lock` یا `[ThreadStatic]` استفاده کن).  
- برای داده‌های تغییرناپذیر از `const` یا `readonly` استفاده کن.  
- از فیلدهای عمومی (public fields) اجتناب کن؛ چون می‌تونن داده‌های نامعتبر دریافت کنن.  

---

## 🔗 منابع معتبر
- [Microsoft Docs: Fields](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)  
- [Microsoft Docs: static](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/static)  
- [Microsoft Docs: readonly](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/readonly)  
- [Microsoft Docs: const](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/const)  
  
- 