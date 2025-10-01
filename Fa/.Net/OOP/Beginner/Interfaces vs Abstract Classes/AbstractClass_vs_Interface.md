# Abstract Class vs Interface در C# — مقایسه کامل

**نسخه:** نهایی برای README.md  
**هدف:** این فایل به‌عنوان مرجع و مقاله‌ای آموزشی برای توسعه‌دهندگان تازه‌کار و نیمه‌تجربی درباره تفاوت‌ها، کاربردها و نکات طراحی بین **کلاس‌های انتزاعی (Abstract Class)** و **واسط‌ها (Interface)** در C# تهیه شده است. متن به زبان فارسی و راست‌به‌چپ نوشته شده است.

---

## فهرست مطالب
- [تعریف کلی: چه زمانی از هر کدام استفاده کنیم؟](#تعریف-کلی-چه-زمانی-از-هر-کدام-استفاده-کنیم)
- [تفاوت‌های ساختاری و فنی](#تفاوت‌های-ساختاری-و-فنی)
- [ویژگی‌های جدید در نسخه‌های اخیر C#](#ویژگی‌های-جدید-در-نسخه‌های-اخیر-c)
- [نمونه کد: کلاس انتزاعی (Abstract Class)](#نمونه-کد-کلاس-انتزاعی-abstract-class)
- [نمونه کد: واسط (Interface)](#نمونه-کد-واسط-interface)
- [نمونه کد: ترکیب abstract class و interface با تمرکز بر Validation](#نمونه-کد-ترکیب-abstract-class-و-interface-با-تمرکز-بر-validation)
- [ترکیب abstract class و interface در طراحی واقعی](#ترکیب-abstract-class-و-interface-در-طراحی-واقعی)
- [مثال‌های پیشرفته: تعارض متدهای پیش‌فرض و static abstract](#مثال‌های-پیشرفته-تعارض-متدهای-پیشفرض-و-static-abstract)
- [چه زمانی از Abstract Class استفاده کنیم؟](#چه-زمانی-از-abstract-class-استفاده-کنیم)
- [چه زمانی از Interface استفاده کنیم؟](#چه-زمانی-از-interface-استفاده-کنیم)
- [چک‌لیست تصمیم‌گیری سریع](#چک‌لیست-تصمیم‌گیری-سریع)
- [محدودیت‌ها و چالش‌های هر دو رویکرد (با مثال)](#محدودیت‌ها-و-چالش‌های-هر-دو-رویکرد-با-مثال)
- [خلاصه و نتیجه‌گیری](#خلاصه-و-نتیجه‌گیری)
- [منابع معتبر](#منابع-معتبر)

---

## تعریف کلی: چه زمانی از هر کدام استفاده کنیم؟
در زبان **C#**، کلاس‌های انتزاعی و واسط‌ها هر دو برای تعریف قراردادها و نمایش انتزاع (abstraction) استفاده می‌شوند، اما هدف و کاربرد هر یک متفاوت است:

- **کلاس انتزاعی (Abstract Class)**  
  زمانی که مجموعه‌ای از کلاس‌ها *خانواده‌ای مرتبط* را تشکیل می‌دهند و نیاز به به اشتراک‌گذاری حالت (state) یا منطق پایه (shared behavior) دارید، از کلاس انتزاعی استفاده کنید. مثال: یک `Account` پایه برای `SavingsAccount` و `CheckingAccount`.

- **واسط (Interface)**  
  وقتی می‌خواهید یک **قابلیت** (capability) را تعریف کنید که ممکن است در انواع نامرتبط نیز پیاده‌سازی شود (مثلاً `IDisposable`, `IComparable`, یا `ILogger`)، از واسط استفاده کنید. واسط‌ها از دید طراحی برای نشان دادن relation از نوع *can-do* مناسب‌اند.

**قاعده ساده و عملی:**  
- اگر رابطه‌ی بین انواع *is-a* است → کلاس انتزاعی مناسب‌تر است.  
- اگر رابطه‌ی بین انواع *can-do* است → واسط مناسب‌تر است.

---

## تفاوت‌های ساختاری و فنی
جدول خلاصه (مناسب برای مرور سریع):

| ویژگی | Abstract Class | Interface |
|-------|----------------|-----------|
| امکان ساخت نمونه (instantiation) | ❌ | ❌ |
| وراثت چندگانه | ❌ (فقط یک کلاس پایه) | ✅ (چندین واسط قابل پیاده‌سازی) |
| نگهداری حالت (instance fields) | ✅ مجاز | ❌ نمی‌تواند فیلد نمونه داشته باشد |
| سازنده (Constructor) | ✅ مجاز (معمولاً `protected`) | ❌ فقط `static constructor` از C# 11 مجاز است |
| متدهای پیاده‌سازی‌شده | ✅ مجاز | ✅ از C# 8 با `default` methods |
| اعضای `static` | ✅ مجاز | ✅ از نسخه‌های جدید (static abstract در C# 11) |
| سطوح دسترسی در اعضا | ✅ کامل (public/private/protected...) | ✅ تا حدی (private/protected در default methods از C# 8/9) |

> توضیح: ویژگی‌های مرتبط با واسط‌ها (interfaces) با معرفی C# 8, 9, 10 و 11 گسترده‌تر شده‌اند (مثل default methods و static abstract)، اما اختلاف اصلی همچنان **عدم امکان نگهداری state** در واسط‌هاست.

---

## ویژگی‌های جدید در نسخه‌های اخیر C#
- **Default Interface Methods (از C# 8):**  
  امکان تعریف متدهایی در واسط با پیاده‌سازی پیش‌فرض که پیاده‌کننده‌ها مجبور به implement آن‌ها نیستند. این ویژگی برای افزودن متد به یک API بدون شکستن پیاده‌سازی‌های فعلی مفید است.

- **Private/Protected Interface Members (C# 8+):**  
  از نسخه‌های جدید می‌توان متدها/منابع کمکی خصوصی در داخل واسط‌ها تعریف کرد که به متدهای پیش‌فرض کمک می‌کنند.

- **Static Abstract Members (C# 11):**  
  واسط‌ها می‌توانند اعضای `static abstract` تعریف کنند و کلاس‌های پیاده‌کننده را موظف نمایند آن‌ها را پیاده‌سازی کنند. این ویژگی به تولید کد جنریک بر پایه عملیات static کمک می‌کند (مثلاً `IParsable<T>`).

---

## نمونه کد: کلاس انتزاعی (Abstract Class)
**مسئله:** می‌خواهیم سیستمی برای هندسه فراهم کنیم که همه اشکال (shapes) *مساحت* داشته باشند ولی نحوه محاسبه مساحت برای هر شکل متفاوت است. همچنین بخشی از رفتار نمایش مساحت یکسان است.

**کد:**
```csharp
public abstract class Shape
{
    protected string Color { get; set; }

    protected Shape(string color)
    {
        Color = color;
    }

    // متد انتزاعی: هر شکل باید آن را پیاده‌سازی کند
    public abstract double CalculateArea();

    // متد دارای پیاده‌سازی که برای همه اشکال مشترک است
    public void DisplayArea()
    {
        Console.WriteLine($"مساحت شکل با رنگ {Color}: {CalculateArea():F2}");
    }
}

public class Circle : Shape
{
    public double Radius { get; set; }

    public Circle(double radius, string color) : base(color)
    {
        Radius = radius;
    }

    public override double CalculateArea() => Math.PI * Radius * Radius;
}
```

**تحلیل:**  
- `Shape` نمی‌تواند نمونه‌سازی شود و حالت مشترک (`Color`) و رفتار مشترک (`DisplayArea`) را فراهم می‌کند.  
- `Circle` متد انتزاعی `CalculateArea` را پیاده‌سازی می‌کند.  
- این الگو از تکرار کد جلوگیری می‌کند و منطق مشترک را در یک محل متمرکز می‌سازد.

---

## نمونه کد: واسط (Interface)
**مسئله:** بخواهیم قابلیت حرکت (`Move`) را برای انواع نامرتبط مثل `Car` و `Bird` تعریف کنیم؛ نیازی به اشتراک state یا ساختار کلاس‌ها نداریم.

**کد:**
```csharp
public interface IMovable
{
    void Move(int distance);

    // متد پیش‌فرض (C# 8+) — پیاده‌سازی پیش‌فرض برای تمامی پیاده‌کننده‌ها
    void Stop() => Console.WriteLine("حرکت متوقف شد.");
}

public class Car : IMovable
{
    public void Move(int distance) => Console.WriteLine($"ماشین {distance} متر حرکت کرد.");
}

public class Bird : IMovable
{
    public void Move(int distance) => Console.WriteLine($"پرنده {distance} متر پرواز کرد.");
}
```

**تحلیل:**  
- `Car` و `Bird` هیچ رابطه ارثی با هم ندارند اما هر دوی آن‌ها `IMovable` را پیاده می‌کنند.  
- می‌توان یک لیست از `IMovable` داشت و بدون دانستن نوع دقیق، `Move` را فراخوانی کرد — این همان پلی‌مورفیسم بر اساس واسط است.

---

## نمونه کد: ترکیب abstract class و interface با تمرکز بر Validation
**مسئله:** سیستمی داریم که موجودیت‌های داده‌ای (entities) حالت مشترک دارند و نیاز است که اعتبارسنجی (validation) را به صورت اجباری پیاده‌سازی کنند.

**کد:**
```csharp
public abstract class DataEntity
{
    public string Name { get; set; }

    protected DataEntity(string name)
    {
        Name = name;
    }

    // روش ذخیره‌سازی (سرنام: Save) را هر موجودیت خودش پیاده‌سازی می‌کند
    public abstract void Save();
}

public interface IValidatable
{
    bool Validate();

    // متد پیش‌فرض برای لاگ کردن خطا
    void LogValidationError(string error) => Console.WriteLine($"خطا: {error}");
}

public class User : DataEntity, IValidatable
{
    public string Email { get; set; }

    public User(string name, string email) : base(name)
    {
        Email = email;
    }

    public override void Save() => Console.WriteLine($"کاربر {Name} ذخیره شد.");

    public bool Validate()
    {
        if (string.IsNullOrEmpty(Email))
        {
            LogValidationError("ایمیل خالی است.");
            return false;
        }
        return true;
    }
}
```

**تحلیل:**  
- `DataEntity` منطق و حالت پایه را فراهم می‌کند.  
- `IValidatable` قرارداد اعتبارسنجی را تحمیل می‌کند (و یک متد کمکی پیش‌فرض برای لاگ دارد).  
- کلاس `User` ترکیبی از هر دو است — این الگو رایج و قدرتمند است زیرا هم حالت پایه را می‌گیرد و هم قابلیت‌های مستقل را اضافه می‌کند.

---

## ترکیب abstract class و interface در طراحی واقعی
**مسئله واقعی:** مدل‌سازی حیوانات؛ همه حیوانات قادر به تولید صدا هستند اما تنها برخی قابلیت پرواز دارند.

**کد:**
```csharp
public abstract class Animal
{
    public string Name { get; set; }

    public Animal(string name) => Name = name;

    public abstract void MakeSound();
}

public interface IFlyable
{
    void Fly();
}

public class Dog : Animal
{
    public Dog(string name) : base(name) { }

    public override void MakeSound() => Console.WriteLine($"{Name} گفت: ووف!");
}

public class Sparrow : Animal, IFlyable
{
    public Sparrow(string name) : base(name) { }

    public override void MakeSound() => Console.WriteLine($"{Name} چیروک کرد!");

    public void Fly() => Console.WriteLine($"{Name} در حال پرواز است!");
}
```

**تحلیل:**  
- `Animal` پوشش خانواده حیوانات را می‌دهد.  
- `IFlyable` قابلیت اختیاری است که فقط برای برخی انواع نیاز است.  
- این طراحی خوانا، گسترش‌پذیر و قابل نگهداری است.

---