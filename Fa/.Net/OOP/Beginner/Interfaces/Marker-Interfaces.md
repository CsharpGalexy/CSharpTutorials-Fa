## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [تعریف Marker Interface چیست؟](#تعریف-marker-interface-چیست؟)
3. [چرا از Marker Interface استفاده می‌کنیم؟](#چرا-از-marker-interface-استفاده-می‌کنیم؟)
4. [مثال‌های کاربردی در C#](#مثال‌های-کاربردی-در-c)
   - [ISerializable](#iserializable)
   - [IComparable و ICloneable (نکته مهم)](#icomparable-و-icloneable-نکته-مهم)
5. [تفاوت Marker Interface با Attributeها](#تفاوت-marker-interface-با-attributeها)
6. [مزایا و معایب Marker Interface](#مزایا-و-معایب-marker-interface)
7. [بهترین شیوه‌های استفاده (Best Practices)](#بهترین-شیوه‌های-استفاده-best-practices)
8. [جمع‌بندی](#جمع‌بندی)
9. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیءگرا (OOP)، گاهی نیاز داریم کلاس‌ها را **برچسب‌گذاری** کنیم تا نشان دهیم آن‌ها قابلیت خاصی دارند یا باید رفتار خاصی نسبت به آن‌ها انجام شود — حتی اگر هیچ متد یا عضوی در آن‌ها تعریف نشده باشد.  
در زبان C#، یکی از راه‌های انجام این کار استفاده از **رابط‌های نشانه** (Marker Interfaces) است.

این مفهوم در زبان‌هایی مانند Java ریشه دارد، اما در C# نیز به شکلی هوشمندانه‌تر و مدرن‌تر پیاده‌سازی شده است.

---

## تعریف Marker Interface چیست؟

**رابط نشانه** (Marker Interface) یک رابط (interface) است که **هیچ عضوی** (متد، پراپرتی، رویداد و ...) **ندارد** و تنها برای **نشان دادن یک ویژگی یا قابلیت** به یک کلاس استفاده می‌شود.

در C#، این رابط‌ها معمولاً به صورت زیر تعریف می‌شوند:

```csharp
public interface IMyMarker { }
```

سپس یک کلاس می‌تواند این رابط را پیاده‌سازی کند:

```csharp
public class MyClass : IMyMarker { }
```

اکنون می‌توانیم در زمان اجرا (Runtime) با استفاده از عملگر `is` یا Reflection بررسی کنیم که آیا یک شیء از نوع `IMyMarker` است یا خیر:

```csharp
if (obj is IMyMarker)
{
    // انجام رفتار خاص
}
```

---

## چرا از Marker Interface استفاده می‌کنیم؟

هدف اصلی Marker Interfaceها **ارائه یک قرارداد (Contract) بدون رفتار** است. این قرارداد به کامپایلر یا فریم‌ورک اجازه می‌دهد تا:

- رفتار خاصی نسبت به کلاس‌هایی که این رابط را پیاده‌سازی کرده‌اند اعمال کند.
- اطمینان حاصل کند که یک شیء قابلیت خاصی (مثل سریال‌سازی) را دارد.
- از طریق Polymorphism، گروهی از اشیاء را بر اساس ویژگی‌های منطقی دسته‌بندی کند.

---

## مثال‌های کاربردی در C#

### ISerializable

یکی از معروف‌ترین مثال‌های Marker Interface در .NET، رابط `ISerializable` است:

```csharp
public interface ISerializable
{
    void GetObjectData(SerializationInfo info, StreamingContext context);
}
```

> ⚠️ **نکته مهم**: اگرچه `ISerializable` یک متد دارد، اما در عمل به عنوان یک **قرارداد نشانه‌ای** استفاده می‌شود. یعنی وجود این رابط به سیستم سریال‌سازی .NET نشان می‌دهد که کلاس می‌خواهد **کنترل کامل فرآیند سریال‌سازی** را داشته باشد.

اما در نسخه‌های جدیدتر .NET (به خصوص با معرفی `System.Text.Json`)، این رابط کمتر استفاده می‌شود و جای خود را به Attributeها و روش‌های دیگر داده است.

### IComparable و ICloneable (نکته مهم)

در گذشته، رابط‌هایی مانند `ICloneable` یا `IComparable` گاهی به عنوان Marker Interface در نظر گرفته می‌شدند، اما **این درست نیست**:

- `IComparable` یک متد (`CompareTo`) دارد → **Marker نیست**.
- `ICloneable` یک متد (`Clone`) دارد → **Marker نیست**.

بنابراین، **یک رابط واقعی Marker باید کاملاً خالی باشد**.

---

## تفاوت Marker Interface با Attributeها

در C#، روش مدرن‌تر و انعطاف‌پذیرتر برای برچسب‌گذاری کلاس‌ها، استفاده از **Attributeها** است:

```csharp
[Serializable]
public class MyClass { }
```

در اینجا، `SerializableAttribute` یک Attribute است که به کامپایلر و ران‌تایم اطلاع می‌دهد که این کلاس قابل سریال‌سازی است.

### مقایسه:

| ویژگی | Marker Interface | Attribute |
|--------|------------------|----------|
| بررسی در زمان اجرا | با `is` یا `as` | با Reflection (`GetCustomAttributes`) |
| ارث‌بری | خودکار (اگر کلاس پایه پیاده‌سازی کند) | نیاز به `Inherited = true` |
| قابلیت Generic Constraint | ✅ (`where T : IMyMarker`) | ❌ |
| خوانایی کد | متوسط | بالا (معنای صریح) |
| استفاده در API عمومی | کمتر توصیه می‌شود | توصیه می‌شود |

> 📌 **نتیجه**: در C#، **Attributeها جایگزین بهتری برای Marker Interfaceها هستند**، مگر در مواردی که نیاز به **Generic Constraint** دارید.

---

## مزایا و معایب Marker Interface

### ✅ مزایا

- امکان استفاده در **Generic Constraints**:
  ```csharp
  public void Process<T>(T item) where T : IMyMarker { ... }
  ```
- بررسی سریع در زمان اجرا با `is` (بدون نیاز به Reflection).
- یکپارچه با سیستم تایپینگ C#.

### ❌ معایب

- **ناقابل انعطاف**: نمی‌توان پارامتر به آن اضافه کرد (برخلاف Attributeها).
- **سردرگمی**: ممکن است توسعه‌دهنده فکر کند رابط باید رفتاری داشته باشد.
- **عدم پشتیبانی از Metadata پیچیده**: فقط بله/خیر، نه اطلاعات بیشتر.

---

## بهترین شیوه‌های استفاده (Best Practices)

1. **از Marker Interface فقط زمانی استفاده کنید که نیاز به Generic Constraint دارید**.
2. **در سایر موارد، از Attributeها استفاده کنید**.
3. نام رابط را با پیشوند `I` و پسوند معنادار (مثل `IMarker`, `ISafeToLog`) بگذارید.
4. مستندسازی کامل برای رابط بنویسید تا دیگران بدانند چه هدفی دارد.
5. از Marker Interface برای جایگزینی رفتارهای واقعی (مثل متد `Clone`) استفاده نکنید.

---

## جمع‌بندی

- **Marker Interface** یک رابط خالی است که فقط برای برچسب‌گذاری کلاس‌ها استفاده می‌شود.
- در C#، **Attributeها جایگزین بهتری** برای این هدف هستند، مگر در موارد خاصی مثل **Generic Constraints**.
- مثال‌های تاریخی مانند `ISerializable` وجود دارند، اما بسیاری از آن‌ها در .NET مدرن جایگزین شده‌اند.
- همیشه به دنبال **خوانایی** و **انعطاف‌پذیری** در طراحی API باشید.

---

## منابع معتبر

1. **Microsoft Docs – Interfaces**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/)

2. **Microsoft Docs – Attributes**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/attributes](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/attributes)

3. **.NET API Browser – ISerializable**  
   [https://learn.microsoft.com/en-us/dotnet/api/system.runtime.serialization.iserializable](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.serialization.iserializable)

4. **C# in Depth – Jon Skeet** (فصل مربوط به Attributes و Interfaces)  
   [https://csharpindepth.com/](https://csharpindepth.com/)

5. **Framework Design Guidelines – Microsoft**  
   [https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/)  
   (بخش "Choosing Between Attributes and Interfaces")

6. **Stack Overflow – When to use marker interfaces vs attributes?**  
   [https://stackoverflow.com/questions/1023068/when-to-use-marker-interfaces-vs-attributes](https://stackoverflow.com/questions/1023068/when-to-use-marker-interfaces-vs-attributes)
