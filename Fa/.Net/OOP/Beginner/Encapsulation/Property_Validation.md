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