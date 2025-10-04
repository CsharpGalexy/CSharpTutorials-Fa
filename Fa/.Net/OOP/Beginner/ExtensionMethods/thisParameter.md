
## فهرست مطالب

1. [مقدمه](#1-مقدمه)
2. [متدهای اکستنشن چیستند؟](#2-متدهای-اکستنشن-چیستند؟)
3. [پارامتر `this` چیست و چه نقشی دارد؟](#3-پارامتر-this-چیست-و-چه-نقشی-دارد؟)
4. [نحوه تعریف یک Extension Method](#4-نحوه-تعریف-یک-extension-method)
5. [قوانین و محدودیت‌های استفاده از `this`](#5-قوانین-و-محدودیت‌های-استفاده-از-this)
6. [مثال‌های کاربردی](#6-مثال‌های-کاربردی)
7. [تفاوت Extension Method با Instance Method](#7-تفاوت-extension-method-با-instance-method)
8. [بهترین روش‌های استفاده (Best Practices)](#8-بهترین-روش‌های-استفاده-best-practices)
9. [جمع‌بندی](#9-جمع‌بندی)
10. [منابع معتبر](#10-منابع-معتبر)

---

## 1. مقدمه

در زبان C#، **متدهای اکستنشن** (Extension Methods) امکانی قدرتمند برای **افزودن رفتار جدید به انواع موجود بدون تغییر کد اصلی آن‌ها** فراهم می‌کنند. این ویژگی به‌ویژه در توسعه‌ی برنامه‌های مبتنی بر OOP (شیءگرایی) بسیار مفید است، چرا که بدون نیاز به ارث‌بری یا تغییر کلاس اصلی، می‌توانید عملکردهای جدیدی به آن اضافه کنید.

قلب این مکانیسم، **پارامتر `this`** است که در ابتدای لیست پارامترهای یک متد استاتیک قرار می‌گیرد و نوعی را که می‌خواهیم گسترش دهیم مشخص می‌کند.

---

## 2. متدهای اکستنشن چیستند؟

متدهای اکستنشن، **متدهای استاتیک** هستند که به‌صورت **غیرمستقیم** به یک نوع (class, struct, interface و حتی انواع داخلی مانند `string` یا `int`) متصل می‌شوند و می‌توان آن‌ها را **مانند یک متد معمولی نمونه (instance method)** فراخوانی کرد.

این متدها:
- در یک **کلاس استاتیک** تعریف می‌شوند.
- خودشان **استاتیک** هستند.
- اولین پارامتر آن‌ها با کلمه‌کلیدی `this` شروع می‌شود.

---

## 3. پارامتر `this` چیست و چه نقشی دارد؟

پارامتر `this` در متدهای اکستنشن، **اولین پارامتر متد** است و نوعی را که می‌خواهیم گسترش دهیم مشخص می‌کند.

### نقش‌های کلیدی:
- **مشخص‌کننده نوع هدف**: نوعی که متد به آن "متصل" می‌شود.
- **ارجاع به نمونه فراخواننده**: در هنگام فراخوانی، مقدار سمت چپ نقطه (`.`) به این پارامتر ارسال می‌شود.
- **فعال‌کننده سینتکس راحت**: بدون نیاز به فراخوانی مستقیم متد استاتیک.

> 🔍 **نکته مهم**: کلمه‌کلیدی `this` فقط در اولین پارامتر یک متد استاتیک در یک کلاس استاتیک مجاز است. در غیر این صورت، کامپایلر خطایی ایجاد می‌کند.

---

## 4. نحوه تعریف یک Extension Method

ساختار کلی:

```csharp
public static class StringExtensions
{
    public static string ToTitleCase(this string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;

        return char.ToUpper(input[0]) + input.Substring(1).ToLower();
    }
}
```

### توضیحات:
- `StringExtensions`: یک کلاس استاتیک (الزامی).
- `ToTitleCase`: یک متد استاتیک (الزامی).
- `this string input`: پارامتر `this` که نوع هدف (`string`) را مشخص می‌کند.

### نحوه استفاده:
```csharp
string name = "john";
Console.WriteLine(name.ToTitleCase()); // خروجی: John
```

> 💡 این فراخوانی، در واقع معادل است با:  
> `StringExtensions.ToTitleCase(name);`  
> اما سینتکس نقطه‌ای خوانایی بیشتری دارد.

---

## 5. قوانین و محدودیت‌های استفاده از `this`

1. **کلاس حاوی متد باید `static` باشد**.
2. **متد اکستنشن باید `static` باشد**.
3. **پارامتر `this` فقط در اولین پارامتر مجاز است**.
4. **نمی‌توان متدهای اکستنشن را برای جایگزینی متدهای موجود استفاده کرد** — اگر متدی با همان امضا در کلاس اصلی وجود داشته باشد، متد اصلی اولویت دارد.
5. **نمی‌توان به فیلدها یا خصوصیات خصوصی (private) نوع هدف دسترسی پیدا کرد** — فقط اعضای عمومی (public) قابل دسترسی هستند.
6. **برای استفاده از متد اکستنشن، فضای نام (namespace) آن باید با `using` وارد شده باشد**.

---

## 6. مثال‌های کاربردی

### مثال ۱: گسترش نوع `int`
```csharp
public static class IntegerExtensions
{
    public static bool IsEven(this int number) => number % 2 == 0;
}

// استفاده
int x = 4;
Console.WriteLine(x.IsEven()); // True
```

### مثال ۲: گسترش یک اینترفیس
```csharp
public static class EnumerableExtensions
{
    public static bool IsNullOrEmpty<T>(this IEnumerable<T> source)
    {
        return source == null || !source.Any();
    }
}

// استفاده
List<string> list = null;
Console.WriteLine(list.IsNullOrEmpty()); // True
```

### مثال ۳: کار با رشته‌ها
```csharp
public static class StringExtensions
{
    public static string Reverse(this string s)
    {
        return new string(s.Reverse().ToArray());
    }
}
```

---

## 7. تفاوت Extension Method با Instance Method

| ویژگی | Extension Method | Instance Method |
|--------|------------------|-----------------|
| تعریف | در کلاس جداگانه (static) | درون خود کلاس |
| دسترسی به اعضای private | ❌ خیر | ✅ بله |
| نیاز به تغییر کد اصلی | ❌ خیر | ✅ بله (اگر کلاس ما نباشد) |
| اولویت در فراخوانی | پایین‌تر | بالاتر |
| نیاز به `using` | ✅ بله | ❌ خیر |

> ⚠️ اگر هم متد اکستنشن و هم متد نمونه با امضای یکسان وجود داشته باشند، **متد نمونه همیشه اولویت دارد**.

---

## 8. بهترین روش‌های استفاده (Best Practices)

1. **از متدهای اکستنشن برای افزودن عملکردهای عمومی و مفید استفاده کنید** (مثل LINQ).
2. **فضای نام مناسبی برای گروه‌بندی متدهای اکستنشن تعریف کنید** (مثلاً `MyApp.Extensions`).
3. **از نام‌های واضح و توصیفی برای متدها استفاده کنید**.
4. **از متدهای اکستنشن برای جایگزینی رفتارهای پیچیده یا حساس کلاس اصلی استفاده نکنید**.
5. **مستندسازی کنید!** از `///` برای توضیح پارامترها و رفتار متد استفاده کنید.

---

## 9. جمع‌بندی

- پارامتر `this` در متدهای اکستنشن، **پلی بین نوع هدف و متد جدید** است.
- این ویژگی به شما اجازه می‌دهد بدون تغییر در کد اصلی، **رفتارهای جدیدی به انواع موجود اضافه کنید**.
- متدهای اکستنشن در کتابخانه‌هایی مانند **LINQ** به‌طور گسترده استفاده شده‌اند.
- رعایت قوانین و بهترین روش‌ها، از سردرگمی و خطا در پروژه‌های بزرگ جلوگیری می‌کند.

---

## 10. منابع معتبر

1. **مستندات رسمی Microsoft (MSDN)**  
   [Extension Methods (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)  
   📌 منبع اصلی و معتبر برای درک عمیق مفاهیم.

2. **C# Language Specification (Microsoft)**  
   [C# 12 Specification – Extension Methods](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#extension-methods)

3. **کتاب: C# in Depth – نوشته Jon Skeet**  
   فصل 9: Extension methods and lambda expressions  
   📘 یکی از معتبرترین منابع برای یادگیری پیشرفته C#.

4. **Pluralsight / Microsoft Learn**  
   دوره‌های آموزشی رسمی در مورد متدهای اکستنشن و OOP در C#.

5. **Stack Overflow – Best Practices for Extension Methods**  
   [When to use extension methods](https://stackoverflow.com/questions/4902002/when-to-use-extension-methods)
