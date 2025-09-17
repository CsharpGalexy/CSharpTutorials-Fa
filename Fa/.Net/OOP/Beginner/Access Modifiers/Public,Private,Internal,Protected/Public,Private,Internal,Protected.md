# 📝 دسترسی‌ها در C#

## فهرست مطالب
- مقدمه‌ای بر دسترسی‌ها در C#
- public
- private
- protected
- internal
- protected internal
- private protected
- نکات مهم و محدودیت‌های دسترسی
- خلاصه و نتیجه‌گیری
- منابع و رفرنس‌ها

---

## مقدمه‌ای بر دسترسی‌ها در C#

در زبان C#، کنترل سطح دسترسی به اعضای کلاس‌ها، ساختارها و حتی خود کلاس‌ها موضوعی کلیدی است. این کنترل‌ها که با **Access Modifiers** مشخص می‌شوند، تعیین می‌کنند چه بخش‌هایی از کد می‌توانند به داده‌ها و متدها دسترسی داشته باشند.

هدف اصلی استفاده از دسترسی‌ها، پیاده‌سازی **کپسوله‌سازی (Encapsulation)** است؛ یعنی محافظت از داده‌ها و منطق داخلی کلاس در برابر دسترسی‌های ناخواسته. این موضوع باعث افزایش امنیت، خوانایی و نگهداشت‌پذیری کد می‌شود.

در C# شش سطح اصلی دسترسی وجود دارد:
- `public`
- `private`
- `protected`
- `internal`
- `protected internal`
- `private protected`

در ادامه، هرکدام از این سطوح را همراه با مثال و توضیح بررسی می‌کنیم.

---

## 🔹 public

کلیدواژه‌ی `public` بازترین سطح دسترسی است. هر عضو `public` از هر جایی، حتی پروژه‌ها یا اسمبلی‌های دیگر، قابل دسترسی خواهد بود.

### مثال
```csharp
public class Car
{
    public string Model = "Mustang";
}

class Program
{
    static void Main()
    {
        Car myCar = new Car();
        Console.WriteLine(myCar.Model); // خروجی: Mustang
    }
}
```

**توضیح:**  
در این مثال، فیلد `Model` عمومی است. بنابراین حتی در کلاس دیگری مثل `Program` می‌توانیم به آن دسترسی داشته باشیم.  

⚠️ نکته: بهتر است فقط زمانی از `public` استفاده کنید که نیاز واقعی به دسترسی عمومی وجود داشته باشد.

---

## 🔹 private

`private` محدودترین سطح دسترسی است. اعضای `private` تنها در داخل همان کلاس تعریف‌شده قابل استفاده هستند و حتی کلاس‌های مشتق‌شده نیز به آن‌ها دسترسی ندارند.

### مثال
```csharp
public class Car
{
    private string Model = "Mustang";

    public void ShowModel()
    {
        Console.WriteLine(Model); // مجاز
    }
}

class Program
{
    static void Main()
    {
        Car myCar = new Car();
        // Console.WriteLine(myCar.Model); // ❌ خطا
        myCar.ShowModel(); // ✅ خروجی: Mustang
    }
}
```

📌 نکته: اگر برای یک عضو دسترسی مشخص نکنید، پیش‌فرض `private` خواهد بود.

---

## 🔹 protected

با `protected`، عضو در کلاس خودش و همچنین در کلاس‌های مشتق‌شده (چه در همان اسمبلی و چه در اسمبلی دیگر) قابل دسترسی است.

### مثال
```csharp
public class Car
{
    protected string Model = "Mustang";
}

public class SportsCar : Car
{
    public void ShowModel()
    {
        Console.WriteLine(Model); // مجاز
    }
}

class Program
{
    static void Main()
    {
        SportsCar myCar = new SportsCar();
        myCar.ShowModel(); // خروجی: Mustang
    }
}
```

---

## 🔹 internal

سطح دسترسی `internal` دسترسی را به کدهای داخل همان اسمبلی محدود می‌کند. اسمبلی می‌تواند یک فایل `.dll` یا `.exe` باشد.

### مثال
```csharp
internal class Car
{
    internal string Model = "Mustang";
}

class Program
{
    static void Main()
    {
        Car myCar = new Car();
        Console.WriteLine(myCar.Model); // مجاز چون در همان اسمبلی هستیم
    }
}
```

📌 نکته: برای کلاس‌های سطح بالا اگر دسترسی مشخص نکنید، پیش‌فرض `internal` است.

