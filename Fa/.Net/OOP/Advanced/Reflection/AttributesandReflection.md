

## 📚 فهرست مطالب

1. [مقدمه](#مقدمه)
2. [Attribute چیست؟](#attribute-چیست؟)
   - [نحوه تعریف و استفاده از Attribute](#نحوه-تعریف-و-استفاده-از-attribute)
   - [Attributeهای داخلی (Built-in Attributes)](#attributeهای-داخلی-built-in-attributes)
3. [Reflection چیست؟](#reflection-چیست؟)
   - [کاربردهای Reflection](#کاربردهای-reflection)
4. [ترکیب Attributes و Reflection](#ترکیب-attributes-و-reflection)
5. [مثال کاربردی: اعتبارسنجی داده با Attribute و Reflection](#مثال-کاربردی-اعتبارسنجی-داده-با-attribute-و-reflection)
6. [نکات عملکردی و هشدارها](#نکات-عملکردی-و-هشدارها)
7. [جمع‌بندی](#جمع‌بندی)
8. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در زبان C#، **Attributes** و **Reflection** دو مفهوم قدرتمند هستند که به شما امکان می‌دهند **اطلاعات متادیتا (metadata)** را به کد خود اضافه کنید و در زمان اجرا (Runtime) به آن‌ها دسترسی پیدا کنید. این قابلیت‌ها به‌ویژه در فریم‌ورک‌هایی مانند ASP.NET Core، Entity Framework و سیستم‌های سریال‌سازی (Serialization) به‌کار می‌روند.

در این مقاله، ابتدا با مفهوم **Attribute** آشنا می‌شویم، سپس **Reflection** را بررسی می‌کنیم و در نهایت نحوه ترکیب این دو را با یک مثال کاربردی یاد می‌گیریم.

---

## Attribute چیست؟

**Attribute** یک راه برای افزودن **متادیتا** (داده‌های توصیفی) به کدهای C# است. این متادیتا در زمان کامپایل در فایل اسمبلی ذخیره می‌شوند و می‌توانند در زمان اجرا با استفاده از **Reflection** خوانده شوند.

### نحوه تعریف و استفاده از Attribute

#### 1. استفاده از Attributeهای از پیش تعریف‌شده

```csharp
[Obsolete("این متد منسوخ شده است. از MyNewMethod استفاده کنید.")]
public void MyOldMethod()
{
    // ...
}
```

در اینجا از **Attribute داخلی** `Obsolete` استفاده کرده‌ایم که به کامپایلر هشدار می‌دهد این متد قدیمی است.

#### 2. تعریف Attribute سفارشی

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method)]
public class MyCustomAttribute : Attribute
{
    public string Description { get; }
    public MyCustomAttribute(string description)
    {
        Description = description;
    }
}
```

استفاده:

```csharp
[MyCustom("این یک کلاس نمونه است.")]
public class SampleClass
{
    [MyCustom("این یک متد نمونه است.")]
    public void SampleMethod() { }
}
```

> ⚠️ نکته: نام کلاس Attribute باید با پسوند `Attribute` تمام شود (مثل `MyCustomAttribute`)، اما هنگام استفاده می‌توانید `Attribute` را حذف کنید (`[MyCustom(...)]`).

---

## Attributeهای داخلی (Built-in Attributes)

C# چندین Attribute داخلی دارد که به‌طور گسترده استفاده می‌شوند:

| Attribute | توضیح |
|----------|--------|
| `[Obsolete]` | نشان می‌دهد یک عضو قدیمی شده است. |
| `[Serializable]` | نشان می‌دهد یک کلاس قابل سریال‌سازی است. |
| `[DllImport]` | برای فراخوانی توابع غیرمدیریتی (Unmanaged) از DLLها. |
| `[Conditional]` | یک متد را فقط در صورت فعال بودن یک Symbol کامپایل اجرا می‌کند. |
| `[CallerMemberName]`, `[CallerFilePath]`, `[CallerLineNumber]` | اطلاعات تماس‌گیرنده را در لاگ‌ها و دیباگ فراهم می‌کنند. |

---

## Reflection چیست؟

**Reflection** امکان بررسی **ساختار اسمبلی‌ها، انواع (Types)، متدها، خصوصیات (Properties)** و سایر اعضای یک برنامه در **زمان اجرا** را فراهم می‌کند. با استفاده از Reflection می‌توان:

- لیست تمام کلاس‌های یک اسمبلی را گرفت.
- متدهای یک کلاس را پیدا و فراخوانی کرد.
- مقادیر خصوصیات را خواند یا نوشت.
- **Attributeهای** تعریف‌شده روی کلاس‌ها یا متدها را استخراج کرد.

### مثال ساده از Reflection

```csharp
Type type = typeof(SampleClass);
Console.WriteLine($"نام کلاس: {type.Name}");

foreach (var method in type.GetMethods())
{
    Console.WriteLine($"متد: {method.Name}");
}
```

---

## کاربردهای Reflection

- **فریم‌ورک‌های ORM** مانند Entity Framework برای نگاشت کلاس‌ها به جداول دیتابیس.
- **سیستم‌های سریال‌سازی** مانند `System.Text.Json` یا `Newtonsoft.Json`.
- **Dependency Injection** برای پیدا کردن سرویس‌ها در زمان اجرا.
- **Unit Testing Frameworks** مانند xUnit یا NUnit برای پیدا کردن متد‌های تست.

---

## ترکیب Attributes و Reflection

هدف اصلی استفاده از Attributeها، **خواندن آن‌ها در زمان اجرا با Reflection** است.

### مثال: خواندن Attributeهای یک کلاس

```csharp
var type = typeof(SampleClass);
var attributes = type.GetCustomAttributes(typeof(MyCustomAttribute), false);

foreach (MyCustomAttribute attr in attributes)
{
    Console.WriteLine(attr.Description);
}
```

همچنین می‌توان Attributeهای یک متد را خواند:

```csharp
var method = type.GetMethod("SampleMethod");
var methodAttrs = method.GetCustomAttributes(typeof(MyCustomAttribute), false);
```

---

## مثال کاربردی: اعتبارسنجی داده با Attribute و Reflection

فرض کنید می‌خواهیم یک سیستم ساده برای اعتبارسنجی خصوصیات یک کلاس داشته باشیم.

### 1. تعریف Attribute اعتبارسنجی

```csharp
[AttributeUsage(AttributeTargets.Property)]
public class RequiredAttribute : Attribute { }

[AttributeUsage(AttributeTargets.Property)]
public class StringLengthAttribute : Attribute
{
    public int MaxLength { get; }
    public StringLengthAttribute(int maxLength)
    {
        MaxLength = maxLength;
    }
}
```

### 2. استفاده در مدل

```csharp
public class User
{
    [Required]
    public string Name { get; set; }

    [StringLength(100)]
    public string Email { get; set; }
}
```

### 3. متد اعتبارسنجی با Reflection

```csharp
public static class Validator
{
    public static List<string> Validate(object obj)
    {
        var errors = new List<string>();
        var type = obj.GetType();

        foreach (var prop in type.GetProperties())
        {
            var value = prop.GetValue(obj);

            // بررسی Required
            if (prop.GetCustomAttribute<RequiredAttribute>() != null && value == null)
            {
                errors.Add($"{prop.Name} اجباری است.");
                continue;
            }

            // بررسی StringLength
            var lengthAttr = prop.GetCustomAttribute<StringLengthAttribute>();
            if (lengthAttr != null && value is string str && str.Length > lengthAttr.MaxLength)
            {
                errors.Add($"{prop.Name} نباید بیشتر از {lengthAttr.MaxLength} کاراکتر باشد.");
            }
        }

        return errors;
    }
}
```

### 4. استفاده

```csharp
var user = new User { Name = null, Email = new string('x', 150) };
var errors = Validator.Validate(user);

foreach (var error in errors)
{
    Console.WriteLine(error);
}
```

خروجی:
```
Name اجباری است.
Email نباید بیشتر از 100 کاراکتر باشد.
```

---

## نکات عملکردی و هشدارها

- **Reflection کند است**: استفاده مکرر از آن در حلقه‌ها یا بخش‌های حساس به عملکرد (مثل رندر صفحه) توصیه نمی‌شود.
- **Cache کردن نتایج**: می‌توانید نتایج `Type.GetProperties()` یا `GetCustomAttributes()` را در یک `Dictionary` ذخیره کنید.
- **امنیت**: Reflection می‌تواند به اعضای `private` دسترسی پیدا کند، پس در محیط‌های امنیتی حساس با احتیاط استفاده شود.
- **قابلیت نگهداری**: کد‌های متکی به Reflection گاهی سخت‌تر دیباگ و تست می‌شوند.

---

## جمع‌بندی

- **Attributeها** برای افزودن متادیتا به کد استفاده می‌شوند.
- **Reflection** امکان بررسی و دستکاری کد در زمان اجرا را فراهم می‌کند.
- ترکیب این دو، امکان ساخت **فریم‌ورک‌های انعطاف‌پذیر و قابل تنظیم** را می‌دهد.
- همیشه **به عملکرد و خوانایی کد** توجه کنید.

---

## منابع معتبر

1. **Microsoft Learn – Attributes**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/)

2. **Microsoft Learn – Reflection**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection)

3. **C# in Depth – Jon Skeet** (فصل 15: Reflection و Attributes)  
   🔗 [https://livebook.manning.com/book/c-sharp-in-depth-fourth-edition](https://livebook.manning.com/book/c-sharp-in-depth-fourth-edition)

4. **.NET Documentation – Built-in Attributes**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes)

5. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   (فصل 18: Attributes and Reflection)
