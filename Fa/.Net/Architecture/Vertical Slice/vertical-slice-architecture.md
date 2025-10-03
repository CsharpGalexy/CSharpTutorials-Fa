

## فهرست مطالب

1. [مقدمه](#مقدمه)  
2. [Vertical Slice چیست؟](#vertical-slice-چیست)  
3. [تفاوت Vertical Slice با Layered Architecture](#تفاوت-vertical-slice-با-layered-architecture)  
4. [مزایای Vertical Slice Architecture](#مزایای-vertical-slice-architecture)  
5. [چالش‌ها و ملاحظات](#چالش‌ها-و-ملاحظات)  
6. [پیاده‌سازی Vertical Slice در سی‌شارپ](#پیاده‌سازی-vertical-slice-در-سی‌شارپ)  
7. [ساختار پوشه‌بندی پیشنهادی](#ساختار-پوشه‌بندی-پیشنهادی)  
8. [نمونه کد عملی](#نمونه-کد-عملی)  
9. [ابزارها و کتابخانه‌های مرتبط](#ابزارها-و-کتابخانه‌های-مرتبط)  
10. [جمع‌بندی](#جمع‌بندی)  
11. [منابع معتبر](#منابع-معتبر)

---

## Vertical Slice چیست؟

**Vertical Slice Architecture** (معماری برش عمودی) یک الگوی طراحی است که در آن هر **ویژگی (Feature)** یا **عملیات (Use Case)** به‌صورت یک واحد مستقل و خودکفا پیاده‌سازی می‌شود. این واحد شامل تمام لایه‌های مورد نیاز — از **Presentation** تا **Domain** و **Data** — است، اما **فقط برای یک عملیات خاص**.

به جای اینکه تمام کنترلرها را در یک پوشه، تمام سرویس‌ها را در پوشه دیگر و تمام ریپوزیتوری‌ها را در جای سوم جمع کنیم (همان‌طور که در معماری لایه‌ای رایج است)، در Vertical Slice هر **عملیات** یک پوشه جداگانه دارد که تمام قطعات مربوط به آن را در خود جای می‌دهد.

> ✅ **مثال ساده**: عملیات "ایجاد سفارش" شامل یک کنترلر، یک سرویس، یک ریپوزیتوری و مدل‌های مربوطه است — همه در یک پوشه به نام `CreateOrder`.

---

## تفاوت Vertical Slice با Layered Architecture

| ویژگی | Layered Architecture | Vertical Slice Architecture |
|--------|----------------------|------------------------------|
| سازمان‌دهی | بر اساس لایه‌ها (Presentation, Business, Data) | بر اساس عملیات/ویژگی‌ها |
| تغییرات | تغییر در یک ویژگی ممکن است چندین پوشه را تحت تأثیر قرار دهد | تمام تغییرات مربوط به یک ویژگی در یک جا هستند |
| تست‌پذیری | متوسط | بالا (هر slice مستقل است) |
| مقیاس‌پذیری | برای پروژه‌های کوچک مناسب‌تر | برای پروژه‌های متوسط و بزرگ ایده‌آل‌تر |

> 🔍 **نکته**: Vertical Slice یک نوع **Feature-Based Architecture** است و با اصول **SOLID** (به‌ویژه **SRP** و **ISP**) هماهنگ است.

---

## مزایای Vertical Slice Architecture

- ✅ **تمرکز بر کسب‌وکار**: هر برش، یک سناریوی کاربردی واقعی را پیاده‌سازی می‌کند.
- ✅ **کاهش coupling**: هر slice به‌صورت منطقی جدا از دیگران است.
- ✅ **تسهیل در توسعه تیمی**: تیم‌ها می‌توانند روی sliceهای مختلف کار کنند بدون تداخل.
- ✅ **آسان‌سازی تست**: تست واحد و یکپارچه‌سازی برای هر ویژگی ساده‌تر است.
- ✅ **قابلیت حذف آسان**: اگر یک ویژگی دیگر نیاز نباشد، کل پوشه آن را می‌توان حذف کرد.

---

## چالش‌ها و ملاحظات

- ⚠️ **تکرار کد**: ممکن است برخی کدهای عمومی (مثل اعتبارسنجی) در چند slice تکرار شوند.  
  → **راه‌حل**: استفاده از **کتابخانه‌های مشترک** یا **base classes**.
- ⚠️ **یادگیری اولیه**: برای مبتدیان ممکن است در ابتدا گیج‌کننده باشد.
- ⚠️ **مدیریت وابستگی‌ها**: نیاز به تنظیم دقیق **Dependency Injection** دارد.

---

## پیاده‌سازی Vertical Slice در سی‌شارپ

در سی‌شارپ (به‌ویژه در ASP.NET Core)، Vertical Slice معمولاً با استفاده از **MediatR** یا **CQRS** پیاده‌سازی می‌شود، اما الزامی به آن نیست. می‌توانید بدون MediatR نیز این معماری را پیاده‌سازی کنید.

### مراحل کلی:

1. هر **عملیات** (مثل `GetUser`, `CreateOrder`) یک پوشه جداگانه دارد.
2. در هر پوشه، فایل‌های زیر وجود دارند:
   - `Handler.cs` یا `Service.cs`: منطق کسب‌وکار
   - `Request.cs` / `Command.cs`: ورودی‌ها
   - `Response.cs`: خروجی‌ها
   - `Validator.cs` (اختیاری): اعتبارسنجی
   - `Endpoint.cs` یا `Controller.cs`: لایه نمایش

---

## ساختار پوشه‌بندی پیشنهادی

برای ریپوزیتوری آموزش OOP شما، ساختار زیر پیشنهاد می‌شود:

```plaintext
src/
└── YourProject/
    ├── Features/                  ← ریشه Vertical Slices
    │   ├── Users/
    │   │   ├── Create/
    │   │   │   ├── CreateUserRequest.cs
    │   │   │   ├── CreateUserHandler.cs
    │   │   │   ├── CreateUserValidator.cs
    │   │   │   └── CreateUserEndpoint.cs
    │   │   └── Get/
    │   │       ├── GetUserQuery.cs
    │   │       ├── GetUserHandler.cs
    │   │       └── GetUserEndpoint.cs
    │   └── Orders/
    │       └── Create/
    │           ├── CreateOrderRequest.cs
    │           ├── CreateOrderHandler.cs
    │           └── CreateOrderEndpoint.cs
    ├── Shared/                    ← کدهای مشترک (مدل‌ها، ابزارها)
    │   ├── Exceptions/
    │   ├── Validators/
    │   └── Extensions/
    ├── Application/               ← تنظیمات عمومی (مثل DI)
    │   └── DependencyInjection.cs
    └── Program.cs
```

> 💡 **نکته آموزشی**: این ساختار به دانش‌آموزان کمک می‌کند تا بفهمند **هر ویژگی یک موجودیت مستقل است** — که یکی از اصول کلیدی OOP و طراحی نرم‌افزار است.

---

## نمونه کد عملی

### `Features/Users/Create/CreateUserRequest.cs`
```csharp
public record CreateUserRequest(string Name, string Email);
```

### `Features/Users/Create/CreateUserHandler.cs`
```csharp
public class CreateUserHandler
{
    private readonly IUserRepository _userRepository;

    public CreateUserHandler(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    public async Task<User> Handle(CreateUserRequest request)
    {
        // اعتبارسنجی، منطق کسب‌وکار، ذخیره‌سازی
        var user = new User(request.Name, request.Email);
        await _userRepository.AddAsync(user);
        return user;
    }
}
```

### `Features/Users/Create/CreateUserEndpoint.cs`
```csharp
public static class CreateUserEndpoint
{
    public static void MapCreateUserEndpoint(this IEndpointRouteBuilder app)
    {
        app.MapPost("/users", async (CreateUserRequest request, CreateUserHandler handler) =>
        {
            var user = await handler.Handle(request);
            return Results.Created($"/users/{user.Id}", user);
        });
    }
}
```

### `Program.cs`
```csharp
var builder = WebApplication.CreateBuilder(args);

// ثبت سرویس‌ها
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddScoped<CreateUserHandler>();

var app = builder.Build();

// ثبت endpointها
app.MapCreateUserEndpoint();

app.Run();
```

---

## ابزارها و کتابخانه‌های مرتبط

- **[MediatR](https://github.com/jbogard/MediatR)**: برای پیاده‌سازی CQRS و کاهش coupling.
- **[FluentValidation](https://docs.fluentvalidation.net/)**: برای اعتبارسنجی در هر slice.
- **[Minimal APIs](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis)**: برای ساده‌سازی endpointها (در .NET 6+).
- **[AutoMapper](https://docs.automapper.org/)**: برای تبدیل بین مدل‌ها (در صورت نیاز).

> 🔧 **پیشنهاد**: برای پروژه‌های آموزشی، ابتدا بدون MediatR شروع کنید تا مفهوم اصلی را درک کنند، سپس MediatR را معرفی کنید.

---

## جمع‌بندی

Vertical Slice Architecture یک روش مدرن، انعطاف‌پذیر و کسب‌وکارمحور برای سازمان‌دهی کد در پروژه‌های سی‌شارپ است. این معماری:

- با اصول OOP و SOLID هماهنگ است.
- توسعه، تست و نگهداری را ساده‌تر می‌کند.
- برای پروژه‌های آموزشی بسیار مناسب است، چون **هر مفهوم را در یک واحد کامل نشان می‌دهد**.

با استفاده از این ساختار در ریپوزیتوری آموزشی خود، دانش‌آموزان می‌توانند:
- یاد بگیرند چگونه کد را بر اساس **عملیات کاربر** سازمان‌دهی کنند.
- درک بهتری از **جداکردن نگرانی‌ها (Separation of Concerns)** پیدا کنند.
- آماده‌سازی برای کار در پروژه‌های واقعی‌تر با معماری‌های مدرن.

---

## منابع معتبر

1. **Jimmy Bogard (خالق MediatR)** – [Vertical Slice Architecture](https://jimmybogard.com/vertical-slice-architecture/)  
   🔗 https://jimmybogard.com/vertical-slice-architecture/

2. **Microsoft Docs – Minimal APIs**  
   🔗 https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis

3. **Steven van Deursen – Feature Slices vs. Layers**  
   🔗 https://blogs.cuttingedge.it/steven/posts/2011/meanwhile-on-the-query-side-of-my-architecture/

4. **Clean Architecture by Robert C. Martin** – اصول پایه‌ای که Vertical Slice بر آن‌ها استوار است.  
   📘 ISBN: 978-0134494166

5. **.NET Architecture Guidance – Microsoft**  
   🔗 https://learn.microsoft.com/en-us/dotnet/architecture/

6. **Vertical Slice Architecture with ASP.NET Core – YouTube (Nick Chapsas)**  
   🔗 https://www.youtube.com/watch?v=5kYqZcQq4uE
