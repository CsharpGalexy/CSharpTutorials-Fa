# 📘 فهرست مطالب: کلیدواژه `new` در C# — تحلیل عمیق

- [🔤 کلیدواژه `new` در C# — چند نقش، یک نام](#-کلیدواژه-new-در-c--چند-نقش-یک-نام)
- [✅ 1. `new` به‌عنوان Modifier (اصلاح‌گر)](#-1-new-بهعنوان-modifier-اصلاحگر)
  - [🎯 چرا نیاز داریم به `new`؟](#-چرا-نیاز-داریم-به-new)
  - [✅ مثال کامل](#-مثال-کامل)
  - [🆚 تفاوت `new` با `override`](#-تفاوت-new-با-override)
  - [✅ کاربردهای `new` Modifier](#-کاربردهای-new-modifier)
  - [⚠️ نکات مهم درباره `new` Modifier](#-نکات-مهم-درباره-new-modifier)
- [✅ 2. `new` به‌عنوان Operator (ساخت شیء)](#-2-new-بهعنوان-operator-ساخت-شیء)
- [✅ 3. `new` به‌عنوان Constraint (محدودیت Generic)](#-3-new-بهعنوان-constraint-محدودیت-generic)
- [🧩 خلاصه: سه نقش `new` در C#](#-خلاصه-سه-نقش-new-در-c)
- [🏁 نتیجه‌گیری](#-نتیجهگیری)
  - [✅ قانون طلایی](#-قانون-طلایی)

---

## 🔤 کلیدواژه `new` در C# — چند نقش، یک نام

...

## ✅ 1. `new` به‌عنوان Modifier (اصلاح‌گر)

...

### 🎯 چرا نیاز داریم به `new`؟

...

### ✅ مثال کامل

...

### 🆚 تفاوت `new` با `override`

...

### ✅ کاربردهای `new` Modifier

...

### ⚠️ نکات مهم درباره `new` Modifier

...

## ✅ 2. `new` به‌عنوان Operator (ساخت شیء)

...

## ✅ 3. `new` به‌عنوان Constraint (محدودیت Generic)

...

## 🧩 خلاصه: سه نقش `new` در C#

...

## 🏁 نتیجه‌گیری

...

### ✅ قانون طلایی

...

---

## 🔤 کلیدواژه `new` در C# — چند نقش، یک نام

کلمه `new` در C# سه نقش اصلی داره:

| نقش | توضیح |
|------|--------|
| 1️⃣ **Operator**: ساخت شیء جدید | مثل `new MyClass()` |
| 2️⃣ **Modifier**: پنهان‌کردن عضو کلاس پایه | `public new void Method()` |
| 3️⃣ **Constraint**: محدودیت در Generic | `where T : new()` |

اما سوال تو درباره **`new` به‌عنوان Modifier** بوده، پس اول روی همون تمرکز می‌کنیم، بعد بقیه رو هم توضیح می‌دم.

---

## ✅ 1. `new` به‌عنوان Modifier (اصلاح‌گر)

وقتی از `new` به‌عنوان modifier استفاده می‌کنی، یعنی **می‌خوای یک عضو (مثلاً متد، خاصیت، فیلد) از کلاس پایه رو در کلاس فرزند "پنهان" کنی** — بدون اینکه از `virtual`/`override` استفاده کنی.

این کار بهش می‌گن **Method Hiding**.

---

### 🎯 چرا نیاز داریم به `new`؟

فرض کن کلاس پایه یه متد داره:

```csharp
public class Animal
{
    public void MakeSound()
    {
        Console.WriteLine("Some generic sound");
    }
}
```

و یه کلاس فرزند هم همین متد رو داره:

```csharp
public class Dog : Animal
{
    public void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```

این کد کامپایل می‌شه، اما **هشدار (warning)** می‌ده:

> ⚠️ `Dog.MakeSound()` hides inherited member `Animal.MakeSound()`. Use the **new** keyword if hiding was intended.

پس برای **رفع هشدار** و **اعلام عمدی بودن پنهان‌کردن**، باید از `new` استفاده کنی:

```csharp
public class Dog : Animal
{
    public new void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```

---

### ✅ مثال کامل

```csharp
public class Animal
{
    public void MakeSound()
    {
        Console.WriteLine("Generic sound");
    }
}

public class Dog : Animal
{
    public new void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

// استفاده:
Animal animal = new Dog();
animal.MakeSound(); // خروجی: "Generic sound"

Dog dog = new Dog();
dog.MakeSound(); // خروجی: "Woof!"
```

> 🔍 **نکته مهم**: با `new`، **پلی‌مورفیسم (چندریختی) انجام نمی‌شه** — نوع متغیر تعیین‌کننده متد فراخوانی‌ست، نه نوع واقعی شیء.

---

### 🆚 تفاوت `new` با `override`

| `new` (Method Hiding) | `override` (Polymorphism) |
|-----------------------|----------------------------|
| عضو کلاس پایه رو **پنهان** می‌کنه | عضو کلاس پایه رو **بازنویسی** می‌کنه |
| فقط وقتی فراخوانی می‌شه که نوع متغیر، کلاس فرزند باشه | با توجه به نوع واقعی شیء فراخوانی می‌شه (چندریختی) |
| نیازی به `virtual` در کلاس پایه نیست | کلاس پایه باید `virtual` یا `abstract` باشه |
| با `new` تعریف می‌شه | با `override` تعریف می‌شه |

#### مثال مقایسه:

```csharp
// با override
Animal animal = new Dog();
animal.MakeSound(); // اگر override باشه → "Woof!"
```

```csharp
// با new
Animal animal = new Dog();
animal.MakeSound(); // با new → "Generic sound" (چون نوع Animal است)
```

---

### ✅ کاربردهای `new` Modifier

- وقتی نمی‌تونی یا نمی‌خوای از `virtual`/`override` استفاده کنی.
- وقتی می‌خوای رفتار قدیمی کلاس پایه حفظ بشه و فقط در کلاس فرزند رفتار جدید داشته باشه.
- در کتابخونه‌های قدیمی که نمی‌شه کد پایه رو تغییر داد.

---

### ⚠️ نکات مهم درباره `new` Modifier

- 🔹 فقط روی اعضایی که **هم نام** با عضو کلاس پایه هستند قابل استفاده‌ست.
- 🔹 می‌تونه روی **متد، خاصیت، فیلد، رویداد، ایندکسر** استفاده بشه.
- 🔹 استفاده از `new` بدون هدف، **بدکدی** محسوب می‌شه — فقط وقتی استفاده کن که واقعاً می‌خوای پنهان‌کردن داشته باشی.
- 🔹 کامپایلر بدون `new` هشدار می‌ده، پس یا `new` بذار، یا `override` (اگر امکان‌پذیره).

---

## ✅ 2. `new` به‌عنوان Operator (ساخت شیء)

این معروف‌ترین کاربرد `new` هست:

```csharp
Person p = new Person();
List<string> list = new List<string>();
int x = new int(); // معادل 0
```

همچنین می‌تونه با **Object Initializer** ترکیب بشه:

```csharp
var person = new Person 
{
    Name = "Ali",
    Age = 25
};
```

و یا حتی برای ساخت **انواع ناشناس (Anonymous Types)**:

```csharp
var obj = new { Name = "Ali", Age = 30 };
```

---

## ✅ 3. `new` به‌عنوان Constraint (محدودیت Generic)

در Genericها، وقتی می‌خوای مطمئن بشی که یه نوع داده‌ای که به عنوان `T` میاد، **سازنده پیش‌فرض (parameterless constructor)** داره، از `new()` constraint استفاده می‌کنی:

```csharp
public class Factory<T> where T : new()
{
    public T Create()
    {
        return new T(); // بدون new() constraint، این خط خطا می‌ده
    }
}
```

استفاده:

```csharp
var factory = new Factory<Person>(); // فقط اگر Person() داشته باشه
var person = factory.Create();
```

> ⚠️ نکته: `new()` constraint فقط وقتی کار می‌کنه که نوع `T` **public constructor بدون پارامتر** داشته باشه.

---

## 🧩 خلاصه: سه نقش `new` در C#

| نوع | کاربرد | مثال |
|------|--------|-------|
| **Modifier** | پنهان‌کردن عضو کلاس پایه | `public new void Method()` |
| **Operator** | ساخت شیء جدید | `new MyClass()` |
| **Constraint** | محدودیت در Generic | `where T : new()` |

---

## 🏁 نتیجه‌گیری

> 🔤 کلمه `new` در C# یه **چندریختی معنایی** داره — یعنی بسته به جایی که استفاده بشه، معنی متفاوتی پیدا می‌کنه.

اما وقتی می‌گیم "`new` به‌عنوان Modifier"، منظور دقیقاً همون **پنهان‌کردن عضو کلاس پایه** در کلاس فرزند هست.

---

### ✅ قانون طلایی:
> اگر می‌خوای **چندریختی (Polymorphism)** داشته باشی → از `virtual` و `override` استفاده کن.  
> اگر می‌خوای فقط در کلاس فرزند رفتار عوض بشه و کلاس پایه دست‌نخورده بمونه → از `new` استفاده کن.

---

اگه خواستی، می‌تونم یه **چک‌لیست یا تصویر ذهنی (Mental Model)** از تفاوت `new` و `override` برات بسازم. فقط بگو! 😊

### USER
به صورت عمیق توضیح بده

### ASSISTANT
خب، وقت آن رسیده که به **عمق واقعی** کلیدواژه `new` در زبان C# برویم — نه فقط از نظر نحوی (Syntax)، بلکه از نظر **معنایی، رفتاری، طراحی شیءگرا، و حتی سطح IL (Intermediate Language)**.

در این توضیح عمیق، تمام جنبه‌های `new` رو پوشش می‌دم:  
- ✅ `new` به عنوان **modifier** (تمرکز اصلی)  
- ✅ `new` به عنوان **operator**  
- ✅ `new` به عنوان **constraint** در generic  
- ✅ تفاوت آن با `override` از نظر **سیستم فراخوانی متدها (vtable vs. static dispatch)**  
- ✅ مثال‌های پیشرفته، نکات پنهان، و سناریوهای واقعی استفاده  
- ✅ تحلیل رفتار در زمان اجرا (Runtime) و کامپایل (Compile-time)

---

## 🔍 بخش ۱: `new` به عنوان Modifier — Method Hiding

### 📌 تعریف دقیق:
وقتی از `new` قبل از یک عضو (متد، خاصیت، فیلد، ایندکسر، رویداد) در یک کلاس فرزند استفاده می‌کنید، در واقع دارید **اعلام می‌کنید که این عضو، عضو هم‌نام در کلاس پایه را پنهان می‌کند (Hides)**.

این کار **نه override است، نه از polymorphism پشتیبانی می‌کند** — بلکه یک **static binding** ایجاد می‌کند.

---

### 🧱 پیش‌نیازها:
- کلاس فرزند باید از کلاس پایه ارث ببرد.
- عضو فرزند باید **هم‌نام** با عضو پایه باشد.
- عضو پایه **نیازی به `virtual` ندارد**.
- برای رفع هشدار کامپایلر، باید `new` اضافه شود (یا `override` اگر امکان‌پذیر باشد).

---

### 💡 مثال پایه:

```csharp
public class Animal
{
    public void MakeSound()
    {
        Console.WriteLine("Generic sound");
    }
}

public class Dog : Animal
{
    public new void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```

حالا این کد رو اجرا کن:

```csharp
Animal animal = new Dog();
animal.MakeSound(); // خروجی: "Generic sound"

Dog dog = new Dog();
dog.MakeSound(); // خروجی: "Woof!"
```

### 🔍 چه اتفاقی افتاد؟

- نوع **استاتیک (در کامپایل)** متغیر `animal` از نوع `Animal` است.
- بنابراین، کامپایلر به دنبال `MakeSound()` در کلاس `Animal` می‌رود.
- از آنجا که `Dog.MakeSound` با `new` تعریف شده، **در vtable اضافه نمی‌شود**.
- پس فراخوانی به صورت **static dispatch** انجام می‌شود — یعنی فقط به نام نوع متغیر نگاه می‌کند، نه نوع واقعی شیء.

---

### ⚙️ از نظر IL (زبان میانی):

وقتی کامپایلر می‌بینه که یه متد با `new` تعریف شده، دستور `call` (نه `callvirt`) رو تولید می‌کنه:

```il
call instance void Animal::MakeSound()
```

در حالی که اگر `virtual`/`override` بود، از `callvirt` استفاده می‌کرد که مکانیزم **چندریختی** رو فعال می‌کنه.

---

### 🔄 تفاوت با `override` — از نظر سیستم فراخوانی

| جنبه | `new` (Method Hiding) | `override` (Polymorphism) |
|------|------------------------|----------------------------|
| نوع باندینگ | Static (کامپایل‌تایم) | Dynamic (ران‌تایم) |
| استفاده از vtable | ❌ خیر | ✅ بله |
| دستور IL | `call` | `callvirt` |
| رفتار با upcast | متد کلاس پایه فراخوانی می‌شود | متد فرزند فراخوانی می‌شود |
| نیاز به `virtual` در پایه | ❌ خیر | ✅ بله |

---

### 🧩 مثال پیشرفته: ترکیب `new` و `virtual`

```csharp
public class Animal
{
    public virtual void Speak() => Console.WriteLine("Animal speaks");
    public void MakeSound() => Console.WriteLine("Animal sound");
}

public class Dog : Animal
{
    public override void Speak() => Console.WriteLine("Dog barks");
    public new void MakeSound() => Console.WriteLine("Dog woofs");
}

// تست:
Animal animal = new Dog();
animal.Speak();        // "Dog barks" → چون override
animal.MakeSound();    // "Animal sound" → چون new و static dispatch
```

اینجا می‌بینیم که:
- `Speak()` با `override` → رفتار پلی‌مورفیک داره.
- `MakeSound()` با `new` → فقط در نوع `Dog` قابل دسترسی است.

---

### 💥 خطرات استفاده از `new`

1. **توهم چندریختی**: توسعه‌دهنده ممکن است فکر کند که `override` شده، در حالی که نه.
2. **رفتار غیرقابل پیش‌بینی** در لیست‌ها:
   ```csharp
   List<Animal> animals = new List<Animal> { new Dog() };
   foreach (var a in animals)
       a.MakeSound(); // همیشه "Animal sound" چاپ می‌شه!
   ```
3. **شکنندگی کد (Fragile Code)**: اگر بعداً کلاس پایه `virtual` شد، بهتر است از `override` استفاده کنید.

---

## 🔍 بخش ۲: `new` به عنوان Operator — ساخت شیء

### ✨ کاربرد اصلی:
ایجاد یک نمونه (Instance) از یک نوع.

```csharp
Person p = new Person();
```

اما این ساده‌تر از ظاهرش نیست!

### 🧠 چه اتفاقی در پس‌پرده می‌افتد؟

1. **حافظه تخصیص داده می‌شود** در managed heap.
2. **سازنده (Constructor)** فراخوانی می‌شود.
3. **فیلدها به مقادیر پیش‌فرض** مقداردهی می‌شوند (حتی قبل از اجرای سازنده).
4. **ارسال ارجاع** به متغیر.

---

### 🛠️ ویژگی‌های پیشرفته `new` operator

#### 1. **Object Initializers**
```csharp
var p = new Person { Name = "Ali", Age = 25 };
// معادل:
// var p = new Person();
// p.Name = "Ali";
// p.Age = 25;
```

#### 2. **Collection Initializers**
```csharp
var list = new List<int> { 1, 2, 3 };
```

#### 3. **Anonymous Types**
```csharp
var person = new { Name = "Ali", Age = 30 };
// نوعش: <>f__AnonymousType0<string, int>
```

#### 4. **Array Creation**
```csharp
int[] arr = new int[5];
var matrix = new int[,] { {1,2}, {3,4} };
```

#### 5. **Stackalloc (در کدهای unsafe)**
```csharp
unsafe
{
    int* ptr = stackalloc int[10];
}
```

---

## 🔍 بخش ۳: `new` به عنوان Constraint — در Generic

### 🎯 مفهوم:
وقتی می‌خوای در یک کلاس یا متد generic، یک نمونه از `T` بسازی، باید مطمئن باشی که `T` یه سازنده پیش‌فرض داره.

```csharp
public class Container<T> where T : new()
{
    private T _item;

    public T Create()
    {
        return new T(); // فقط با new() constraint مجازه
    }
}
```

### ⚠️ محدودیت‌ها:

- `T` باید **public constructor بدون پارامتر** داشته باشه.
- کار نمی‌کنه با:
  - Structها که constructor دارن (مگر اینکه اکسپلیسیت باشه)
  - کلاس‌هایی که فقط سازنده پارامتردار دارن
  - کلاس‌های `static`

### 🔄 ترکیب با سایر constraintها:
```csharp
where T : class, new()
```
> فقط کلاس‌هایی که reference type هستند و سازنده پیش‌فرض دارند.

---

## 🧠 بخش ۴: تحلیل عمیق — چه زمانی از `new` modifier استفاده کنیم؟

### ✅ موارد مجاز و منطقی:

1. **وقتی کد پایه قفل شده (مثل کتابخونه خارجی)**  
   نمی‌تونی `virtual` اضافه کنی، پس `new` تنها راه برای تغییر رفتاره.

2. **وقتی می‌خوای دو رفتار متفاوت داشته باشی بسته به نوع متغیر**  
   مثلاً در سیستم‌های logging یا serialization قدیمی.

3. **وقتی می‌خوای backward compatibility داشته باشی**  
   یعنی کدهای قدیمی همچنان رفتار قبلی رو داشته باشن.

---

### ❌ موارد غیرمجاز یا بد practice:

1. **وقتی کلاس پایه `virtual` داره — اما تو `new` استفاده می‌کنی**  
   ❌ اشتباه است. باید `override` استفاده کنی.

2. **وقتی می‌خوای polymorphism داشته باشی — اما `new` استفاده می‌کنی**  
   ❌ رفتار اجرا نمی‌شود.

3. **استفاده از `new` بدون دلیل — فقط برای رفع هشدار**  
   ❌ ضد الگو (Anti-pattern) است.

---

## 🧩 بخش ۵: مثال واقعی از دنیای واقعی

فرض کن یه کتابخونه قدیمی داری:

```csharp
// از یه NuGet Package میاد
public class Logger
{
    public void Log(string message)
    {
        File.WriteAllText("log.txt", message);
    }
}
```

حالا تو می‌خوای رفتارش رو عوض کنی:

```csharp
public class AdvancedLogger : Logger
{
    public new void Log(string message)
    {
        Console.WriteLine($"[LOG] {message}");
        // نمی‌خوای فایل بنویسه!
    }
}
```

اما یه سرویس قدیمی هنوز از `Logger` استفاده می‌کنه:

```csharp
public class LegacyService
{
    public void DoWork(Logger logger)
    {
        logger.Log("Work done"); // این همیشه File.WriteAllText می‌زنه!
    }
}
```

پس `new` فقط برای کدهایی کار می‌کنه که مستقیماً `AdvancedLogger` رو فراخوانی می‌کنن.

> 🧠 این یه مثال از **tight coupling** و **عدم کنترل بر کد پایه** است.

---

## 🏁 جمع‌بندی نهایی: مثل یه متخصص C# فکر کن

| سوال | پاسخ |
|------|-------|
| `new` modifier چیه؟ | ابزاری برای **پنهان‌کردن** عضو کلاس پایه بدون override |
| چه زمانی ازش استفاده کنیم؟ | وقتی **نمی‌تونیم یا نمی‌خواهیم override کنیم** و رفتار static می‌خواهیم |
| چه زمانی نباید استفاده کرد؟ | وقتی کلاس پایه `virtual` داره — باید `override` کنی |
| آیا polymorphism داره؟ | ❌ خیر — فقط static dispatch |
| تفاوت با `override` چیه؟ | `override` dynamic، `new` static است |
| آیا امنه؟ | فقط اگر عمدی و با درک کامل استفاده بشه |

---



> ✅ **جمله طلایی**:  
> "اگر می‌خوای رفتار کلاس فرزند جایگزین کلاس پایه بشه — **override** کن.  
> اگر می‌خوای دو رفتار موازی داشته باشی بسته به نوع متغیر — **new** کن."

---
