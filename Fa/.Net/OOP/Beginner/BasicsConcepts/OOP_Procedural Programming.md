# OOP vs Procedural Programming 

# فهرست مطالب

- [🟢 مفاهیم پایه](#-مفاهیم-پایه)
- [🔹 Procedural Programming (رویه‌ای)](#-procedural-programming-رویه‌ای)
- [🔹 Object-Oriented Programming (شیءگرا)](#-object-oriented-programming-شیءگرا)
- [🔸 تفاوت کلیدی در منطق انتخاب (Selection)](#-تفاوت-کلیدی-در-منطق-انتخاب-selection)
- [🟠 شباهت‌ها](#-شباهتها)
- [📊 جدول مقایسه کامل](#-جدول-مقایسه-کامل)
- [📊 جدول مقایسه ساده](#-جدول-مقایسه-ساده)
- [💻 کد نمونه Procedural در C#](#-کد-نمونه-procedural-در-c)
- [💻 کد نمونه OOP در C#](#-کد-نمونه-oop-در-c)


---

# 🟢 مفاهیم پایه

- **State (وضعیت/داده):** اطلاعاتی که برنامه نگه می‌دارد.  
- **Behavior (رفتار/منطق):** کارهایی که روی داده انجام می‌شود.  

---

## 🔹 Procedural Programming (رویه‌ای)

- داده و رفتار جدا از هم هستند.  
- داده‌ها داخل **ساختارهای داده‌ای** ذخیره می‌شوند.  
- رفتار (منطق) در **توابع/پروسیجرها** نوشته می‌شود.  
- برای انجام یک کار، داده‌ها به توابع پاس داده می‌شوند.  

➡️ **مثال ساده:** تابعی که یک مستطیل می‌گیرد و مساحتش را حساب می‌کند.  

---

## 🔹 Object-Oriented Programming (شیءگرا)

- داده + رفتار در یک بسته به نام **شیء (Object)** قرار می‌گیرند.  
- برای انجام کار فقط با شیء صحبت می‌کنیم، نیازی به پاس دادن ساختار داده نیست.  
- این موضوع باعث **کپسوله‌سازی (Encapsulation)** و **پنهان کردن اطلاعات** می‌شود.  

➡️ **مثال ساده:** کلاس `Rectangle` خودش طول و عرض را نگه می‌دارد و متدی برای محاسبه مساحت دارد.  

---

## 🔸 تفاوت کلیدی در منطق انتخاب (Selection)

- در **Procedural**: انتخاب با **if/else** و ساختارهای شرطی انجام می‌شود.  
- در **OOP**: انتخاب بیشتر با **Polymorphism** انجام می‌شود  
  (یعنی چند کلاس مختلف می‌توانند یک متد مشترک داشته باشند و هرکدام رفتار مخصوص خود را پیاده‌سازی کنند).  

---

## 🟠 شباهت‌ها

- هر دو در دسته **Imperative Programming (برنامه‌نویسی دستوری)** هستند.  
- هر دو با تغییر وضعیت (**State Mutation**) و دستورالعمل‌های مرحله به مرحله کار می‌کنند.  
- مثل نوشتن یک دستور آشپزی: *"این کار رو بکن، بعد اون رو انجام بده..."*  


| ویژگی                    | برنامه‌نویسی رویه‌ای (Procedural)                    | برنامه‌نویسی شیءگرا (OOP)                                        |
| ------------------------ | ---------------------------------------------------- | ---------------------------------------------------------------- |
| بخش‌بندی برنامه          | برنامه به بخش‌های کوچک به نام **توابع** تقسیم می‌شود | برنامه به بخش‌های کوچک به نام **اشیاء** تقسیم می‌شود             |
| رویکرد                   | **Top-down** (بالا به پایین)                         | **Bottom-up** (پایین به بالا)                                    |
| دسترسی به داده‌ها        | دسترسی مشخص‌کننده ندارد                              | دارای **Access Specifiers** مثل `private`، `public`، `protected` |
| افزودن داده و توابع جدید | سخت است                                              | آسان است                                                         |
| امنیت داده‌ها            | داده‌ها مخفی نمی‌شوند، کمتر امن است                  | داده‌ها مخفی می‌شوند (**Data Hiding**) و امن‌تر است              |
| اورلودینگ                | ممکن نیست                                            | ممکن است (**Method Overloading**)                                |
| مفاهیم شیءگرا            | **Inheritance** و **Data Hiding** وجود ندارد         | از **Inheritance** و **Data Hiding** استفاده می‌کند              |
| اهمیت                    | تابع مهم‌تر از داده است                              | داده مهم‌تر از تابع است                                          |
| مدل طراحی                | بر اساس دنیای **غیر واقعی**                          | بر اساس دنیای **واقعی**                                          |
| اندازه برنامه            | برای برنامه‌های **متوسط** مناسب است                  | برای برنامه‌های **بزرگ و پیچیده** مناسب است                      |
| انتزاع                   | از مفهوم **Procedure Abstraction** استفاده می‌کند    | از مفهوم **Data Abstraction** استفاده می‌کند                     |
| قابلیت استفاده مجدد کد   | ندارد                                                | دارد (**Code Reusability**)                                      |
| مثال‌ها                  | C, FORTRAN, Pascal, Basic                            | C++, Java, Python, C#                                            |




| ویژگی               | Procedural                        | OOP                                    |
| ------------------- | --------------------------------- | -------------------------------------- |
| واحد اصلی           | تابع / پروسیجر                    | شیء                                    |
| تمرکز               | دستورالعمل‌ها و ترتیب اجرای آن‌ها | داده‌ها و رفتار اشیاء                  |
| دانستن جزئیات       | باید بداند چگونه انجام شود        | فقط می‌داند چه کاری باید انجام شود     |
| قابلیت استفاده مجدد | محدود                             | بالا، اشیاء قابل تعویض و ماژولار هستند |
| پروژه مناسب         | کوچک و ساده                       | بزرگ و پیچیده                          |


🔹 Procedural Programming در C#

در این روش همه چیز خطی و به صورت دستورالعمل‌ها و توابع ساده نوشته می‌شود.

```csharp
using System;
using System.Threading;

class Program
{
    static void Main()
    {
        // حرکت با توان کامل
        SetMotorPower(5);
        Thread.Sleep(2000);

        // چرخاندن فرمان
        SetSteering(15);
        Thread.Sleep(2000);

        // توقف
        SetMotorPower(0);
    }

    static void SetMotorPower(int volts)
    {
        Console.WriteLine($"Motor power set to {volts} volts");
    }

    static void SetSteering(int angle)
    {
        Console.WriteLine($"Steering set to {angle} degrees left");
    }
}

```

🔸 در اینجا Main همه مراحل را مستقیم و پشت سر هم اجرا می‌کند. هیچ «شیء» یا «کپسوله‌سازی» وجود ندارد.

🔹 Object-Oriented Programming در C#

در این روش همه چیز در قالب کلاس‌ها و اشیاء تعریف می‌شود.

```csharp
using System;
using System.Threading;

class Motor
{
    public void SetPower(int power)
    {
        Console.WriteLine($"Motor power set to {power}");
    }
}

class Steering
{
    public void SetAngle(int angle)
    {
        Console.WriteLine($"Steering set to {angle} degrees left");
    }
}

class CarController
{
    private Motor motor;
    private Steering steering;

    public CarController(Motor m, Steering s)
    {
        motor = m;
        steering = s;
    }

    public void DriveSequence()
    {
        motor.SetPower(5);
        Thread.Sleep(2000);

        steering.SetAngle(15);
        Thread.Sleep(2000);

        motor.SetPower(0);
    }
}

class Program
{
    static void Main()
    {
        Motor motor = new Motor();
        Steering steering = new Steering();
        CarController car = new CarController(motor, steering);

        car.DriveSequence();
    }
}
```
