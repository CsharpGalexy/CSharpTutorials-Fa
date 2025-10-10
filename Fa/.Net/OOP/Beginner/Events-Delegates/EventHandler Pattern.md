
## فهرست مطالب

1. [مقدمه‌ای بر رویدادها (Events) و نمایندگان (Delegates)](#%D9%85%D9%82%D8%AF%D9%85%D9%87%E2%80%8C%D8%A7%DB%8C-%D8%A8%D8%B1-%D8%B1%D9%88%DB%8C%D8%AF%D8%A7%D8%AF%D9%87%D8%A7-events-%D9%88-%D9%86%D9%85%D8%A7%DB%8C%D9%86%D8%AF%DA%AF%D8%A7%D9%86-delegates)
2. [چرا از الگوی EventHandler استفاده می‌کنیم؟](#%DA%86%D8%B1%D8%A7-%D8%A7%D8%B2-%D8%A7%D9%84%DA%AF%D9%88%DB%8C-eventhandler-%D8%A7%D8%B3%D8%AA%D9%81%D8%A7%D8%AF%D9%87-%D9%85%DB%8C%E2%80%8C%DA%A9%D9%86%DB%8C%D9%85)
3. [ساختار استاندارد الگوی EventHandler](#ساختار-استاندارد-الگوی-eventhandler)
4. [پیاده‌سازی ساده با EventHandler\<TEventArgs>](#پیادهسازی-ساده-با-eventhandlerteventargs)
5. [ایجاد رویدادهای سفارشی با EventArgs سفارشی](#ایجاد-رویدادهای-سفارشی-با-eventargs-سفارشی)
6. [تفاوت‌های مهم: delegate vs event vs EventHandler](#تفاوت‌های-مهم-delegate-vs-event-vs-eventhandler)
7. [نکات امنیتی و بهترین روش‌ها (Best Practices)](#نکات-امنیتی-و-بهترین-روش‌ها-best-practices)
8. [جمع‌بندی](#جمع‌بندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه‌ای بر رویدادها (Events) و نمایندگان (Delegates)

در برنامه‌نویسی شیءگرا (OOP)، **رویدادها (Events)** یک مکانیزم برای اطلاع‌رسانی از یک کلاس به کلاس‌های دیگر در مورد رخ دادن یک اتفاق خاص هستند. برای پیاده‌سازی رویدادها در C# از **نمایندگان (Delegates)** استفاده می‌شود.

- **Delegate**: نوعی اشاره‌گر به متد است که امکان انتقال متد به عنوان پارامتر را فراهم می‌کند.
- **Event**: یک عضو کلاس است که از delegate استفاده می‌کند و فقط امکان **اشتراک‌گذاری** (+=) و **لغو اشتراک** (-=) را فراهم می‌کند — نه فراخوانی مستقیم از خارج کلاس.

---

## چرا از الگوی EventHandler استفاده می‌کنیم؟

الگوی `EventHandler` یک **قرارداد استاندارد** در .NET است که نحوه تعریف و پردازش رویدادها را یکسان می‌کند. این کار مزایای زیر را دارد:

- **خوانایی کد**: توسعه‌دهندگان دیگر بلافاصله متوجه ساختار رویداد می‌شوند.
- **هماهنگی با کتابخانه‌های .NET**: تمام رویدادهای داخلی .NET (مثل `Button.Click`) از این الگو پیروی می‌کنند.
- **قابلیت گسترش**: با استفاده از `EventArgs` می‌توان اطلاعات اضافی به همراه رویداد ارسال کرد.

---

## ساختار استاندارد الگوی EventHandler

در .NET، دو نسخه اصلی از `EventHandler` وجود دارد:

### 1. `EventHandler` (بدون داده)
```csharp
public delegate void EventHandler(object sender, EventArgs e);
```

### 2. `EventHandler<TEventArgs>` (با داده سفارشی)
```csharp
public delegate void EventHandler<TEventArgs>(object sender, TEventArgs e)
    where TEventArgs : EventArgs;
```

هر دو نسخه دارای دو پارامتر ثابت هستند:

- **`sender`**: شیء‌ای که رویداد را ارسال کرده است (معمولاً `this`).
- **`e`**: شیء‌ای از نوع `EventArgs` یا زیرکلاس آن که داده‌های مرتبط با رویداد را حمل می‌کند.

---

## پیاده‌سازی ساده با `EventHandler<TEventArgs>`

در این مثال، یک کلاس `TemperatureMonitor` ایجاد می‌کنیم که هنگام عبور دما از یک آستانه، رویدادی را فعال می‌کند.

```csharp
using System;

// 1. تعریف رویداد با EventHandler استاندارد
public class TemperatureMonitor
{
    // رویداد با نوع استاندارد
    public event EventHandler TemperatureThresholdExceeded;

    private double _currentTemperature;
    public double Threshold { get; set; } = 30.0;

    public void UpdateTemperature(double newTemp)
    {
        _currentTemperature = newTemp;
        if (_currentTemperature > Threshold)
        {
            // فراخوانی رویداد (با امنیت null)
            OnTemperatureThresholdExceeded();
        }
    }

    protected virtual void OnTemperatureThresholdExceeded()
    {
        // الگوی امن برای فراخوانی رویداد
        TemperatureThresholdExceeded?.Invoke(this, EventArgs.Empty);
    }
}

// 2. استفاده از رویداد در کلاس دیگر
class Program
{
    static void Main()
    {
        var monitor = new TemperatureMonitor();
        monitor.TemperatureThresholdExceeded += OnThresholdExceeded;

        monitor.UpdateTemperature(35); // رویداد فعال می‌شود
    }

    static void OnThresholdExceeded(object sender, EventArgs e)
    {
        Console.WriteLine("دمای آستانه را رد کرده است!");
    }
}
```

---

## ایجاد رویدادهای سفارشی با `EventArgs` سفارشی

اگر نیاز به ارسال اطلاعات اضافی دارید، یک کلاس از `EventArgs` مشتق کنید:

```csharp
public class TemperatureChangedEventArgs : EventArgs
{
    public double CurrentTemperature { get; }
    public double Threshold { get; }

    public TemperatureChangedEventArgs(double currentTemp, double threshold)
    {
        CurrentTemperature = currentTemp;
        Threshold = threshold;
    }
}

public class AdvancedTemperatureMonitor
{
    // رویداد با EventArgs سفارشی
    public event EventHandler<TemperatureChangedEventArgs> TemperatureChanged;

    private double _currentTemperature;
    public double Threshold { get; set; } = 30.0;

    public void UpdateTemperature(double newTemp)
    {
        _currentTemperature = newTemp;
        OnTemperatureChanged(new TemperatureChangedEventArgs(_currentTemperature, Threshold));
    }

    protected virtual void OnTemperatureChanged(TemperatureChangedEventArgs e)
    {
        TemperatureChanged?.Invoke(this, e);
    }
}

// مصرف‌کننده
static void Main()
{
    var monitor = new AdvancedTemperatureMonitor();
    monitor.TemperatureChanged += (sender, e) =>
    {
        Console.WriteLine($"دمای فعلی: {e.CurrentTemperature}°C (آستانه: {e.Threshold}°C)");
    };

    monitor.UpdateTemperature(32);
}
```

---

## تفاوت‌های مهم: `delegate` vs `event` vs `EventHandler`

| مورد | توضیح |
|------|--------|
| **`delegate`** | یک نوع داده برای اشاره به متد. قابلیت فراخوانی مستقیم از هر جایی را دارد. |
| **`event`** | یک عضو کلاس که از delegate استفاده می‌کند، اما فقط اجازه `+=` و `-=` را از خارج کلاس می‌دهد. |
| **`EventHandler`** | یک delegate از پیش تعریف‌شده در .NET که ساختار استاندارد رویدادها را تعیین می‌کند. |

> 🔒 **نکته امنیتی**: استفاده از `event` به جای `public delegate` جلوی فراخوانی یا null کردن تصادفی رویداد را می‌گیرد.

---

## نکات امنیتی و بهترین روش‌ها (Best Practices)

1. **همیشه از `?.Invoke()` برای فراخوانی رویداد استفاده کنید**  
   این کار از `NullReferenceException` جلوگیری می‌کند.

2. **رویدادها را در متد `protected virtual` فراخوانی کنید**  
   این کار امکان override کردن رفتار را در کلاس‌های فرزند فراهم می‌کند.

3. **هرگز رویداد را از خارج کلاس فراخوانی نکنید**  
   فقط کلاس صادرکننده رویداد باید آن را فعال کند.

4. **برای داده‌های سفارشی، از `EventArgs` مشتق کنید**  
   حتی اگر فقط یک مقدار دارید، این کار کد را گسترش‌پذیر نگه می‌دارد.

5. **از `EventArgs.Empty` برای رویدادهای بدون داده استفاده کنید**  
   این کار از ایجاد شیء‌های اضافی جلوگیری می‌کند.

---

## جمع‌بندی

الگوی `EventHandler` یکی از ستون‌های اصلی برنامه‌نویسی شیءگرا در C# است. با رعایت این الگو:

- کدهای شما با استانداردهای .NET هماهنگ می‌شوند.
- خوانایی و نگهداری‌پذیری کد افزایش می‌یابد.
- ارتباط بین اجزای برنامه به صورت loose-coupled انجام می‌شود.

این الگو به‌ویژه در توسعه رابط‌های کاربری (UI)، سیستم‌های مبتنی بر پیام (Messaging)، و الگوهای طراحی مانند **Observer** بسیار کاربردی است.

---

## منابع معتبر

1. **Microsoft Learn – Events (C# Programming Guide)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)

2. **Microsoft Learn – EventHandler\<TEventArgs> Delegate**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.eventhandler-1](https://learn.microsoft.com/en-us/dotnet/api/system.eventhandler-1)

3. **C# in Depth – Jon Skeet** (فصل 5: Delegates and Events)  
   ISBN: 978-1617294532

4. **Framework Design Guidelines – Microsoft** (فصل 7: Events)  
   [https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/event](https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/event)

5. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   فصل 15: Understanding Event Processing

