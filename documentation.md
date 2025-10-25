# Software Quality Guide: Code Smells, Principles and Patterns

This guide is written for developers who want to improve the quality of their code.  It explains common **code smells**, fundamental design principles and key **design patterns** using clear language and simple Java examples.  The examples show both problematic designs and how to refactor them.  At the end you'll find a brief explanation of the numbered citations used throughout this document.

---

## 1 Understanding Code Smells

Code smells are symptoms of deeper design problems.  Recognizing them early allows you to refactor before your code base becomes hard to maintain.  The following categories describe typical smells and how to address them.

### 1.1 Bloaters – When Code Grows Too Large

**Bloaters** are classes, methods or parameter lists that have become excessively large:

* **Long Method:** A long method tries to do too much.  Keeping methods short (around ten lines) makes them easier to read and test.  _Refactoring tip:_ Extract portions into private helper methods.

* **Large Class:** A class that holds many fields and methods often violates the single responsibility principle.  Break it into multiple classes, each focused on a single responsibility.

* **Primitive Obsession:** Using primitive types (e.g., `String`, `int`) to represent domain concepts results in scattered validation and no encapsulation.  Replace them with small classes (value objects) that validate and encapsulate the data.

* **Long Parameter List:** When a method accepts more than about three parameters, it becomes unclear what each argument represents.  Group related parameters into objects or use the builder pattern for optional parameters.

* **Data Clumps:** Groups of variables that always appear together hint at a missing object.  Encapsulate them into a class.

#### Example: Splitting a Long Method

The following `UserService` method registers a user and sends notifications.  It is long and hard to test:

```java
// PROBLEM: one method handles registration, validation and notifications
public class UserService {
    public void registerUser(String email, String password) {
        // validate
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
        if (password.length() < 8) {
            throw new IllegalArgumentException("Password too short");
        }
        // save to database
        User user = new User(email, password);
        Database.save(user);
        // send welcome email
        EmailService.send("Welcome", email);
        // log
        Logger.info("User registered: " + email);
    }
}
```

Refactoring splits responsibilities into separate methods and classes:

```java
public class UserService {
    private final Validator validator;
    private final UserRepository repo;
    private final Notifier notifier;
    public UserService(Validator validator, UserRepository repo, Notifier notifier) {
        this.validator = validator;
        this.repo = repo;
        this.notifier = notifier;
    }
    public void register(String email, String password) {
        validator.validateCredentials(email, password);
        User user = new User(email, password);
        repo.save(user);
        notifier.welcome(user);
    }
}

// Each collaborator focuses on a single responsibility
public class Validator {
    public void validateCredentials(String email, String password) {
        if (email == null || !email.contains("@")) throw new IllegalArgumentException("Invalid email");
        if (password.length() < 8) throw new IllegalArgumentException("Password too short");
    }
}
public class UserRepository { public void save(User u) { /* save logic */ } }
public class Notifier { public void welcome(User u) { EmailService.send("Welcome", u.getEmail()); } }
```

The refactored method now reads like a story and is easier to maintain.

### 1.2 Object‑Orientation Abusers

These smells result from misusing object‑oriented features:

* **Switch Statements:** Relying on `switch` or `if‑else` to choose behavior based on type leads to duplicated code.  Use polymorphism instead: create subclasses that implement the behavior.

* **Temporary Field:** A class has a field that is only used in certain cases.  Move it into the appropriate class or method.

* **Refused Bequest:** A subclass inherits methods it doesn’t need.  Instead of subclassing, consider composition or extracting shared behavior into an interface.

* **Alternative Classes with Different Interfaces:** Two classes perform similar work but expose different methods.  Unify them under a common interface or superclass.

#### Example: Eliminating a Switch Statement

Consider a `SalaryCalculator` that calculates employee bonuses using a `switch` on role:

```java
// PROBLEM: lots of conditionals for different roles
public class SalaryCalculator {
    public double calculateBonus(Employee e) {
        switch (e.getRole()) {
            case "ENGINEER": return e.getSalary() * 0.2;
            case "MANAGER": return e.getSalary() * 0.3 + 1000;
            case "CEO":     return e.getSalary() * 0.5 + 5000;
            default: throw new IllegalArgumentException("Unknown role");
        }
    }
}
```

Refactor using polymorphism.  Define a `Role` interface with a method that calculates the bonus and let each role implement it:

