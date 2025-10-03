

## فهرست مطالب

1. [مقدمه‌ای بر Type Variance](#مقدمه%E2%80%8Cای-بر-type-variance)
2. [سه نوع واریانس در C#](#سه-نوع-واریانس-در-c)
3. [Invariance چیست؟](#invariance-چیست)
4. [چرا Invariance مهم است؟](#چرا-invariance-مهم-است)
5. [مثال‌های کاربردی Invariance در C#](#مثال%E2%80%8Cهای-کاربردی-invariance-در-c)
6. [تفاوت Invariance با Covariance و Contravariance](#تفاوت-invariance-با-covariance-و-contravariance)
7. [جمع‌بندی](#جمع%E2%80%8Cبندی)
8. [منابع معتبر](#منابع-معتبر)

---

## مقدمه‌ای بر Type Variance

در برنامه‌نویسی شیء‌گرا (OOP)، **Type Variance** (واریانس نوع) به رفتار انواع عمومی (Generic Types) نسبت به روابط سلسله‌مراتبی بین انواع پایه و فرزند اشاره دارد.

به عبارت ساده:  
اگر `Cat` از `Animal` ارث‌بری کند، آیا `List<Cat>` می‌تواند جایگزین `List<Animal>` شود؟  
پاسخ به این سوال به **نوع واریانس** بستگی دارد.

در C#، واریانس فقط در **انواع عمومی رابط (interface)** و **دِلِگیت (delegate)** پشتیبانی می‌شود — نه در کلاس‌های عمومی مانند `List<T>`.

---

## سه نوع واریانس در C#

C# از سه نوع واریانس پشتیبانی می‌کند:

| نوع واریانس | کلیدواژه | معنی |
|--------------|----------|------|
| **Covariance** | `out` | اجازه می‌دهد نوع فرزند جایگزین نوع پایه شود (روند خروجی) |
| **Contravariance** | `in` | اجازه می‌دهد نوع پایه جایگزین نوع فرزند شود (روند ورودی) |
| **Invariance** | بدون کلیدواژه | هیچ جایگزینی مجاز نیست |

---

## Invariance چیست؟

**Invariance** (ناوردا) به این معنی است که یک نوع عمومی **مستقل از رابطه سلسله‌مراتبی** انواع است. یعنی:

- اگر `T1` از `T2` ارث‌بری کند،  
- آنگاه `GenericType<T1>` **نمی‌تواند** جایگزین `GenericType<T2>` شود،  
- و بالعکس هم صادق نیست.

به عبارت دیگر: **هیچ رابطه‌ای بین `GenericType<T1>` و `GenericType<T2>` وجود ندارد**، حتی اگر `T1` و `T2` با هم مرتبط باشند.

> ⚠️ **نکته مهم**: در C#، تمام کلاس‌های عمومی (مثل `List<T>`, `Stack<T>`) **ناوردا (Invariant)** هستند.

---

## چرا Invariance مهم است؟

Invariance برای **ایمنی نوع (Type Safety)** ضروری است.  
در نظر بگیرید:

```csharp
List<Animal> animals = new List<Animal>();
List<Cat> cats = new List<Cat>();

// اگر List<T> Covariant بود، این خط مجاز می‌شد:
List<Animal> animals = cats; // ❌ این خط در C# خطا می‌دهد!

// حالا فرض کنیم مجاز بود:
animals.Add(new Dog()); // Dog از Animal ارث‌بری می‌کند
Cat myCat = cats[0]; // ❌ خطای زمان اجرا! چون یک Dog در لیست cats قرار گرفته!
```

بنابراین، C# به‌طور پیش‌فرض تمام کلاس‌های عمومی را **ناوردا** در نظر می‌گیرد تا از چنین خطاهایی جلوگیری کند.

---

## مثال‌های کاربردی Invariance در C#

### مثال ۱: `List<T>` ناوردا است

```csharp
class Animal { }
class Cat : Animal { }

// این خط‌ها کامپایل نمی‌شوند:
List<Animal> animals = new List<Cat>(); // ❌ خطای کامپایل
List<Cat> cats = new List<Animal>();    // ❌ خطای کامپایل
```

### مثال ۲: `IList<T>` نیز ناوردا است

حتی اگر از رابط استفاده کنیم:

```csharp
IList<Animal> animals = new List<Cat>(); // ❌ خطای کامپایل
```

چون `IList<T>` هم برای خواندن و هم برای نوشتن استفاده می‌شود، پس **Invariant** است.

### مثال ۳: مقایسه با رابط‌های Covariant

در مقابل، `IEnumerable<T>` **Covariant** است (با کلیدواژه `out`):

```csharp
IEnumerable<Animal> animals = new List<Cat>(); // ✅ مجاز است!
```

چون `IEnumerable<T>` فقط برای **خواندن** استفاده می‌شود (متد `GetEnumerator()` فقط خروجی دارد).

---

## تفاوت Invariance با Covariance و Contravariance

| ویژگی | Invariance | Covariance (`out`) | Contravariance (`in`) |
|--------|------------|--------------------|------------------------|
| جایگزینی | ❌ ممنوع | ✅ `Derived → Base` | ✅ `Base → Derived` |
| کاربرد | خواندن + نوشتن | فقط خواندن | فقط نوشتن |
| مثال در C# | `List<T>`, `IList<T>` | `IEnumerable<out T>` | `Action<in T>` |
| ایمنی نوع | حداکثر ایمنی | ایمن برای خروجی | ایمن برای ورودی |

---

## جمع‌بندی

- **Invariance** یعنی نوع عمومی نسبت به روابط سلسله‌مراتبی انواع **حساسیتی نشان نمی‌دهد** — یعنی هیچ جایگزینی مجاز نیست.
- در C#، تمام **کلاس‌های عمومی** (مثل `List<T>`) و رابط‌هایی که هم برای ورودی و هم خروجی استفاده می‌شوند (مثل `IList<T>`) **ناوردا** هستند.
- این رفتار برای **جلوگیری از خطاهای زمان اجرا** و حفظ **ایمنی نوع** ضروری است.
- برای استفاده از Covariance یا Contravariance، باید از **رابط‌ها یا دلگیت‌هایی** استفاده کنید که با `out` یا `in` تعریف شده‌اند.

---

## منابع معتبر

1. **مستندات رسمی مایکروسافت (Microsoft Docs)**  
   - [Variance in Generic Interfaces (C#)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-generic-interfaces)  
   - [Covariance and Contravariance](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/)  
   - [Invariance in Generics](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generics-and-arrays#variance-in-generic-interfaces)

2. **C# Language Specification (ECMA-334)**  
   - بخش 19.5: Variance Safety

3. **کتاب "C# in Depth" نوشته Jon Skeet**  
   - فصل 13: Variance in C#

4. **Stack Overflow – پاسخ‌های تأییدشده توسط تیم C#**  
   - [Why are generic collections invariant?](https://stackoverflow.com/questions/2456073/why-are-generic-collections-invariant)

