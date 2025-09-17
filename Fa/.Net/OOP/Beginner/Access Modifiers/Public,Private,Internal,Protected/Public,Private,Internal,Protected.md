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
