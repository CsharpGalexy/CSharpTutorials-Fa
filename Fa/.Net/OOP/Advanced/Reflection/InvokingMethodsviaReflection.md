

## 📚 فهرست مطالب

1. [مقدمه‌ای بر Reflection در C#](#1-%D9%85%D9%82%D8%AF%D9%85%D9%87%E2%80%8C%D8%A7%DB%8C-%D8%A8%D8%B1-reflection-%D8%AF%D8%B1-c)
2. [چرا از Reflection برای فراخوانی متد استفاده می‌کنیم؟](#2-%DA%86%D8%B1%D8%A7-%D8%A7%D8%B2-reflection-%D8%A8%D8%B1%D8%A7%DB%8C-%D9%81%D8%B1%D8%A7%D8%AE%D9%88%D8%A7%D9%86%DB%8C-%D9%85%D8%AA%D8%AF-%D8%A7%D8%B3%D8%AA%D9%81%D8%A7%D8%AF%D9%87-%D9%85%DB%8C%E2%80%8C%DA%A9%D9%86%DB%8C%D9%85)
3. [مروری بر کلاس‌های کلیدی در System.Reflection](#3-%D9%85%D8%B1%D9%88%D8%B1%DB%8C-%D8%A8%D8%B1-%DA%A9%D9%84%D8%A7%D8%B3%E2%80%8C%D9%87%D8%A7%DB%8C-%DA%A9%D9%84%DB%8C%D8%AF%DB%8C-%D8%AF%D8%B1-systemreflection)
4. [فراخوانی متد‌های عمومی بدون پارامتر](#4-فراخوانی-متد‌های-عمومی-بدون-پارامتر)
5. [فراخوانی متد‌های با پارامتر](#5-فراخوانی-متد‌های-با-پارامتر)
6. [فراخوانی متد‌های خصوصی (Private)](#6-فراخوانی-متد‌های-خصوصی-private)
7. [فراخوانی متد‌های استاتیک](#7-فراخوانی-متد‌های-استاتیک)
8. [مدیریت خطا در Reflection](#8-مدیریت-خطا-در-reflection)
9. [نکات عملکردی و بهترین روش‌ها](#9-%D9%86%DA%A9%D8%A7%D8%AA-%D8%B9%D9%85%D9%84%DA%A9%D8%B1%D8%AF%DB%8C-%D9%88-%D8%A8%D9%87%D8%AA%D8%B1%DB%8C%D9%86-%D8%B1%D9%88%D8%B4%E2%80%8C%D9%87%D8%A7)
10. [جمع‌بندی](#10-%D8%AC%D9%85%D8%B9%E2%80%8C%D8%A8%D9%86%D8%AF%DB%8C)
11. [منابع معتبر](#11-منابع-معتبر)

---

## 1. مقدمه‌ای بر Reflection در C#

**Reflection** یکی از قدرتمندترین ویژگی‌های .NET است که به برنامه‌نویس اجازه می‌دهد **اطلاعات درون‌نگری (Introspection)** از نوع‌ها (Types)، متد‌ها، خصوصیت‌ها و سایر اعضای یک کلاس را در **زمان اجرا (Runtime)** به دست آورد و حتی آن‌ها را فراخوانی یا تغییر دهد.

در C#، Reflection از طریق فضای نام `System.Reflection` پیاده‌سازی شده است.

> 🔍 **نکته**: Reflection زمانی مفید است که نام متد یا نوع کلاس در زمان کامپایل مشخص نباشد (مثلاً در پلاگین‌ها، ORMها، تست‌های واحد، سریالایزرها و ...).

---

## 2. چرا از Reflection برای فراخوانی متد استفاده می‌کنیم؟

- **اجرای پویای متد‌ها**: وقتی نام متد یا کلاس در زمان کامپایل مشخص نیست.
- **فریم‌ورک‌های عمومی**: مانند Entity Framework، NUnit، ASP.NET Core (برای Dependency Injection).
- **ابزارهای توسعه**: مانند سریالایزرها، مپ‌کننده‌ها (AutoMapper)، و سیستم‌های پلاگین.
- **تست‌نویسی**: برای فراخوانی متد‌های خصوصی در تست‌های واحد.

---

## 3. مروری بر کلاس‌های کلیدی در System.Reflection

| کلاس | توضیح |
|------|--------|
| `Type` | نماینده یک نوع (کلاس، ساختار، رابط و ...) در زمان اجرا |
| `MethodInfo` | اطلاعات یک متد (نام، پارامترها، نوع بازگشتی و ...) |
| `BindingFlags` | تعیین محدوده جستجو (مثلاً فقط متد‌های خصوصی یا استاتیک) |
| `Activator` | برای ایجاد نمونه از یک کلاس در زمان اجرا |

---

## 4. فراخوانی متد‌های عمومی بدون پارامتر

فرض کنید کلاس زیر را داریم:

```csharp
public class Calculator
{
    public int Add() => 5 + 3;
}
```

برای فراخوانی متد `Add` با Reflection:

```csharp
using System;
using System.Reflection;

class Program
{
    static void Main()
    {
        Type type = typeof(Calculator);
        object instance = Activator.CreateInstance(type);
        MethodInfo method = type.GetMethod("Add");
        object result = method.Invoke(instance, null);
        Console.WriteLine(result); // خروجی: 8
    }
}
```

> ✅ `GetMethod("Add")`: متد عمومی با نام مشخص را پیدا می‌کند.  
> ✅ `Invoke(instance, null)`: چون متد پارامتر ندارد، آرگومان دوم `null` است.

---

## 5. فراخوانی متد‌های با پارامتر

کلاس به‌روزشده:

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
}
```

فراخوانی با Reflection:

```csharp
Type type = typeof(Calculator);
object instance = Activator.CreateInstance(type);
MethodInfo method = type.GetMethod("Add");

object[] parameters = { 10, 20 };
object result = method.Invoke(instance, parameters);
Console.WriteLine(result); // خروجی: 30
```

> ⚠️ **نکته**: ترتیب و نوع پارامترها باید دقیقاً با امضای متد مطابقت داشته باشد.

---

## 6. فراخوانی متد‌های خصوصی (Private)

اگر متد `private` باشد:

```csharp
public class Calculator
{
    private string GetMessage() => "Hello from private!";
}
```

برای دسترسی به آن:

```csharp
BindingFlags flags = BindingFlags.NonPublic | BindingFlags.Instance;
MethodInfo method = type.GetMethod("GetMessage", flags);
object result = method.Invoke(instance, null);
Console.WriteLine(result); // خروجی: Hello from private!
```

> 🔑 `BindingFlags.NonPublic`: برای دسترسی به اعضای خصوصی  
> 🔑 `BindingFlags.Instance`: برای متد‌های غیراستاتیک

---

## 7. فراخوانی متد‌های استاتیک

مثال:

```csharp
public class MathHelper
{
    public static double Square(double x) => x * x;
}
```

فراخوانی:

```csharp
Type type = typeof(MathHelper);
MethodInfo method = type.GetMethod("Square");
object result = method.Invoke(null, new object[] { 4.0 });
Console.WriteLine(result); // خروجی: 16
```

> 💡 برای متد‌های استاتیک، اولین پارامتر `Invoke` باید `null` باشد (چون نیازی به نمونه نیست).

---

## 8. مدیریت خطا در Reflection

Reflection ممکن است خطاهای زیر را ایجاد کند:

- `ArgumentNullException`: اگر نام متد `null` باشد.
- `AmbiguousMatchException`: اگر چند متد با همان نام ولی امضاهای متفاوت وجود داشته باشد.
- `TargetInvocationException`: اگر متد فراخوانی‌شده خودش استثنا پرتاب کند.

مثال مدیریت خطا:

```csharp
try
{
    object result = method.Invoke(instance, parameters);
}
catch (TargetInvocationException ex)
{
    Console.WriteLine($"خطا در متد فراخوانی‌شده: {ex.InnerException?.Message}");
}
catch (Exception ex)
{
    Console.WriteLine($"خطای Reflection: {ex.Message}");
}
```

---

## 9. نکات عملکردی و بهترین روش‌ها

- ❌ **Reflection کند است**: هر بار که `Invoke` فراخوانی می‌شود، overhead زیادی دارد.
- ✅ **کش کردن `MethodInfo`**: اگر قرار است یک متد را چندین بار فراخوانی کنید، `MethodInfo` را ذخیره کنید.
- ✅ **استفاده از `Delegate.CreateDelegate`**: برای بهبود عملکرد، می‌توانید یک delegate از `MethodInfo` ایجاد کنید.
- ✅ **از Reflection فقط زمانی استفاده کنید که واقعاً نیاز دارید**.

مثال بهینه‌سازی با delegate:

```csharp
var method = typeof(Calculator).GetMethod("Add");
var del = (Func<Calculator, int, int, int>)Delegate.CreateDelegate(
    typeof(Func<Calculator, int, int, int>), method);

var calc = new Calculator();
int result = del(calc, 5, 7); // سریع‌تر از Invoke!
```

---

## 10. جمع‌بندی

- Reflection امکان **فراخوانی پویای متد‌ها** را فراهم می‌کند.
- می‌توانید متد‌های **عمومی، خصوصی، استاتیک و با پارامتر** را فراخوانی کنید.
- همیشه **خطاها را مدیریت** کنید.
- برای کاربردهای عملیاتی، **از کش یا delegate** برای بهبود عملکرد استفاده کنید.
- Reflection ابزاری قدرتمند اما **حساس** است — با مسئولیت استفاده کنید!

---

## 11. منابع معتبر

1. **Microsoft Learn – Reflection (C#)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection/](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection/)

2. **MethodInfo.Invoke Method – Official Documentation**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo.invoke](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo.invoke)

3. **BindingFlags Enum – Microsoft Docs**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.reflection.bindingflags](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.bindingflags)

4. **C# in Depth – Jon Skeet (Chapter on Reflection)**  
   ISBN: 978-1617294433 – بخش مربوط به Reflection

5. **Pro C# 10 with .NET 6 – Andrew Troelsen & Philip Japikse**  
   فصل 18: Reflection, Attributes, and Dynamic Types

6. **Stack Overflow – Best Practices for Reflection Performance**  
   [https://stackoverflow.com/questions/128320/why-is-reflection-slow](https://stackoverflow.com/questions/128320/why-is-reflection-slow)