```java
interface Role { double bonus(double baseSalary); }
class Engineer implements Role { public double bonus(double s) { return s * 0.2; } }
class Manager implements Role { public double bonus(double s) { return s * 0.3 + 1000; } }
class CEO implements Role { public double bonus(double s) { return s * 0.5 + 5000; } }

class Employee {
    private double salary;
    private Role role;
    // constructor and getters
    public double calculateBonus() { return role.bonus(salary); }
}
```

Adding a new role now requires only a new class implementing `Role`.  The `SalaryCalculator` no longer needs `switch` statements.

### 1.3 Change Preventers

These smells make modifying the code risky and time‑consuming:

* **Divergent Change:** A single class changes in different ways for different reasons.  Split the class into multiple ones, each handling one reason to change.

* **Shotgun Surgery:** A simple change forces modifications in many classes (e.g., renaming a concept).  Redesign so related responsibilities live together, reducing scattered dependencies.

* **Parallel Inheritance Hierarchies:** Whenever you create a subclass in one hierarchy, you must also create a corresponding subclass in another hierarchy.  Instead of two parallel hierarchies, extract shared behavior to a common interface or use composition.

#### Example: Parallel Inheritance Hierarchies

Suppose you have a hierarchy of `Employee` types and a parallel hierarchy of `EmployeeSerializer` types to convert them to JSON, XML, etc.  Every time you add a new employee type, you must create new serializer classes.  A better design is to move serialization into the employee classes or use the **Visitor** pattern (not covered here) to encapsulate algorithms that operate on elements of an object structure.

### 1.4 Dispensables – Code You Can Remove

**Dispensables** add no real value and should be removed:

* **Comments:** Code should be self‑explanatory.  Comments that merely explain what the code does are redundant.  Keep only comments that explain why a decision was made.

* **Duplicate Code:** Copy‑pasted logic should be extracted into a method or class.  DRY (Don’t Repeat Yourself) means each piece of knowledge should have a single authoritative representation.

* **Lazy Class:** A class that does little or no work can be merged into another class.

* **Data Class:** A class that contains only fields and getters/setters is little more than a record.  Either give it behavior or replace it with a simple data structure.

* **Dead Code:** Remove unused classes, fields or methods.

* **Speculative Generality:** Do not add flexibility “just in case.”  Avoid abstract classes or hooks that are never used.

#### Example: Replacing a Data Class

```java
// PROBLEM: Person is only a container of data
public class Person {
    private String firstName;
    private String lastName;
    // getters/setters
}
```

If `Person` is just a bag of data, you may instead use a record (Java 16+):

```java
public record Person(String firstName, String lastName) {}
```

Alternatively, if `Person` has behavior (e.g., formatting its name), put that logic inside the class:

```java
public class Person {
    private final String firstName;
    private final String lastName;
    public Person(String first, String last) { this.firstName = first; this.lastName = last; }
    public String fullName() { return firstName + " " + lastName; }
}
```

### 1.5 Couplers – Unnecessary Coupling

These smells appear when classes know too much about each other:

* **Feature Envy:** A method uses data from another class more than its own data.  Move the method to the class it envies.

* **Inappropriate Intimacy:** Two classes have intimate knowledge of each other’s internals.  Introduce proper encapsulation and use interfaces.

* **Message Chains:** Chained method calls like `obj.getA().getB().getC()` make clients aware of an object’s internal structure.  Provide a method that returns what the client needs or use a **Facade**.

* **Middle Man:** A class does nothing but delegate calls to another class.  Remove it and let clients talk to the real object directly.

#### Example: Feature Envy

```java
// PROBLEM: OrderPrinter knows too much about Order details
public class OrderPrinter {
    public void printReceipt(Order order) {
        double total = 0;
        for (OrderLine line : order.getLines()) {
            total += line.getProduct().getPrice() * line.getQuantity();
        }
        System.out.println("Total: " + total);
    }
}
```

Here `OrderPrinter` envys `Order` because it calculates totals using `Order`’s data.  Move the calculation into `Order`:

```java
public class Order {
    private List<OrderLine> lines;
    public double total() {
        return lines.stream().mapToDouble(l -> l.getProduct().getPrice() * l.getQuantity()).sum();
    }
    public List<OrderLine> getLines() { return lines; }
}
public class OrderPrinter {
    public void printReceipt(Order order) { System.out.println("Total: " + order.total()); }
}
```

This change reduces coupling: `OrderPrinter` no longer knows how to calculate totals; it delegates to `Order`.

---

## 2 Design Principles (SOLID, DRY, KISS)

Design principles provide guidelines to write clean, maintainable code:

