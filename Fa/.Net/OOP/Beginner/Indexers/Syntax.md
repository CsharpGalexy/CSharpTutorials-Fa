

## فهرست مطالب

1. [مقدمه](#1-مقدمه)  
2. [Indexer چیست؟](#2-indexer-چیست)  
3. [سینتکس پایه Indexer](#3-سینتکس-پایه-indexer)  
4. [مثال ساده: کلاسی با Indexer](#4-مثال-ساده-کلاسی-با-indexer)  
5. [Indexer با انواع مختلف پارامتر](#5-indexer-با-انواع-مختلف-پارامتر)  
6. [Indexer فقط خواندنی (Read-only)](#6-indexer-فقط-خواندنی-read-only)  
7. [Indexer در رابط‌ها (Interfaces)](#7-indexer-در-رابط‌ها-interfaces)  
8. [تفاوت Indexer با آرایه‌ها و لیست‌ها](#8-تفاوت-indexer-با-آرایه‌ها-و-لیست‌ها)  
9. [نکات بهینه‌سازی و بهترین روش‌ها (Best Practices)](#9-نکات-بهینه‌سازی-و-بهترین-روش‌ها-best-practices)  
10. [جمع‌بندی](#10-جمع‌بندی)  
11. [منابع معتبر](#11-منابع-معتبر)

---

## 1. مقدمه

در برنامه‌نویسی شیءگرای C#، **Indexer** یکی از ویژگی‌های قدرتمند است که به شما امکان می‌دهد یک کلاس یا ساختار (struct) را به گونه‌ای طراحی کنید که مانند یک آرایه یا لیست قابل دسترسی باشد — یعنی بتوانید با استفاده از عملگر `[]` به اعضای آن دسترسی داشته باشید. این ویژگی، خوانایی کد را افزایش داده و رابط کاربری شیء را طبیعی‌تر می‌کند.

این راهنما به‌صورت گام‌به‌گام، از سطح مقدماتی تا متوسط، Indexer در C# را توضیح می‌دهد و برای استفاده در آموزش‌های OOP مناسب است.

---

## 2. Indexer چیست؟

**Indexer** یک عضو خاص در یک کلاس یا ساختار است که به شما اجازه می‌دهد نمونه‌ای از آن کلاس را مانند یک آرایه فهرست‌بندی‌شده (indexed) استفاده کنید.

به جای اینکه یک متد مانند `GetValue(int index)` بنویسید، می‌توانید مستقیماً بنویسید:  
```csharp
myObject[5] = "Hello";
```

این کار با تعریف یک **Indexer** در کلاس امکان‌پذیر است.

> 💡 **نکته:** Indexer شبیه به یک "پراپرتی" است، اما با یک پارامتر ایندکس.

---

## 3. سینتکس پایه Indexer

سینتکس عمومی Indexer در C# به این صورت است:

```csharp
public نوع_بازگشتی this[نوع_پارامتر ایندکس]
{
    get { /* منطق بازیابی */ }
    set { /* منطق تنظیم */ }
}
```

- کلمه کلیدی `this` برای تعریف Indexer استفاده می‌شود.
- می‌توانید فقط `get`، فقط `set` یا هر دو را داشته باشید.
- نوع بازگشتی معمولاً نوع داده‌ای است که می‌خواهید از طریق Indexer دسترسی داشته باشید.
- پارامتر ایندکس می‌تواند هر نوعی باشد (مثلاً `int`، `string` و غیره).

---

## 4. مثال ساده: کلاسی با Indexer

در این مثال، یک کلاس `SimpleList` ایجاد می‌کنیم که از یک آرایه داخلی استفاده می‌کند و Indexer برای دسترسی به عناصر آن فراهم می‌کند:

```csharp
public class SimpleList
{
    private string[] _items = new string[100];
    private int _count = 0;

    public string this[int index]
    {
        get
        {
            if (index < 0 || index >= _count)
                throw new IndexOutOfRangeException();
            return _items[index];
        }
        set
        {
            if (index < 0 || index >= _count)
                throw new IndexOutOfRangeException();
            _items[index] = value;
        }
    }

    public void Add(string item)
    {
        if (_count < _items.Length)
        {
            _items[_count] = item;
            _count++;
        }
    }

    public int Count => _count;
}
```

**استفاده از کلاس:**

```csharp
var list = new SimpleList();
list.Add("Apple");
list.Add("Banana");

Console.WriteLine(list[0]); // خروجی: Apple
list[1] = "Orange";
Console.WriteLine(list[1]); // خروجی: Orange
```

---

## 5. Indexer با انواع مختلف پارامتر

Indexer محدود به `int` نیست! می‌توانید از هر نوعی به عنوان ایندکس استفاده کنید:

### مثال: Indexer با کلید رشته‌ای (مانند دیکشنری)

```csharp
public class PersonCollection
{
    private Dictionary<string, string> _data = new();

    public string this[string key]
    {
        get => _data.TryGetValue(key, out var value) ? value : null;
        set => _data[key] = value;
    }
}
```

**استفاده:**

```csharp
var people = new PersonCollection();
people["Ali"] = "Engineer";
Console.WriteLine(people["Ali"]); // خروجی: Engineer
```

> ⚠️ **هشدار:** اگر چند Indexer با امضاهای متفاوت داشته باشید (مثلاً یکی با `int` و یکی با `string`)، کامپایلر بر اساس نوع پارامتر، Indexer مناسب را انتخاب می‌کند (Overloading Indexer).

---

## 6. Indexer فقط خواندنی (Read-only)

اگر فقط `get` را تعریف کنید، Indexer فقط خواندنی خواهد بود:

```csharp
public class ReadOnlyList
{
    private readonly string[] _items = { "A", "B", "C" };

    public string this[int index] => _items[index]; // expression-bodied
}
```

در این حالت، تلاش برای نوشتن (`list[0] = "X"`) خطای کامپایل ایجاد می‌کند.

---

## 7. Indexer در رابط‌ها (Interfaces)

می‌توانید Indexer را در یک رابط (interface) تعریف کنید:

```csharp
public interface IIndexedCollection
{
    string this[int index] { get; set; }
}
```

سپس کلاس‌هایی که این رابط را پیاده‌سازی می‌کنند، باید Indexer را فراهم کنند:

```csharp
public class MyCollection : IIndexedCollection
{
    private string[] _data = new string[10];

    public string this[int index]
    {
        get => _data[index];
        set => _data[index] = value;
    }
}
```

> 📌 **نکته مهم:** رابط‌های معروفی مانند `IList<T>` و `IDictionary<TKey, TValue>` از Indexer استفاده می‌کنند.

---

## 8. تفاوت Indexer با آرایه‌ها و لیست‌ها

| ویژگی | آرایه (`T[]`) | لیست (`List<T>`) | Indexer سفارشی |
|--------|----------------|------------------|----------------|
| انعطاف‌پذیری | پایین | متوسط | **بالا** |
| نوع ایندکس | فقط `int` | فقط `int` | **هر نوع** |
| منطق داخلی | ثابت | پیش‌فرض | **سفارشی** |
| عملکرد | بسیار سریع | سریع | بستگی به پیاده‌سازی دارد |

Indexer به شما اجازه می‌دهد **منطق دسترسی** را کنترل کنید — مثلاً لاگ‌گیری، اعتبارسنجی، کش کردن و غیره.

---

## 9. نکات بهینه‌سازی و بهترین روش‌ها (Best Practices)

1. **اعتبارسنجی ایندکس**: همیشه بررسی کنید که ایندکس در محدوده مجاز باشد.
2. **خطاهای مناسب**: از `IndexOutOfRangeException` یا `ArgumentException` استفاده کنید.
3. **اجتناب از منطق سنگین**: Indexer باید سریع باشد؛ منطق پیچیده را به متد جداگانه منتقل کنید.
4. **مستندسازی**: از XML Comments برای توضیح Indexer استفاده کنید.
5. **استفاده از expression-bodied members** برای Indexerهای ساده:
   ```csharp
   public string this[int i] => _items[i];
   ```

---

## 10. جمع‌بندی

- Indexer یک ویژگی قدرتمند در C# برای شبیه‌سازی رفتار آرایه‌ها در کلاس‌های سفارشی است.
- با استفاده از `this[...]` تعریف می‌شود و می‌تواند `get` و/یا `set` داشته باشد.
- پارامتر ایندکس می‌تواند هر نوعی باشد — حتی چند پارامتر (در C# 8.0+).
- Indexer در رابط‌ها قابل تعریف است و بخشی از طراحی استاندارد .NET است.
- برای کدهای خوانا و شهودی، Indexer را در جاهای مناسب استفاده کنید.

---

## 11. منابع معتبر

1. **مستندات رسمی Microsoft (MSDN)**  
   [Indexers (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/)  
   *منبع اصلی و به‌روز برای یادگیری Indexer در C#.*

2. **C# Language Specification (ECMA-334)**  
   [ECMA-334 Standard – Section on Indexers](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)  
   *استاندارد رسمی زبان C#.*

3. **Microsoft Learn – C# Fundamentals**  
   [Object-Oriented Programming in C#](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/)  
   *بخشی از آموزش‌های رایگان مایکروسافت در مورد OOP.*

4. **C# in Depth – Jon Skeet**  
   *کتاب معتبر برای درک عمیق مفاهیم C#، از جمله Indexer و متدهای پیشرفته.*

5. **.NET API Reference – IList<T>, IDictionary<TKey, TValue>**  
   [IList<T>.Item Property](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ilist-1.item)  
   [IDictionary<TKey,TValue>.Item Property](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.idictionary-2.item)  
   *نمونه‌های واقعی از Indexer در کتابخانه‌های استاندارد .NET.*

