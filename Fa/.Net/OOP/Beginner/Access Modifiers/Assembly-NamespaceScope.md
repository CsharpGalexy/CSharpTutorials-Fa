

# 📚 Assembly & Namespace Scope در C#  




---
## 📖 فهرست مطالب

1. [مقدمه](#1-مقدمه)  
2. [Namespace (فضای نام)](#2-namespace-فضای-نام)  
   - [2.1. تعریف Namespace](#21-تعریف-namespace)  
   - [2.2. چرا از Namespace استفاده می‌کنیم؟](#22-چرا-از-namespace-استفاده-میکنیم)  
   - [2.3. نحوه تعریف و استفاده](#23-نحوه-تعریف-و-استفاده)  
   - [2.4. Nested Namespace (فضای نام تو در تو)](#24-nested-namespace-فضای-نام-تو-در-تو)  
   - [2.5. using Statement](#25-using-statement)  
   - [2.6. Alias در Namespace](#26-alias-در-namespace)  
   - [2.7. Best Practices](#27-best-practices)  
3. [Assembly (مجمع یا اسمبلی)](#3-assembly-مجمع-یا-اسمبلی)  
   - [3.1. تعریف Assembly](#31-تعریف-assembly)  
   - [3.2. انواع Assembly](#32-انواع-assembly)  
   - [3.3. ساختار داخلی Assembly](#33-ساختار-داخلی-assembly)  
   - [3.4. Metadata و Manifest](#34-metadata-و-manifest)  
   - [3.5. Strong Name و Versioning](#35-strong-name-و-versioning)  
   - [3.6. محل ذخیره Assembly](#36-محل-ذخیره-assembly)  
4. [تفاوت Namespace و Assembly](#4-تفاوت-namespace-و-assembly)  
5. [نحوه استفاده از Assembly خارجی](#5-نحوه-استفاده-از-assembly-خارجی)  
6. [مثال عملی: پروژه چند لایه با Namespace و Assembly](#6-مثال-عملی-پروژه-چند-لایه-با-namespace-و-assembly)  
7. [جمع‌بندی](#7-جمعبندی)  
8. [منابع معتبر](#8-منابع-معتبر)

---

## 1. مقدمه

وقتی در C# کدنویسی می‌کنید، با دو مفهوم مهم روبرو می‌شوید: **Namespace** و **Assembly**.  
این دو مفهوم نقش کلیدی در **سازماندهی کد، مدیریت ماژول‌ها و جلوگیری از تداخل نام‌ها** دارند.

در این مستند، این دو مفهوم را از صفر تا صد توضیح می‌دهیم، با مثال‌های ساده و عملی — مناسب برای کسی که هنوز OOP را تازه یاد گرفته!

---

## 2. Namespace (فضای نام)

### 2.1. تعریف Namespace

**Namespace** یک **کانتینر منطقی** است که برای گروه‌بندی کلاس‌ها، رابط‌ها، enum‌ها و سایر عناصر در C# استفاده می‌شود.

به زبان ساده: مثل یک "پوشه" است که کدهای مرتبط را در آن قرار می‌دهید.

```csharp
namespace MyApplication.Utilities
{
    public class Logger
    {
        public void Log(string message)
        {
            Console.WriteLine($"Log: {message}");
        }
    }
}
```

### 2.2. چرا از Namespace استفاده می‌کنیم؟

- ❌ **جلوگیری از تداخل نام**: اگر دو کلاس با نام `User` داشته باشید، ولی در دو namespace مختلف (`App.Models.User` و `Admin.Models.User`) باشند، مشکلی ندارد.
- ✅ **سازماندهی بهتر کد**: کدهای مرتبط را در namespace مناسب قرار می‌دهید.
- 🔍 **قابلیت جستجو و نگهداری بالاتر**

### 2.3. نحوه تعریف و استفاده

#### تعریف:

```csharp
namespace MyApp.Services
{
    public class EmailService { }
}
```

#### استفاده (بدون using):

```csharp
MyApp.Services.EmailService email = new MyApp.Services.EmailService();
```

#### استفاده (با using):

```csharp
using MyApp.Services;

// حالا می‌توانید بدون پیشوند namespace بنویسید:
EmailService email = new EmailService();
```

### 2.4. Nested Namespace (فضای نام تو در تو)

می‌توانید namespaceها را تو در تو تعریف کنید:

```csharp
namespace Company.Project.Module
{
    public class Processor { }
}
```

معادل است با:

```csharp
namespace Company
{
    namespace Project
    {
        namespace Module
        {
            public class Processor { }
        }
    }
}
```

### 2.5. using Statement

کلمه کلیدی `using` به شما اجازه می‌دهد بدون نوشتن کامل namespace، از کلاس‌ها استفاده کنید.

```csharp
using System;
using MyApp.Data;

class Program
{
    static void Main()
    {
        var db = new Database(); // بدون نیاز به MyApp.Data.Database
    }
}
```

> ⚠️ توجه: `using` فقط برای خوانایی است. روی عملکرد اجرایی تأثیر ندارد.

### 2.6. Alias در Namespace

اگر دو کلاس با نام یکسان در namespaceهای مختلف داشته باشید، می‌توانید از **Alias** استفاده کنید:

```csharp
using AdminUser = AdminApp.Models.User;
using CustomerUser = CustomerApp.Models.User;

class Program
{
    static void Main()
    {
        AdminUser user1 = new AdminUser();
        CustomerUser user2 = new CustomerUser();
    }
}
```

### 2.7. Best Practices

- از الگوی `Company.Project.Module` استفاده کنید (مثلاً: `Acme.Ecommerce.Web`)
- namespaceها را طبق ساختار پوشه‌های پروژه تنظیم کنید
- از namespaceهای خیلی طولانی یا کوتاه خودداری کنید
- از Visual Studio برای اصلاح خودکار `using`ها استفاده کنید

---

## 3. Assembly (مجمع یا اسمبلی)

### 3.1. تعریف Assembly

**Assembly** واحد اصلی **تولید و اجرای کد در .NET** است.  
یک Assembly می‌تواند یک **فایل `.dll` یا `.exe`** باشد.

- `.exe`: برنامه قابل اجرا (مثل یک برنامه ویندوزی)
- `.dll`: کتابخانه قابل استفاده در پروژه‌های دیگر

هر Assembly حاوی **IL Code (Intermediate Language)**، **Metadata** و **Manifest** است.

### 3.2. انواع Assembly

| نوع | توضیح |
|-----|------|
| **Private Assembly** | فقط برای یک پروژه استفاده می‌شود و در پوشه `bin` قرار می‌گیرد |
| **Shared (Public) Assembly** | در GAC (Global Assembly Cache) نصب می‌شود و توسط چندین برنامه استفاده می‌شود |

> 🔐 Shared Assembly نیاز به **Strong Name** دارد.

### 3.3. ساختار داخلی Assembly

یک Assembly شامل این بخش‌هاست:

- **IL Code**: کد کامپایل شده به Intermediate Language
- **Metadata**: اطلاعاتی درباره کلاس‌ها، متدها، متغیرها و ...
- **Manifest**: اطلاعاتی درباره خود Assembly مثل نام، نسخه، culture و لیست وابستگی‌ها

### 3.4. Metadata و Manifest

- **Manifest**: مثل "شناسنامه" Assembly است. شامل:
  - نام Assembly
  - نسخه (Version)
  - Culture
  - لیست Assemblyهای وابسته
- **Metadata**: اطلاعاتی درباره تمام typeها (کلاس‌ها، متدها، ...) که توسط Reflection قابل خواندن است.

### 3.5. Strong Name و Versioning

- **Strong Name**: یک امضای دیجیتال است که برای شناسایی منحصر به فرد یک Assembly استفاده می‌شود.
- با استفاده از فایل `.snk` ایجاد می‌شود.
- اجازه می‌دهد Assembly در GAC نصب شود.

📌 نسخه‌گذاری Assembly به صورت زیر است:
```
Major.Minor.Build.Revision
مثلاً: 1.0.0.0
```

### 3.6. محل ذخیره Assembly

- **Private**: در پوشه `bin\Debug` یا `bin\Release` پروژه
- **Shared**: در **GAC** (Global Assembly Cache) در مسیر سیستمی مثل:
  ```
  C:\Windows\Microsoft.NET\assembly\
  ```

---

## 4. تفاوت Namespace و Assembly

| مورد | Namespace | Assembly |
|------|-----------|---------|
| نوع | منطقی (Logical) | فیزیکی (Physical) |
| هدف | سازماندهی کد، جلوگیری از تداخل نام | بسته‌بندی و توزیع کد |
| محل قرارگیری | درون یک یا چند فایل C# | یک فایل `.dll` یا `.exe` |
| تأثیر در کامپایل | خیر | بله (وابستگی‌ها در کامپایل تشخیص داده می‌شود) |
| قابل استفاده در پروژه‌های دیگر | فقط با `using` | با اضافه کردن Reference |

✅ **نکته مهم**:  
- یک Assembly می‌تواند شامل چندین Namespace باشد.  
- یک Namespace می‌تواند در چند Assembly وجود داشته باشد (اما توصیه نمی‌شود!).

---

## 5. نحوه استفاده از Assembly خارجی

فرض کنید یک کتابخانه به نام `MathLibrary.dll` دارید.

### مراحل استفاده:

1. در Visual Studio → راست کلیک روی **Dependencies** → **Add Reference**
2. گزینه **Browse** → فایل `MathLibrary.dll` را انتخاب کنید
3. در کد، namespace آن را با `using` اضافه کنید:

```csharp
using MathLibrary;

Calculator calc = new Calculator();
int result = calc.Add(5, 3);
```

> 💡 اگر dll دارای Strong Name باشد، می‌تواند در GAC نصب شود و برای همه پروژه‌ها در دسترس باشد.

---

## 6. مثال عملی: پروژه چند لایه با Namespace و Assembly

فرض کنید یک پروژه ساده OOP داریم با ساختار زیر:

```
MyEcommerceApp/
│
├── MyEcommerceApp.Core/       → Models & Interfaces (.dll)
├── MyEcommerceApp.Services/   → Business Logic (.dll)
└── MyEcommerceApp.UI/         → Console App (.exe)
```

### مرحله ۱: Core (مدل‌ها)

**File**: `Product.cs`  
**Namespace**: `MyEcommerceApp.Core.Models`

```csharp
namespace MyEcommerceApp.Core.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```

### مرحله ۲: Services (سرویس‌ها)

**File**: `ProductService.cs`  
**Namespace**: `MyEcommerceApp.Services`

```csharp
using MyEcommerceApp.Core.Models;

namespace MyEcommerceApp.Services
{
    public class ProductService
    {
        public List<Product> GetProducts()
        {
            return new List<Product>
            {
                new Product { Id = 1, Name = "Laptop" },
                new Product { Id = 2, Name = "Mouse" }
            };
        }
    }
}
```

### مرحله ۳: UI (رابط کاربری)

**File**: `Program.cs`  
**Namespace**: `MyEcommerceApp.UI`

```csharp
using MyEcommerceApp.Services;
using MyEcommerceApp.Core.Models;

namespace MyEcommerceApp.UI
{
    class Program
    {
        static void Main()
        {
            var service = new ProductService();
            var products = service.GetProducts();

            foreach (var p in products)
            {
                Console.WriteLine(p.Name);
            }
        }
    }
}
```

### مرحله ۴: اضافه کردن Reference

- به `MyEcommerceApp.UI`، Reference از `MyEcommerceApp.Services` اضافه کنید.
- `MyEcommerceApp.Services` هم باید Reference از `MyEcommerceApp.Core` داشته باشد.

✅ حالا هر لایه در یک Assembly جدا است و با namespace مناسب سازماندهی شده.

---

## 7. جمع‌بندی

| مفهوم | خلاصه |
|-------|--------|
| **Namespace** | ابزاری برای سازماندهی منطقی کد و جلوگیری از تداخل نام |
| **Assembly** | واحد فیزیکی بسته‌بندی کد (`.dll` یا `.exe`) |
| **using** | برای اختصار در دسترسی به namespaceها |
| **Reference** | برای استفاده از Assemblyهای خارجی |
| **Best Practice** | از ساختار منطقی namespace و لایه‌بندی Assembly استفاده کنید |

📌 این دو مفهوم پایه‌ای برای فهم **OOP، Dependency Injection، Layered Architecture** و **تست واحد** هستند.

---

## 8. منابع معتبر

1. **Microsoft Learn (رسمی)**  
   - [Namespaces](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/namespace)  
   - [Assemblies in .NET](https://learn.microsoft.com/en-us/dotnet/standard/assembly/)  
   - [Using Namespaces](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-directive)

2. **C# Guide – Microsoft Docs**  
   - [Organizing code with namespaces](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/namespaces)  
   - [Create and use .NET assemblies](https://learn.microsoft.com/en-us/dotnet/standard/assembly/create)

3. **Books**  
   - *C# 10 and .NET 6 – Modern Cross-Platform Development* by Mark J. Price  
   - *CLR via C#* by Jeffrey Richter (برای درک عمیق‌تر از Assembly و CLR)

4. **Stack Overflow (برای مثال‌های عملی)**  
   - [What is the difference between a namespace and an assembly?](https://stackoverflow.com/questions/16284629/what-is-the-difference-between-a-namespace-and-an-assembly)

5. **YouTube Channels (آموزش تصویری)**  
   - Nick Chapsas (Clean Architecture, Layering)  
   - Programming with Mosh – C# OOP Tutorials

---
