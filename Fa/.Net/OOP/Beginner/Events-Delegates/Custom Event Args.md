

## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [Delegate چیست؟](#delegate-%DA%86%DB%8C%D8%B3%D8%AA)
3. [Event چیست؟](#event-%DA%86%DB%8C%D8%B3%D8%AA)
4. [EventArgs و Custom EventArgs](#eventargs-و-custom-eventargs)
5. [الگوی طراحی Event در C# (Event Design Pattern)](#الگوی-طراحی-event-در-c-event-design-pattern)
6. [مثال کاربردی: ساخت یک کلاس با Event و Custom EventArgs](#مثال-کاربردی-ساخت-یک-کلاس-با-event-و-custom-eventargs)
7. [نکات بهینه‌سازی و بهترین روش‌ها (Best Practices)](#%D9%86%DA%A9%D8%A7%D8%AA-%D8%A8%D9%87%DB%8C%D9%86%D9%87%E2%80%8C%D8%B3%D8%A7%D8%B2%DB%8C-%D9%88-%D8%A8%D9%87%D8%AA%D8%B1%DB%8C%D9%86-%D8%B1%D9%88%D8%B4%E2%80%8C%D9%87%D8%A7-best-practices)
8. [جمع‌بندی](#%D8%AC%D9%85%D8%B9%E2%80%8C%D8%A8%D9%86%D8%AF%DB%8C)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP)، **Event** و **Delegate** از مفاهیم کلیدی برای پیاده‌سازی الگوهای طراحی مانند **Observer** هستند. آن‌ها به شما این امکان را می‌دهند که کامپوننت‌ها به صورت **loosely coupled** (با کوپلینگ ضعیف) با یکدیگر تعامل داشته باشند.  
در C#، برای ارسال اطلاعات همراه با یک رویداد (Event)، از کلاس‌هایی مانند `EventArgs` و زیرکلاس‌های سفارشی آن (`Custom EventArgs`) استفاده می‌شود.

---

## Delegate چیست؟

### تعریف
**Delegate** یک نوع داده (Type) است که به یک متد (Method) اشاره می‌کند. به عبارت دیگر، Delegate یک **اشاره‌گر به متد** است که امکان ذخیره‌سازی و فراخوانی متد را فراهم می‌کند.

### ویژگی‌ها
- شبیه به Function Pointer در C++ ولی **نوع‌گرا (Type-safe)**.
- می‌تواند به متد‌های استاتیک یا نمونه (Instance) اشاره کند.
- پشتیبانی از **Multicast Delegates** (چندین متد را می‌توان به یک Delegate متصل کرد).

### نحوه تعریف
```csharp
public delegate void MyDelegate(string message);
```

### استفاده
```csharp
MyDelegate del = new MyDelegate(Console.WriteLine);
del("Hello from delegate!");
```

> ⚠️ از C# 2.0 به بعد، می‌توانید از **Method Group Conversion** استفاده کنید:
```csharp
MyDelegate del = Console.WriteLine;
```

---

## Event چیست؟

### تعریف
**Event** یک عضو کلاس است که امکان **اشتراک‌گذاری رویدادها** بین کلاس‌ها را فراهم می‌کند. Event بر پایه Delegate ساخته می‌شود و **Encapsulation** را برای Delegate اعمال می‌کند.

### چرا Event به جای Delegate مستقیم؟
- Event فقط اجازه **+=** و **-=** را به خارج از کلاس می‌دهد (نه فراخوانی مستقیم).
- این کار امنیت و کپسوله‌سازی را حفظ می‌کند.

### نحوه تعریف
```csharp
public event EventHandler MyEvent;
```

یا با Delegate سفارشی:
```csharp
public event MyDelegate CustomEvent;
```

---

## EventArgs و Custom EventArgs

### EventArgs چیست؟
`EventArgs` کلاس پایه‌ای است که برای انتقال داده‌های مرتبط با یک رویداد استفاده می‌شود. اگر رویداد شما نیازی به ارسال داده ندارد، از `EventArgs.Empty` استفاده می‌شود.

### ساخت Custom EventArgs
برای ارسال اطلاعات سفارشی همراه با Event، یک کلاس از `EventArgs` ارث‌بری می‌کنیم:

```csharp
public class TemperatureChangedEventArgs : EventArgs
{
    public double OldTemperature { get; }
    public double NewTemperature { get; }

    public TemperatureChangedEventArgs(double oldTemp, double newTemp)
    {
        OldTemperature = oldTemp;
        NewTemperature = newTemp;
    }
}
```

---

## الگوی طراحی Event در C# (Event Design Pattern)

مایکروسافت یک **الگوی استاندارد** برای پیاده‌سازی Event در C# تعریف کرده است:

### امضای استاندارد Event Handler
```csharp
public delegate void EventHandler<TEventArgs>(object sender, TEventArgs e)
    where TEventArgs : EventArgs;
```

### قوانین طلایی
1. نام Event باید با **Changed**، **ing** (برای قبل از اتفاق) یا **ed** (برای بعد از اتفاق) تمام شود.  
   مثال: `TemperatureChanged`, `Saving`, `Saved`
2. همیشه از `EventHandler<T>` استفاده کنید مگر در موارد خاص.
3. همیشه یک متد محافظت‌شده (protected virtual) برای فراخوانی Event ایجاد کنید:  
   ```csharp
   protected virtual void OnTemperatureChanged(TemperatureChangedEventArgs e)
   {
       TemperatureChanged?.Invoke(this, e);
   }
   ```

---

## مثال کاربردی: ساخت یک کلاس با Event و Custom EventArgs

```csharp
using System;

// 1. Custom EventArgs
public class TemperatureChangedEventArgs : EventArgs
{
    public double OldValue { get; }
    public double NewValue { get; }

    public TemperatureChangedEventArgs(double oldVal, double newVal)
    {
        OldValue = oldVal;
        NewValue = newVal;
    }
}

// 2. کلاس اصلی
public class Thermostat
{
    private double _temperature;

    // 3. Event با Custom EventArgs
    public event EventHandler<TemperatureChangedEventArgs> TemperatureChanged;

    public double Temperature
    {
        get => _temperature;
        set
        {
            if (_temperature != value)
            {
                double oldTemp = _temperature;
                _temperature = value;
                // 4. فراخوانی Event
                OnTemperatureChanged(new TemperatureChangedEventArgs(oldTemp, value));
            }
        }
    }

    // 5. متد محافظت‌شده برای فراخوانی Event
    protected virtual void OnTemperatureChanged(TemperatureChangedEventArgs e)
    {
        TemperatureChanged?.Invoke(this, e);
    }
}

// 6. استفاده در برنامه اصلی
class Program
{
    static void Main()
    {
        var thermostat = new Thermostat();
        thermostat.TemperatureChanged += OnTemperatureChanged;

        thermostat.Temperature = 25.5;
        thermostat.Temperature = 30.0;
    }

    static void OnTemperatureChanged(object sender, TemperatureChangedEventArgs e)
    {
        Console.WriteLine($"Temperature changed from {e.OldValue} to {e.NewValue}");
    }
}
```

**خروجی:**
```
Temperature changed from 0 to 25.5
Temperature changed from 25.5 to 30
```

---

## نکات بهینه‌سازی و بهترین روش‌ها (Best Practices)

✅ **همیشه از `EventHandler<T>` استفاده کنید** مگر اینکه نیاز خاصی داشته باشید.  
✅ **Event را null نکنید** — از Null-Conditional Operator (`?.`) استفاده کنید.  
✅ **از کلمه کلیدی `virtual` برای متد `OnEventName` استفاده کنید** تا کلاس‌های فرزند بتوانند رفتار را تغییر دهند.  
✅ **Custom EventArgs را immutable (غیرقابل تغییر) نگه دارید** — فقط از Propertyهای `get` استفاده کنید.  
❌ **Event را مستقیماً از خارج کلاس invoke نکنید** — فقط کلاس مالک Event می‌تواند آن را فراخوانی کند.

---

## جمع‌بندی

- **Delegate**: اشاره‌گر امن به متد.
- **Event**: رابط امن برای اشتراک‌گذاری رویدادها بین کلاس‌ها.
- **EventArgs**: حامل داده‌های مرتبط با رویداد.
- **Custom EventArgs**: برای ارسال اطلاعات سفارشی همراه Event.
- رعایت **الگوی استاندارد مایکروسافت** برای نوشتن کد قابل نگهداری و استاندارد.

این سه مفهوم در کنار هم، پایه‌های **برنامه‌نویسی رخدادمحور (Event-Driven Programming)** در C# را تشکیل می‌دهند و در فریم‌ورک‌هایی مانند WPF، ASP.NET و Unity به‌طور گسترده استفاده می‌شوند.

---

## منابع معتبر

1. **Microsoft Learn – Delegates**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

2. **Microsoft Learn – Events**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)

3. **Microsoft Learn – EventArgs Class**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.eventargs](https://learn.microsoft.com/en-us/dotnet/api/system.eventargs)

4. **Framework Design Guidelines – Event Design** (by Krzysztof Cwalina & Brad Abrams)  
   [https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/event](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/event)

5. **C# in Depth – Jon Skeet** (فصل مربوط به Delegates و Events)  
   [https://csharpindepth.com/](https://csharpindepth.com/)

6. **.NET Documentation – EventHandler<TEventArgs> Delegate**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.eventhandler-1](https://learn.microsoft.com/en-us/dotnet/api/system.eventhandler-1)
