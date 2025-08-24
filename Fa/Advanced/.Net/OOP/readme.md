
![]./OOP_Roadmap.jpg)

# 🟢 بخش اول: مفاهیم پایه (Basics)
#### 1.	مقدمه و مفاهیم پایه
o	What is OOP?
o	Principles of OOP: Encapsulation, Inheritance, Polymorphism, Abstraction
o	OOP vs Procedural Programming
o	Advantages of OOP in C#
#### 2.	کلاس‌ها و اشیا (Classes & Objects)
o	Class Definition
o	Object Instantiation
o	this Keyword
o	Object Initializers
o	new Modifier
#### 3.	فیلدها و ویژگی‌ها (Fields & Properties)
o	Fields (Instance, Static)
o	Properties: Auto-Implemented, Full (get/set), Read-only/Write-only, Init-only, Expression-bodied, Computed, Backing Fields
#### 4.	سازنده‌ها و تخریب‌کننده‌ها (Constructors & Destructors)
o	Default, Parameterized, Overloading
o	Static Constructor
o	Private Constructor
o	Destructor (~ClassName)
o	Finalizers and Garbage Collection
#### 5.	مدیریت دسترسی (Access Modifiers)
o	public, private, protected, internal, protected internal, private protected
o	Assembly and Namespace Scope
o	Best Practices
6.	انکپسولاسیون (Encapsulation)
o	Information Hiding
o	Public Interface vs Private Implementation
o	Property Validation
________________________________________
# 🟡 بخش دوم: مفاهیم متوسط (Intermediate)
#### 7.	وراثت (Inheritance)
o	Base/Derived, base Keyword
o	Method Overriding (virtual, override, sealed override)
o	Constructor Chaining
o	Multi-level, Hierarchical Inheritance
o	C# Limitation: No Multiple Inheritance
#### 8.	چندریختی (Polymorphism)
o	Compile-time: Method/Operator/Constructor Overloading
o	Run-time: Method Overriding, Abstract Methods, Dynamic Binding
o	انواع: Subtype, Parametric (Generics), Ad-hoc
#### 9.	ابستراکت کلاس‌ها (Abstract Classes)
o	Abstract Methods & Properties
o	Cannot be Instantiated
o	Can Have Concrete Methods
o	Use Cases
#### 10.	اینترفیس‌ها (Interfaces)
•	Definition & Implementation
•	Multiple Interfaces
•	Default Interface Methods (C# 8+)
•	IComparable, IEnumerable, IDisposable …
•	Marker Interfaces
•	Interface Inheritance
#### 11.	تفاوت‌های کلیدی
•	Abstract Class vs Interface
•	Abstraction vs Encapsulation vs Information Hiding
•	When to Use Which?
#### 12.	کلاس‌های استاتیک (Static Classes)
•	Only Static Members
•	Utility Classes
•	static Constructors
#### 13.	اعضا و متدهای استاتیک (Static Members)
•	Fields, Properties, Methods
•	Memory Considerations
•	Thread Safety
#### 14.	مخفی‌سازی متدها (Method Hiding)
•	new Keyword
•	Hiding vs Overriding
#### 15.	چندوجهی نوع (Type Variance)
•	Covariance (out T)
•	Contravariance (in T)
•	Invariance
#### 16.	انواع کلاس‌های خاص
•	Sealed, Partial, Static
•	Record Types (C# 9+): Immutability, Value Equality, with keyword
•	Anonymous Types
#### 17.	توسعه کلاس‌ها (Extension Methods)
•	Static Methods as Instance Methods
•	this Parameter
•	LINQ Examples
#### 18.	متدهای ناشناس و لامبدا (Lambda & Anonymous Methods)
•	Anonymous Methods
•	Lambda Expressions
•	Expression Trees (intro)
•	Use with LINQ, Events, Delegates
#### 19.	ایندکسرها (Indexers)
•	Custom Indexing
•	Syntax
•	Overloading Indexers
#### 20.	رویدادها و دلیگیت‌ها (Events & Delegates)
•	Delegates (Func, Action, Predicate)
•	Multicast Delegates
•	Events, event Keyword
•	EventHandler Pattern
•	Custom Event Args
________________________________________
#  بخش سوم: مفاهیم پیشرفته (Advanced)🔴 بخش سوم: مفاهیم پیشرفته (Advanced)
#### 21.	واکنش‌گرایی و Dispatching
•	Static Dispatch (Early Binding)
•	Dynamic Dispatch (Late Binding)
•	Single vs Multiple Dispatch
•	Virtual Method Table (VMT)
#### 22.	بازتاب (Reflection)
•	Inspecting Types at Runtime
•	Creating Instances Dynamically
•	Invoking Methods via Reflection
•	Attributes and Reflection
•	Use Cases: Serialization, DI, ORM
#### 23.	صیغه‌ها (Attributes)
•	Built-in Attributes ([Obsolete], [Serializable], [DllImport], …)
•	Custom Attributes
•	Attribute Usage & Targets
•	Reading Attributes via Reflection
#### 24.	انواع ارجاعی قابل null (Nullable Reference Types)
•	C# 8+ Feature
•	string? vs string
•	Enabling Nullable Context
•	Best Practices
#### 25.	ابستراکشن و تخصصی‌سازی
•	Procedural Abstraction
•	Data Abstraction
•	Specification & Parameterization
•	Designing for Extensibility
#### 26.	مفاهیم پیشرفته و مدرن
•	Records with Deconstruction (C# 9+)
•	Init-only setters
•	Pattern Matching (is, switch)
•	Primary Constructors (C# 12)
•	File-scoped Namespaces
•	Top-level Statements
#### 27.	بهترین روش‌ها و طراحی (Design Principles)
•	SOLID Principles
•	Composition over Inheritance
•	Law of Demeter
•	Dependency Injection (Intro)
•	Design Patterns (Factory, Singleton, Strategy, Observer, Adapter, Decorator)
#### 28.	امنیت و عملکرد
•	Immutable Objects
•	Defensive Copying
•	Performance of Virtual Methods
•	Sealing for Security

