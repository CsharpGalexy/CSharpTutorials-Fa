
# ویژگی‌ها (Properties) در C#

## فهرست مطالب
- [1. Auto-Implemented Properties](#1-auto-implemented-properties-پایهای)
- [2. Full Properties (با get/set سفارشی)](#2-full-properties-با-getset-سفارشی-پایهای)
- [3. Read-only Properties](#3-read-only-properties-پایهای-تا-متوسط)
- [4. Write-only Properties](#4-write-only-properties-کمکاربرد)
- [5. Init-only Properties](#5-init-only-properties-پیشرفته---c-90)
- [6. Expression-bodied Properties](#6-expression-bodied-properties-متوسط)
- [7. Computed Properties](#-computed-properties-مفهومی)
- [8. Backing Fields](#نکات-پیشرفته)
- [جمع‌بندی و نکات حرفه‌ای](#جمع‌بندی-و-نکات-حرفه‌ای)

---

## 1. **Auto-Implemented Properties** (پایه‌ای)
- **تعریف**: خاصیت‌هایی که **بدون نیاز به Backing Field صریح** تعریف می‌شوند. کامپایلر به‌صورت خودکار یک فیلد پشتیبان مخفی ایجاد می‌کند.
- **محدودیت**: نمی‌توانید منطق سفارشی (مثل اعتبارسنجی) در `get` یا `set` قرار دهید.
- **نسخه C#**: 3.0+
- **مثال**:
  ```csharp
  public class Person
  {
      public string Name { get; set; } // Auto-Implemented
      public int Age { get; set; }
  }
  ```
- **نکته**: 
  - برای مقداردهی اولیه در تعریف خاصیت:
    ```csharp
    public string Name { get; set; } = "Unknown";
    ```

---

## 2. **Full Properties (با get/set سفارشی)** (پایه‌ای)
- **تعریف**: خاصیت‌هایی که **از یک Backing Field صریح** استفاده می‌کنند و امکان اضافه کردن منطق در `get` یا `set` وجود دارد.
- **کاربرد**: اعتبارسنجی، لاگ‌گیری، تغییر مقدار قبل از ذخیره و...
- **مثال**:
  ```csharp
  public class Product
  {
      private decimal _price;
      public decimal Price
      {
          get => _price;
          set
          {
              if (value < 0) throw new ArgumentException("Price can't be negative!");
              _price = value;
          }
      }
  }
  ```

---

## 3. **Read-only Properties** (پایه‌ای تا متوسط)
دو روش اصلی برای خاصیت‌های فقط خواندنی:

### الف) **Getter-Only Auto Property** (C# 6.0+)
- مقدار فقط در **سازنده** یا هنگام تعریف قابل تنظیم است.
- **مثال**:
  ```csharp
  public class User
  {
      public string Id { get; } = Guid.NewGuid().ToString();
      public User(string name) 
      {
          Name = name; // تنها جایی که می‌توان مقدار داد
      }
  }
  ```

### ب) **Private Setter**
- مقدار در کلاس قابل تغییر است، اما خارج از کلاس فقط خواندنی است.
- **مثال**:
  ```csharp
  public class Logger
  {
      public string LastLog { get; private set; }
      public void Log(string message)
      {
          LastLog = message;
      }
  }
  ```

---

## 4. **Write-only Properties** (کم‌کاربرد)
- خاصیت‌هایی که **فقط `set` دارند** و خارج از کلاس قابل خواندن نیستند.
- **کاربرد نادر**: مثلاً برای پردازش مقدار ورودی بدون نمایش آن (مثل رمز عبور).
- **مثال**:
  ```csharp
  public class Security
  {
      private string _hashedPassword;
      public string Password
      {
          set => _hashedPassword = Hash(value);
      }
      private string Hash(string input) => /* الگوریتم هش */;
  }
  ```

---

## 5. **Init-only Properties** (پیشرفته - C# 9.0+)
- خاصیت‌هایی که **فقط در حین ساخت شیء** (در سازنده یا Object Initializer) قابل تنظیم هستند و پس از آن **غیرقابل تغییر** می‌شوند.
- **کاربرد اصلی**: ایجاد اشیاء **Immutable** (مثل Record Types).
- **مثال**:
  ```csharp
  public class Person
  {
      public string Name { get; init; }
      public int Age { get; init; }
  }

  // استفاده:
  var person = new Person { Name = "Ali", Age = 30 }; // ✅
  person.Age = 31; // ❌ خطای کامپایل
  ```
- **نکته**: 
  - با `init` می‌توانید از **Object Initializer** استفاده کنید، در حالی که `private set` این امکان را نمی‌دهد.

---

## 6. **Expression-bodied Properties** (متوسط)
- نحو **فشرده** برای تعریف خاصیت‌های ساده با استفاده از `=>`.
- **نسخه C#**: 6.0+ (برای `get`)، 7.0+ (برای `set`).
- **مثال**:
  ```csharp
  public class Circle
  {
      public double Radius { get; set; }
      public double Area => Math.PI * Radius * Radius; // بدون نیاز به {}
      public double Circumference
      {
          get => 2 * Math.PI * Radius;
          set => Radius = value / (2 * Math.PI);
      }
  }
  ```

---

## 7. **Computed Properties** (مفهومی)
- خاصیت‌هایی که **مقدار آن‌ها محاسبه می‌شود** و در حافظه ذخیره نمی‌شود.
- **نحو پیاده‌سازی**: معمولاً با **Expression-bodied** یا **Full Property** پیاده‌سازی می‌شوند.
- **مثال**:
  ```csharp
  public class Rectangle
  {
      public double Width { get; set; }
      public double Height { get; set; }
      public double Diagonal => Math.Sqrt(Width * Width + Height * Height);
  }
  ```
- **تفاوت با معمولی**: 
  - در خاصیت معمولی، مقدار در Backing Field ذخیره می‌شود.
  - در Computed، مقدار همیشه محاسبه می‌شود (مثل یک متد).

---

## 8. **Backing Fields** (مکانیسم پایه)
- **تعریف**: فیلد‌های **private** که در پس‌زمینه برای ذخیره مقدار خاصیت‌ها استفاده می‌شوند.
- **دو حالت**:
  1. **مخفی (در Auto-Implemented)**: کامپایلر خودش ایجاد می‌کند.
     ```csharp
     public string Name { get; set; } // کامپایلر فیلد مخفی ایجاد می‌کند
     ```
  2. **صریح (در Full Property)**: شما خودتان تعریف می‌کنید.
     ```csharp
     private string _name;
     public string Name { get => _name; set => _name = value; }
     ```
- **چرا مهم است؟** 
  - برای **کنترل دسترسی** به مقدار (مثل اعتبارسنجی).
  - برای **جدا کردن منطق** ذخیره‌سازی از رفتار.

---

## جمع‌بندی و نکات حرفه‌ای
| نوع خاصیت          | کاربرد اصلی                          | نسخه C#   | مثال کاربردی                     |
|---------------------|--------------------------------------|-----------|----------------------------------|
| Auto-Implemented    | سریع‌ترین راه برای خاصیت ساده        | 3.0+      | `public int Id { get; set; }`    |
| Init-only           | اشیاء Immutable                    | 9.0+      | `public string Name { get; init; }` |
| Expression-bodied   | خاصیت‌های محاسباتی کوتاه            | 6.0+      | `public double Area => ...`      |
| Full Property       | اعتبارسنجی/منطق پیچیده             | تمام نسخه‌ها | `private int _age; public int Age { ... }` |

### نکات پیشرفته:
1. **ترکیب Init-only با Records**:
   ```csharp
   public record Person(string Name, int Age); // تمام خاصیت‌ها init-only هستند
   ```
2. **اجتناب از Side Effects در Computed Properties**:
   - مقدار Computed نباید وضعیت شیء را تغییر دهد (مثلاً افزایش یک شمارنده).
3. **Backing Field هوشمند در Auto-Implemented**:
   - کامپایلر برای هر خاصیت، یک فیلد مخفی با نام `_<<PropertyName>>k__BackingField` ایجاد می‌کند (قابل دیدن در IL).

---

✅ **مهم‌ترین اصل**: همیشه **کمترین دسترسی لازم** را فراهم کنید (مثلاً به جای `public set` از `init` یا `private set` استفاده کنید). این کار کمک می‌کند کد شما **قابل اعتمادتر** و **قابل نگهداری‌تر** باشد
