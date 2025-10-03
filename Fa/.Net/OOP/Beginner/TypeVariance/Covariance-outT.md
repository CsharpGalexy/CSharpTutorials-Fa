
## فهرست مطالب

1. [مقدمه: Type Variance چیست؟](#مقدمه-type-variance-چیست)
2. [Covariance چیست؟](#covariance-چیست)
3. [چرا Covariance مهم است؟](#چرا-covariance-مهم-است)
4. [نحوه استفاده از `out T` در C#](#نحوه-استفاده-از-out-t-در-c)
5. [مثال‌های کاربردی](##مثال%E2%80%8Cهای-کاربردی)
6. [محدودیت‌های Covariance](#محدودیت%E2%80%8Cهای-covariance)
7. [تفاوت Covariance با Contravariance](#تفاوت-covariance-با-contravariance)
8. [جمع‌بندی](#جمع%E2%80%8Cبندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه: Type Variance چیست؟

در برنامه‌نویسی شیء‌گرا (OOP)، **Type Variance** (واریانس نوع) به رفتار یک ساختار عمومی (Generic) نسبت به روابط سلسله مراتبی انواع (Inheritance) اشاره دارد. به عبارت ساده‌تر، وقتی یک کلاس یا رابط عمومی داریم مثل `IEnumerable<T>`، می‌خواهیم بدانیم آیا می‌توانیم به جای `T` از یک زیرنوع یا ابرنوع آن استفاده کنیم یا خیر.

در C#، سه نوع واریانس وجود دارد:

- **Covariance** (`out T`)
- **Contravariance** (`in T`)
- **Invariance** (بدون کلمه کلیدی)

در این مقاله فقط به **Covariance** می‌پردازیم.

---

## Covariance چیست؟

**Covariance** اجازه می‌دهد که یک ساختار عمومی (مثل رابط یا دیلگیت) با یک **زیرنوع** (Subtype) جایگزین شود. به عبارت دیگر، اگر `Cat` زیرنوع `Animal` باشد، آنگاه `IEnumerable<Cat>` را بتوان به عنوان `IEnumerable<Animal>` استفاده کرد.

در C#، برای فعال‌سازی Covariance در رابط‌ها و دیلگیت‌ها، از کلمه کلیدی **`out`** قبل از پارامتر نوع استفاده می‌شود:

```csharp
interface IProducer<out T>
{
    T Produce();
}
```

کلمه کلیدی `out` نشان می‌دهد که `T` فقط در **خروجی** (مثلاً مقدار بازگشتی متد) استفاده می‌شود و هرگز در ورودی (مثل پارامتر متد) نیست.

---

## چرا Covariance مهم است؟

بدون Covariance، کد زیر **کامپایل نمی‌شود**:

```csharp
IEnumerable<string> strings = new List<string>();
IEnumerable<object> objects = strings; // ❌ بدون Covariance خطای کامپایل می‌دهد
```

اما با وجود Covariance در `IEnumerable<out T>`، این کد **کاملاً معتبر** است، چون `string` زیرنوع `object` است و `IEnumerable<T>` به صورت **Covariant** تعریف شده است.

این ویژگی:
- انعطاف‌پذیری کد را افزایش می‌دهد.
- از نیاز به تبدیل دستی (casting) جلوگیری می‌کند.
- امنیت نوع (Type Safety) را حفظ می‌کند.

---

## نحوه استفاده از `out T` در C#

### شرایط استفاده از `out`:

1. فقط در **رابط‌ها** (`interface`) و **دیلگیت‌ها** (`delegate`) قابل استفاده است.
2. پارامتر نوع (`T`) **نباید** در موقعیت‌های **ورودی** (مثل پارامتر متد یا فیلد) استفاده شود.
3. فقط در **موقعیت‌های خروجی** (مثل مقدار بازگشتی متد یا پراپرتی فقط خواندنی) مجاز است.

### مثال معتبر:

```csharp
public interface ICovariant<out T>
{
    T GetValue(); // ✅ فقط خروجی
    // void SetValue(T value); // ❌ این خط باعث خطا می‌شود
}
```

### مثال نامعتبر:

```csharp
public interface IInvalid<out T>
{
    void SetValue(T value); // ❌ خطای کامپایل: T در موقعیت ورودی استفاده شده
}
```

---

## مثال‌های کاربردی

### مثال ۱: استفاده از `IEnumerable<out T>`

```csharp
class Animal { public virtual void Speak() => Console.WriteLine("..."); }
class Dog : Animal { public override void Speak() => Console.WriteLine("Woof!"); }

var dogs = new List<Dog> { new Dog(), new Dog() };
IEnumerable<Animal> animals = dogs; // ✅ ممکن است چون IEnumerable<out T> است

foreach (var animal in animals)
    animal.Speak();
```

### مثال ۲: ساخت رابط Covariant سفارشی

```csharp
public interface IFactory<out T>
{
    T Create();
}

public class DogFactory : IFactory<Dog>
{
    public Dog Create() => new Dog();
}

// استفاده:
IFactory<Dog> dogFactory = new DogFactory();
IFactory<Animal> animalFactory = dogFactory; // ✅ مجاز است
```

---

## محدودیت‌های Covariance

- **کلاس‌ها (class)** نمی‌توانند Covariant باشند. فقط **رابط‌ها** و **دیلگیت‌ها**.
- پارامتر نوع با `out` **نباید** در موقعیت‌های ورودی (مثل پارامتر متد یا فیلد) استفاده شود.
- Covariance فقط برای **انواع مرجع** (Reference Types) کار می‌کند، نه انواع مقداری (Value Types) مگر اینکه با boxing تبدیل شوند.

---

## تفاوت Covariance با Contravariance

| ویژگی | Covariance (`out T`) | Contravariance (`in T`) |
|--------|----------------------|--------------------------|
| جهت تبدیل | زیرنوع → ابرنوع | ابرنوع → زیرنوع |
| مثال | `IEnumerable<Cat>` → `IEnumerable<Animal>` | `Action<Animal>` → `Action<Cat>` |
| موقعیت استفاده | فقط خروجی | فقط ورودی |
| کلمه کلیدی | `out` | `in` |

> 💡 نکته: `Action<T>` در C# به صورت `Action<in T>` تعریف شده است (Contravariant).

---

## جمع‌بندی

- **Covariance** با کلمه کلیدی `out` در C# پیاده‌سازی می‌شود.
- فقط در **رابط‌ها** و **دیلگیت‌ها** قابل استفاده است.
- اجازه می‌دهد یک ساختار عمومی با زیرنوع جایگزین شود (مثلاً `IEnumerable<Cat>` به جای `IEnumerable<Animal>`).
- برای حفظ **Type Safety**، پارامتر نوع فقط می‌تواند در موقعیت‌های **خروجی** استفاده شود.
- این ویژگی کد را انعطاف‌پذیر‌تر و خوانا‌تر می‌کند.

---

## منابع معتبر

1. **Microsoft Docs – Covariance and Contravariance**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/)

2. **C# Language Specification – Variance in Generic Interfaces**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/interfaces#variance](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/interfaces#variance)

3. **Eric Lippert’s Blog (Former C# Team Member) – Covariance and Contravariance FAQ**  
   [https://ericlippert.com/2009/11/30/whats-the-difference-between-covariance-and-assignment-compatibility/](https://ericlippert.com/2009/11/30/whats-the-difference-between-covariance-and-assignment-compatibility/)

4. **C# in Depth – Jon Skeet (Chapter on Variance)**  
   ISBN: 978-1617294938 – فصل 13: Variance in generic types

5. **Stack Overflow – When to use `out` keyword in C# generics?**  
   [https://stackoverflow.com/questions/273336/what-is-the-out-keyword-in-c-sharp](https://stackoverflow.com/questions/273336/what-is-the-out-keyword-in-c-sharp)

---

> ✅ این مطلب برای استفاده در ریپوزیتوری آموزش OOP شما مناسب است و با رعایت اصول مستندسازی، خوانایی و استناد به منابع معتبر نوشته شده است.  
> 📌 پیشنهاد: این متن را در یک فایل `Covariance-in-CSharp.md` در پوشه‌ی `docs/type-variance/` ریپوزیتوری خود قرار دهید.
