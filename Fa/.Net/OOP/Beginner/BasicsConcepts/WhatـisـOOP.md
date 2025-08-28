# آموزش OOP (برنامه‌نویسی شیءگرا)

## فهرست مطالب
- [پس OOP چیه؟](#پس-oop-چیه)
- [🟡 هر شیء (Object) دو چیز مهم داره](#-🟡-هر-شیء-(Object)-دو-چیز-مهم-داره)
  - [1. ویژگی (Property)](#1-ویژگی-property)
  - [2. عمل (Method)](#2-عمل-method)
- [🔵 اصل مهم OOP (خیلی ساده)](#🔵-اصل-مهم-oop-خیلی-ساده)
  - [1. کلاس (Class)](#1-کلاس-class)
  - [2. شیء (Object)](#2-شیء-object)

---

#### پس OOP چیه؟

OOP یعنی برنامه‌نویسی به سبکی که دنیای واقعی رو شبیه‌سازی می‌کنه.
ما چیزهایی مثل ماشین، کاربر، حساب بانکی، دانش‌آموز و ... رو به شکل اشیاء (Objects) در برنامه می‌سازیم.

🔹 مثال در #C
```csharp
// مدل‌سازی یک ماشین در برنامه
class Car
{
    public string Color { get; set; }
    public string Model { get; set; }
    public int Speed { get; set; }

    public void Drive()
    {
        Console.WriteLine($"{Color} {Model} در حال حرکت است با سرعت {Speed} کیلومتر!");
    }
}
```

#### 🟡 هر شیء (Object) دو چیز مهم داره:
###### 1. ویژگی (Property)

مشخصات یا داده‌های مربوط به اون شیء.

📌 مثال: رنگ ماشین، نام کاربر، موجودی حساب بانکی.

🔹 مثال در #C
```csharp
class BankAccount
{
    public string Owner { get; set; }
    public double Balance { get; set; }
}

```
```csharp
BankAccount acc = new BankAccount();
acc.Owner = "علی";
acc.Balance = 5000;

Console.WriteLine($"صاحب حساب: {acc.Owner}, موجودی: {acc.Balance}");
```

###### 2. عمل (Method)

کارها یا رفتارهایی که شیء می‌تونه انجام بده.

📌 مثال: حرکت کردن ماشین، ورود به سیستم، واریز یا برداشت پول از حساب.

🔹 مثال در #C
```csharp
class BankAccount
{
    public string Owner { get; set; }
    public double Balance { get; set; }

    // متد واریز
    public void Deposit(double amount)
    {
        Balance += amount;
        Console.WriteLine($"{amount} تومان واریز شد. موجودی جدید: {Balance}");
    }

    // متد برداشت
    public void Withdraw(double amount)
    {
        Balance -= amount;
        Console.WriteLine($"{amount} تومان برداشت شد. موجودی جدید: {Balance}");
    }
}

```
```csharp
BankAccount acc = new BankAccount { Owner = "علی", Balance = 5000 };
acc.Deposit(2000);   // ✅ واریز
acc.Withdraw(1000);  // ✅ برداشت
```

#### 🔵  اصل مهم OOP (خیلی ساده):
###### 1. کلاس (Class)

کلاس مثل یه طرح کلی یا قالبه.
مثلاً: "طرح ماشین" که می‌گه هر ماشین باید رنگ، مدل و سرعت داشته باشه.
بعد با استفاده از این طرح، می‌تونیم ماشین‌های واقعی بسازیم.

🔹 مثال در #C
```csharp
class Car
{
    public string Color { get; set; }
    public string Model { get; set; }
    public int Speed { get; set; }
}
```

###### 2. شیء (Object)

یه نمونه واقعی ساخته‌شده از کلاس.
مثلاً: یه ماشین قرمز پراید با سرعت ۸۰ کیلومتر بر ساعت.

🔹 مثال در #C
```csharp
Car myCar = new Car();
myCar.Color = "قرمز";
myCar.Model = "پراید";
myCar.Speed = 80;

Console.WriteLine($"{myCar.Model} {myCar.Color} با سرعت {myCar.Speed} کیلومتر حرکت می‌کند.");
```
