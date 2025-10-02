

## فهرست مطالب

1. [مقدمه: Variance چیست؟](#مقدمه-variance-چیست)
2. [Contravariance چیست؟](#contravariance-چیست)
3. [تفاوت Contravariance با Covariance و Invariance](#تفاوت-contravariance-با-covariance-و-invariance)
4. [نحوه استفاده از Contravariance در C#](#نحوه-استفاده-از-contravariance-در-c)
5. [مثال‌های عملی در C#](#مثالهای-عملی-در-c)
6. [محدودیت‌ها و نکات مهم](#محدودیتها-و-نکات-مهم)
7. [کاربردهای رایج Contravariance](#کاربردهای-رایج-contravariance)
8. [جمع‌بندی](#جمعبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه: Variance چیست؟

**Variance** (واریانس نوع) در برنامه‌نویسی به رفتار یک سازهٔ عمومی (generic) نسبت به روابط زیرمجموعه‌بودن (subtyping) بین انواع ورودی یا خروجی آن اشاره دارد. در C#، variance فقط برای **انواع رابط (interface)** و **دِلِگیت‌ها (delegate)** قابل اعمال است و در کلاس‌های عمومی (generic classes) پشتیبانی نمی‌شود.

سه نوع variance وجود دارد:
- **Covariance** (`out T`)
- **Contravariance** (`in T`)
- **Invariance** (بدون کلمه کلیدی)

در این مستند، فقط به **Contravariance** می‌پردازیم.

---

## Contravariance چیست؟

**Contravariance** اجازه می‌دهد که یک نوع عمومی با یک **نوع پایه‌تر** (base type) جایگزین نوع مشخص‌شده در تعریف شود، **اما فقط برای ورودی‌ها** (مثلاً پارامترهای متد).

در C#، برای فعال‌سازی contravariance از کلمه کلیدی `in` در تعریف رابط یا دِلِگیت استفاده می‌شود.

> 🔑 **نکته کلیدی**: Contravariance جهت رابطهٔ زیرمجموعه‌بودن را **معکوس** می‌کند.

به عبارت دیگر:
اگر `Dog` زیرمجموعهٔ `Animal` باشد (`Dog : Animal`)، آنگاه:
- یک `Action<Animal>` می‌تواند جایگزین `Action<Dog>` شود (چون `Action<T>` contravariant است).

---

## تفاوت Contravariance با Covariance و Invariance

| نوع Variance      | کلمه کلیدی | جهت رابطه | کاربرد |
|------------------|------------|------------|--------|
| **Covariance**   | `out T`    | همان جهت (`Dog → Animal`) | برای **خروجی** (مثلاً بازگشتی متد) |
| **Contravariance** | `in T`     | معکوس (`Animal → Dog`) | برای **ورودی** (مثلاً پارامتر متد) |
| **Invariance**   | بدون کلمه کلیدی | بدون تغییر | هم ورودی و هم خروجی |

---

## نحوه استفاده از Contravariance در C#

در C#، برای تعریف یک رابط یا دِلِگیت contravariant، از کلمه کلیدی `in` قبل از پارامتر نوع عمومی استفاده می‌کنیم.

### سینتکس:

```csharp
interface IContravariant<in T>
{
    void DoSomething(T item);
}
```

یا برای دِلِگیت:

```csharp
delegate void MyDelegate<in T>(T item);
```

### شرایط استفاده:
- `T` فقط باید در **موقعیت ورودی** (پارامتر متد) استفاده شود.
- نباید در بازگشتی متد یا به عنوان فیلد یا خاصیت استفاده شود.

---

## مثال‌های عملی در C#

### مثال ۱: استفاده از `IComparer<in T>`

یکی از معروف‌ترین مثال‌های contravariance در کتابخانه‌های استاندارد .NET، رابط `IComparer<in T>` است:

```csharp
public interface IComparer<in T>
{
    int Compare(T x, T y);
}
```

فرض کنید دو کلاس داریم:

```csharp
class Animal { public string Name { get; set; } }
class Dog : Animal { }
```

حالا یک مقایس‌کننده برای `Animal` می‌سازیم:

```csharp
public class AnimalComparer : IComparer<Animal>
{
    public int Compare(Animal x, Animal y) => 
        string.Compare(x.Name, y.Name);
}
```

با contravariance می‌توانیم این مقایس‌کننده را برای لیستی از `Dog` استفاده کنیم:

```csharp
List<Dog> dogs = new() { new Dog { Name = "Rex" }, new Dog { Name = "Buddy" } };
dogs.Sort(new AnimalComparer()); // ✅ مجاز است!
```

چون `IComparer<Animal>` می‌تواند جایگزین `IComparer<Dog>` شود.

---

### مثال ۲: دِلِگیت `Action<in T>`

دِلِگیت `Action<T>` در C# contravariant است:

```csharp
Action<Animal> feedAnimal = a => Console.WriteLine($"Feeding {a.Name}");
Action<Dog> feedDog = feedAnimal; // ✅ مجاز است!

feedDog(new Dog { Name = "Max" }); // خروجی: Feeding Max
```

این کار ممکن است چون `Action<Animal>` می‌تواند هر شیء از نوع `Animal` (و زیرمجموعه‌های آن مثل `Dog`) را بپذیرد.

---

## محدودیت‌ها و نکات مهم

1. **فقط برای رابط و دِلِگیت**: Contravariance در C# فقط برای `interface` و `delegate` پشتیبانی می‌شود، نه برای کلاس‌های عمومی.
2. **فقط برای ورودی**: نوع `in T` نباید در موقعیت خروجی (مثل بازگشتی متد یا خاصیت get) استفاده شود.
3. **Reference Types فقط**: Variance فقط برای انواع مرجع (reference types) کار می‌کند، نه انواع مقداری (value types) مثل `int` یا `struct`.
4. **نامناسب برای mutable state**: اگر رابط شامل متدهایی باشد که حالت شیء را تغییر می‌دهند، contravariance ممکن است منجر به مشکلات ایمنی نوع شود.

---

## کاربردهای رایج Contravariance

- **مقایسه‌کننده‌ها**: مانند `IComparer<in T>` یا `IEqualityComparer<in T>`
- **اکشن‌ها و هندلرها**: مانند `Action<in T>`, `EventHandler<in TEventArgs>`
- **فریم‌ورک‌های event-driven**: برای پذیرش هندلرهای عمومی‌تر برای رویدادهای خاص‌تر
- **الگوهای طراحی**: مانند Visitor یا Strategy که نیاز به انعطاف‌پذیری در ورودی دارند

---

## جمع‌بندی

Contravariance در C# یک ویژگی قدرتمند برای افزایش انعطاف‌پذیری کد است. با استفاده از کلمه کلیدی `in`، می‌توانیم رابط‌ها و دِلِگیت‌هایی تعریف کنیم که اجازه می‌دهند نوع پایه‌تر به جای نوع مشتق‌شده استفاده شود — اما فقط در موقعیت‌های **ورودی**. این ویژگی به کد شما اجازه می‌دهد کمتر تکرارشونده، عمومی‌تر و قابل استفاده‌تر باشد، بدون از دست دادن ایمنی نوع (type safety).

---

## منابع معتبر

1. **مستندات رسمی Microsoft**  
   [Variance in Generic Interfaces (C#)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-generic-interfaces)  
   [Contravariance for Generic Type Parameters](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/in-generic-modifier)

2. **C# Language Specification (ECMA-334)**  
   [ECMA-334 C# Language Specification – Section on Variance](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

3. **Jon Skeet – C# in Depth**  
   فصل 13: Variance in delegates and interfaces  
   ISBN: 978-1617294430

4. **Stack Overflow – Official Explanation**  
   [What is the difference between covariance and contravariance?](https://stackoverflow.com/questions/2662369/covariance-and-contravariance-real-world-example)

5. **Pluralsight Course**  
   *C# Generics and Collections* by Scott Allen – بخش Variance
