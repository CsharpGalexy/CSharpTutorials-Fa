

## فهرست مطالب

1. [مقدمه](#مقدمه)
2. [متدهای ناشناس (Anonymous Methods)](#متدهای-ناشناس-anonymous-methods)
   - [تعریف](#تعریف)
   - [نحو (Syntax)](#نحو-syntax)
   - [مثال کاربردی](#مثال-کاربردی)
3. [عبارات لامبدا (Lambda Expressions)](#عبارات-لامبدا-lambda-expressions)
   - [تعریف](#تعریف-1)
   - [نحو (Syntax)](#نحو-syntax-1)
   - [انواع عبارات لامبدا](#انواع-عبارات-لامبدا)
   - [مثال‌های کاربردی](#مثالهای-کاربردی)
4. [تفاوت‌های کلیدی بین Anonymous Methods و Lambda Expressions](#تفاوت‌های-کلیدی-بین-anonymous-methods-و-lambda-expressions)
5. [استفاده در LINQ و Delegateها](#استفاده-در-linq-و-delegateها)
6. [نکات پیشرفته (برای سطح متوسط)](#نکات-پیشرفته-برای-سطح-متوسط)
7. [جمع‌بندی](#جمع‌بندی)
8. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در C#، گاهی نیاز داریم توابعی را تعریف کنیم که فقط یک‌بار استفاده می‌شوند یا به عنوان آرگومان به متد دیگری ارسال می‌شوند. برای این کار، دو راهکار اصلی وجود دارد:

- **متدهای ناشناس (Anonymous Methods)**
- **عبارات لامبدا (Lambda Expressions)**

هر دو این مفاهیم به شما امکان می‌دهند کدی را بدون نام‌گذاری تابع (یعنی بدون تعریف یک متد جداگانه با نام) بنویسید. این ویژگی‌ها به‌ویژه در برنامه‌نویسی تابعی (Functional Programming) و کار با LINQ بسیار کاربردی هستند.

---

## متدهای ناشناس (Anonymous Methods)

### تعریف

متدهای ناشناس، بلوک‌هایی از کد هستند که بدون نام تعریف می‌شوند و معمولاً برای پیاده‌سازی delegateها استفاده می‌شوند. این ویژگی از نسخه‌ی **C# 2.0** معرفی شد.

### نحو (Syntax)

```csharp
delegate(پارامترها)
{
    // بدنه‌ی متد
}
```

### مثال کاربردی

```csharp
// تعریف یک delegate
public delegate void MessageHandler(string message);

class Program
{
    static void Main()
    {
        // استفاده از متد ناشناس
        MessageHandler handler = delegate(string msg)
        {
            Console.WriteLine("پیام: " + msg);
        };

        handler("سلام دنیا!");
    }
}
```

> 💡 **نکته**: متدهای ناشناس می‌توانند به متغیرهای محلی (local variables) در متد اطرافشان دسترسی داشته باشند (این پدیده به نام **Closure** شناخته می‌شود).

---

## عبارات لامبدا (Lambda Expressions)

### تعریف

عبارات لامبدا، نحوی فشرده‌تر و خواناتر برای نوشتن متدهای ناشناس هستند. این ویژگی از نسخه‌ی **C# 3.0** معرفی شد و امروزه جایگزین اصلی متدهای ناشناس محسوب می‌شود.

### نحو (Syntax)

```csharp
(پارامترها) => عبارت یا بلوک دستوری
```

- `=>` عملگر لامبدا نام دارد.
- اگر بدنۀ لامبدا فقط یک عبارت باشد، نیازی به آکولاد نیست.
- اگر بدنۀ لامبدا شامل چند دستور باشد، باید داخل `{}` قرار گیرد.

### انواع عبارات لامبدا

1. **Expression Lambda**  
   وقتی بدنه فقط یک عبارت است:
   ```csharp
   x => x * x
   ```

2. **Statement Lambda**  
   وقتی بدنه شامل چند دستور است:
   ```csharp
   (x, y) => { 
       var sum = x + y;
       Console.WriteLine(sum);
       return sum;
   }
   ```

### مثال‌های کاربردی

#### مثال ۱: استفاده در لیست

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0).ToList();
// خروجی: [2, 4]
```

#### مثال ۲: تعریف delegate با لامبدا

```csharp
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(3, 4)); // خروجی: 7
```

#### مثال ۳: لامبدای چندخطی

```csharp
Action<string> greet = name =>
{
    Console.WriteLine("درود!");
    Console.WriteLine($"خوش آمدی، {name}!");
};

greet("علی");
```

---

## تفاوت‌های کلیدی بین Anonymous Methods و Lambda Expressions

| ویژگی | Anonymous Method | Lambda Expression |
|--------|------------------|-------------------|
| معرفی شده در | C# 2.0 | C# 3.0 |
| نحو نوشتاری | طولانی‌تر و کمتر خوانا | فشرده و خواناتر |
| پشتیبانی از Infer Type | ❌ | ✅ (با `var` یا نوع مشخص) |
| قابلیت تبدیل به Expression Tree | ❌ | ✅ (بسیار مهم در LINQ to SQL) |
| استفاده در LINQ | محدود | کاملاً پشتیبانی‌شده |

> ✅ **توصیه**: همیشه از **Lambda Expressions** استفاده کنید مگر در موارد بسیار خاص که نیاز به سازگاری با کدهای قدیمی داشته باشید.

---

## استفاده در LINQ و Delegateها

Lambda Expressions به‌طور گسترده در **LINQ (Language Integrated Query)** استفاده می‌شوند:

```csharp
var result = employees
    .Where(e => e.Salary > 5000)
    .OrderBy(e => e.Name)
    .Select(e => e.Name);
```

همچنین با delegateهای داخلی مانند `Action`, `Func`, و `Predicate` کار می‌کنند:

- `Action<T>`: متدی بدون خروجی
- `Func<T, TResult>`: متدی با خروجی
- `Predicate<T>`: متدی که `bool` برمی‌گرداند

---

## نکات پیشرفته (برای سطح متوسط)

### 1. Closure (بستار)

لامبداها می‌توانند به متغیرهای خارج از محدوده‌ی خود دسترسی داشته باشند:

```csharp
int factor = 2;
Func<int, int> multiplier = n => n * factor;
Console.WriteLine(multiplier(5)); // 10
```

### 2. Expression Trees

لامبداها می‌توانند به `Expression<TDelegate>` تبدیل شوند — این ویژگی پایه‌ی LINQ to SQL است:

```csharp
Expression<Func<int, bool>> expr = x => x > 5;
// این کد به جای اجرا، ساختار درختی عبارت را نگه می‌دارد.
```

### 3. async Lambda

از C# 5.0 به بعد، می‌توان لامبداها را async تعریف کرد:

```csharp
Func<Task> asyncLambda = async () =>
{
    await Task.Delay(1000);
    Console.WriteLine("تکمیل شد!");
};
```

---

## جمع‌بندی

- **متدهای ناشناس** راهی برای نوشتن توابع بدون نام در C# 2.0 بودند.
- **عبارات لامبدا** جایگزین مدرن‌تر، خواناتر و قدرتمندتری هستند که از C# 3.0 پشتیبانی می‌شوند.
- لامبداها در LINQ، event handling، و برنامه‌نویسی تابعی کاربرد گسترده‌ای دارند.
- برای کدهای جدید، همیشه از **Lambda Expressions** استفاده کنید.

---

## منابع معتبر

1. **Microsoft Learn – Lambda Expressions (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-expressions)

2. **Microsoft Learn – Anonymous Methods (C#)**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods)

3. **C# in Depth – Jon Skeet** (فصل‌های مربوط به delegate و لامبدا)  
   🔗 [https://csharpindepth.com/](https://csharpindepth.com/)

4. **.NET Documentation – Delegates and Lambdas**  
   🔗 [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

5. **C# Language Specification (ECMA-334)**  
   🔗 [https://www.ecma-international.org/publications-and-standards/standards/ecma-334/](https://www.ecma-international.org/publications-and-standards/standards/ecma-334/)

