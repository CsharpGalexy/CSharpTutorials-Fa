### USER
از مقدماتی تا متوط هر چی در مورد LINQ Examplesدر Extension Methods فقط در مورد این بگو میدونی در زبان سیشارپ بگو به صورت مستند میخوام



یه ریپوزیتوری دارم دارم آموزش oop میزارم توش  برای اون میخوام



لطفا از منابع معتبر جمع اوری کن



منابع هم بگو در اخر

دارای فهرست مطالب و لینک دهی معتبر  باشه

یه جوری باشه خوانایی  خوبی داشته باشه برای کسی که مبتدی است

### ASSISTANT
# آموزش LINQ با تمرکز بر **Extension Methods** در C#  
*(برای ریپوزیتوری آموزش OOP)*

---

## 📚 فهرست مطالب

1. [مقدمه‌ای بر LINQ و Extension Methods](#1-مقدمه‌ای-بر-linq-و-extension-methods)  
2. [Extension Methods چیست؟](#2-extension-methods-چیست)  
3. [چرا LINQ از Extension Methods استفاده می‌کند؟](#3-چرا-linq-از-extension-methods-استفاده-می‌کند)  
4. [مثال‌های کاربردی LINQ با Extension Methods](#4-مثال‌های-کاربردی-linq-با-extension-methods)  
   - [4.1 Where – فیلتر کردن داده‌ها](#41-where--فیلتر-کردن-داده‌ها)  
   - [4.2 Select – تبدیل داده‌ها](#42-select--تبدیل-داده‌ها)  
   - [4.3 OrderBy / OrderByDescending – مرتب‌سازی](#43-orderby--orderbydescending--مرتب‌سازی)  
   - [4.4 GroupBy – گروه‌بندی داده‌ها](#44-groupby--گروه‌بندی-داده‌ها)  
   - [4.5 Any / All – بررسی شرایط](#45-any--all--بررسی-شرایط)  
   - [4.6 First / FirstOrDefault – یافتن اولین مورد](#46-first--firstordefault--یافتن-اولین-مورد)  
   - [4.7 Count / Sum / Average – محاسبات آماری](#47-count--sum--average--محاسبات-آماری)  
5. [تفاوت Query Syntax و Method Syntax](#5-تفاوت-query-syntax-و-method-syntax)  
6. [نکات مهم برای توسعه‌دهندگان مبتدی](#6-نکات-مهم-برای-توسعه‌دهندگان-مبتدی)  
7. [منابع معتبر](#7-منابع-معتبر)

---

## 1. مقدمه‌ای بر LINQ و Extension Methods

**LINQ** (Language Integrated Query) یکی از قدرتمندترین ویژگی‌های زبان C# است که به شما امکان می‌دهد به صورت **declarative** (توصیفی) روی مجموعه‌های داده (مانند آرایه‌ها، لیست‌ها، دیتابیس‌ها و غیره) پرس‌وجو انجام دهید.

در LINQ دو سبک نوشتاری وجود دارد:
- **Query Syntax** (مثل SQL)
- **Method Syntax** (با استفاده از **Extension Methods**)

در این راهنما، فقط روی **Method Syntax** و **Extension Methods** تمرکز می‌کنیم، چون این سبک بیشتر در کدنویسی مدرن و Functional Programming استفاده می‌شود.

---

## 2. Extension Methods چیست؟

**Extension Methods** روشی برای **افزودن متد به کلاس‌های موجود بدون تغییر کد اصلی آن‌ها** هستند. این متد‌ها باید:
- در یک کلاس `static` تعریف شوند.
- خود متد نیز `static` باشد.
- اولین پارامتر آن‌ها با کلیدواژه `this` شروع شود.

### مثال ساده:
```csharp
public static class StringExtensions
{
    public static bool IsNullOrEmpty(this string str)
    {
        return string.IsNullOrEmpty(str);
    }
}

// استفاده:
string name = null;
bool isEmpty = name.IsNullOrEmpty(); // کار می‌کند!
```

> 🔍 LINQ تمام متد‌های خود (مثل `Where`, `Select`, `OrderBy` و ...) را به صورت **Extension Method** روی رابط `IEnumerable<T>` پیاده‌سازی کرده است.

---

## 3. چرا LINQ از Extension Methods استفاده می‌کند؟

- **قابلیت گسترش (Extensibility):** بدون نیاز به تغییر کلاس‌های اصلی (مثل `List<T>`)، می‌توانید رفتار جدید اضافه کنید.
- **Fluent API:** امکان زنجیره‌ای نوشتن متد‌ها (`list.Where(...).Select(...).OrderBy(...)`) فراهم می‌شود.
- **یکپارچگی با IEnumerable<T>:** هر نوعی که `IEnumerable<T>` را پیاده‌سازی کند (مثل آرایه، لیست، دیکشنری، دیتابیس)، می‌تواند از LINQ استفاده کند.

---

## 4. مثال‌های کاربردی LINQ با Extension Methods

فرض کنید یک کلاس `Person` داریم:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string City { get; set; }
}
```

و یک لیست از افراد:

```csharp
var people = new List<Person>
{
    new Person { Name = "Ali", Age = 25, City = "Tehran" },
    new Person { Name = "Sara", Age = 30, City = "Shiraz" },
    new Person { Name = "Reza", Age = 20, City = "Tehran" },
    new Person { Name = "Narges", Age = 35, City = "Isfahan" }
};
```

### 4.1 Where – فیلتر کردن داده‌ها

فقط افراد بالای 25 سال:

```csharp
var adults = people.Where(p => p.Age > 25).ToList();
```

> 💡 `Where` یک **Extension Method** روی `IEnumerable<T>` است.

---

### 4.2 Select – تبدیل داده‌ها

استخراج فقط نام‌ها:

```csharp
var names = people.Select(p => p.Name).ToList(); // List<string>
```

یا تبدیل به یک ساختار جدید:

```csharp
var nameAges = people.Select(p => new { p.Name, p.Age }).ToList();
```

---

### 4.3 OrderBy / OrderByDescending – مرتب‌سازی

مرتب‌سازی بر اساس سن:

```csharp
var sortedByAge = people.OrderBy(p => p.Age).ToList();
var sortedDesc = people.OrderByDescending(p => p.Age).ToList();
```

ترکیب با سایر متد‌ها:

```csharp
var youngTehranis = people
    .Where(p => p.City == "Tehran")
    .OrderBy(p => p.Age)
    .ToList();
```

---

### 4.4 GroupBy – گروه‌بندی داده‌ها

گروه‌بندی بر اساس شهر:

```csharp
var groupedByCity = people.GroupBy(p => p.City);

foreach (var group in groupedByCity)
{
    Console.WriteLine($"City: {group.Key}");
    foreach (var person in group)
        Console.WriteLine($"  - {person.Name}");
}
```

---

### 4.5 Any / All – بررسی شرایط

آیا حداقل یک نفر از تهران است؟

```csharp
bool hasTehranis = people.Any(p => p.City == "Tehran");
```

آیا همه بالای 18 سال هستند؟

```csharp
bool allAdults = people.All(p => p.Age >= 18);
```

> ✅ این متد‌ها **بهینه‌تر** از `Count() > 0` هستند چون در اولین مورد منطبق، متوقف می‌شوند.

---

### 4.6 First / FirstOrDefault – یافتن اولین مورد

اولین فرد از شیراز:

```csharp
var firstShirazi = people.First(p => p.City == "Shiraz");
```

اگر مطمئن نیستید که وجود دارد:

```csharp
var firstMashhadi = people.FirstOrDefault(p => p.City == "Mashhad"); // null اگر نباشد
```

> ⚠️ `First()` اگر موردی پیدا نکند، **استثنا** می‌اندازد. `FirstOrDefault()` در این صورت `null` (یا مقدار پیش‌فرض) برمی‌گرداند.

---

### 4.7 Count / Sum / Average – محاسبات آماری

تعداد افراد:

```csharp
int count = people.Count();
int adultsCount = people.Count(p => p.Age > 25);
```

جمع سن‌ها:

```csharp
int totalAge = people.Sum(p => p.Age);
```

میانگین سن:

```csharp
double avgAge = people.Average(p => p.Age);
```

---

## 5. تفاوت Query Syntax و Method Syntax

| ویژگی | Query Syntax | Method Syntax |
|--------|--------------|----------------|
| شبیه‌تر به | SQL | C# معمولی |
| نحوه نوشتن | `from p in people where p.Age > 25 select p` | `people.Where(p => p.Age > 25)` |
| پشتیبانی از Extension Methods | ❌ | ✅ |
| رایج در | مبتدی‌ها | توسعه‌دهندگان حرفه‌ای |
| قابلیت زنجیره‌ای | محدود | کامل |

> 💡 هر دو سبک **یکسان کامپایل می‌شوند**! Query Syntax در پس‌زمینه به Method Syntax تبدیل می‌شود.

---

## 6. نکات مهم برای توسعه‌دهندگان مبتدی

1. **ToList() یا ToArray() را فراموش نکنید:**  
   LINQ **lazy evaluation** دارد. یعنی تا زمانی که نتیجه را مصرف نکنید (مثلاً با `foreach` یا `ToList()`)، پرس‌وجو اجرا نمی‌شود.

2. **Null Reference را چک کنید:**  
   قبل از استفاده از LINQ روی یک لیست، مطمئن شوید `null` نیست.

3. **از FirstOrDefault به جای First استفاده کنید** مگر اینکه مطمئن باشید داده وجود دارد.

4. **Performance:**  
   LINQ برای داده‌های کوچک و متوسط عالی است، اما برای حلقه‌های بسیار حجیم، گاهی `for` سریع‌تر است (هرچند خوانایی LINQ بیشتر است).

5. **قابلیت تست‌پذیری:**  
   کدهای LINQ را می‌توان به راحتی تست کرد چون functional هستند (side-effect ندارند).

---

## 7. منابع معتبر

1. **Microsoft Learn – LINQ (Language-Integrated Query)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/linq/](https://learn.microsoft.com/en-us/dotnet/csharp/linq/)

2. **Microsoft Learn – Extension Methods**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)

3. **C# in Depth – Jon Skeet** (فصل 11: LINQ)  
   🔗 [https://csharpindepth.com/](https://csharpindepth.com/)

4. **.NET Documentation – IEnumerable<T> Interface**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1)

5. **Pluralsight – LINQ Fundamentals** (دوره ویدیویی)  
   🔗 [https://www.pluralsight.com/courses/linq-fundamentals-csharp-6](https://www.pluralsight.com/courses/linq-fundamentals-csharp-6)

---

✅ این مطلب به گونه‌ای نوشته شده که برای **دانش‌آموزان مبتدی تا متوسط** در ریپوزیتوری آموزش OOP شما مناسب باشد و همچنین از **اصول بهترین شیوه‌های کدنویسی (Best Practices)** پیروی کند.

اگر نیاز به نسخه Markdown برای گیت‌هاب دارید، می‌توانم آن را نیز ارائه دهم.