---

## 🔹 protected internal

این ترکیب معادل **(protected OR internal)** است:

- داخل همان اسمبلی: همه می‌توانند دسترسی داشته باشند.  
- خارج از اسمبلی: فقط کلاس‌های مشتق‌شده می‌توانند دسترسی داشته باشند.

### مثال
```csharp
public class Car
{
    protected internal string Model = "Mustang";
}

class AnotherClass
{
    public void AccessModel()
    {
        Car car = new Car();
        Console.WriteLine(car.Model); // مجاز چون در همان اسمبلی هستیم
    }
}

public class SportsCar : Car
{
    public void ShowModel()
    {
        Console.WriteLine(Model); // مجاز حتی اگر در اسمبلی دیگر باشد
    }
}
```

---

## 🔹 private protected

این سطح دسترسی از C# 7.2 معرفی شد و معادل **(private AND protected)** است:

فقط در همان کلاس یا کلاس‌های مشتق‌شده و فقط در همان اسمبلی قابل دسترسی است.

### مثال
```csharp
public class Car
{
    private protected string Model = "Mustang";
}

public class SportsCar : Car
{
    public void ShowModel()
    {
        Console.WriteLine(Model); // مجاز چون مشتق‌شده و در همان اسمبلی است
    }
}

class Program
{
    static void Main()
    {
        SportsCar myCar = new SportsCar();
        myCar.ShowModel(); // خروجی: Mustang
    }
}
```

---

## ⚡ نکات مهم و محدودیت‌های دسترسی

- هر عضو فقط یک سطح دسترسی دارد (به‌جز ترکیب‌های `protected internal` و `private protected`).  
- فضای نام (`namespace`) سطح دسترسی ندارد و همیشه عمومی است.  
- اعضای `enum` همیشه `public` هستند.  
- سطح دسترسی یک عضو نمی‌تواند از سطح دسترسی کلاس والد بازتر باشد.  
  - مثال: اگر کلاس `internal` باشد، نمی‌توان در آن یک متد `public` قرار داد که از بیرون اسمبلی دیده شود.  
- `struct`ها نمی‌توانند از `protected`، `protected internal` یا `private protected` استفاده کنند چون ارث‌بری ندارند.  
- اپراتورهای تعریف‌شده توسط کاربر باید `public` و `static` باشند.  
- برای کلاس‌های تو در تو (nested classes)، سطح دسترسی می‌تواند متفاوت از کلاس بیرونی باشد، اما به سطح دسترسی آن وابسته است.  

---

## 📊 خلاصه و نتیجه‌گیری

انتخاب سطح دسترسی مناسب یکی از پایه‌های طراحی اصولی در C# است. برای تصمیم‌گیری بهتر، جدول زیر را در نظر بگیرید:

| سطح دسترسی | درون کلاس | کلاس‌های مشتق‌شده (همان اسمبلی) | کلاس‌های مشتق‌شده (اسمبلی دیگر) | کلاس‌های دیگر (همان اسمبلی) | کلاس‌های دیگر (اسمبلی دیگر) | پیش‌فرض |
|------------|-----------|--------------------------------|--------------------------------|-----------------------------|-----------------------------|----------|
| public     | ✅        | ✅                             | ✅                             | ✅                          | ✅                          | —        |
| private    | ✅        | ❌                             | ❌                             | ❌                          | ❌                          | اعضای کلاس |
| protected  | ✅        | ✅                             | ✅                             | ❌                          | ❌                          | —        |
| internal   | ✅        | ✅                             | ❌                             | ✅                          | ❌                          | کلاس سطح بالا |
| protected internal | ✅ | ✅                           | ✅                             | ✅                          | ❌                          | —        |
| private protected | ✅  | ✅                             | ❌                             | ❌                          | ❌                          | —        |

📌 توصیه: همیشه اصل **کمترین سطح دسترسی لازم (Least Privilege)** را رعایت کنید. یعنی از `private` شروع کنید و فقط در صورت نیاز سطح دسترسی را بازتر کنید.

---

## 📚 منابع و رفرنس‌ها

- [Accessibility Levels - C# Reference (Microsoft Learn)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/accessibility-levels)  
- [Access Modifiers (C# Programming Guide) - Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)  
- [C# Language Specification - Access Modifiers (Microsoft Learn)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#accessibility)  
- Stack Overflow: *Difference between access modifiers*  
- C# Access Modifiers - Tutlane  
- Access Modifiers in C# - GeeksforGeeks  
