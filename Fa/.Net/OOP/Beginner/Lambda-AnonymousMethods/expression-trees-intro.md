
## فهرست مطالب

1. [مقدمه: Expression Tree چیست؟](#1-مقدمه-expression-tree-چیست)
2. [تفاوت Lambda Expression و Expression Tree](#2-تفاوت-lambda-expression-و-expression-tree)
3. [چرا Expression Trees مهم هستند؟](#3-چرا-expression-trees-مهم-هستند)
4. [ساخت Expression Tree در C#](#4-ساخت-expression-tree-در-c)
   - [4.1. با استفاده از Lambda Expression](#41-با-استفاده-از-lambda-expression)
   - [4.2. با استفاده از Expression API](#42-با-استفاده-از-expression-api)
5. [کامپایل و اجرا](#5-کامپایل-و-اجرا)
6. [مثال‌های کاربردی](#6-مثالهای-کاربردی)
7. [محدودیت‌ها](#7-محدودیتها)
8. [جمع‌بندی](#8-جمعبندی)
9. [منابع معتبر](#9-منابع-معتبر)

---

## 1. مقدمه: Expression Tree چیست؟

در C#، **Expression Tree** (درخت عبارت) یک ساختار داده‌ای است که **کد** را به صورت **درختی از گره‌ها (nodes)** نمایش می‌دهد. برخلاف کد کامپایل‌شده که مستقیماً اجرا می‌شود، Expression Tree اجازه می‌دهد کد **در زمان اجرا (runtime)** تحلیل، تغییر یا ترجمه شود.

این ویژگی به‌ویژه در سناریوهایی مانند **LINQ to SQL** بسیار کاربردی است، جایی که نیاز است عبارت‌های LINQ به SQL تبدیل شوند.

---

## 2. تفاوت Lambda Expression و Expression Tree

در C#، یک **Lambda Expression** می‌تواند به دو شکل مختلف کامپایل شود:

| نوع | تعریف | نوع داده |
|------|--------|----------|
| **Delegate** | کدی که مستقیماً اجرا می‌شود | `Func<T>`, `Action<T>` |
| **Expression Tree** | نمایش ساختاری کد به صورت درخت | `Expression<Func<T>>` |

### مثال:

```csharp
// Lambda به عنوان Delegate
Func<int, bool> isEven = x => x % 2 == 0;

// Lambda به عنوان Expression Tree
Expression<Func<int, bool>> isEvenExpr = x => x % 2 == 0;
```

> 🔍 **نکته**: فقط **عبارات (expressions)** قابل تبدیل به Expression Tree هستند. دستورات (statements) مانند `if`, `for`, یا بلوک‌های `{ }` پشتیبانی نمی‌شوند.

---

## 3. چرا Expression Trees مهم هستند؟

Expression Trees امکانات زیر را فراهم می‌کنند:

- **تحلیل کد در زمان اجرا**: مثلاً برای لاگ‌کردن یا اعتبارسنجی.
- **تولید کد پویا**: مانند ساخت ORMها (مثل Entity Framework).
- **ترجمه به زبان دیگر**: مثلاً تبدیل LINQ به SQL.
- **بهینه‌سازی پویا**: تغییر رفتار توابع بر اساس ورودی.

---

## 4. ساخت Expression Tree در C#

### 4.1. با استفاده از Lambda Expression

ساده‌ترین راه ساخت Expression Tree، استفاده از **lambda expression** و تخصیص آن به یک متغیر از نوع `Expression<TDelegate>` است:

```csharp
Expression<Func<int, int, int>> addExpr = (x, y) => x + y;
```

این کد یک درخت عبارت ایجاد می‌کند که عمل جمع دو عدد را نشان می‌دهد.

### 4.2. با استفاده از Expression API

برای کنترل کامل‌تر، می‌توان از کلاس‌های موجود در فضای نام `System.Linq.Expressions` استفاده کرد:

```csharp
using System.Linq.Expressions;

ParameterExpression paramX = Expression.Parameter(typeof(int), "x");
ParameterExpression paramY = Expression.Parameter(typeof(int), "y");
BinaryExpression body = Expression.Add(paramX, paramY);
Expression<Func<int, int, int>> addExpr = 
    Expression.Lambda<Func<int, int, int>>(body, paramX, paramY);
```

این روش برای ساخت عبارات پیچیده یا پویا بسیار مفید است.

---

## 5. کامپایل و اجرا

Expression Tree خودش قابل اجرا نیست. برای اجرا، باید آن را **کامپایل** کرد:

```csharp
var compiled = isEvenExpr.Compile(); // تبدیل به Func<int, bool>
bool result = compiled(4); // true
```

> ⚠️ **هشدار**: کامپایل هزینه‌بر است. بهتر است نتیجه را کش کنید اگر چندین بار استفاده می‌شود.

---

## 6. مثال‌های کاربردی

### 6.1. لاگ‌کردن نام متغیر (مانند `nameof` ولی پویاتر)

```csharp
public static string GetPropertyName<T>(Expression<Func<T>> expr)
{
    return (expr.Body as MemberExpression)?.Member.Name;
}

// استفاده:
string name = GetPropertyName(() => customer.Name); // "Name"
```

### 6.2. ساخت فیلتر پویا برای LINQ

```csharp
public static Expression<Func<T, bool>> And<T>(
    Expression<Func<T, bool>> expr1,
    Expression<Func<T, bool>> expr2)
{
    var parameter = Expression.Parameter(typeof(T));
    var body = Expression.AndAlso(
        Expression.Invoke(expr1, parameter),
        Expression.Invoke(expr2, parameter)
    );
    return Expression.Lambda<Func<T, bool>>(body, parameter);
}
```

---

## 7. محدودیت‌ها

- فقط **عبارات (expressions)** پشتیبانی می‌شوند — نه دستورات (statements).
- نمی‌توان از **متغیرهای محلی** (local variables) به راحتی استفاده کرد (مگر با closure).
- عملیات‌هایی مانند `try-catch`, `await`, `yield return` پشتیبانی نمی‌شوند.
- عملکرد کامپایل در زمان اجرا ممکن است کند باشد.

---

## 8. جمع‌بندی

Expression Trees یکی از قدرتمندترین ویژگی‌های C# برای **متا‌برنامه‌نویسی** هستند. آن‌ها به شما اجازه می‌دهند کد را نه به عنوان دستورالعمل، بلکه به عنوان **داده** در نظر بگیرید. این ویژگی پایه‌ی فناوری‌هایی مانند LINQ to Entities، AutoMapper و بسیاری از فریم‌ورک‌های مدرن است.

برای توسعه‌دهندگان OOP، درک Expression Trees به درک عمیق‌تری از **انعطاف‌پذیری** و **قدرت بازتاب (reflection)** در .NET کمک می‌کند.

---

## 9. منابع معتبر

1. **Microsoft Learn – Expression Trees (C#)**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/expression-trees/](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/expression-trees/)

2. **C# Language Specification – Anonymous Functions and Expression Trees**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/expressions#anonymous-function-expressions](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/expressions#anonymous-function-expressions)

3. **Jon Skeet – C# in Depth (Chapter on Expression Trees)**  
   Manning Publications, 4th Edition – [https://www.manning.com/books/c-sharp-in-depth-fourth-edition](https://www.manning.com/books/c-sharp-in-depth-fourth-edition)

4. **Stack Overflow Documentation (Archived but valuable)**  
   [https://stackoverflow.com/documentation/c%23/756/expression-trees](https://web.archive.org/web/20171201000000*/https://stackoverflow.com/documentation/c%23/756/expression-trees)

5. **Entity Framework Source Code (نمونه کاربرد واقعی)**  
   [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)

