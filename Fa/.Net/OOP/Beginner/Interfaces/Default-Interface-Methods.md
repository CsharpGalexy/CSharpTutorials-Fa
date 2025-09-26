

## فهرست مطالب

- [مقدمه](#مقدمه)
- [تعریف Default Interface Methods (DIM)](#تعریف-default-interface-methods-dim)
- [چرا DIM معرفی شد؟](#چرا-dim-معرفی-شد)
- [نحوه استفاده از DIM](#نحوه-استفاده-از-dim)
- [مزایای DIM](#مزایای-dim)
- [محدودیت‌ها و نکات مهم](#محدودیت‌ها-و-نکات-مهم)
- [تفاوت DIM با کلاس‌های انتزاعی](#تفاوت-dim-با-کلاس‌های-انتزاعی)
- [مثال‌های کاربردی](#مثال‌های-کاربردی)
- [جمع‌بندی](#جمع‌بندی)
- [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در نسخه‌های قبلی C#، رابط‌ها (Interfaces) فقط می‌توانستند **اعلان** اعضا را داشته باشند و **هیچ پیاده‌سازی پیش‌فرضی** نداشتند. این محدودیت باعث می‌شد که اگر بخواهیم یک عضو جدید به یک رابط موجود اضافه کنیم، تمام کلاس‌هایی که از آن رابط پیاده‌سازی می‌کردند، نیاز به تغییر داشته باشند — که این امر به‌ویژه در کتابخانه‌های عمومی (public libraries) مشکل‌ساز بود.

با معرفی **Default Interface Methods (DIM)** در **C# 8.0** (در سال 2019)، این محدودیت برداشته شد و امکان تعریف **پیاده‌سازی پیش‌فرض برای اعضا در رابط‌ها** فراهم گردید.

---

## تعریف Default Interface Methods (DIM)

**Default Interface Methods** یا **DIM** امکان تعریف **متد، خاصیت (property)، رویداد (event) و ایندکسر (indexer)** با **بدنه پیاده‌سازی پیش‌فرض** در داخل یک رابط را فراهم می‌کند. این ویژگی به توسعه‌دهندگان اجازه می‌دهد بدون شکستن کد موجود، رابط‌های قدیمی را گسترش دهند.

> 🔹 **نکته**: DIM فقط در C# 8.0 و بالاتر (در .NET Core 3.0+ یا .NET 5+) پشتیبانی می‌شود.

---

## چرا DIM معرفی شد؟

### 1. **حل مشکل "شکستن تغییرات" (Breaking Changes)**
در کتابخانه‌های عمومی (مثل NuGet packages)، اضافه کردن یک متد جدید به یک رابط، باعث می‌شد تمام کلاس‌های پیاده‌سازی‌کننده آن رابط کامپایل نشوند. DIM این مشکل را با ارائه یک پیاده‌سازی پیش‌فرض حل می‌کند.

### 2. **پشتیبانی از "Mixin"-ها**
DIM امکان شبیه‌سازی رفتار **Mixin** (ترکیب رفتار بدون ارث‌بری چندگانه) را فراهم می‌کند.

### 3. **همگام‌سازی با زبان‌های دیگر**
زبان‌هایی مانند Java (از نسخه 8) و Kotlin از قبل این قابلیت را داشتند. Microsoft برای هماهنگی با روندهای مدرن برنامه‌نویسی، این ویژگی را به C# اضافه کرد.

---

## نحوه استفاده از DIM

### ساختار کلی

```csharp
public interface IMyInterface
{
    // متد با پیاده‌سازی پیش‌فرض
    void DefaultMethod()
    {
        Console.WriteLine("این یک متد پیش‌فرض است.");
    }

    // خاصیت با پیاده‌سازی پیش‌فرض
    string DefaultProperty => "مقدار پیش‌فرض";

    // متد انتزاعی (بدون بدنه — همچنان نیاز به پیاده‌سازی دارد)
    void RequiredMethod();
}
```

### پیاده‌سازی در کلاس

```csharp
public class MyClass : IMyInterface
{
    // نیازی به پیاده‌سازی DefaultMethod نیست
    public void RequiredMethod()
    {
        Console.WriteLine("پیاده‌سازی الزامی");
    }
}
```

### override کردن متد پیش‌فرض

```csharp
public class MyCustomClass : IMyInterface
{
    public void RequiredMethod() { }

    // override متد پیش‌فرض
    public void DefaultMethod()
    {
        Console.WriteLine("نسخه سفارشی‌شده!");
    }
}
```

---

## مزایای DIM

✅ **گسترش رابط بدون شکستن کد موجود**  
✅ **کاهش تکرار کد** (چون رفتار مشترک در رابط قرار می‌گیرد)  
✅ **پشتیبانی از رفتارهای اختیاری**  
✅ **افزایش انعطاف‌پذیری در طراحی API**

---

## محدودیت‌ها و نکات مهم

### ❌ محدودیت‌ها:

1. **فیلدها (Fields) نمی‌توانند در رابط تعریف شوند** (حتی `private` یا `static`).
2. **سازنده‌ها (Constructors) در رابط مجاز نیستند**.
3. **وراثت چندگانه از رابط‌ها مجاز است، اما در صورت تداخل نام متد، باید در کلاس پیاده‌سازی‌کننده مشخص شود کدام یک استفاده شود**:

```csharp
interface IA
{
    void Do() => Console.WriteLine("IA");
}

interface IB
{
    void Do() => Console.WriteLine("IB");
}

class C : IA, IB
{
    // خطای کامپایل بدون پیاده‌سازی صریح
    void IA.Do() => ((IA)this).Do(); // یا مستقیم پیاده‌سازی
    void IB.Do() => ((IB)this).Do();
}
```

4. **اعضای پیش‌فرض نمی‌توانند `private` باشند** (اما از C# 8.0 به بعد، اعضای `private` در رابط مجاز شده‌اند — البته فقط برای استفاده داخلی در خود رابط).

---

## تفاوت DIM با کلاس‌های انتزاعی

| ویژگی | رابط با DIM | کلاس انتزاعی |
|--------|--------------|----------------|
| وراثت چندگانه | ✅ مجاز است | ❌ فقط یک کلاس والد |
| فیلد (Field) | ❌ مجاز نیست | ✅ مجاز است |
| سازنده (Constructor) | ❌ مجاز نیست | ✅ مجاز است |
| اعضای `protected` | ❌ ندارد | ✅ دارد |
| پیاده‌سازی پیش‌فرض | ✅ از C# 8.0 | ✅ همیشه داشته |
| هدف اصلی | تعریف قرارداد + رفتار اختیاری | تعریف پایه‌ای برای وراثت |

> 💡 **راهنمایی طراحی**:  
> - از **رابط** برای تعریف **قابلیت‌ها** (capabilities) استفاده کنید.  
> - از **کلاس انتزاعی** برای تعریف **یک نوع پایه** (base type) استفاده کنید.

---

## مثال‌های کاربردی

### مثال ۱: افزودن متد جدید به رابط قدیمی

```csharp
// رابط قدیمی
public interface ILogger
{
    void Log(string message);
}

// در C# 8.0+ می‌توانیم اضافه کنیم بدون شکستن کد
public interface ILogger
{
    void Log(string message);

    // متد جدید با پیاده‌سازی پیش‌فرض
    void LogError(string error) => Log($"ERROR: {error}");
}
```

حالا تمام کلاس‌هایی که `ILogger` را پیاده‌سازی کرده‌اند، بدون تغییر کار می‌کنند و متد `LogError` را به‌صورت پیش‌فرض دارند.

---

### مثال ۲: رفتار مشترک برای انواع مختلف

```csharp
public interface IDrawable
{
    void Draw();

    // رفتار پیش‌فرض برای انیمیشن
    void Animate()
    {
        Console.WriteLine("در حال انیمیت کردن...");
        Draw();
    }
}

public class Circle : IDrawable
{
    public void Draw() => Console.WriteLine("رسم دایره");
    // Animate به‌صورت پیش‌فرض ارث‌بری می‌شود
}
```

---

## جمع‌بندی

- **Default Interface Methods** یک ویژگی قدرتمند در C# 8.0+ است که انعطاف‌پذیری رابط‌ها را افزایش می‌دهد.
- این ویژگی به‌ویژه برای **توسعه‌دهندگان کتابخانه** بسیار مفید است، چون امکان گسترش رابط بدون شکستن کد کاربران را فراهم می‌کند.
- با این حال، جایگزین کلاس‌های انتزاعی نیست و هر کدام کاربردهای خاص خود را دارند.
- همیشه با دقت از DIM استفاده کنید تا طراحی شما از اصول SOLID (به‌ویژه اصل تفکیک رابط — ISP) پیروی کند.

---

## منابع معتبر

1. **مستندات رسمی Microsoft (Microsoft Docs)**  
   [Default Interface Methods - C# 8.0](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#default-interface-members)  
   [Interfaces - C# Guide](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface)

2. **Proposal رسمی در GitHub (C# Language Design)**  
   [Default Interface Methods Proposal](https://github.com/dotnet/csharplang/blob/main/proposals/default-interface-methods.md)

3. **کتاب "C# 9.0 in a Nutshell" — Joseph Albahari**  
   فصل 4: Object-Oriented Features → بخش Interfaces

4. **مقاله تحلیلی از Microsoft**  
   [Versioning with Default Interface Methods](https://devblogs.microsoft.com/dotnet/default-implementations-in-interfaces/)

5. **Pluralsight — C# 8.0 Features**  
   [C# 8.0: Default Interface Methods](https://www.pluralsight.com/courses/c-sharp-8-fundamentals)