### 2.1 Single Responsibility Principle (SRP)

Every class or method should have one reason to change.  Splitting responsibilities leads to smaller, more focused classes.  In our `UserService` example, the validation, persistence and notification duties were separated into collaborators.

### 2.2 Open–Closed Principle (OCP)

Software entities should be **open for extension but closed for modification**.  New behavior can be added via subclassing or composition without changing existing code.  For example, adding a new role in the bonus calculator only requires a new `Role` implementation.

### 2.3 Liskov Substitution Principle (LSP)

Subtypes must be substitutable for their base types.  If a method expects a base class, it should work with any subclass without breaking correctness.  Avoid weakening preconditions or strengthening postconditions in subclasses.

### 2.4 Interface Segregation Principle (ISP)

Clients should not be forced to depend on methods they do not use.  Prefer several small, cohesive interfaces over one large “fat” interface.

### 2.5 Dependency Inversion Principle (DIP)

High‑level modules should depend on abstractions, not on concrete implementations.  Details should depend on abstractions, allowing you to swap implementations easily.  Using interfaces in `UserService` enables dependency injection and testing.

### 2.6 DRY – Don’t Repeat Yourself

Avoid duplication of logic or data.  Extract common code into reusable methods or classes.  Duplication tends to drift out of sync, causing bugs.

### 2.7 KISS – Keep It Simple, Stupid

Prefer simple solutions over complex ones.  Simpler code is easier to understand, test and maintain.  Resist the temptation to add flexibility until it is needed.

---

## 3 Creational Design Patterns

Creational patterns provide ways to instantiate objects that decouple creation logic from usage.  Each pattern solves a particular problem and improves flexibility.

### 3.1 Singleton – One Instance Globally

The **Singleton** pattern ensures that a class has only one instance and provides global access to it.  It is useful for classes representing shared resources (configuration, logging).  The typical implementation hides the constructor and exposes a static `getInstance()` method.  A synchronized method can ensure thread safety:

```java
public class Logger {
    private static Logger instance;
    private Logger() {}
    public static synchronized Logger getInstance() {
        if (instance == null) instance = new Logger();
        return instance;
    }
    public void log(String msg) { System.out.println(msg); }
}
// Usage
Logger log1 = Logger.getInstance();
Logger log2 = Logger.getInstance();
System.out.println(log1 == log2); // true
```

### 3.2 Factory Method – Delegating Object Creation

The **Factory Method** pattern defines an interface for creating objects and lets subclasses decide which concrete type to instantiate.  It is an alternative to calling constructors directly.  For instance, a `Dialog` class might declare an abstract `createButton()` method.  Concrete subclasses (`WindowsDialog`, `WebDialog`) override this method to create platform‑specific buttons.

```java
abstract class Dialog {
    public void render() {
        Button okButton = createButton();
        okButton.onClick();
        okButton.render();
    }
    protected abstract Button createButton();
}
class WindowsDialog extends Dialog {
    protected Button createButton() { return new WindowsButton(); }
}
class WebDialog extends Dialog {
    protected Button createButton() { return new HTMLButton(); }
}
interface Button { void render(); void onClick(); }
// concrete buttons implement platform‑specific rendering
```

### 3.3 Abstract Factory – Families of Related Objects

**Abstract Factory** creates families of related objects without specifying their concrete classes.  If your system needs different themes (Mac, Windows, Linux), abstract factory provides a way to create matching UI components.  See the GUI example in the previous section for a full implementation.

### 3.4 Builder – Constructing Complex Objects

**Builder** separates the construction of a complex object from its representation.  The builder collects parameters and eventually creates the object.  This pattern is ideal for objects with many optional parameters:

```java
public class Computer {
    private final String cpu;
    private final int ram;
    private final boolean graphicsCard;
    private Computer(Builder b) {
        cpu = b.cpu; ram = b.ram; graphicsCard = b.graphicsCard;
    }
    public static class Builder {
        private String cpu = "i3";
        private int ram = 8;
        private boolean graphicsCard;
        public Builder cpu(String cpu) { this.cpu = cpu; return this; }
        public Builder ram(int ram) { this.ram = ram; return this; }
        public Builder graphicsCard(boolean enabled) { this.graphicsCard = enabled; return this; }
        public Computer build() { return new Computer(this); }
    }
    public String toString() { return cpu + "/" + ram + "GB/graphics=" + graphicsCard; }
}
// Usage
Computer gaming = new Computer.Builder().cpu("i9").ram(32).graphicsCard(true).build();
System.out.println(gaming);
```

