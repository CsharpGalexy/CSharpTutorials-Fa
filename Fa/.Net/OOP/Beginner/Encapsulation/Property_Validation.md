# اعتبارسنجی خصوصیات (Property Validation) در C#

## فهرست مطالب
- [چرا اعتبارسنجی خصوصیات مهم است؟](#چرا-اعتبارسنجی-خصوصیات-مهم-است)  
- [روش اول: اعتبارسنجی در داخل Setter خصوصیت](#روش-اول-اعتبارسنجی-در-داخل-setter-خصوصیت)  
- [روش دوم: استفاده از Data Annotations](#روش-دوم-استفاده-از-data-annotations)  
- [اجرای دستی اعتبارسنجی با کلاس Validator](#اجرای-دستی-اعتبارسنجی-با-کلاس-validator)  
- [ملاحظات و محدودیت‌های Data Annotations](#ملاحظات-و-محدودیتهای-data-annotations)  
- [الگوهای مدرن در C# برای Validation](#الگوهای-مدرن-در-c-برای-validation)  
- [خلاصه و نتیجه‌گیری](#خلاصه-و-نتیجهگیری)  
- [منابع معتبر](#منابع-معتبر)  

---
## چرا اعتبارسنجی خصوصیات مهم است؟
در توسعه نرم‌افزار، یکی از اصول مهم **حفظ صحت و یکپارچگی داده‌ها** است.  
اگر داده‌های نامعتبر وارد سیستم شوند، می‌توانند منجر به خطاهای منطقی، مشکلات امنیتی یا حتی از کار افتادن برنامه شوند.  

مثال:  
- سن یک فرد نمی‌تواند منفی یا بالاتر از ۱۲۰ باشد.  
- قیمت یک محصول نمی‌تواند صفر یا منفی باشد.  

اعتبارسنجی (Validation) به ما کمک می‌کند تا از همان ابتدا جلوی این خطاها را بگیریم.

---

## روش اول: اعتبارسنجی در داخل Setter خصوصیت
یکی از ساده‌ترین روش‌ها این است که منطق بررسی را مستقیماً در Setter قرار دهیم.

### نمونه کد:
```csharp
public class Person
{
    private int age;

    public int Age
    {
        get => age;
        set
        {
            if (value < 0 || value > 120)
                throw new ArgumentOutOfRangeException(
                    paramName: nameof(value),
                    message: "Age must be between 0 and 120."
                );
            age = value;
        }
    }
}
```

### توضیح:
- فیلد `age` خصوصی است و فقط از طریق خصوصیت `Age` مقداردهی می‌شود.  
- در `setter` بررسی می‌کنیم مقدار داخل بازه مجاز باشد.  
- در صورت نامعتبر بودن، استثنا پرتاب می‌کنیم.  

این روش ساده است و در پروژه‌های کوچک یا کلاس‌های مستقل بسیار کاربردی می‌باشد.

---
## روش دوم: استفاده از Data Annotations
در پروژه‌های ASP.NET Core یا EF Core می‌توان از **Attributes** برای اعتبارسنجی استفاده کرد.

### نمونه کد:
```csharp
using System.ComponentModel.DataAnnotations;

public class Product
{
    [Required(ErrorMessage = "Name is required.")]
    [StringLength(50, ErrorMessage = "Name cannot be longer than 50 characters.")]
    public string Name { get; set; }

    [Range(1, 1000, ErrorMessage = "Price must be between 1 and 1000.")]
    public decimal Price { get; set; }
}
```

### توضیح:
- `[Required]` می‌گوید فیلد نمی‌تواند خالی باشد.  
- `[StringLength]` طول رشته را محدود می‌کند.  
- `[Range]` محدوده مجاز برای قیمت تعیین می‌کند.  

این ویژگی‌ها در ASP.NET Core به‌صورت خودکار هنگام ارسال فرم‌ها اجرا می‌شوند و پیام‌های خطا به کاربر نمایش داده می‌شوند.

---

## اجرای دستی اعتبارسنجی با کلاس Validator
گاهی لازم است اعتبارسنجی را به صورت دستی و خارج از ASP.NET اجرا کنیم.  

### نمونه کد:
```csharp
using System;
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;

public class Program
{
    public static void Main()
    {
        var product = new Product { Name = "", Price = 1500 };

        var context = new ValidationContext(product);
        var results = new List<ValidationResult>();

        bool isValid = Validator.TryValidateObject(product, context, results, true);

        if (!isValid)
        {
            foreach (var result in results)
            {
                Console.WriteLine(result.ErrorMessage);
            }
        }
    }
}
```

### توضیح:
- `ValidationContext` زمینه اعتبارسنجی را مشخص می‌کند.  
- `Validator.TryValidateObject` خصوصیات شیء را بررسی می‌کند.  
- اگر خطایی وجود داشته باشد، پیام‌ها چاپ می‌شوند.  

این روش برای **کنسول اپلیکیشن‌ها، سرویس‌ها یا تست‌های واحد** مفید است.

---

## ملاحظات و محدودیت‌های Data Annotations
- بیشتر برای پروژه‌های **ASP.NET Core و EF Core** مناسب هستند.  
- برای قوانین پیچیده یا شرطی کافی نیستند.  
- برای سناریوهای پیچیده‌تر می‌توان از کتابخانه‌هایی مانند **FluentValidation** استفاده کرد.  

---

## الگوهای مدرن در C# برای Validation
از C# 9 به بعد می‌توان از **init-only properties** یا **Recordها** استفاده کرد.  

### نمونه کد:
```csharp
public class Person
{
    public string Name { get; init; }
    public int Age { get; init; }

    public Person(string name, int age)
    {
        if (string.IsNullOrWhiteSpace(name))
            throw new ArgumentException("Name is required.", nameof(name));
        if (age < 0 || age > 120)
            throw new ArgumentOutOfRangeException(nameof(age), "Invalid age.");

        Name = name;
        Age = age;
    }
}
```

### توضیح:
- مقدار خصوصیات فقط در **سازنده** تعیین می‌شود.  
- پس از ساخته شدن شیء دیگر قابل تغییر نیستند.  
- مناسب برای معماری‌های مدرن مثل **DDD** یا **CQRS** هستند.  

---
