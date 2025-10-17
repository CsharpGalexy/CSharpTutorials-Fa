

## 📚 فهرست مطالب

1. [مقدمه‌ای بر Reflection در C#](#1-مقدمهای-بر-reflection-در-c)
2. [چرا به Reflection نیاز داریم؟](#2-چرا-به-reflection-نیاز-داریم)
3. [نحوه دسترسی به اطلاعات نوع در زمان اجرا](#3-نحوه-دسترسی-به-اطلاعات-نوع-در-زمان-اجرا)
4. [کلاس `Type` و روش‌های پرکاربرد آن](#4-کلاس-type-و-روشهای-پرکاربرد-آن)
5. [تفاوت `typeof()`، `GetType()` و `is`/`as`](#5-تفاوت-typeof-gettype-و-isas)
6. [مثال‌های کاربردی](#6-مثالهای-کاربردی)
7. [ملاحظات عملکردی و بهترین روش‌ها](#7-ملاحظات-عملکردی-و-بهترین-روشها)
8. [جمع‌بندی](#8-جمعبندی)
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه‌ای بر Reflection در C#

**Reflection** (انعکاس یا بازتاب) یکی از قدرتمندترین ویژگی‌های .NET است که به برنامه‌نویسان اجازه می‌دهد **اطلاعاتی دربارهٔ انواع** (Types) در زمان اجرا (Runtime) به‌دست آورند. این اطلاعات شامل نام کلاس، متدها، ویژگی‌ها (Properties)، فیلدها، سازنده‌ها (Constructors)، وراثت، اینترفیس‌ها و غیره است.

در C#، Reflection از طریق فضای نام `System.Reflection` و کلاس اصلی `Type` پیاده‌سازی شده است.

---

## 2. چرا به Reflection نیاز داریم؟

Reflection در موارد زیر بسیار مفید است:

- **سریال‌سازی/دسیریال‌سازی** (Serialization/Deserialization): مانند JSON.NET یا `System.Text.Json`.
- **ORMها** (Object-Relational Mappers): مثل Entity Framework که از Reflection برای نگاشت کلاس‌ها به جداول دیتابیس استفاده می‌کنند.
- **فریم‌ورک‌های تست واحد** (Unit Testing Frameworks): مانند NUnit یا xUnit.
- **Dependency Injection Containers**: برای کشف و تزریق وابستگی‌ها.
- **پلاگین‌ها و ماژول‌های قابل بارگذاری پویا** (Dynamic Plugin Loading).

---

## 3. نحوه دسترسی به اطلاعات نوع در زمان اجرا

برای بازرسی یک نوع در زمان اجرا، ابتدا باید یک شیء از نوع `Type` دریافت کنید. سه روش اصلی برای این کار وجود دارد:

### الف) با استفاده از `typeof()`
```csharp
Type type = typeof(string);
```
> این روش در **زمان کامپایل** نوع را مشخص می‌کند و برای انواع شناخته‌شده استفاده می‌شود.

### ب) با استفاده از متد `GetType()`
```csharp
string text = "Hello";
Type type = text.GetType();
```
> این روش در **زمان اجرا** نوع واقعی شیء را برمی‌گرداند (حتی اگر از طریق پایه‌کلاس مرجع داده شده باشد).

### ج) با استفاده از `Object.GetType()` روی هر شیء
```csharp
object obj = new List<int>();
Type type = obj.GetType(); // System.Collections.Generic.List`1[System.Int32]
```

---

## 4. کلاس `Type` و روش‌های پرکاربرد آن

کلاس `Type` حاوی اطلاعات جامعی دربارهٔ یک نوع است. برخی از ویژگی‌ها و متدهای پرکاربرد:

| ویژگی/متد | توضیح |
|-----------|--------|
| `Name` | نام ساده نوع (مثلاً `"String"`) |
| `FullName` | نام کامل همراه با فضای نام (مثلاً `"System.String"`) |
| `IsClass`, `IsValueType`, `IsInterface` | بررسی نوع |
| `GetMethods()` | لیست تمام متدهای عمومی |
| `GetProperties()` | لیست ویژگی‌های عمومی |
| `GetFields()` | لیست فیلدهای عمومی |
| `GetConstructors()` | لیست سازنده‌ها |
| `IsAssignableFrom(Type c)` | بررسی اینکه آیا نوع داده‌شده قابل تخصیص به این نوع است (برای وراثت و اینترفیس) |
| `GetGenericArguments()` | برای انواع Generic |

### مثال:
```csharp
Type listType = typeof(List<int>);
Console.WriteLine(listType.Name); // List`1
Console.WriteLine(listType.IsGenericType); // True
Console.WriteLine(string.Join(", ", listType.GetGenericArguments())); // System.Int32
```

---

## 5. تفاوت `typeof()`، `GetType()` و `is`/`as`

| روش | زمان ارزیابی | کاربرد |
|------|--------------|--------|
| `typeof(T)` | کامپایل‌تایم | وقتی نوع `T` را می‌شناسید |
| `obj.GetType()` | ران‌تایم | وقتی شیء دارید و می‌خواهید نوع واقعی آن را بدانید |
| `obj is T` | ران‌تایم | بررسی اینکه آیا `obj` از نوع `T` یا زیرمجموعه‌اش است |
| `obj as T` | ران‌تایم | تبدیل ایمن به نوع `T` (اگر ممکن باشد، در غیر این‌صورت `null`) |

> ⚠️ `typeof()` روی متغیر کار نمی‌کند، فقط روی نام نوع (مثل `int`، `MyClass`) کار می‌کند.

---

## 6. مثال‌های کاربردی

### مثال ۱: لیست کردن تمام ویژگی‌های یک کلاس
```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}

// در Main:
Type personType = typeof(Person);
foreach (var prop in personType.GetProperties())
{
    Console.WriteLine($"{prop.Name} : {prop.PropertyType.Name}");
}
// خروجی:
// Name : String
// Age : Int32
```

### مثال ۲: بررسی اینکه آیا یک شیء از نوع خاصی است
```csharp
object obj = "Hello";
if (obj.GetType() == typeof(string))
    Console.WriteLine("It's a string!");
```

یا بهتر:
```csharp
if (obj is string)
    Console.WriteLine("It's a string!");
```

### مثال ۳: بررسی وراثت
```csharp
class Animal { }
class Dog : Animal { }

Dog dog = new Dog();
Console.WriteLine(dog.GetType().IsSubclassOf(typeof(Animal))); // True
Console.WriteLine(typeof(Animal).IsAssignableFrom(dog.GetType())); // True
```

---

## 7. ملاحظات عملکردی و بهترین روش‌ها

- **Reflection کند است**: استفادهٔ مکرر از آن می‌تواند عملکرد برنامه را کاهش دهد.
- **کش کردن `Type`**: اگر چندین بار به یک نوع دسترسی دارید، `Type` را کش کنید.
- **از `is` و `as` به‌جای `GetType()` برای بررسی نوع استفاده کنید** (مگر اینکه نیاز به اطلاعات دقیق داشته باشید).
- **برای دسترسی به اعضای خصوصی** (private)، نیاز به `BindingFlags.NonPublic` دارید:
  ```csharp
  var privateField = type.GetField("fieldName", BindingFlags.NonPublic | BindingFlags.Instance);
  ```

---

## 8. جمع‌بندی

- Reflection ابزاری قدرتمند برای **بازرسی و تعامل با انواع در زمان اجرا** است.
- کلاس `Type` مرکز اصلی کار با Reflection است.
- `typeof()` برای انواع شناخته‌شده در کامپایل‌تایم، و `GetType()` برای شیء‌های موجود در ران‌تایم استفاده می‌شود.
- همیشه به **عملکرد** و **امنیت** هنگام استفاده از Reflection توجه کنید.
- در بسیاری از فریم‌ورک‌های مدرن (مثل ASP.NET Core)، Reflection به‌صورت هوشمندانه و بهینه‌شده استفاده می‌شود.

---

## 9. منابع معتبر

1. **Microsoft Learn – Reflection (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection)

2. **Microsoft Learn – Type Class**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.type](https://learn.microsoft.com/en-us/dotnet/api/system.type)

3. **C# in Depth – Jon Skeet** (فصل مربوط به Reflection)  
   🔗 [https://csharpindepth.com/](https://csharpindepth.com/)

4. **.NET Documentation – System.Reflection Namespace**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/api/system.reflection](https://learn.microsoft.com/en-us/dotnet/api/system.reflection)

5. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse** (فصل 18: Reflection and Metadata)