### 3.5 Prototype – Cloning Existing Objects

**Prototype** lets you clone existing objects instead of creating them from scratch.  When object creation is expensive, cloning is efficient.  Implement a `clone()` method (deep copy) or use copy constructors.

```java
public interface Copyable<T> { T copy(); }
public class ColorPalette implements Copyable<ColorPalette> {
    private List<String> colors;
    public ColorPalette(List<String> colors) { this.colors = new ArrayList<>(colors); }
    public ColorPalette copy() { return new ColorPalette(new ArrayList<>(this.colors)); }
}
ColorPalette original = new ColorPalette(List.of("red", "green"));
ColorPalette cloned = original.copy();
```

---

## 4 Structural Design Patterns

Structural patterns describe how classes and objects can be composed to form larger structures.

### 4.1 Bridge – Decoupling Abstraction and Implementation

The **Bridge** pattern separates an abstraction from its implementation so the two can vary independently.  For example, a remote control (abstraction) operates devices like TVs or radios (implementations).  See the earlier TV/Radio example for a full code listing.

### 4.2 Adapter – Making Interfaces Compatible

**Adapter** converts one interface into another so incompatible classes can work together.  The square‑peg/round‑hole example demonstrates how an adapter wraps a `SquarePeg` and exposes the `RoundPeg` interface.

### 4.3 Decorator – Adding Responsibilities Dynamically

**Decorator** attaches additional responsibilities to an object at runtime.  Decorators wrap the original object and delegate calls, adding new behavior before or after.  The notifier example shows how to stack email and SMS decorators around a basic notifier.

### 4.4 Composite – Tree Structures

**Composite** lets clients treat individual objects and compositions of objects uniformly.  It is ideal for hierarchical structures like graphics scenes or file systems.  The example demonstrates `Graphic` leaves (`Dot`, `Circle`) and `CompoundGraphic` containers.

### 4.5 Proxy – Controlling Access to Objects

**Proxy** provides a placeholder for another object and controls access to it.  A proxy may add caching, security checks or lazy initialization.  In the video downloader example, the proxy caches downloaded videos to avoid repeated downloads.

---

## 5 Behavioral Design Patterns

Behavioral patterns define how objects interact and distribute responsibilities.

### 5.1 Observer – One‑to‑Many Notifications

**Observer** establishes a one‑to‑many relationship where observers automatically receive updates when the subject changes.  The weather station example shows how displays subscribe to a station and get notified when the temperature changes.

### 5.2 Strategy – Selecting Algorithms at Runtime

**Strategy** defines a family of algorithms and makes them interchangeable.  The shipping cost and salary bonus examples replace `switch` statements with strategies.

### 5.3 Template Method – Defining Algorithm Skeletons

**Template Method** defines the skeleton of an algorithm in a base class while allowing subclasses to override specific steps.  The `GameAI` example illustrates how subclasses provide implementations for building structures and units.

### 5.4 State – Encapsulating State‑Dependent Behavior

**State** allows an object to change its behavior when its internal state changes.  Instead of huge `if` or `switch` statements, the object delegates behavior to state objects.  The document publishing example demonstrates transitions between `Draft`, `Moderation` and `Published` states.

### 5.5 Mediator – Reducing Class Coupling

**Mediator** centralizes communication between objects, preventing them from referring to each other directly.  The authentication dialog shows how UI controls communicate through a mediator object.

### 5.6 Iterator – Accessing Collections Sequentially

**Iterator** provides a way to traverse elements of a collection without exposing its internal representation.  The book collection example demonstrates a custom iterator that walks through books.

---

## 6 Understanding the Citations

Throughout this document you may notice bracketed citations like `【849602717434773†L10-L12】`.  These are references to the original sources used while researching this guide.  Each citation links to a line range in a publicly available article or documentation.  The numbers inside the brackets encode an identifier for the source and the line numbers.  For example:

* `【849602717434773†L10-L12】` refers to lines 10–12 of the Refactoring.Guru article on the Composite pattern.

These citations are provided for transparency and to credit the original authors.  You do not need to follow them unless you want to read the original references for deeper study.

---

### Summary

By learning to identify code smells and understanding the SOLID, DRY and KISS principles, you can refactor your code to be cleaner and more maintainable.  Creational patterns help with object creation, structural patterns help assemble classes and objects into larger structures, and behavioral patterns govern interactions.  Use this document as a quick reference and study the examples to reinforce your understanding.
