# 🚀 OOP Roadmap in C#

![](../OOP_Roadmap.jpg)

این مستند یک نقشه‌ی راه جامع برای یادگیری **برنامه‌نویسی شی‌گرا (OOP)** در زبان C# است.  
مطالب در سه سطح **پایه، متوسط و پیشرفته** دسته‌بندی شده‌اند.

---


## 📑 فهرست مطالب (Table of Contents)

- [1. مقدمه و مفاهیم پایه](#1-مقدمه-و-مفاهیم-پایه)
- [2. کلاس‌ها و اشیا (Classes & Objects)](#2-کلاسها-و-اشیا-classes--objects)
- [3. فیلدها و ویژگی‌ها (Fields & Properties)](#3-فیلدها-و-ویژگیها-fields--properties)
- [4. سازنده‌ها و تخریب‌کننده‌ها (Constructors & Destructors)](#4-سازندهها-و-تخریبکنندهها-constructors--destructors)
- [5. مدیریت دسترسی (Access Modifiers)](#5-مدیریت-دسترسی-access-modifiers)
- [6. انکپسولاسیون (Encapsulation)](#6-انکپسولاسیون-encapsulation)
- [7. وراثت (Inheritance)](#7-وراثت-inheritance)
- [8. چندریختی (Polymorphism)](#8-چندریختی-polymorphism)
- [9. ابستراکت کلاس‌ها (Abstract Classes)](#9-ابستراکت-کلاسه-abstract-classes)
- [10. اینترفیس‌ها (Interfaces)](#10-اینترفیسها-interfaces)
- [11. تفاوت‌های کلیدی](#11-تفاوتهای-کلیدی)
- [12. کلاس‌های استاتیک (Static Classes)](#12-کلاسه-استاتیک-static-classes)
- [13. اعضا و متدهای استاتیک (Static Members)](#13-اعضا-و-متدهای-استاتیک-static-members)
- [14. مخفی‌سازی متدها (Method Hiding)](#14-مخفیسازی-متدها-method-hiding)
- [15. چندوجهی نوع (Type Variance)](#15-چندوجهی-نوع-type-variance)
- [16. انواع کلاس‌های خاص](#16-انواع-کلاسه-خاص)
- [17. توسعه کلاس‌ها (Extension Methods)](#17-توسعه-کلاسه-extension-methods)
- [18. متدهای ناشناس و لامبدا (Lambda & Anonymous Methods)](#18-متدهای-ناشناس-و-لامبدا-lambda--anonymous-methods)
- [19. ایندکسرها (Indexers)](#19-ایندکسرها-indexers)
- [20. رویدادها و دلیگیت‌ها (Events & Delegates)](#20-رویدادها-و-دلیگیتها-events--delegates)

---

# 🟢 بخش اول: مفاهیم پایه (Basics)

### 1. مقدمه و مفاهیم پایه
- [What is OOP?](./BasicsConcepts/WhatـisـOOP.md)
- Principles of OOP: Encapsulation, Inheritance, Polymorphism, Abstraction  
- [OOP vs Procedural Programming ](./BasicsConcepts/OOP_ProceduralProgramming.md)
- [Advantages of OOP in C#](./BasicsConcepts/Advantages_of_OOP.md)
### 2. کلاس‌ها و اشیا (Classes & Objects)
- [Class Definition ](./Class_Object/Definition_Class.md)  
- [ Object Instantiation ](./Class_Object/Object_Instantiation.md)  
- [`this` Keyword  ](./Class_Object/this_Keyword.md)  
- [ Object Initializers ](./Class_Object/ObjectـInitializers.md)  
- [  `new` Modifier  ](./Class_Object/new_Keyword_Modifier.md)  

### 3. فیلدها و ویژگی‌ها (Fields & Properties)
- Fields (Instance, Static)  
- [ Properties: Auto-Implemented, Full (get/set), Read-only/Write-only, Init-only, Expression-bodied, Computed, Backing Fields   ](./Class_Object/Properties.md)  

### 4. سازنده‌ها و تخریب‌کننده‌ها (Constructors & Destructors)
- Default, Parameterized, Overloading  
- Static Constructor  
- Private Constructor  
- Destructor (`~ClassName`)  
- Finalizers & Garbage Collection  

### 5. مدیریت دسترسی (Access Modifiers)
- [public, private, protected, internal, protected internal, private protected ](./Access_Modifiers/Public_Private_Internal_Protected.md)
 - Assembly & Namespace Scope  
- Best Practices  

### 6. انکپسولاسیون (Encapsulation)
- Information Hiding  
- [Public Interface vs Private Implementation](./Encapsulation/Public_Interface_vs_Private_Implementation.md)
- [Property Validation ](./Encapsulation/Property_Validation.md)
 



### 7. وراثت (Inheritance)
- Base/Derived, `base` Keyword  
- Method Overriding (`virtual`, `override`, `sealed override`)  
- Constructor Chaining  
- Multi-level & Hierarchical Inheritance
- [Limitation: No Multiple Inheritance](./Inheritance/Limitation_No_Multiple_Inheritance.md)


### 8. چندریختی (Polymorphism)
- Compile-time: Method/Operator/Constructor Overloading  
- Run-time: Method Overriding, Abstract Methods, Dynamic Binding  
- Types: Subtype, Parametric (Generics), Ad-hoc  

### 9. ابستراکت کلاس‌ها (Abstract Classes)
- Abstract Methods & Properties  
- Cannot be Instantiated  
- Can Have Concrete Methods  
- Use Cases  

### 10. اینترفیس‌ها (Interfaces)
- Definition & Implementation  
- Multiple Interfaces  
- Default Interface Methods (C# 8+)  
- Common Interfaces: `IComparable`, `IEnumerable`, `IDisposable` …  
- Marker Interfaces  
- Interface Inheritance  

### 11. تفاوت‌های کلیدی
- Abstract Class vs Interface  
- Abstraction vs Encapsulation vs Information Hiding  
- When to Use Which?  

### 12. کلاس‌های استاتیک (Static Classes)
- Only Static Members  
- Utility Classes  
- static Constructors  

### 13. اعضا و متدهای استاتیک (Static Members)
- Fields, Properties, Methods  
- Memory Considerations  
- Thread Safety  

### 14. مخفی‌سازی متدها (Method Hiding)
- `new` Keyword  
- Hiding vs Overriding  

### 15. چندوجهی نوع (Type Variance)
- Covariance (`out T`)  
- Contravariance (`in T`)  
- Invariance  

### 16. انواع کلاس‌های خاص
- Sealed, Partial, Static  
- Record Types (C# 9+): Immutability, Value Equality, `with` keyword  
- Anonymous Types  

### 17. توسعه کلاس‌ها (Extension Methods)
- Static Methods as Instance Methods  
- `this` Parameter  
- LINQ Examples  

### 18. متدهای ناشناس و لامبدا (Lambda & Anonymous Methods)
- Anonymous Methods  
- Lambda Expressions  
- Expression Trees (intro)  
- Use with LINQ, Events, Delegates  

### 19. ایندکسرها (Indexers)
- Custom Indexing  
- Syntax  
- Overloading Indexers  

### 20. رویدادها و دلیگیت‌ها (Events & Delegates)
- Delegates (`Func`, `Action`, `Predicate`)  
- Multicast Delegates  
- Events, `event` Keyword  
- EventHandler Pattern  
- Custom Event Args  




---

📌 این نقشه راه برای یادگیری **OOP در C#** طراحی شده است و می‌تواند به عنوان یک **راهنمای آموزشی** یا **منبع مرجع** مورد استفاده قرار گیرد.
