#  اشیاء در Csharp



## 🔹 شیء (Object) چیست؟
شیء یک **نمونه (Instance)** از یک کلاس است. وقتی یک شیء از کلاس می‌سازیم، حافظه‌ای برای آن اختصاص داده می‌شود.

### 🔸 ساخت شیء با کلمه کلیدی `new`
```csharp
Person person1 = new Person();  
person1.Name = "الیسا";  
person1.Age = 28;  
person1.Introduce();  
// خروجی: سلام! من الیسا هستم و 28 سالمه.
```

### 🔸 استفاده از Initializer برای ساخت شیء
```csharp
Person person2 = new Person {  
  Name = "بابک",  
  Age = 35  
};  
person2.Introduce();
```

---

## 🔹 سازنده (Constructor)
سازنده یک **متد خاص** است که هنگام ایجاد شیء فراخوانی می‌شود و برای مقداردهی اولیه استفاده می‌شود.

```csharp
public class Person {  
  public string Name { get; set; }  
  public int Age { get; set; }  

  public Person(string name, int age) {  
    Name = name;  
    Age = age;  
  }  

  public void Introduce() => Console.WriteLine($"من {Name} هستم، {Age} ساله.");  
}
```

### استفاده از سازنده:
```csharp
Person p = new Person("سارا", 22);  
p.Introduce();  
// خروجی: من سارا هستم، 22 ساله.
```
