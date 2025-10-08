
## فهرست مطالب

1. [مقدمه: Indexer چیست؟](#مقدمه-indexer-چیست)
2. [چرا از Indexer استفاده می‌کنیم؟](#چرا-از-indexer-استفاده-می‌کنیم)
3. [ساختار پایه Indexer در C#](#ساختار-پایه-indexer-در-c)
4. [Overloading Indexers چیست؟](#overloading-indexers-چیست)
5. [مثال‌های کاربردی Overloading Indexers](#مثالهای-کاربردی-overloading-indexers)
   - [مثال ۱: Indexer با نوع int و string](#مثال-۱-indexer-با-نوع-int-و-string)
   - [مثال ۲: Indexer با چند پارامتر](#مثال-۲-indexer-با-چند-پارامتر)
6. [ملاحظات و بهترین روش‌ها (Best Practices)](#ملاحظات-و-بهترین-روشها-best-practices)
7. [تفاوت Indexer با Property](#تفاوت-indexer-با-property)
8. [جمع‌بندی](#جمعبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه: Indexer چیست؟

در زبان C#، **Indexer** یک عضو خاص کلاس یا ساختار (struct) است که به شما امکان می‌دهد نمونه‌ای از یک کلاس را **مانند یک آرایه** اندیس‌گذاری کنید.  
به عبارت دیگر، Indexer به شما اجازه می‌دهد تا از عملگر `[]` برای دسترسی به عناصر داخل یک شیء استفاده کنید — دقیقاً همان‌طور که با آرایه‌ها یا لیست‌ها کار می‌کنید.

> 🔹 **نکته:** Indexerها معمولاً در کلاس‌هایی استفاده می‌شوند که داده‌های داخلی خود را به صورت آرایه‌ای یا دیکشنری‌مانند نگه می‌دارند.

---

## چرا از Indexer استفاده می‌کنیم؟

- **خوانایی کد:** کد شما شبیه به کار با آرایه‌ها می‌شود و درک آن ساده‌تر است.
- **انعطاف‌پذیری:** می‌توانید منطق دسترسی به داده را کنترل کنید (مثلاً اعتبارسنجی، لاگ‌گیری، محاسبه).
- **شبیه‌سازی رفتار آرایه‌ها:** برای کلاس‌هایی که مجموعه‌ای از داده‌ها را نگه می‌دارند، Indexer رابط کاربری طبیعی‌تری فراهم می‌کند.

---

## ساختار پایه Indexer در C#

یک Indexer ساده در C# به این شکل تعریف می‌شود:

```csharp
public class MyCollection
{
    private string[] data = new string[100];

    // Indexer
    public string this[int index]
    {
        get { return data[index]; }
        set { data[index] = value; }
    }
}
```

حالا می‌توانید از آن به این شکل استفاده کنید:

```csharp
MyCollection mc = new MyCollection();
mc[0] = "Hello";
Console.WriteLine(mc[0]); // خروجی: Hello
```

---

## Overloading Indexers چیست؟

**Overloading Indexers** یعنی تعریف چند Indexer با **امضا (signature) متفاوت** در یک کلاس.  
همان‌طور که می‌توانید متد‌ها را با پارامترهای متفاوت overload کنید، Indexerها نیز قابل overload هستند — به شرطی که **نوع پارامترهای آن‌ها متفاوت باشد**.

> ⚠️ **نکته مهم:** Indexerها فقط بر اساس **نوع پارامتر(های) ورودی** overload می‌شوند — نه بر اساس نوع بازگشتی.

---

## مثال‌های کاربردی Overloading Indexers

### مثال ۱: Indexer با نوع `int` و `string`

فرض کنید یک کلاس دارید که هم با اندیس عددی و هم با کلید رشته‌ای قابل دسترسی است:

```csharp
public class StudentCollection
{
    private string[] names = { "Ali", "Reza", "Sara" };
    private Dictionary<string, int> nameToIndex = new()
    {
        ["Ali"] = 0,
        ["Reza"] = 1,
        ["Sara"] = 2
    };

    // Indexer با int
    public string this[int index]
    {
        get => names[index];
        set => names[index] = value;
    }

    // Indexer با string (Overloaded)
    public string this[string name]
    {
        get => names[nameToIndex[name]];
        set => names[nameToIndex[name]] = value;
    }
}
```

استفاده:

```csharp
StudentCollection students = new();
Console.WriteLine(students[0]);      // Ali
Console.WriteLine(students["Reza"]); // Reza
```

---

### مثال ۲: Indexer با چند پارامتر

Indexerها می‌توانند چند پارامتر نیز بپذیرند — مثلاً برای شبیه‌سازی یک ماتریس:

```csharp
public class Matrix
{
    private int[,] data = new int[10, 10];

    // Indexer دو بعدی
    public int this[int row, int col]
    {
        get => data[row, col];
        set => data[row, col] = value;
    }

    // Indexer یک‌بعدی (مثلاً برای دسترسی به کل سطر)
    public int[] this[int row]
    {
        get
        {
            int[] result = new int[10];
            for (int i = 0; i < 10; i++)
                result[i] = data[row, i];
            return result;
        }
    }
}
```

> ❗ توجه: در این مثال، دو Indexer با امضاهای متفاوت داریم:  
> - `this[int, int]`  
> - `this[int]` → بازگشتی `int[]`

---

## ملاحظات و بهترین روش‌ها (Best Practices)

1. **اعتبارسنجی ورودی:** همیشه ورودی‌های Indexer را بررسی کنید (مثلاً `index < 0` یا `key == null`).
2. **استثناهای مناسب:** از `IndexOutOfRangeException` یا `KeyNotFoundException` استفاده کنید.
3. **از `get` و `set` به صورت جداگانه استفاده کنید:** اگر فقط خواندنی است، `set` را حذف کنید.
4. **از overloading بیش از حد خودداری کنید:** فقط زمانی overload کنید که منطق کاربردی داشته باشد.
5. **مستندسازی:** حتماً Indexerها را با XML Comments مستند کنید.

---

## تفاوت Indexer با Property

| ویژگی | Property | Indexer |
|--------|----------|---------|
| نحوه دسترسی | `obj.Name` | `obj[0]` یا `obj["key"]` |
| پارامتر | ندارد | حتماً دارد (حداقل یکی) |
| static بودن | می‌تواند static باشد | **نمی‌تواند** static باشد |
| نام | دارد (مثلاً `Name`) | بدون نام — با کلمه کلیدی `this` تعریف می‌شود |

---

## جمع‌بندی

- Indexerها به شما اجازه می‌دهند شیء خود را مانند یک آرایه اندیس‌گذاری کنید.
- **Overloading Indexers** با تغییر نوع یا تعداد پارامترهای Indexer انجام می‌شود.
- این قابلیت به کد شما **خوانایی بیشتر** و **انعطاف‌پذیری منطقی** می‌دهد.
- همیشه به **اعتبارسنجی** و **استثناهای مناسب** توجه کنید.

---

## منابع معتبر

1. **Microsoft Learn – Indexers (C# Programming Guide)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/)

2. **Microsoft Learn – Indexer Overloading**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/using-indexers](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/using-indexers)

3. **C# Language Specification – Indexers**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/indexers](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/indexers)

4. **"C# in Depth" by Jon Skeet** – فصل مربوط به عضوهای کلاس و Indexers  
   (ISBN: 978-1617294532)

5. **"CLR via C#" by Jeffrey Richter** – بخش مربوط به طراحی کلاس‌های قابل اندیس‌گذاری  
   (ISBN: 978-0735667457)

