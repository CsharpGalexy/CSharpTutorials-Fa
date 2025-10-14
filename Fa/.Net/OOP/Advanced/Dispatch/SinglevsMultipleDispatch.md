

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [Dispatch چیست؟](#dispatch-چیست)  
3. [Single Dispatch چیست؟](#single-dispatch-چیست)  
4. [Multiple Dispatch چیست؟](#multiple-dispatch-چیست)  
5. [پیاده‌سازی Single Dispatch در C#](#پیاده‌سازی-single-dispatch-در-c)  
6. [چالش Multiple Dispatch در C#](#چالش-multiple-dispatch-در-c)  
7. [راه‌حل‌های شبیه‌سازی Multiple Dispatch در C#](#راه‌حل‌های-شبیه‌سازی-multiple-dispatch-در-c)  
8. [مقایسه و کاربردها](#مقایسه-و-کاربردها)  
9. [جمع‌بندی](#جمع‌بندی)  
10. [منابع معتبر](#منابع-معتبر)

---

## مقدمه

در برنامه‌نویسی شیء‌گرا (OOP)، **Dispatch** به فرآیند انتخاب روش (Method) مناسب برای اجرا در زمان اجرا (Runtime) یا زمان کامپایل (Compile-time) اشاره دارد. در C#، بیشتر موارد از **Single Dispatch** پیروی می‌کنند، اما گاهی نیاز به رفتاری مشابه **Multiple Dispatch** نیز احساس می‌شود. این راهنما به‌صورت گام‌به‌گام و با مثال‌های عملی، این مفاهیم را برای توسعه‌دهندگان مبتدی تا متوسط توضیح می‌دهد.

---

## Dispatch چیست؟

**Dispatch** (یا **Method Dispatch**) فرآیندی است که طی آن سیستم تصمیم می‌گیرد کدام پیاده‌سازی یک متد باید فراخوانی شود. این تصمیم می‌تواند بر اساس نوع شیء فراخوانی‌کننده، نوع پارامترها، یا هر دو باشد.

دو نوع اصلی Dispatch وجود دارد:

- **Single Dispatch**: تصمیم‌گیری فقط بر اساس نوع **شیء فراخوانی‌کننده**.
- **Multiple Dispatch**: تصمیم‌گیری بر اساس نوع **شیء فراخوانی‌کننده** **و** نوع **پارامترهای متد**.

---

## Single Dispatch چیست؟

در **Single Dispatch**، تنها نوع شیء‌ای که متد روی آن فراخوانی می‌شود (یعنی `this`) در زمان اجرا بررسی می‌شود تا متد مناسب انتخاب شود. این روش در اکثر زبان‌های شیء‌گرا مانند C#، Java و C++ پشتیبانی می‌شود.

### ویژگی‌ها:
- پشتیبانی مستقیم توسط زبان.
- استفاده از **Polymorphism** و **Virtual Methods**.
- تصمیم‌گیری فقط بر اساس نوع **گیرنده** (Receiver).

---

## Multiple Dispatch چیست؟

در **Multiple Dispatch**، سیستم نوع **هم گیرنده** و **هم پارامترهای متد** را در زمان اجرا بررسی می‌کند تا متد مناسب را انتخاب کند. این ویژگی در زبان‌هایی مانند **Common Lisp** و **Julia** به‌صورت ذاتی پشتیبانی می‌شود، اما در C# به‌صورت مستقیم وجود ندارد.

### ویژگی‌ها:
- انعطاف‌پذیری بیشتر در پیاده‌سازی الگوهای رفتاری.
- پیچیدگی بیشتر در پیاده‌سازی.
- در C# نیاز به شبیه‌سازی دارد.

---

## پیاده‌سازی Single Dispatch در C#

C# به‌طور پیش‌فرض از **Single Dispatch** پشتیبانی می‌کند. این کار با استفاده از کلمات کلیدی `virtual`، `override` و `abstract` انجام می‌شود.

### مثال:

```csharp
public abstract class Animal
{
    public abstract void MakeSound();
}

public class Dog : Animal
{
    public override void MakeSound() => Console.WriteLine("Woof!");
}

public class Cat : Animal
{
    public override void MakeSound() => Console.WriteLine("Meow!");
}

class Program
{
    static void Main()
    {
        Animal myPet = new Dog();
        myPet.MakeSound(); // خروجی: Woof!
    }
}
```

در اینجا، متد `MakeSound` بر اساس نوع واقعی شیء (`Dog`) در زمان اجرا فراخوانی می‌شود — این **Single Dispatch** است.

---

## چالش Multiple Dispatch در C#

در C#، **Overloading** متد بر اساس نوع پارامترها در **زمان کامپایل** انجام می‌شود (Static Dispatch). بنابراین، اگر از نوع پایه (Base Type) استفاده کنیم، Overload مناسب انتخاب نمی‌شود.

### مثال مشکل‌دار:

```csharp
public class AudioPlayer
{
    public void Play(Animal animal) => Console.WriteLine("Generic sound");
    public void Play(Dog dog) => Console.WriteLine("Woof sound");
    public void Play(Cat cat) => Console.WriteLine("Meow sound");
}

class Program
{
    static void Main()
    {
        Animal myPet = new Dog();
        var player = new AudioPlayer();
        player.Play(myPet); // خروجی: "Generic sound" ❌
    }
}
```

اگرچه `myPet` در واقع یک `Dog` است، اما چون متغیر از نوع `Animal` تعریف شده، کامپایلر متد `Play(Animal)` را انتخاب می‌کند. این **عدم پشتیبانی از Multiple Dispatch** است.

---

## راه‌حل‌های شبیه‌سازی Multiple Dispatch در C#

با وجود اینکه C# از Multiple Dispatch پشتیبانی نمی‌کند، راه‌هایی برای شبیه‌سازی آن وجود دارد:

### 1. استفاده از `dynamic` (از C# 4.0)

```csharp
player.Play((dynamic)myPet); // خروجی: "Woof sound" ✅
```

> ⚠️ **هشدار**: استفاده از `dynamic` عملکرد را کاهش می‌دهد و خطاهای زمان اجرا را افزایش می‌دهد.

### 2. الگوی Visitor (Visitor Pattern)

این الگو به‌صورت صریح از Double Dispatch استفاده می‌کند:

```csharp
public interface IAnimalVisitor
{
    void Visit(Dog dog);
    void Visit(Cat cat);
}

public abstract class Animal
{
    public abstract void Accept(IAnimalVisitor visitor);
}

public class Dog : Animal
{
    public override void Accept(IAnimalVisitor visitor) => visitor.Visit(this);
    public void MakeSound() => Console.WriteLine("Woof!");
}

public class Cat : Animal
{
    public override void Accept(IAnimalVisitor visitor) => visitor.Visit(this);
    public void MakeSound() => Console.WriteLine("Meow!");
}

public class SoundVisitor : IAnimalVisitor
{
    public void Visit(Dog dog) => dog.MakeSound();
    public void Visit(Cat cat) => cat.MakeSound();
}
```

### 3. استفاده از `switch` یا `if-else` بر اساس `GetType()`

```csharp
public void Play(Animal animal)
{
    switch (animal)
    {
        case Dog d: Console.WriteLine("Woof sound"); break;
        case Cat c: Console.WriteLine("Meow sound"); break;
        default: Console.WriteLine("Generic sound"); break;
    }
}
```

> این روش نقض اصل **Open/Closed** است و برای سیستم‌های بزرگ توصیه نمی‌شود.

---

## مقایسه و کاربردها

| ویژگی | Single Dispatch | Multiple Dispatch |
|--------|------------------|--------------------|
| پشتیبانی در C# | ✅ بومی | ❌ (نیاز به شبیه‌سازی) |
| زمان تصمیم‌گیری | Runtime (برای گیرنده) | Runtime (برای گیرنده + پارامترها) |
| عملکرد | بالا | پایین‌تر (در صورت شبیه‌سازی) |
| انعطاف‌پذیری | متوسط | بالا |
| مثال کاربردی | Polymorphism در UI, Games | سیستم‌های Collision, Audio, AI |

---

## جمع‌بندی

- C# به‌طور پیش‌فرض از **Single Dispatch** پشتیبانی می‌کند — این همان چیزی است که در اکثر موارد OOP نیاز دارید.
- **Multiple Dispatch** در C# وجود ندارد، اما می‌توان آن را با `dynamic`، **Visitor Pattern** یا سایر تکنیک‌ها شبیه‌سازی کرد.
- برای پروژه‌های آموزشی، توصیه می‌شود ابتدا Single Dispatch را به‌خوبی یاد بگیرید، سپس در صورت نیاز به راه‌حل‌های پیشرفته‌تر مانند Visitor مراجعه کنید.

---

## منابع معتبر

1. **Microsoft Docs – Polymorphism**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism)

2. **C# Language Specification – Virtual Methods**  
   [https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#virtual-methods](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#virtual-methods)

3. **Eric Lippert’s Blog – Why Doesn’t C# Implement “Double Dispatch”?**  
   [https://ericlippert.com/2004/01/29/why-doesnt-c-implement-double-dispatch/](https://ericlippert.com/2004/01/29/why-doesnt-c-implement-double-dispatch/)

4. **Design Patterns: Elements of Reusable Object-Oriented Software (GoF)** – Visitor Pattern  
   ISBN: 978-0201633610

5. **C# in Depth – Jon Skeet** (فصل مربوط به Dynamic و Dispatch)  
   [https://livebook.manning.com/book/c-sharp-in-depth-fourth-edition](https://livebook.manning.com/book/c-sharp-in-depth-fourth-edition)

6. **Wikipedia – Multiple Dispatch**  
   [https://en.wikipedia.org/wiki/Multiple_dispatch](https://en.wikipedia.org/wiki/Multiple_dispatch)

