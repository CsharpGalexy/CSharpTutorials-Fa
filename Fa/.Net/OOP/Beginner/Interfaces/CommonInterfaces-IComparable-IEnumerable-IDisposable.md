
## فهرست مطالب

1. [مقدمه‌ای بر Interface در C#](#مقدمه‌ای-بر-interface-در-c)
2. [چرا از Interface استفاده می‌کنیم؟](#چرا-از-interface-استفاده-می‌کنیم)
3. [IComparable<T> – مقایسه‌ی اشیاء](#icomparablet--مقایسه‌ی-اشیاء)
4. [IEnumerable<T> – پیمایش مجموعه‌ها](#ienumerablet--پیمایش-مجموعه‌ها)
5. [IDisposable – مدیریت منابع غیرمدیریت‌شده](#idisposable--مدیریت-منابع-غیرمدیریت‌شده)
6. [جمع‌بندی و بهترین روش‌ها (Best Practices)](#جمع‌بندی-و-بهترین-روش‌ها-best-practices)
7. [منابع معتبر](#منابع-معتبر)

---

## مقدمه‌ای بر Interface در C#

در C#، **Interface** یک قرارداد (Contract) است که مشخص می‌کند یک کلاس چه رفتارهایی را باید پیاده‌سازی کند. Interface‌ها **هیچ پیاده‌سازی‌ای ندارند** و فقط شامل امضای (Signature) متد، پراپرتی، ایندکسر یا ایونت هستند.

مثال ساده:
```csharp
public interface IAnimal
{
    void MakeSound();
}
```

هر کلاسی که `IAnimal` را پیاده‌سازی کند، **باید** متد `MakeSound()` را داشته باشد.

---

## چرا از Interface استفاده می‌کنیم؟

- **چندشکلی (Polymorphism)**: امکان استفاده از اشیاء مختلف با یک نوع مشترک.
- **جایگزینی آسان (Loose Coupling)**: کدها به جای وابستگی به کلاس‌های خاص، به Interface وابسته می‌شوند.
- **قابلیت تست‌پذیری**: راحت‌تر می‌توان Mock ساخت.
- **پیاده‌سازی قراردادهای استاندارد**: مانند `IComparable` برای مقایسه یا `IDisposable` برای آزاد کردن منابع.

---

## IComparable<T> – مقایسه‌ی اشیاء

### هدف
اجازه می‌دهد اشیاء یک کلاس را با یکدیگر **مقایسه** کنید (مثلاً برای مرتب‌سازی).

### نحوه استفاده
کلاس شما باید متد `CompareTo(T other)` را پیاده‌سازی کند.

### مثال
```csharp
public class Person : IComparable<Person>
{
    public string Name { get; set; }
    public int Age { get; set; }

    public int CompareTo(Person? other)
    {
        if (other == null) return 1;
        return Age.CompareTo(other.Age); // مقایسه بر اساس سن
    }
}
```

### کاربرد
```csharp
var people = new List<Person>
{
    new Person { Name = "Ali", Age = 30 },
    new Person { Name = "Reza", Age = 25 }
};

people.Sort(); // مرتب‌سازی بر اساس CompareTo
```

> 💡 نکته: `IComparable<T>` برای **مقایسه‌ی طبیعی** (Natural Ordering) است. اگر نیاز به چندین روش مرتب‌سازی دارید، از `IComparer<T>` استفاده کنید.

---

## IEnumerable<T> – پیمایش مجموعه‌ها

### هدف
اجازه می‌دهد یک شیء را در **حلقه‌های foreach** استفاده کنید.

### نحوه کار
هر کلاسی که `IEnumerable<T>` را پیاده‌سازی کند، باید متد `GetEnumerator()` را داشته باشد که یک `IEnumerator<T>` برمی‌گرداند.

### مثال ساده
```csharp
public class MyCollection : IEnumerable<int>
{
    private readonly int[] _items = { 1, 2, 3, 4, 5 };

    public IEnumerator<int> GetEnumerator()
    {
        return ((IEnumerable<int>)_items).GetEnumerator();
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}
```

### کاربرد
```csharp
var collection = new MyCollection();
foreach (var item in collection)
{
    Console.WriteLine(item); // 1, 2, 3, 4, 5
}
```

> 💡 نکته: در عمل، نیازی به پیاده‌سازی دستی `IEnumerable<T>` نیست. معمولاً از `List<T>`, `Array`, یا LINQ استفاده می‌کنیم که از قبل این رابط را پیاده‌سازی کرده‌اند.

---

## IDisposable – مدیریت منابع غیرمدیریت‌شده

### هدف
آزاد کردن **منابع غیرمدیریت‌شده** (Unmanaged Resources) مانند فایل‌ها، سوکت‌ها، هندل‌های ویندوز و غیره.

### چرا مهم است؟
Garbage Collector در .NET فقط حافظه‌ی مدیریت‌شده را آزاد می‌کند. منابع سیستمی (مثل فایل‌ها) باید **دستی** آزاد شوند.

### نحوه پیاده‌سازی
کلاس شما باید متد `Dispose()` را پیاده‌سازی کند.

### الگوی استاندارد (Dispose Pattern)
```csharp
public class FileManager : IDisposable
{
    private FileStream? _fileStream;
    private bool _disposed = false;

    public FileManager(string path)
    {
        _fileStream = new FileStream(path, FileMode.Open);
    }

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                // آزاد کردن منابع مدیریت‌شده
                _fileStream?.Close();
                _fileStream?.Dispose();
            }
            // آزاد کردن منابع غیرمدیریت‌شده (در اینجا نیازی نیست)
            _disposed = true;
        }
    }

    ~FileManager()
    {
        Dispose(false);
    }
}
```

### کاربرد با `using`
```csharp
using (var manager = new FileManager("data.txt"))
{
    // کار با فایل
} // Dispose به‌صورت خودکار فراخوانی می‌شود
```

> 💡 نکته: از C# 8 به بعد می‌توانید از **using declaration** استفاده کنید:
> ```csharp
> using var manager = new FileManager("data.txt");
> // Dispose در پایان scope فراخوانی می‌شود
> ```

---

## جمع‌بندی و بهترین روش‌ها (Best Practices)

| Interface | زمان استفاده | نکته مهم |
|----------|--------------|----------|
| `IComparable<T>` | وقتی نیاز به مرتب‌سازی طبیعی دارید | فقط یک منطق مقایسه‌ی پیش‌فرض داشته باشید |
| `IEnumerable<T>` | وقتی می‌خواهید شیء را در foreach بگیرید | معمولاً نیازی به پیاده‌سازی دستی نیست |
| `IDisposable` | وقتی کلاس از منابع غیرمدیریت‌شده استفاده می‌کند | همیشه از `using` استفاده کنید |

✅ **نکات کلیدی:**
- Interface‌ها را برای **رفتار** تعریف کنید، نه برای داده.
- هرگز از `IDisposable` در کلاس‌هایی که منبع غیرمدیریت‌شده ندارند استفاده نکنید.
- برای مقایسه‌های جایگزین، از `IComparer<T>` استفاده کنید، نه چندین `IComparable`.

---

## منابع معتبر

1. **Microsoft Learn – Interfaces (C#)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/)

2. **Microsoft Learn – IComparable<T> Interface**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.icomparable-1](https://learn.microsoft.com/en-us/dotnet/api/system.icomparable-1)

3. **Microsoft Learn – IEnumerable<T> Interface**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1)

4. **Microsoft Learn – Implementing a Dispose method**  
   [https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-dispose)

5. **C# in Depth – Jon Skeet** (فصل Interface و مدیریت منابع)  
   [https://csharpindepth.com/](https://csharpindepth.com/)

6. **.NET Design Guidelines – Framework Design Guidelines**  
   Krzysztof Cwalina & Brad Abrams – Addison-Wesley
