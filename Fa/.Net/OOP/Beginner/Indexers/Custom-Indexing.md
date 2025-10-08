
## فهرست مطالب

1. [مقدمه: Indexer چیست؟](#1-مقدمه-indexer-چیست)
2. [چرا از Indexer استفاده می‌کنیم؟](#2-چرا-از-indexer-استفاده-میکنیم)
3. [ساختار پایه Indexer در C#](#3-ساختار-پایه-indexer-در-c)
4. [مثال ساده: Indexer در یک کلاس شخصی‌سازی‌شده](#4-مثال-ساده-indexer-در-یک-کلاس-شخصیسازیشده)
5. [Indexer با انواع داده‌ی غیر عددی (مثلاً string)](#5-indexer-با-انواع-دادهی-غیر-عددی-مثلا-string)
6. [Indexer چندپارامتری (Multi-parameter Indexer)](#6-indexer-چندپارامتری-multi-parameter-indexer)
7. [تفاوت Indexer با متد عادی](#7-%D8%AA%D9%81%D8%A7%D9%88%D8%AA-indexer-%D8%A8%D8%A7-%D9%85%D8%AA%D8%AF-%D8%B9%D8%A7%D8%AF%DB%8C)
8. [بهترین شیوه‌های استفاده از Indexer (Best Practices)](#8-بهترین-شیوههای-استفاده-از-indexer-best-practices)
9. [محدودیت‌ها و نکات مهم](#9-محدودیتها-و-نکات-مهم)
10. [جمع‌بندی](#10-جمعبندی)
11. [منابع معتبر](#11-منابع-معتبر)

---

## 1. مقدمه: Indexer چیست؟

در C#، **Indexer** (شاخص‌گذار یا اندیس‌گذار) یک عضو خاص از کلاس یا ساختار است که به شما اجازه می‌دهد **یک شیء را مانند یک آرایه** فهرست‌بندی (index) کنید.  
به عبارت دیگر، با استفاده از Indexer می‌توانید از عملگر `[]` برای دسترسی به عناصر داخل یک شیء استفاده کنید — دقیقاً همان‌طور که با آرایه‌ها یا لیست‌ها کار می‌کنید.

مثال ساده:
```csharp
MyCollection obj = new MyCollection();
obj[0] = "Hello"; // استفاده از Indexer
string value = obj[0];
```

---

## 2. چرا از Indexer استفاده می‌کنیم؟

- **خوانایی کد**: کد شما بیشتر شبیه به کار با ساختارهای داخلی C# (مثل `List<T>` یا `Dictionary<TKey, TValue>`) می‌شود.
- **ابسترکشن**: جزئیات ذخیره‌سازی داخلی را پنهان می‌کند و رابط ساده‌ای برای کاربر فراهم می‌کند.
- **انعطاف‌پذیری**: می‌توانید منطق دسترسی به داده را کنترل کنید (مثلاً اعتبارسنجی، لاگ‌گیری، تبدیل نوع و غیره).

---

## 3. ساختار پایه Indexer در C#

Indexer با کلمه‌ی کلیدی `this` تعریف می‌شود و شبیه یک خاصیت (property) است:

```csharp
public тип this[نوع پارامتر]
{
    get { /* منطق دریافت */ }
    set { /* منطق تنظیم */ }
}
```

- `this` نشان‌دهنده‌ی شیء جاری است.
- Indexer می‌تواند فقط `get`، فقط `set` یا هر دو را داشته باشد.
- نوع بازگشتی (`return type`) معمولاً نوع داده‌ای است که می‌خواهید از طریق اندیس دریافت یا تنظیم کنید.

---

## 4. مثال ساده: Indexer در یک کلاس شخصی‌سازی‌شده

فرض کنید می‌خواهیم یک کلاس `SimpleList` داشته باشیم که حداکثر 10 عنصر رشته‌ای را نگه می‌دارد:

```csharp
public class SimpleList
{
    private string[] _items = new string[10];

    public string this[int index]
    {
        get
        {
            if (index < 0 || index >= _items.Length)
                throw new IndexOutOfRangeException("Index out of range.");
            return _items[index];
        }
        set
        {
            if (index < 0 || index >= _items.Length)
                throw new IndexOutOfRangeException("Index out of range.");
            _items[index] = value;
        }
    }
}
```

**استفاده:**
```csharp
var list = new SimpleList();
list[0] = "C#";
Console.WriteLine(list[0]); // خروجی: C#
```

---

## 5. Indexer با انواع داده‌ی غیر عددی (مثلاً string)

Indexer محدود به `int` نیست! می‌توانید از هر نوعی استفاده کنید — مثلاً `string` برای شبیه‌سازی یک دیکشنری:

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

---

## 6. Indexer چندپارامتری (Multi-parameter Indexer)

می‌توانید چندین پارامتر به Indexer بدهید — مثلاً برای شبیه‌سازی یک ماتریس دو بعدی:

```csharp
public class Matrix
{
    private int[,] _matrix = new int[3, 3];

    public int this[int row, int col]
    {
        get => _matrix[row, col];
        set => _matrix[row, col] = value;
    }
}
```

**استفاده:**
```csharp
var m = new Matrix();
m[0, 1] = 42;
Console.WriteLine(m[0, 1]); // خروجی: 42
```

> ⚠️ توجه: Indexer فقط یک بار در یک کلاس می‌تواند تعریف شود، اما **overload** آن با تغییر تعداد یا نوع پارامترها ممکن است.

---

## 7. تفاوت Indexer با متد عادی

| ویژگی | Indexer | متد عادی |
|--------|--------|----------|
| نحوه فراخوانی | `obj[0]` | `obj.GetValue(0)` |
| قابلیت overload | بله (با تغییر پارامترها) | بله |
| استفاده از `this` | الزامی | خیر |
| شبیه‌سازی رفتار آرایه | بله | خیر |
| فقط در کلاس/ساختار | بله | هر جا |

---

## 8. بهترین شیوه‌های استفاده از Indexer (Best Practices)

1. **اعتبارسنجی ورودی**: همیشه محدوده‌ی اندیس را بررسی کنید.
2. **استثناهای مناسب**: از `IndexOutOfRangeException` یا `ArgumentException` استفاده کنید.
3. **مستندسازی**: Indexer را با XML comment مستند کنید.
4. **اجتناب از منطق سنگین**: Indexer باید سریع باشد — مانند دسترسی به آرایه.
5. **استفاده از `get`/`set` مناسب**: اگر فقط خواندنی است، `set` را حذف کنید.

---

## 9. محدودیت‌ها و نکات مهم

- Indexer **نام ندارد** — فقط با `this` تعریف می‌شود.
- نمی‌توان آن را به صورت `static` تعریف کرد.
- نمی‌توان آن را به عنوان `ref` یا `out` استفاده کرد.
- در رابط‌ها (Interfaces) نیز قابل تعریف است:
  ```csharp
  public interface IMyCollection
  {
      string this[int index] { get; set; }
  }
  ```

---

## 10. جمع‌بندی

Indexer یکی از قابلیت‌های قدرتمند C# برای **شبیه‌سازی رفتار آرایه‌ها** در کلاس‌های سفارشی است. با استفاده از آن می‌توانید رابط کاربری شهودی‌تری برای کلاس‌های خود ایجاد کنید و کد را خواناتر و حرفه‌ای‌تر بنویسید.  
به یاد داشته باشید که Indexer جایگزین مناسبی برای متد نیست، بلکه یک **رابط دسترسی خاص** برای زمانی است که شیء شما ذاتاً قابل فهرست‌بندی است (مثل لیست، دیکشنری، ماتریس و غیره).

---

## 11. منابع معتبر

1. **Microsoft Learn – Indexers (C# Programming Guide)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/)

2. **C# Language Specification – Indexers**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/indexers](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/indexers)

3. **"C# in Depth" by Jon Skeet** – فصل مربوط به عضوهای کلاس (Indexers در بخش خاصیت‌ها و اعضای پیشرفته)  
   📚 ISBN: 978-1617294532

4. **"Professional C#" by Wrox (Wiley)** – بخش Indexers در فصل Object-Oriented Programming  
   📚 ISBN: 978-1119725347

5. **Stack Overflow – Common Patterns for Indexers**  
   🔗 [https://stackoverflow.com/questions/5060633/what-is-an-indexer-in-c](https://stackoverflow.com/questions/5060633/what-is-an-indexer-in-c)

