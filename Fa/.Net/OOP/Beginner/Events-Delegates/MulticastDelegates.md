

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [Delegate چیست؟](#delegate-چیست)  
3. [تفاوت Delegate ساده و Multicast Delegate](#تفاوت-delegate-ساده-و-multicast-delegate)  
4. [چگونه یک Multicast Delegate ایجاد کنیم؟](#چگونه-یک-multicast-delegate-ایجاد-کنیم)  
5. [نحوه اضافه و حذف متد‌ها به Multicast Delegate](#نحوه-اضافه-و-حذف-متدها-به-multicast-delegate)  
6. [نکات مهم در مورد بازگشتی (Return Type) و پارامترها](#نکات-مهم-در-مورد-بازگشتی-return-type-و-پارامترها)  
7. [استفاده از Multicast Delegate در Events](#استفاده-از-multicast-delegate-در-events)  
8. [مثال کاربردی: سیستم اعلان (Notification System)](#مثال-کاربردی-سیستم-اعلان-notification-system)  
9. [جمع‌بندی](#جمع‌بندی)  
10. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP) با زبان C#، **Delegates** یکی از مفاهیم قدرتمند و کاربردی هستند که به شما امکان می‌دهند تا **مرجعی به متد** (Method Reference) داشته باشید.  
یکی از ویژگی‌های خاص و مفید `Delegate` در C#، قابلیت **چندپخشی** (Multicast) بودن آن است. این ویژگی به شما اجازه می‌دهد چندین متد را به یک delegate متصل کنید و همزمان آن‌ها را فراخوانی کنید.

در این مستند، به طور کامل و با مثال‌های ساده، مفهوم **Multicast Delegates** را بررسی می‌کنیم.

---

## Delegate چیست؟

Delegate در C# نوعی **نوع امن** (type-safe) از اشاره‌گر به متد است. شما می‌توانید یک delegate را تعریف کنید که یک امضای خاص (Signature) داشته باشد — یعنی تعداد و نوع پارامترها و نوع بازگشتی آن مشخص باشد — و سپس هر متدی که همان امضا را داشته باشد را به آن متصل کنید.

### مثال ساده:

```csharp
public delegate void MyDelegate(string message);

public class Program
{
    public static void Main()
    {
        MyDelegate del = new MyDelegate(ShowMessage);
        del("Hello from delegate!");
    }

    public static void ShowMessage(string msg)
    {
        Console.WriteLine(msg);
    }
}
```

---

## تفاوت Delegate ساده و Multicast Delegate

- **Delegate ساده**: فقط یک متد را به خود متصل می‌کند.
- **Multicast Delegate**: می‌تواند **چندین متد** را به خود متصل کند و هنگام فراخوانی، تمام آن‌ها را **به ترتیب** اجرا کند.

> ⚠️ تمام delegateهایی که در C# تعریف می‌شوند، در واقع از کلاس `System.MulticastDelegate` ارث‌بری می‌کنند. بنابراین **تمام delegateها در C# به طور پیش‌فرض multicast هستند** — مگر اینکه فقط یک متد به آن‌ها متصل شده باشد.

---

## چگونه یک Multicast Delegate ایجاد کنیم؟

برای ایجاد یک Multicast Delegate، کافی است چندین متد را با استفاده از عملگرهای `+` یا `+=` به یک delegate متصل کنید.

### مثال:

```csharp
public delegate void NotificationDelegate(string message);

public class Program
{
    public static void Main()
    {
        NotificationDelegate notify = null;
        notify += EmailNotification;
        notify += SmsNotification;
        notify += PushNotification;

        notify("Your order has been shipped!");
    }

    public static void EmailNotification(string msg) => Console.WriteLine($"[Email] {msg}");
    public static void SmsNotification(string msg) => Console.WriteLine($"[SMS] {msg}");
    public static void PushNotification(string msg) => Console.WriteLine($"[Push] {msg}");
}
```

**خروجی:**
```
[Email] Your order has been shipped!
[SMS] Your order has been shipped!
[Push] Your order has been shipped!
```

---

## نحوه اضافه و حذف متد‌ها به Multicast Delegate

- برای **اضافه کردن** متد: از `+=` استفاده کنید.
- برای **حذف کردن** متد: از `-=` استفاده کنید.

### مثال:

```csharp
notify += EmailNotification;   // اضافه کردن
notify -= SmsNotification;     // حذف کردن
```

> ⚠️ اگر متدی که می‌خواهید حذف کنید در لیست نباشد، خطایی رخ نمی‌دهد، اما delegate بدون تغییر باقی می‌ماند.

---

## نکات مهم در مورد بازگشتی (Return Type) و پارامترها

- **پارامترها**: تمام متدهای متصل به یک delegate باید **همان امضای delegate** را داشته باشند (همان تعداد و نوع پارامترها).
- **نوع بازگشتی**:
  - اگر delegate از نوع `void` باشد، مشکلی نیست.
  - اگر delegate نوع بازگشتی داشته باشد (مثلاً `int`)، **فقط مقدار بازگشتی آخرین متد** در نظر گرفته می‌شود.

### مثال با بازگشتی:

```csharp
public delegate int MathOperation(int a, int b);

public static void Main()
{
    MathOperation op = Add;
    op += Multiply;
    int result = op(3, 4); // فقط نتیجه Multiply (12) بازگردانده می‌شود
    Console.WriteLine(result); // خروجی: 12
}

public static int Add(int a, int b) => a + b;
public static int Multiply(int a, int b) => a * b;
```

> ✅ توصیه: برای delegateهای multicast، بهتر است از **نوع بازگشتی `void`** استفاده کنید تا رفتار غیرمنتظره ایجاد نشود.

---

## استفاده از Multicast Delegate در Events

در C#، **Eventها** در واقع wrapperهایی روی Multicast Delegate هستند. وقتی چندین event handler به یک event متصل می‌شوند، در پس‌زمینه یک Multicast Delegate مدیریت می‌کند که همه آن‌ها را فراخوانی کند.

### مثال:

```csharp
public class Button
{
    public event Action Click;

    public void OnClick()
    {
        Click?.Invoke(); // همه handlerها فراخوانی می‌شوند
    }
}

public class Program
{
    public static void Main()
    {
        Button btn = new Button();
        btn.Click += () => Console.WriteLine("Handler 1");
        btn.Click += () => Console.WriteLine("Handler 2");
        btn.OnClick();
    }
}
```

**خروجی:**
```
Handler 1
Handler 2
```

---

## مثال کاربردی: سیستم اعلان (Notification System)

```csharp
public delegate void Notify(string message);

public class NotificationService
{
    public Notify OnNotify;

    public void SendNotification(string msg)
    {
        OnNotify?.Invoke(msg);
    }
}

public class Program
{
    static void Main()
    {
        var service = new NotificationService();
        service.OnNotify += LogToConsole;
        service.OnNotify += SaveToDatabase;
        service.OnNotify += SendEmail;

        service.SendNotification("User logged in.");
    }

    static void LogToConsole(string msg) => Console.WriteLine($"[LOG] {msg}");
    static void SaveToDatabase(string msg) => Console.WriteLine($"[DB] Saved: {msg}");
    static void SendEmail(string msg) => Console.WriteLine($"[EMAIL] Sent: {msg}");
}
```

---

## جمع‌بندی

- تمام delegateها در C# از نوع **Multicast** هستند.
- می‌توان چندین متد را با `+=` به یک delegate متصل کرد.
- برای حذف متد از `-=` استفاده کنید.
- در delegateهای multicast، اگر نوع بازگشتی وجود داشته باشد، فقط **آخرین مقدار بازگشتی** استفاده می‌شود.
- Eventها در C# بر پایه Multicast Delegate ساخته شده‌اند.
- بهترین کاربرد Multicast Delegate در **پیاده‌سازی الگوهای طراحی** مانند Observer و سیستم‌های اعلان است.

---

## منابع معتبر

1. **Microsoft Learn – Delegates (C# Programming Guide)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

2. **Microsoft Learn – Multicast Delegates**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/delegates-with-named-vs-anonymous-methods#multicast-delegates](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/delegates-with-named-vs-anonymous-methods#multicast-delegates)

3. **C# in Depth – Jon Skeet** (Chapter on Delegates and Events)  
   🔗 [https://csharpindepth.com/](https://csharpindepth.com/)

4. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   (فصل 15: Delegates, Events, and Lambda Expressions)

5. **Stack Overflow – How do multicast delegates work?**  
   🔗 [https://stackoverflow.com/questions/2306734/what-is-a-multicast-delegate](https://stackoverflow.com/questions/2306734/what-is-a-multicast-delegate)

