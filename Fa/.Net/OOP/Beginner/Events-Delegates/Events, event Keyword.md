

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [چرا به رویداد (Event) نیاز داریم؟](#چرا-به-رویداد-event-نیاز-داریم)  
3. [تعریف رویداد (Event) در C#](#تعریف-رویداد-event-در-c)  
4. [رابطه Events و Delegates](#رابطه-events-و-delegates)  
5. [ساختار پایه یک Event](#ساختار-پایه-یک-event)  
6. [استاندارد .NET برای Events: `EventHandler` و `EventArgs`](#استاندارد-net-برای-events-eventhandler-و-eventargs)  
7. [تفاوت `event` با یک Delegate معمولی](#تفاوت-event-با-یک-delegate-معمولی)  
8. [مثال کاربردی: سیستم اطلاع‌رسانی](#مثال-کاربردی-سیستم-اطلاع‌رسانی)  
9. [Event Accessors (add/remove)](#event-accessors-addremove)  
10. [بهترین روش‌ها (Best Practices)](#بهترین-روش‌ها-best-practices)  
11. [جمع‌بندی](#جمع‌بندی)  
12. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیءگرا (OOP)، **رویدادها (Events)** یکی از مفاهیم کلیدی برای پیاده‌سازی الگوی **Observer** هستند. آن‌ها به شما این امکان را می‌دهند که یک شیء (Publisher) بتواند به شیء یا اشیاء دیگر (Subscribers) اطلاع دهد که یک اتفاق خاص رخ داده است — بدون اینکه نیاز باشد که Publisher از وجود Subscribers مطلع باشد.

در زبان C#، رویدادها با استفاده از **Delegateها** و کلمه کلیدی `event` پیاده‌سازی می‌شوند.

---

## چرا به رویداد (Event) نیاز داریم؟

فرض کنید یک کلاس `Timer` دارید که هر ۱ ثانیه یک بار کاری را انجام می‌دهد. حالا می‌خواهید وقتی زمان تمام شد، یک پیام نمایش داده شود یا یک عملیات خاص انجام شود. بدون رویداد، باید مستقیماً کد نمایش پیام را داخل کلاس `Timer` بنویسید — که این کار **انعطاف‌پذیری** و **جداکنندگی مسئولیت‌ها (Separation of Concerns)** را از بین می‌برد.

رویدادها به شما اجازه می‌دهند که:
- Publisher (مثلاً `Timer`) فقط مسئول اعلام وقوع یک اتفاق باشد.
- Subscriber (مثلاً `Main` یا یک فرم UI) تصمیم بگیرد چه کاری انجام شود.

---

## تعریف رویداد (Event) در C#

در C#، یک **رویداد** یک عضو کلاس است که با کلمه کلیدی `event` تعریف می‌شود و از یک **Delegate** پشتیبانی می‌کند. این Delegate تعیین می‌کند که چه نوع متدی می‌تواند به رویداد مشترک شود (Subscribe).

```csharp
public event EventHandler MyEvent;
```

---

## رابطه Events و Delegates

- **Delegate**: یک نوع اشاره‌گر به متد است. می‌تواند به یک یا چند متد اشاره کند (Multicast).
- **Event**: یک **محدودیت امنیتی و دسترسی** روی یک Delegate اعمال می‌کند. فقط کلاس مربوطه می‌تواند رویداد را **فراخوانی** کند، ولی هر کلاس دیگری می‌تواند به آن **مشترک شود** یا **لغو اشتراک** کند.

> به عبارت ساده:  
> **Event = Delegate + کنترل دسترسی**

---

## ساختار پایه یک Event

```csharp
// 1. تعریف Delegate (اختیاری اگر از EventHandler استفاده کنیم)
public delegate void MyEventHandler(object sender, EventArgs e);

// 2. تعریف Event
public event MyEventHandler SomethingHappened;

// 3. روش اعلام رویداد (فقط داخل کلاس)
protected virtual void OnSomethingHappened()
{
    SomethingHappened?.Invoke(this, EventArgs.Empty);
}
```

---

## استاندارد .NET برای Events: `EventHandler` و `EventArgs`

در .NET، یک قرارداد استاندارد برای رویدادها وجود دارد:

- نوع Delegate: `EventHandler<TEventArgs>`
- پارامترها:
  - `object sender`: شیء که رویداد را ارسال کرده.
  - `TEventArgs e`: داده‌های مرتبط با رویداد.

### مثال با `EventHandler`:

```csharp
public class TemperatureMonitor
{
    public event EventHandler<TemperatureChangedEventArgs> TemperatureChanged;

    private int _temperature;
    public int Temperature
    {
        get => _temperature;
        set
        {
            if (_temperature != value)
            {
                _temperature = value;
                OnTemperatureChanged(new TemperatureChangedEventArgs(_temperature));
            }
        }
    }

    protected virtual void OnTemperatureChanged(TemperatureChangedEventArgs e)
    {
        TemperatureChanged?.Invoke(this, e);
    }
}

public class TemperatureChangedEventArgs : EventArgs
{
    public int NewTemperature { get; }
    public TemperatureChangedEventArgs(int temperature)
    {
        NewTemperature = temperature;
    }
}
```

### استفاده:

```csharp
var monitor = new TemperatureMonitor();
monitor.TemperatureChanged += (sender, e) =>
{
    Console.WriteLine($"دمای جدید: {e.NewTemperature}°C");
};

monitor.Temperature = 30; // خروجی: دمای جدید: 30°C
```

---

## تفاوت `event` با یک Delegate معمولی

| ویژگی | Delegate معمولی | Event |
|--------|------------------|-------|
| دسترسی به `Invoke` | از هر جایی می‌شود فراخوانی کرد | فقط داخل کلاس تعریف‌شده |
| دسترسی به `=` (جایگزینی) | هر کسی می‌تواند لیست مشترکین را پاک کند | فقط داخل کلاس می‌توان `=` استفاده کرد |
| امنیت | کمتر | بیشتر (جلوگیری از بازنویسی تصادفی) |
| قرارداد طراحی | عمومی | برای الگوی Publisher/Subscriber |

> ⚠️ بدون `event`، یک Subscriber می‌تواند به اشتباه تمام مشترکین دیگر را با `myDelegate = null;` پاک کند!

---

## مثال کاربردی: سیستم اطلاع‌رسانی

```csharp
// Publisher
public class NewsAgency
{
    public event EventHandler<string> NewsPublished;

    public void PublishNews(string news)
    {
        Console.WriteLine($"خبر جدید منتشر شد: {news}");
        NewsPublished?.Invoke(this, news);
    }
}

// Subscriber
class Program
{
    static void Main()
    {
        var agency = new NewsAgency();
        agency.NewsPublished += OnNewsReceived; // اشتراک
        agency.PublishNews("هوش مصنوعی جایزه گرفت!");
    }

    static void OnNewsReceived(object sender, string news)
    {
        Console.WriteLine($"دریافت شد: {news}");
    }
}
```

خروجی:
```
خبر جدید منتشر شد: هوش مصنوعی جایزه گرفت!
دریافت شد: هوش مصنوعی جایزه گرفت!
```

---

## Event Accessors (add/remove)

می‌توانید رفتار `+=` و `-=` را سفارشی کنید:

```csharp
private EventHandler _myEvent;

public event EventHandler MyEvent
{
    add
    {
        Console.WriteLine("یک مشترک جدید اضافه شد.");
        _myEvent += value;
    }
    remove
    {
        Console.WriteLine("یک مشترک حذف شد.");
        _myEvent -= value;
    }
}
```

این کار برای لاگ‌گیری، اعتبارسنجی یا مدیریت منابع مفید است.

---

## بهترین روش‌ها (Best Practices)

1. **همیشه از `EventHandler<T>` استفاده کنید** مگر اینکه دلیل قوی داشته باشید.
2. **هرگز Event را مستقیماً `null` نکنید** — از `?.Invoke` یا بررسی `null` استفاده کنید.
3. **متد `OnEventName` را `protected virtual` تعریف کنید** تا کلاس‌های فرزند بتوانند رفتار را تغییر دهند.
4. **از `EventArgs.Empty` برای رویدادهای بدون داده استفاده کنید**.
5. **هرگز Event را خارج از کلاس فراخوانی نکنید** — این کار نقض اصول طراحی است.

---

## جمع‌بندی

- رویدادها (Events) در C# یک مکانیزم **ایمن و استاندارد** برای پیاده‌سازی ارتباط یک‌طرفه بین اشیاء هستند.
- آن‌ها بر پایه Delegateها ساخته شده‌اند اما با محدودیت‌های دسترسی برای جلوگیری از سوءاستفاده.
- استفاده از قرارداد استاندارد `EventHandler<TEventArgs>` باعث خوانایی و سازگاری بیشتر کد می‌شود.
- رویدادها ستون اصلی برنامه‌نویسی واکنش‌گرا (Reactive Programming) و توسعه رابط‌های کاربری هستند.

---

## منابع معتبر

1. **Microsoft Learn – Events (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/events-overview](https://learn.microsoft.com/en-us/dotnet/csharp/events-overview)

2. **Microsoft Learn – Delegates**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

3. **C# in Depth – Jon Skeet** (فصل ۵: Delegates and Events)  
   📚 ISBN: 978-1617294532

4. **Framework Design Guidelines – Microsoft**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/event](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/event)

5. **C# Station Tutorial – Events**  
   🔗 [http://www.csharp-station.com/Tutorial/CSharp/Lesson14](http://www.csharp-station.com/Tutorial/CSharp/Lesson14)
