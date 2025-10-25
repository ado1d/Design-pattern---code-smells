# Complete Guide to Code Smells, Design Principles and Design Patterns

## Quick Navigation

Below is a compact table of contents. Click a link to jump to the section.

- [Complete Guide to Code Smells, Design Principles and Design Patterns](#complete-guide-to-code-smells-design-principles-and-design-patterns)
  - [Quick Navigation](#quick-navigation)
  - [Introduction](#introduction)
  - [Part 1 – Code Smells and Their Remedies](#part1--codesmells-and-their-remedies)
    - [Overview of Code Smells](#overview-of-code-smells)
    - [1. Bloaters](#1bloaters)
      - [1.1 Long Method](#11longmethod)
      - [1.2 Large Class](#12largeclass)
      - [1.3 Primitive Obsession](#13primitiveobsession)
      - [1.4 Long Parameter List](#14long-parameter-list)
      - [1.5 Data Clumps](#15dataclumps)
    - [2. Object‑Orientation Abusers](#2objectorientation-abusers)
      - [2.1 Switch Statements](#21switchstatements)
      - [2.2 Temporary Field](#22temporaryfield)
      - [2.3 Refused Bequest](#23refusedbequest)
      - [2.4 Alternative Classes with Different Interfaces](#24alternative-classes-with-different-interfaces)
    - [3. Change Preventers](#3changepreventers)
      - [3.1 Divergent Change](#31divergent-change)
      - [3.2 Shotgun Surgery](#32shotgun-surgery)
      - [3.3 Parallel Inheritance Hierarchies](#33parallelinheritancehierarchies)
    - [4. Dispensables](#4dispensables)
      - [4.1 Comments](#41comments)
      - [4.2 Duplicate Code](#42duplicate-code)
      - [4.3 Lazy Class](#43lazy-class)
      - [4.4 Data Class](#44dataclass)
      - [4.5 Dead Code](#45dead-code)
      - [4.6 Speculative Generality](#46speculative-generality)
    - [5. Couplers](#5couplers)
      - [5.1 Feature Envy](#51featureenvy)
      - [5.2 Inappropriate Intimacy](#52inappropriateintimacy)
      - [5.3 Message Chains](#53messagechains)
      - [5.4 Middle Man](#54middleman)
  - [Part 2 – Design Principles](#part2--design-principles)
    - [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
    - [Open–Closed Principle (OCP)](#openclosed-principle-ocp)
    - [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
    - [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
    - [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
    - [DRY – Don’t Repeat Yourself](#dry--dont-repeat-yourself)
    - [KISS – Keep It Simple, Stupid](#kiss--keep-it-simple-stupid)
  - [Part 3 – Creational Design Patterns](#part3--creational-design-patterns)
    - [1. Singleton](#1singleton)
    - [2. Factory Method](#2factory-method)
    - [3. Abstract Factory](#3abstract-factory)
    - [4. Builder](#4builder)
    - [5. Prototype](#5prototype)
  - [Part 4 – Structural Design Patterns](#part4--structural-design-patterns)
    - [1. Bridge](#1bridge)
    - [2. Adapter](#2adapter)
    - [3. Decorator](#3decorator)
    - [4. Composite](#4composite)
    - [5. Proxy](#5proxy)
  - [Part 5 – Behavioral Design Patterns](#part5--behavioral-design-patterns)
    - [1. Observer](#1observer)
    - [2. Strategy](#2strategy)
    - [3. Template Method](#3templatemethod)
    - [4. State](#4state)
    - [5. Mediator](#5mediator)
    - [6. Iterator](#6iterator)
  - [Conclusion](#conclusion)
  - [Appendix – Extended Design Pattern Discussion](#appendix-extended-designpattern-discussion)
    - [Singleton: variations and best practices](#singleton-variations-and-best-practices)
    - [Factory Method: advanced usage and variations](#factorymethod-advanced-usage-and-variations)
    - [Abstract Factory: cross‑platform design](#abstractfactory-crossplatform-design)
    - [Builder: telescoping constructors and immutability](#builder-telescoping-constructors-and-immutability)
    - [Prototype: deep vs shallow copying](#prototype-deep-vs-shallow-copying)
    - [Bridge: decoupling abstractions and implementations](#bridge-decoupling-abstractions-and-implementations)
    - [Adapter: class vs object adapters](#adapter-class-vs-object-adapters)
    - [Decorator: layering behaviours](#decorator-layering-behaviours)
    - [Composite: transparent vs safe composites](#composite-transparent-vs-safe-composites)
    - [Proxy: types and real‑world analogies](#proxy-types-and-realworld-analogies)
    - [Observer: push vs pull notifications](#observer-push-vs-pull-notifications)
    - [Strategy: selecting algorithms at runtime](#strategy-selecting-algorithms-at-runtime)
    - [Template Method: hooks and customization](#templatemethod-hooks-and-customization)
    - [State: enumerations and dynamic transitions](#state-enumerations-and-dynamic-transitions)
    - [Mediator: centralized communication examples](#mediator-centralized-communication-examples)

---

## Introduction

Writing clean and maintainable code is essential for the long‑term success of a software project.  This guide is meant to be a detailed resource on the most common **code smells**, fundamental **design principles**, and widely used **design patterns**.  It explains why certain code constructs are problematic (the “problem” code), demonstrates how to apply the corresponding principle or pattern (the “refactored” code), and provides an easy‑to‑follow narrative for each topic.  Feel free to read sequentially or jump to the sections that are most relevant to your current needs.

---

## Part 1 – Code Smells and Their Remedies

### Overview of Code Smells

“Code smell” is a term coined by Martin Fowler to describe symptoms in the source code that may indicate a deeper problem.  A smell is not a bug; it doesn’t prevent the program from working.  Instead, it hints at possible trouble down the road—poor readability, fragility, or difficulty extending the system.  Recognising code smells helps you refactor early and maintain a healthy code base.

The smells are grouped into five broad categories: **Bloaters**, **Object‑Orientation Abusers**, **Change Preventers**, **Dispensables**, and **Couplers**.  The following sections provide a detailed explanation and examples for each specific smell.

### 1. Bloaters

Bloaters are code artifacts that have grown too large.  They decrease clarity and make maintenance harder.

#### 1.1 Long Method

A long method tries to handle multiple responsibilities at once.  Such methods are difficult to understand, test and reuse.  Break them into smaller, well‑named methods.

**Problem Example – Calculating Invoice and Reporting**

```java
// This method does too much: calculates totals, applies tax and discount,
// updates inventory and prints a report.  It violates SRP and is hard to maintain.
public class InvoiceService {
        public void processInvoice(String customerName, double amount, double taxRate,
                                                             double discount, String productId, int quantity) {
                // calculate totals
                double tax = amount * taxRate;
                double total = amount + tax - discount;
                // update inventory
                int stock = Database.getStock(productId);
                Database.updateStock(productId, stock - quantity);
                // print report
                System.out.println("Customer: " + customerName);
                System.out.println("Amount: " + amount + ", Tax: " + tax + ", Discount: " + discount);
                System.out.println("Total due: " + total);
        }
}
```

**Refactored Example – Single Responsibility per Method**

```java
public class InvoiceProcessor {
        private final InventoryService inventory;
        private final InvoicePrinter printer;
        public InvoiceProcessor(InventoryService inventory, InvoicePrinter printer) {
                this.inventory = inventory;
                this.printer = printer;
        }
        public void process(Invoice invoice) {
                double total = calculateTotal(invoice);
                updateInventory(invoice);
                printer.print(invoice, total);
        }
        private double calculateTotal(Invoice invoice) {
                double subtotal = invoice.lines().stream().mapToDouble(l -> l.price() * l.quantity()).sum();
                double tax = subtotal * invoice.taxRate();
                return subtotal + tax - invoice.discount();
        }
        private void updateInventory(Invoice invoice) {
                for (InvoiceLine line : invoice.lines()) {
                        inventory.remove(line.productId(), line.quantity());
                }
        }
}

class InvoicePrinter {
        public void print(Invoice invoice, double total) {
                System.out.println("Customer: " + invoice.customerName());
                System.out.println("Total due: " + total);
        }
}
```

The responsibilities are separated: total calculation, inventory update and printing.

#### 1.2 Large Class

Large classes contain too many fields or methods.  They are hard to understand and maintain.  Splitting them into smaller classes improves cohesion and reduces complexity.

**Problem Example – All‑in‑One Account Class**

```java
public class Account {
        private List<Transaction> transactions = new ArrayList<>();
        private String owner;
        private String email;
        private double balance;
        // deposit and withdrawal operations
        public void deposit(double amount) { /* add transaction */ }
        public void withdraw(double amount) { /* add transaction */ }
        public double getBalance() { /* compute current balance */ return balance; }
        // printing operations
        public void printStatement() { /* iterate transactions and print */ }
        public void exportToCsv(String fileName) { /* write transactions to file */ }
        public void emailStatement() { /* send statement to owner’s email */ }
}
```

**Refactored Example – Separate Concerns**

```java
public class BankAccount {
        private final String owner;
        private double balance;
        private final List<Transaction> history = new ArrayList<>();
        public BankAccount(String owner) { this.owner = owner; }
        public void deposit(double amount) { balance += amount; history.add(new Transaction("Deposit", amount)); }
        public void withdraw(double amount) { balance -= amount; history.add(new Transaction("Withdraw", amount)); }
        public double getBalance() { return balance; }
        public List<Transaction> getHistory() { return Collections.unmodifiableList(history); }
}

public class AccountReporter {
        public void printStatement(BankAccount account) {
                System.out.println("Account holder: " + account.owner);
                for (Transaction t : account.getHistory()) {
                        System.out.println(t.type() + ": " + t.amount());
                }
                System.out.println("Balance: " + account.getBalance());
        }
        public void export(BankAccount account, String fileName) { /* write CSV */ }
        public void emailStatement(BankAccount account, String email) { /* send via email */ }
}
```

`BankAccount` now handles only deposits and withdrawals; printing and exporting are delegated to `AccountReporter`.

#### 1.3 Primitive Obsession

Primitive obsession is the overuse of primitive types (int, double, String) for domain concepts.  Encapsulate them into small classes (value objects) to add validation and behavior.

**Problem Example – Address as Strings**

```java
public class Order {
        private String shippingStreet;
        private String shippingCity;
        private String shippingZip;
        private String billingStreet;
        private String billingCity;
        private String billingZip;
        // getters and setters
}
```

The code duplicates fields for shipping and billing addresses.  There is no validation.

**Refactored Example – Address Object**

```java
public class Address {
        private final String street;
        private final String city;
        private final String zip;
        public Address(String street, String city, String zip) {
                if (street == null || city == null || zip == null) throw new IllegalArgumentException("All fields required");
                this.street = street; this.city = city; this.zip = zip;
        }
        // getters
}

public class Order {
        private final Address shippingAddress;
        private final Address billingAddress;
        public Order(Address shipping, Address billing) { this.shippingAddress = shipping; this.billingAddress = billing; }
        public Address getShippingAddress() { return shippingAddress; }
        public Address getBillingAddress() { return billingAddress; }
}
```

Now `Address` encapsulates validation; `Order` has only two `Address` fields.

#### 1.4 Long Parameter List

Methods with many parameters are difficult to call and maintain.  Group related parameters into a class or use a builder.

Refer to the earlier `OrderRequest` builder example for a solution.

#### 1.5 Data Clumps

Repeating groups of variables indicates a missing type.  Create a class to represent the group.  See the `Rectangle` example earlier, where two pairs of coordinates (`x1`, `y1`, `x2`, `y2`) were replaced with `Point` objects.

### 2. Object‑Orientation Abusers

These smells arise when object‑oriented features are misused.

#### 2.1 Switch Statements

Replace conditionals on type with polymorphism.  See the animal sound example earlier.

#### 2.2 Temporary Field

Remove temporary fields by creating specialized subclasses or separate classes.  See the `Dialog` example with message and error dialogs.

#### 2.3 Refused Bequest

Reconsider the inheritance relationship; perhaps use composition or split the hierarchy.  The ostrich example illustrated this problem.

#### 2.4 Alternative Classes with Different Interfaces

Provide a common interface or superclass for classes doing the same job.  The `Shape` interface example solves this smell.

### 3. Change Preventers

These smells make the code hard to change.

#### 3.1 Divergent Change

Split classes that must change for unrelated reasons.  For example, if a single class both parses and renders documents, create separate parser and renderer classes.

#### 3.2 Shotgun Surgery

Gather scattered code into one place.  For example, if multiple classes manipulate the same concept (like `Address`), factor that concept into its own class.

#### 3.3 Parallel Inheritance Hierarchies

Use the **Visitor** pattern or move behavior into one hierarchy to avoid synchronized hierarchies.

### 4. Dispensables

These smells involve unnecessary code that should be removed or simplified.

#### 4.1 Comments

Write self‑documenting code.  Use method names to convey intent.  Keep comments for explaining *why*, not *what*.

#### 4.2 Duplicate Code

Extract duplicated code into methods or classes.  The `calculateSum` example shows how to remove duplicated loops.

#### 4.3 Lazy Class

Remove classes that do little or nothing.  Inline their behavior into other classes.

#### 4.4 Data Class

Add behavior to data classes or replace them with records.

#### 4.5 Dead Code

Delete unused classes, methods, variables.

#### 4.6 Speculative Generality

Avoid writing code for future possibilities that might never happen.  YAGNI: You Aren’t Gonna Need It.

### 5. Couplers

These smells deal with unwanted coupling.

#### 5.1 Feature Envy

Move methods that use another class’s data more than their own to that class.  The order total example demonstrates this.

#### 5.2 Inappropriate Intimacy

Hide fields using encapsulation.  The `Wallet` example shows how to encapsulate balance.

#### 5.3 Message Chains

Provide façade methods to hide chains of calls.  See the `getCountryName` example.

#### 5.4 Middle Man

Remove classes that just delegate.  Let clients talk directly to the real object.

---

## Part 2 – Design Principles

Design principles guide you in structuring your software to be flexible, robust and maintainable.  Below are the most widely adopted principles.

### Single Responsibility Principle (SRP)

Each class or function should have one reason to change.  Splitting responsibilities leads to small, focused classes.

**Problem Example – Mixed Responsibilities**

```java
class ReportService {
        public void generateReport(List<Order> orders) {
                // 1. aggregate data
                double total = 0;
                for (Order o : orders) total += o.amount();
                // 2. format report
                StringBuilder sb = new StringBuilder();
                sb.append("Orders\n");
                for (Order o : orders) sb.append(o.toString()).append("\n");
                sb.append("Total: ").append(total);
                // 3. save to file
                try (FileWriter out = new FileWriter("report.txt")) {
                        out.write(sb.toString());
                } catch (IOException e) { e.printStackTrace(); }
        }
}
```

`generateReport` aggregates data, formats it and saves it.  Any change (formatting or saving) touches the same method.

**Refactored Example – Separate Responsibilities**

```java
class ReportGenerator {
        public Report buildReport(List<Order> orders) {
                double total = orders.stream().mapToDouble(Order::amount).sum();
                return new Report(orders, total);
        }
}
class ReportFormatter {
        public String format(Report report) {
                StringBuilder sb = new StringBuilder("Orders\n");
                for (Order o : report.orders()) sb.append(o.toString()).append("\n");
                sb.append("Total: ").append(report.total());
                return sb.toString();
        }
}
class ReportSaver {
        public void save(String contents, String filename) {
                try (FileWriter out = new FileWriter(filename)) { out.write(contents); } catch (IOException e) { e.printStackTrace(); }
        }
}

ReportGenerator generator = new ReportGenerator();
ReportFormatter formatter = new ReportFormatter();
ReportSaver saver = new ReportSaver();

Report report = generator.buildReport(orders);
String content = formatter.format(report);
saver.save(content, "report.txt");
```

Now the code is modular; changes to formatting or saving affect their respective classes.

### Open–Closed Principle (OCP)

Classes should be open for extension but closed for modification.  You should be able to extend behavior without altering existing code.

**Problem Example – New Discount Strategy**

```java
class PriceCalculator {
        public double calculatePrice(double basePrice, String customerType) {
                if (customerType.equals("regular")) return basePrice;
                if (customerType.equals("vip")) return basePrice * 0.9;
                if (customerType.equals("employee")) return basePrice * 0.8;
                throw new IllegalArgumentException("Unknown type");
        }
}
```

Every time you add a new customer type, you modify `calculatePrice()`.

**Refactored Example – Strategy Pattern**

```java
interface DiscountStrategy { double apply(double basePrice); }
class RegularDiscount implements DiscountStrategy { public double apply(double basePrice) { return basePrice; } }
class VIPDiscount implements DiscountStrategy { public double apply(double basePrice) { return basePrice * 0.9; } }
class EmployeeDiscount implements DiscountStrategy { public double apply(double basePrice) { return basePrice * 0.8; } }

class PriceCalculator {
        private final DiscountStrategy strategy;
        public PriceCalculator(DiscountStrategy strategy) { this.strategy = strategy; }
        public double calculate(double basePrice) { return strategy.apply(basePrice); }
}

// adding a new discount type requires adding a new class implementing DiscountStrategy
```

### Liskov Substitution Principle (LSP)

Subtypes must be substitutable for their base types without altering the desirable properties of a program.  Don’t override methods with incompatible behavior.

**Problem Example – Rectangle/Square**

See the earlier example: deriving `Square` from `Rectangle` violates LSP because setting width and height behave differently for squares.  The fix is to avoid inheritance and model squares separately.

### Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they do not use.  Split “fat” interfaces into multiple smaller ones.

**Problem Example – Multifunction Interface**

```java
interface Worker {
        void cook();
        void clean();
        void fixCar();
}
class Chef implements Worker {
        public void cook() { /* ... */ }
        public void clean() { /* ... */ }
        public void fixCar() { throw new UnsupportedOperationException(); }
}
```

**Refactored Example – Segregated Interfaces**

```java
interface Cook { void cook(); }
interface Cleaner { void clean(); }
interface Mechanic { void fixCar(); }

class Chef implements Cook, Cleaner {
        public void cook() { /* ... */ }
        public void clean() { /* ... */ }
}
class MechanicImpl implements Mechanic {
        public void fixCar() { /* ... */ }
}
```

### Dependency Inversion Principle (DIP)

High‑level modules should not depend on low‑level modules; both should depend on abstractions.  Use interfaces and dependency injection to invert the dependency.

**Problem Example – Hard‑coded Dependency**

```java
class OrderService {
        private final EmailNotifier notifier = new EmailNotifier();
        public void confirmOrder(Order order) {
                // business logic
                notifier.sendConfirmation(order);
        }
}
class EmailNotifier {
        public void sendConfirmation(Order o) { /* send email */ }
}
```

`OrderService` depends on `EmailNotifier`.  To send SMS notifications, you must modify the service.

**Refactored Example – Depend on Abstraction**

```java
interface Notifier { void sendConfirmation(Order order); }
class EmailNotifier implements Notifier { public void sendConfirmation(Order o) { /* email */ } }
class SmsNotifier implements Notifier { public void sendConfirmation(Order o) { /* SMS */ } }

class OrderService {
        private final Notifier notifier;
        public OrderService(Notifier notifier) { this.notifier = notifier; }
        public void confirmOrder(Order order) { /* business logic */ notifier.sendConfirmation(order); }
}

// Usage
Notifier notifier = new EmailNotifier();
OrderService service = new OrderService(notifier);
```

Now `OrderService` depends on the `Notifier` interface and can work with any implementation.

### DRY – Don’t Repeat Yourself

Duplication is the enemy of maintenance.  Consolidate duplicated code.

**Problem Example – Repeated Calculations**

```java
double distance1 = Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
// later in code
double distance2 = Math.sqrt(Math.pow(xb - xa, 2) + Math.pow(yb - ya, 2));
```

**Refactored Example – Extract Method**

```java
public static double distance(int x1, int y1, int x2, int y2) {
        return Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
}

double d1 = distance(x1, y1, x2, y2);
double d2 = distance(xa, ya, xb, yb);
```

### KISS – Keep It Simple, Stupid

Prefer simple solutions over complex ones.  Avoid over‑engineering.

**Problem Example – Overcomplicated Sorting**

```java
public List<String> sortStrings(List<String> list) {
        // A complex quicksort implementation with manual partitions
        // ... 100 lines of code ...
        return list;
}
```

**Refactored Example – Use Built‑in Sort**

```java
public List<String> sortStrings(List<String> list) {
        list.sort(Comparator.naturalOrder());
        return list;
}
```

---

## Part 3 – Creational Design Patterns

Creational patterns abstract the instantiation process, making the system independent of the way objects are created.

### 1. Singleton

Ensures a class has only one instance and provides a global point of access.

**Problem Scenario**: Several parts of the system need access to a configuration object, but each part creates its own instance.  This results in inconsistent state and unnecessary memory use.

```java
public class Configuration {
        private final Properties props = new Properties();
        public Configuration() { loadFromFile(); }
        private void loadFromFile() {
                // load properties
        }
        public String get(String key) { return props.getProperty(key); }
}

// Each client calls new Configuration(), causing multiple loads
Configuration c1 = new Configuration();
Configuration c2 = new Configuration();
```

**Refactored Using Singleton**

```java
public class Configuration {
        private static final Configuration INSTANCE = new Configuration();
        private final Properties props;
        private Configuration() { props = new Properties(); load(); }
        private void load() { /* load props */ }
        public static Configuration getInstance() { return INSTANCE; }
        public String get(String key) { return props.getProperty(key); }
}

Configuration conf = Configuration.getInstance();
```

### 2. Factory Method

Defines an interface for creating an object but lets subclasses decide which class to instantiate.  It allows a class to defer instantiation to subclasses.

**Problem Scenario**: A logistics application needs to create different types of transport (truck, ship, plane).  The client uses `if`/`switch` statements to decide which transport to instantiate, violating OCP.

```java
class TransportService {
        public void planDelivery(String type) {
                if (type.equals("truck")) {
                        Truck t = new Truck();
                        t.deliver();
                } else if (type.equals("ship")) {
                        Ship s = new Ship();
                        s.deliver();
                } else if (type.equals("plane")) {
                        Plane p = new Plane();
                        p.deliver();
                }
        }
}
```

**Refactored Using Factory Method**

```java
abstract class Logistics {
        public void planDelivery() {
                Transport transport = createTransport();
                transport.deliver();
        }
        protected abstract Transport createTransport();
}
interface Transport { void deliver(); }
class Truck implements Transport { public void deliver() { System.out.println("Deliver by truck"); } }
class Ship implements Transport  { public void deliver() { System.out.println("Deliver by ship"); } }
class Plane implements Transport { public void deliver() { System.out.println("Deliver by plane"); } }
class RoadLogistics extends Logistics { protected Transport createTransport() { return new Truck(); } }
class SeaLogistics  extends Logistics { protected Transport createTransport() { return new Ship(); } }
class AirLogistics  extends Logistics { protected Transport createTransport() { return new Plane(); } }

// Client chooses the appropriate subclass
Logistics logistics = new RoadLogistics();
logistics.planDelivery();
```

### 3. Abstract Factory

Creates families of related objects without specifying their concrete classes.  Useful when you need to ensure consistency across products.

**Problem Scenario**: Building a UI library that needs to support different look‑and‑feel themes (Windows, Mac, Linux).  The client code uses conditionals to create platform‑specific widgets.

```java
class UIClient {
        public void render(String os) {
                Button btn;
                TextField tf;
                if (os.equals("Windows")) {
                        btn = new WindowsButton();
                        tf  = new WindowsTextField();
                } else if (os.equals("Mac")) {
                        btn = new MacButton();
                        tf  = new MacTextField();
                } else {
                        throw new IllegalArgumentException();
                }
                btn.render(); tf.render();
        }
}
interface Button { void render(); }
interface TextField { void render(); }
class WindowsButton implements Button { public void render() { System.out.println("Windows Button"); } }
class WindowsTextField implements TextField { public void render() { System.out.println("Windows TextField"); } }
class MacButton implements Button { public void render() { System.out.println("Mac Button"); } }
class MacTextField implements TextField { public void render() { System.out.println("Mac TextField"); } }

// Client code uses conditionals to create specific classes
UIClient client = new UIClient();
client.render("Windows");
client.render("Mac");
```

**Refactored Using Abstract Factory**

```java
interface Button { void render(); }
interface TextField { void render(); }
class WindowsButton implements Button { public void render() { System.out.println("Windows Button"); } }
class WindowsTextField implements TextField { public void render() { System.out.println("Windows TextField"); } }
class MacButton implements Button { public void render() { System.out.println("Mac Button"); } }
class MacTextField implements TextField { public void render() { System.out.println("Mac TextField"); } }

interface UIFactory {
        Button createButton();
        TextField createTextField();
}
class WindowsFactory implements UIFactory {
        public Button createButton() { return new WindowsButton(); }
        public TextField createTextField() { return new WindowsTextField(); }
}
class MacFactory implements UIFactory {
        public Button createButton() { return new MacButton(); }
        public TextField createTextField() { return new MacTextField(); }
}

class UIClient {
        private UIFactory factory;
        public UIClient(UIFactory factory) { this.factory = factory; }
        public void render() {
                Button btn = factory.createButton();
                TextField tf = factory.createTextField();
                btn.render(); tf.render();
        }
}

// Client chooses a factory based on the current OS
UIClient client = new UIClient(new WindowsFactory());
client.render();
```

Adding a new platform requires creating a new factory and product classes without modifying existing code.

### 4. Builder

Separates the construction of a complex object from its representation.  Builder is useful when an object has many optional or complex parameters.

**Problem Scenario**: Creating a `Meal` object that consists of a main course, side, drink and dessert.  Using a telescoping constructor with many parameters is error‑prone.

```java
public class Meal {
        private final String mainDish;
        private final String side;
        private final String drink;
        private final String dessert;
        public Meal(String mainDish, String side, String drink, String dessert) {
                this.mainDish = mainDish;
                this.side = side;
                this.drink = drink;
                this.dessert = dessert;
        }
        // getters omitted
}

// Client must remember parameter order
Meal meal = new Meal("Steak", "Fries", "Coke", "Ice Cream");
```

**Refactored Using Builder**

```java
public class Meal {
        private final String mainDish;
        private final String side;
        private final String drink;
        private final String dessert;
        private Meal(Builder b) {
                this.mainDish = b.mainDish;
                this.side = b.side;
                this.drink = b.drink;
                this.dessert = b.dessert;
        }
        public static class Builder {
                private String mainDish;
                private String side;
                private String drink;
                private String dessert;
                public Builder mainDish(String value) { this.mainDish = value; return this; }
                public Builder side(String value) { this.side = value; return this; }
                public Builder drink(String value) { this.drink = value; return this; }
                public Builder dessert(String value) { this.dessert = value; return this; }
                public Meal build() { return new Meal(this); }
        }
        public String toString() { return mainDish + ", " + side + ", " + drink + ", " + dessert; }
}

// Client uses builder for clarity
Meal meal = new Meal.Builder().mainDish("Steak").side("Fries").drink("Coke").dessert("Ice Cream").build();
System.out.println(meal);
```

### 5. Prototype

Creates new objects by copying an existing object (the prototype) rather than instantiating from scratch.  Useful when object creation is expensive or the exact type is unknown.

**Problem Scenario**: Creating complex graphical shapes (polygons, groups) from scratch every time is expensive.  Cloning an existing shape is more efficient.

```java
abstract class Shape implements Cloneable {
        int x, y;
        String color;
        public Shape clone() throws CloneNotSupportedException { return (Shape) super.clone(); }
}
class Rectangle extends Shape { int width, height; }
class Circle extends Shape { int radius; }

// Creating a copy
Rectangle original = new Rectangle();
original.width = 10; original.height = 5; original.color = "Red";
Rectangle copy = (Rectangle) original.clone();
```

**Refactored**: The classes already implement `clone()`.  The prototype pattern formalises cloning by exposing a `clone()` or `copy()` method on a prototype interface.

```java
interface Prototype<T> { T copy(); }
class Rectangle implements Prototype<Rectangle> {
        int width, height; String color;
        public Rectangle copy() {
                Rectangle clone = new Rectangle();
                clone.width = this.width;
                clone.height = this.height;
                clone.color = this.color;
                return clone;
        }
}
Rectangle original = new Rectangle();
Rectangle clone = original.copy();
```

---

## Part 4 – Structural Design Patterns

Structural patterns show how to assemble objects and classes into larger structures.

### 1. Bridge

Splits a large class or set of closely related classes into two separate hierarchies—one for abstraction and another for implementation—so they can evolve independently.

**Problem Scenario**: Suppose we have two types of devices (TV, Radio) and two types of remote controls (Basic, Advanced).  Without a bridge, we may end up with four concrete classes: `BasicTVRemote`, `AdvancedTVRemote`, `BasicRadioRemote`, `AdvancedRadioRemote`.  Adding a new device multiplies these combinations.

**Refactored Using Bridge**

```java
// Implementor interface
interface Device {
        void enable(); void disable(); boolean isEnabled(); int getVolume(); void setVolume(int vol);
}
class TV implements Device {
        private boolean on; private int volume;
        public void enable() { on = true; }
        public void disable() { on = false; }
        public boolean isEnabled() { return on; }
        public int getVolume() { return volume; }
        public void setVolume(int vol) { volume = vol; }
}
class Radio implements Device { /* similar implementation */ }

// Abstraction hierarchy
abstract class Remote {
        protected Device device;
        protected Remote(Device device) { this.device = device; }
        public void togglePower() { if (device.isEnabled()) device.disable(); else device.enable(); }
        public void volumeUp() { device.setVolume(device.getVolume() + 10); }
        public void volumeDown() { device.setVolume(device.getVolume() - 10); }
}
class BasicRemote extends Remote { public BasicRemote(Device device) { super(device); } }
class AdvancedRemote extends Remote {
        public AdvancedRemote(Device device) { super(device); }
        public void mute() { device.setVolume(0); }
}

// Client code
Remote remote = new BasicRemote(new TV());
remote.togglePower(); remote.volumeUp();
Remote adv = new AdvancedRemote(new Radio());
adv.togglePower(); ((AdvancedRemote) adv).mute();
```

### 2. Adapter

Allows objects with incompatible interfaces to collaborate by wrapping an existing class with a new interface.

**Problem Scenario**: An analytics library expects data in JSON format, but our system produces XML.  We cannot change either.  We need an adapter to convert XML to JSON on the fly.

```java
class XmlReport {
        public String getXml() { return "<report>...</report>"; }
}
interface JsonReport { String getJson(); }

// Adapter that converts XML to JSON
class XmlToJsonAdapter implements JsonReport {
        private final XmlReport xmlReport;
        public XmlToJsonAdapter(XmlReport xmlReport) { this.xmlReport = xmlReport; }
        public String getJson() {
                String xml = xmlReport.getXml();
                // convert xml to json (simplified)
                return "{\"report\":\"converted\"}";
        }
}

// Client using JSON interface
JsonReport report = new XmlToJsonAdapter(new XmlReport());
System.out.println(report.getJson());
```

### 3. Decorator

Dynamically adds additional responsibilities to objects by wrapping them.  Decorators adhere to the same interface as the underlying object.

**Problem Scenario**: We have a simple `Notifier` that sends messages.  Requirements change to support logging, SMS notifications and encryption.  Subclassing leads to many combinations (e.g., `LoggingSmsNotifier`).

**Refactored Using Decorator**

```java
interface Notifier { void send(String message); }
class SimpleNotifier implements Notifier {
        public void send(String message) { System.out.println("Sending: " + message); }
}
abstract class NotifierDecorator implements Notifier {
        protected final Notifier wrappee;
        public NotifierDecorator(Notifier wrappee) { this.wrappee = wrappee; }
        public void send(String message) { wrappee.send(message); }
}
class LoggingDecorator extends NotifierDecorator {
        public LoggingDecorator(Notifier wrappee) { super(wrappee); }
        public void send(String message) {
                System.out.println("Logging message: " + message);
                super.send(message);
        }
}
class SmsDecorator extends NotifierDecorator {
        public SmsDecorator(Notifier wrappee) { super(wrappee); }
        public void send(String message) {
                super.send(message);
                System.out.println("Sending SMS: " + message);
        }
}
// Compose decorators as needed
Notifier notifier = new SmsDecorator(new LoggingDecorator(new SimpleNotifier()));
notifier.send("Hello");
```

### 4. Composite

Composes objects into tree structures so that clients can work with individual objects and compositions uniformly.  Useful for representing hierarchies like file systems or GUI elements.

**Problem Scenario**: A graphic editor manipulates shapes (`Dot`, `Circle`) and groups of shapes.  The client must treat single shapes and groups differently if there is no unified interface.

**Refactored Using Composite**

```java
interface Graphic {
        void draw();
        void move(int dx, int dy);
}
class Dot implements Graphic {
        int x, y;
        public Dot(int x, int y) { this.x = x; this.y = y; }
        public void draw() { System.out.println("Draw dot at (" + x + "," + y + ")"); }
        public void move(int dx, int dy) { x += dx; y += dy; }
}
class Circle extends Dot {
        int radius;
        public Circle(int x, int y, int radius) { super(x, y); this.radius = radius; }
        public void draw() { System.out.println("Draw circle at (" + x + "," + y + ") radius " + radius); }
}
class CompoundGraphic implements Graphic {
        private final List<Graphic> children = new ArrayList<>();
        public void add(Graphic child) { children.add(child); }
        public void draw() { for (Graphic child : children) child.draw(); }
        public void move(int dx, int dy) { for (Graphic child : children) child.move(dx, dy); }
}

CompoundGraphic picture = new CompoundGraphic();
picture.add(new Dot(1, 2));
CompoundGraphic group = new CompoundGraphic();
group.add(new Circle(5, 5, 3));
group.add(new Dot(2, 2));
picture.add(group);
picture.draw();
```

### 5. Proxy

Provides a placeholder to control access to another object, adding behavior such as caching, lazy loading or access control.

**Problem Scenario**: An application downloads images from the internet and displays them.  Without caching, reloading an image results in unnecessary network requests.

```java
interface Image {
        void display();
}
class RealImage implements Image {
        private final String url;
        public RealImage(String url) { this.url = url; loadFromNetwork(); }
        private void loadFromNetwork() { System.out.println("Downloading " + url); }
        public void display() { System.out.println("Displaying " + url); }
}
```

**Refactored Using Proxy with Caching**

```java
class ImageProxy implements Image {
        private final String url;
        private RealImage realImage;
        public ImageProxy(String url) { this.url = url; }
        public void display() {
                if (realImage == null) realImage = new RealImage(url);
                realImage.display();
        }
}

Image image = new ImageProxy("http://example.com/img.jpg");
// First call downloads
image.display();
// Second call uses cached RealImage
image.display();
```

---

## Part 5 – Behavioral Design Patterns

Behavioral patterns are concerned with communication between objects.

### 1. Observer

Establishes a one‑to‑many dependency between objects so that when one object (the subject) changes state, all its dependents (observers) are notified and updated automatically.

**Problem Scenario**: A news agency publishes stories.  Subscribers should receive updates when a new story is published.  Without an observer pattern, the agency would have to call each subscriber manually.

```java
// Without Observer, the agency manually notifies each subscriber
class NewsAgency {
        private final List<Subscriber> subscribers = new ArrayList<>();
        public void publishStory(String story) {
                for (Subscriber s : subscribers) {
                        s.update(story);
                }
        }
        public void addSubscriber(Subscriber s) { subscribers.add(s); }
}
interface Subscriber { void update(String story); }
```

**Refactored Using Observer**

```java
interface Observer { void update(String data); }
interface Subject {
        void attach(Observer o);
        void detach(Observer o);
        void notifyObservers();
}
class NewsAgency implements Subject {
        private final List<Observer> observers = new ArrayList<>();
        private String latestNews;
        public void attach(Observer o) { observers.add(o); }
        public void detach(Observer o) { observers.remove(o); }
        public void publishNews(String news) { this.latestNews = news; notifyObservers(); }
        public void notifyObservers() { for (Observer o : observers) o.update(latestNews); }
}
class Reader implements Observer {
        private final String name;
        public Reader(String name) { this.name = name; }
        public void update(String news) { System.out.println(name + " received: " + news); }
}

NewsAgency agency = new NewsAgency();
Observer john = new Reader("John");
Observer jane = new Reader("Jane");
agency.attach(john);
agency.attach(jane);
agency.publishNews("New Observer Pattern Article!");
```

### 2. Strategy

Defines a family of algorithms, encapsulates each one, and makes them interchangeable.  Strategy lets the algorithm vary independently from clients that use it.

**Problem Scenario**: An image processing program applies different filters (grayscale, sepia, invert).  Without strategy, a single `applyFilter()` method would contain multiple `if` statements.

```java
public void applyFilter(String type, BufferedImage img) {
        if (type.equals("grayscale")) {
                // convert img to grayscale
        } else if (type.equals("sepia")) {
                // apply sepia tone
        } else if (type.equals("invert")) {
                // invert colors
        }
}
```

**Refactored Using Strategy**

```java
interface FilterStrategy { void apply(BufferedImage img); }
class GrayscaleFilter implements FilterStrategy { public void apply(BufferedImage img) { /* grayscale logic */ } }
class SepiaFilter implements FilterStrategy    { public void apply(BufferedImage img) { /* sepia logic */ } }
class InvertFilter implements FilterStrategy   { public void apply(BufferedImage img) { /* invert logic */ } }

class ImageProcessor {
        private FilterStrategy strategy;
        public void setStrategy(FilterStrategy strategy) { this.strategy = strategy; }
        public void process(BufferedImage img) { strategy.apply(img); }
}
// Usage
ImageProcessor processor = new ImageProcessor();
processor.setStrategy(new GrayscaleFilter());
processor.process(image);
```

### 3. Template Method

Defines the skeleton of an algorithm in an operation, deferring some steps to subclasses.  Template method lets subclasses redefine certain steps without changing the algorithm’s structure.

**Problem Scenario**: Data miners for various file formats (CSV, XML, JSON) implement the same sequence: open file, parse data, analyze results, output report.  Subclasses repeat the sequence, leading to code duplication.

```java
class CsvDataMiner {
        public void mine(String path) {
                readCsv(path); parseCsv(); analyze(); writeReport();
        }
        void readCsv(String path) { /* ... */ }
        void parseCsv() { /* ... */ }
        void analyze() { /* generic analysis */ }
        void writeReport() { /* ... */ }
}
class JsonDataMiner { /* similar code but reads JSON */ }
```

**Refactored Using Template Method**

```java
abstract class DataMiner {
        public final void mine(String path) {
                openFile(path);
                extractData();
                analyzeData();
                sendReport();
        }
        protected abstract void openFile(String path);
        protected abstract void extractData();
        protected void analyzeData() { System.out.println("Analyzing data"); }
        protected void sendReport() { System.out.println("Sending report"); }
}
class CsvDataMiner extends DataMiner {
        protected void openFile(String path) { System.out.println("Opening CSV: " + path); }
        protected void extractData() { System.out.println("Parsing CSV data"); }
}
class JsonDataMiner extends DataMiner {
        protected void openFile(String path) { System.out.println("Opening JSON: " + path); }
        protected void extractData() { System.out.println("Parsing JSON data"); }
}

DataMiner miner = new CsvDataMiner();
miner.mine("data.csv");
```

### 4. State

Allows an object to change its behavior when its internal state changes.  The object appears to change its class.  This pattern eliminates large conditional statements for state‑dependent behavior.

**Problem Scenario**: A `VendingMachine` behaves differently depending on whether it has money, is out of stock or is dispensing an item.  Without state pattern, methods contain multiple `if`/`switch` statements.

```java
class VendingMachine {
        private String state = "idle";
        public void insertCoin() {
                if (state.equals("idle")) { state = "hasMoney"; }
                else System.out.println("Cannot insert coin now");
        }
        public void pressButton() {
                if (state.equals("hasMoney")) { state = "dispensing"; dispense(); }
                else System.out.println("Please insert coin");
        }
        private void dispense() {
                System.out.println("Dispensing item"); state = "idle";
        }
}
```

**Refactored Using State**

```java
interface VMState {
        void insertCoin();
        void pressButton();
}
class IdleState implements VMState {
        private final VendingMachine vm;
        public IdleState(VendingMachine vm) { this.vm = vm; }
        public void insertCoin() { System.out.println("Coin inserted"); vm.setState(vm.getHasMoneyState()); }
        public void pressButton() { System.out.println("Insert coin first"); }
}
class HasMoneyState implements VMState {
        private final VendingMachine vm;
        public HasMoneyState(VendingMachine vm) { this.vm = vm; }
        public void insertCoin() { System.out.println("Already have coin"); }
        public void pressButton() { System.out.println("Dispensing"); vm.setState(vm.getIdleState()); }
}

class VendingMachine {
        private final VMState idleState;
        private final VMState hasMoneyState;
        private VMState state;
        public VendingMachine() {
                idleState = new IdleState(this);
                hasMoneyState = new HasMoneyState(this);
                state = idleState;
        }
        public void setState(VMState state) { this.state = state; }
        public VMState getIdleState() { return idleState; }
        public VMState getHasMoneyState() { return hasMoneyState; }
        public void insertCoin() { state.insertCoin(); }
        public void pressButton() { state.pressButton(); }
}

VendingMachine vm = new VendingMachine();
vm.pressButton(); // Insert coin first
vm.insertCoin();  // Coin inserted
vm.insertCoin();  // Already have coin
vm.pressButton(); // Dispensing
```

### 5. Mediator

Reduces chaotic dependencies between objects.  Components send messages through a mediator instead of directly to each other, promoting loose coupling.

**Problem Scenario**: A chat room has multiple participants.  Each participant keeps references to the others to send messages.  Adding or removing a participant requires updating every participant’s references.

```java
class Participant {
        private final String name;
        private final List<Participant> others;
        public Participant(String name) { this.name = name; this.others = new ArrayList<>(); }
        public void addParticipant(Participant p) { others.add(p); }
        public void send(String msg) {
                for (Participant p : others) p.receive(name + ": " + msg);
        }
        public void receive(String msg) { System.out.println(msg); }
}
```

**Refactored Using Mediator**

```java
interface ChatMediator { void sendMessage(String msg, Participant user); }
class ChatRoom implements ChatMediator {
        private final List<Participant> participants = new ArrayList<>();
        public void addUser(Participant user) { participants.add(user); }
        public void sendMessage(String msg, Participant user) {
                for (Participant p : participants) {
                        if (p != user) p.receive(user.getName() + ": " + msg);
                }
        }
}
class Participant {
        private final String name;
        private final ChatMediator chat;
        public Participant(String name, ChatMediator chat) { this.name = name; this.chat = chat; }
        public String getName() { return name; }
        public void send(String msg) { chat.sendMessage(msg, this); }
        public void receive(String msg) { System.out.println(msg); }
}

ChatMediator chatRoom = new ChatRoom();
Participant alice = new Participant("Alice", chatRoom);
Participant bob   = new Participant("Bob", chatRoom);
chatRoom.addUser(alice);
chatRoom.addUser(bob);
alice.send("Hello Bob");
bob.send("Hi Alice");
```

### 6. Iterator

Provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation.  Allows multiple traversals and different traversal algorithms.

**Problem Scenario**: A `BookCollection` stores books in an array.  Client code needs to iterate through books but shouldn’t know how they are stored.

```java
class BookCollection {
        private Book[] books;
        public Book get(int index) { return books[index]; }
        public int size() { return books.length; }
}
// Client code must know how to loop through the array
for (int i = 0; i < collection.size(); i++) {
        Book b = collection.get(i);
        System.out.println(b.getTitle());
}
```

**Refactored Using Iterator**

```java
interface Iterator<T> { boolean hasNext(); T next(); }
interface Aggregate<T> { Iterator<T> createIterator(); }
class BookCollection implements Aggregate<Book> {
        private final List<Book> books = new ArrayList<>();
        public void add(Book b) { books.add(b); }
        public Iterator<Book> createIterator() {
                return new Iterator<>() {
                        private int index = 0;
                        public boolean hasNext() { return index < books.size(); }
                        public Book next() { return books.get(index++); }
                };
        }
}

BookCollection collection = new BookCollection();
collection.add(new Book("Design Patterns"));
collection.add(new Book("Refactoring"));
Iterator<Book> iter = collection.createIterator();
while (iter.hasNext()) {
        System.out.println(iter.next().getTitle());
}
```

---

## Conclusion

This guide has delivered a comprehensive exploration of code smells, design principles, and design patterns.  By studying the problem examples and refactored solutions, you should develop an intuition for recognising design issues and applying appropriate patterns to solve them.  Use this document as a reference when writing or reviewing code, and continually look for opportunities to simplify and improve your designs.
## Appendix – Extended Design Pattern Discussion

The previous sections introduced each design pattern with a problem scenario and a refactored solution.  While those examples demonstrate the core idea, many patterns have multiple variations and subtleties in real‑world use.  This appendix dives deeper into each pattern, offering additional motivation, alternative implementations, pros and cons, and richer code examples.  These extended discussions help you recognise when to use a pattern and how to adapt it to your specific needs.

### Singleton: variations and best practices

**Motivation and pitfalls** – The Singleton pattern ensures there is exactly one instance of a class.  It is tempting to use singletons as a global variable replacement, but excessive use leads to hidden dependencies and difficulty testing.  To avoid problems, use singletons only when there is truly a single logical resource (e.g., configuration, printer spooler).  There are two main initialization strategies:

* **Eager initialization**: instantiate the singleton when the class is loaded.  This approach is simple and inherently thread‑safe because class loading happens once.  The downside is that the instance is created even if it is never used.
* **Lazy initialization**: delay instantiation until the first request.  Lazy initialization saves resources but must be carefully synchronized in multithreaded code.

**Thread‑safe lazy initialization** – The naive lazy implementation with a `null` check is not safe under concurrency.  To handle this, Java developers often use the **double‑checked locking** pattern:

```java
public class LazySingleton {
        private static volatile LazySingleton instance;
        private LazySingleton() {} // private constructor
        public static LazySingleton getInstance() {
                if (instance == null) {
                        synchronized (LazySingleton.class) {
                                if (instance == null) {
                                        instance = new LazySingleton();
                                }
                        }
                }
                return instance;
        }
}

// Usage – threads safely obtain the single instance
LazySingleton s = LazySingleton.getInstance();
```

The `volatile` keyword prevents instruction reordering, ensuring that once an instance is created other threads see the fully constructed object.  The inner `if` check avoids creating multiple instances when multiple threads enter the outer `if` simultaneously.

**Using an enum** – Since Java 5, the easiest way to implement a singleton is through an `enum`.  The JVM ensures that an enum value is instantiated only once and provides serialization guarantees:

```java
public enum EnumSingleton {
        INSTANCE;
        public void doSomething() {
                System.out.println("Doing something");
        }
}

EnumSingleton singleton = EnumSingleton.INSTANCE;
singleton.doSomething();
```

Enums avoid the complexities of double‑checked locking and protect against multiple instantiation through serialization or reflection attacks.  However, they cannot extend other classes, and some developers find them less expressive.

### Factory Method: advanced usage and variations

**When to use** – The Factory Method pattern is helpful when a class must delegate the creation of objects to its subclasses.  It removes the need for the base class to know about concrete classes, complying with the open‑closed principle.  Factory methods are also useful when a class cannot anticipate the class of the objects it needs to create or when a class wants its subclasses to specify the created objects.

**Parameterized factory** – Sometimes the type of product cannot be decided solely by the class hierarchy.  A factory method may take a parameter that influences the created object.  The following example shows a document editor supporting both text and spreadsheet documents:

```java
abstract class DocumentApp {
        public Document newDocument(String type) {
                Document doc = createDocument(type);
                doc.open();
                return doc;
        }
        protected abstract Document createDocument(String type);
}
interface Document { void open(); }
class TextDocument implements Document { public void open() { System.out.println("Opening text doc"); } }
class SpreadsheetDocument implements Document { public void open() { System.out.println("Opening spreadsheet doc"); } }

class OfficeApp extends DocumentApp {
        protected Document createDocument(String type) {
                return switch (type) {
                        case "text" -> new TextDocument();
                        case "sheet" -> new SpreadsheetDocument();
                        default -> throw new IllegalArgumentException("Unknown type " + type);
                };
        }
}

DocumentApp app = new OfficeApp();
Document txt = app.newDocument("text");
Document sheet = app.newDocument("sheet");
```

Even though the factory method uses a `switch`, the `switch` lives in one place (the factory).  Adding a new document type involves updating only the factory subclass rather than sprinkling conditionals across the client code.

**Factory with lambdas** – In Java 8 and above, the factory method can use functional interfaces to supply objects.  A `Supplier` is a zero‑argument function that returns an instance.  This leads to simple factories without explicit subclasses:

```java
import java.util.Map;
import java.util.function.Supplier;

class ShapeFactory {
        private final Map<String, Supplier<Shape>> registry;
        public ShapeFactory() {
                registry = Map.of(
                        "circle", Circle::new,
                        "square", Square::new,
                        "triangle", Triangle::new
                );
        }
        public Shape create(String type) {
                Supplier<Shape> s = registry.get(type);
                if (s == null) throw new IllegalArgumentException("Unknown shape");
                return s.get();
        }
}
interface Shape { void draw(); }
class Circle implements Shape { public void draw() { System.out.println("Drawing circle"); } }
class Square implements Shape { public void draw() { System.out.println("Drawing square"); } }
class Triangle implements Shape { public void draw() { System.out.println("Drawing triangle"); } }

ShapeFactory factory = new ShapeFactory();
Shape s1 = factory.create("circle");
s1.draw();
```

This lambda‑based factory is easy to extend: just add a new entry to the `Map`.  It remains open for extension and closed for modification.

### Abstract Factory: cross‑platform design

**Motivation** – When software needs to create families of related objects (such as buttons, scroll bars and windows) that must work together, the Abstract Factory pattern provides an interface for creating the entire family without specifying concrete classes.  It ensures that products from the same family are used together.

**Cross‑platform UI example** – Suppose we are developing a drawing application that supports multiple operating systems (Windows, macOS, Linux).  Each OS has a distinct look and feel.  We need to build the same user interface with platform‑appropriate widgets.  The naïve approach uses conditional logic throughout the codebase:

```java
// Problem: conditional creation sprinkled across the code
Button button;
Panel panel;
if (os.equals("Windows")) {
        button = new WinButton();
        panel  = new WinPanel();
} else if (os.equals("Mac")) {
        button = new MacButton();
        panel  = new MacPanel();
} else {
        button = new GtkButton();
        panel  = new GtkPanel();
}
button.render(); panel.render();
```

**Refactored using Abstract Factory** – We define product interfaces (`Button`, `Panel`) and factory interfaces.  Concrete factories implement the creation of matching products.  Clients receive a factory and use it to create UI elements.  Adding a new platform involves creating a new factory and product classes without modifying existing code:

```java
interface Button { void render(); }
interface Panel  { void render(); }
class WinButton implements Button { public void render() { System.out.println("Win button"); } }
class WinPanel  implements Panel  { public void render() { System.out.println("Win panel");  } }
class MacButton implements Button { public void render() { System.out.println("Mac button"); } }
class MacPanel  implements Panel  { public void render() { System.out.println("Mac panel");  } }
class GtkButton implements Button { public void render() { System.out.println("Gtk button"); } }
class GtkPanel  implements Panel  { public void render() { System.out.println("Gtk panel");  } }

interface UIFactory {
        Button createButton();
        Panel createPanel();
}
class WinFactory implements UIFactory {
        public Button createButton() { return new WinButton(); }
        public Panel  createPanel()  { return new WinPanel();  }
}
class MacFactory implements UIFactory {
        public Button createButton() { return new MacButton(); }
        public Panel  createPanel()  { return new MacPanel();  }
}
class GtkFactory implements UIFactory {
        public Button createButton() { return new GtkButton(); }
        public Panel  createPanel()  { return new GtkPanel();  }
}

class DrawingApp {
        private final UIFactory factory;
        public DrawingApp(UIFactory factory) { this.factory = factory; }
        public void renderUI() {
                Button b = factory.createButton();
                Panel  p = factory.createPanel();
                b.render(); p.render();
        }
}

DrawingApp app = new DrawingApp(new MacFactory());
app.renderUI();
```

Adding a dark mode theme is as simple as creating `DarkButton` and `DarkPanel` classes and a `DarkFactory`.  None of the existing factories or client code changes.

### Builder: telescoping constructors and immutability

**Telescoping constructors** – A class with many optional parameters becomes unwieldy when you need multiple constructors.  For instance, a `Person` class might have `firstName`, `lastName`, `age`, `address`, `email`, `phone`, etc.  A telescoping constructor would require many overloaded constructors:

```java
class Person {
        private final String firstName;
        private final String lastName;
        private final Integer age;
        private final String address;
        private final String email;
        private final String phone;
        // Telescoping constructor: hard to read and maintain
        public Person(String firstName, String lastName) {
                this(firstName, lastName, null, null, null, null);
        }
        public Person(String firstName, String lastName, Integer age) {
                this(firstName, lastName, age, null, null, null);
        }
        public Person(String firstName, String lastName, Integer age,
                                    String address, String email, String phone) {
                this.firstName = firstName;
                this.lastName  = lastName;
                this.age      = age;
                this.address  = address;
                this.email    = email;
                this.phone    = phone;
        }
}
```

**Fluent builder** – The Builder pattern solves this by separating object construction from its representation.  It provides a fluent API that lets you set optional fields step by step.  Once all fields are configured, call `build()` to create an immutable object.  The builder can enforce invariants and supply defaults:

```java
class Person {
        private final String firstName;
        private final String lastName;
        private final Integer age;
        private final String address;
        private final String email;
        private final String phone;
        private Person(Builder builder) {
                this.firstName = builder.firstName;
                this.lastName  = builder.lastName;
                this.age       = builder.age;
                this.address   = builder.address;
                this.email     = builder.email;
                this.phone     = builder.phone;
        }
        public static class Builder {
                private final String firstName;
                private final String lastName;
                private Integer age;
                private String address;
                private String email;
                private String phone;
                public Builder(String firstName, String lastName) {
                        this.firstName = firstName;
                        this.lastName  = lastName;
                }
                public Builder age(int age) { this.age = age; return this; }
                public Builder address(String a) { this.address = a; return this; }
                public Builder email(String e) { this.email = e; return this; }
                public Builder phone(String p) { this.phone = p; return this; }
                public Person build() { return new Person(this); }
        }
        public String toString() {
                return firstName + " " + lastName + (age != null ? ", age " + age : "") +
                             (address != null ? ", address=" + address : "") +
                             (email != null ? ", email=" + email : "");
        }
}

Person bob = new Person.Builder("Bob", "Builder").age(30)
                             .address("123 Main St").email("bob@example.com").build();
System.out.println(bob);
```

Builders often return `this` to enable method chaining, producing a readable call chain.  The built object can be immutable, improving thread safety and reliability.

**Immutable builders** – Some builders create immutable intermediate steps.  Instead of mutating the builder, each setter returns a new builder with the updated state (similar to functional programming).  This approach ensures that intermediate builder states are never modified, but it can be heavier due to object creation.

### Prototype: deep vs shallow copying

**Shallow copy** – The prototype pattern copies an existing object (prototype) to create new instances quickly.  The default `clone()` implementation in Java performs a **shallow copy**, copying primitive fields and object references without cloning referenced objects.  Shallow copy is fast but the prototype and clone share mutable internal objects, leading to unintended side effects.

```java
class Address { String street; String city; }
class Employee implements Cloneable {
        String name;
        Address address;
        public Employee clone() {
                try { return (Employee) super.clone(); }
                catch (CloneNotSupportedException e) { throw new AssertionError(); }
        }
}

Employee e1 = new Employee();
e1.name = "Alice";
e1.address = new Address();
e1.address.street = "1st Ave";
Employee e2 = e1.clone();
e2.address.street = "2nd Ave";
// e1.address.street is now "2nd Ave" because address reference is shared
```

**Deep copy** – To avoid shared references, implement a deep copy.  Each object creates its own copy of mutable fields when cloning:

```java
class Address implements Cloneable {
        String street; String city;
        public Address clone() {
                try { return (Address) super.clone(); }
                catch (CloneNotSupportedException e) { throw new AssertionError(); }
        }
}
class Employee implements Cloneable {
        String name; Address address;
        public Employee clone() {
                Employee copy;
                try { copy = (Employee) super.clone(); }
                catch (CloneNotSupportedException e) { throw new AssertionError(); }
                copy.address = address.clone(); // deep copy nested object
                return copy;
        }
}

Employee e1 = new Employee();
e1.name = "Bob";
e1.address = new Address();
e1.address.street = "Main St";
Employee e2 = e1.clone();
e2.address.street = "Elm St";
// e1.address.street remains "Main St"
```

When using prototypes, consider whether you need deep or shallow copies.  Also, think about whether you can implement copying by serializing and deserializing the object (e.g., via JSON), though that may be slower.

### Bridge: decoupling abstractions and implementations

**Motivation** – When a class has multiple dimensions of variability, a single inheritance hierarchy leads to a combinatorial explosion of subclasses.  The Bridge pattern splits the class into an **abstraction** hierarchy and an **implementation** hierarchy.  Each abstraction contains a reference to an implementation and delegates work to it.  The two hierarchies can vary independently.

**Example – shapes and colours** – Suppose you need to draw shapes (circle, square) in different colours (red, blue).  Without a bridge, you might create `RedCircle`, `BlueCircle`, `RedSquare`, `BlueSquare`.  With the bridge, you create separate shape and colour hierarchies:

```java
// Implementor hierarchy
interface Color { String fill(); }
class Red implements Color { public String fill() { return "red"; } }
class Blue implements Color { public String fill() { return "blue"; } }

// Abstraction hierarchy
abstract class Shape {
        protected Color color;
        protected Shape(Color color) { this.color = color; }
        public abstract void draw();
}
class Circle extends Shape {
        private final int radius;
        public Circle(int radius, Color color) { super(color); this.radius = radius; }
        public void draw() {
                System.out.println("Drawing a " + color.fill() + " circle of radius " + radius);
        }
}
class Square extends Shape {
        private final int side;
        public Square(int side, Color color) { super(color); this.side = side; }
        public void draw() {
                System.out.println("Drawing a " + color.fill() + " square of side " + side);
        }
}

Shape redCircle = new Circle(5, new Red());
redCircle.draw(); // outputs: Drawing a red circle of radius 5
Shape blueSquare = new Square(3, new Blue());
blueSquare.draw(); // outputs: Drawing a blue square of side 3
```

The `Shape` abstraction delegates colour selection to the `Color` implementor.  Adding a new shape or new colour doesn’t multiply classes: a new colour is just a new `Color` implementation, and a new shape is a new `Shape` subclass.

### Adapter: class vs object adapters

**Class adapter** – The canonical example of an adapter converts one interface into another.  In languages that support multiple inheritance, you can create an adapter by inheriting from both the target interface and the adaptee class.  Java doesn’t support multiple implementation inheritance, but you can simulate a class adapter by extending the adaptee and implementing the target interface:

```java
// Target interface
interface MediaPlayer { void playMp3(String filename); }
// Adaptee class
class AdvancedMediaPlayer {
        public void playVlc(String filename) { System.out.println("Playing VLC: " + filename); }
        public void playMp4(String filename) { System.out.println("Playing MP4: " + filename); }
}
// Class adapter: inherits from AdvancedMediaPlayer and adapts to MediaPlayer
class MediaAdapter extends AdvancedMediaPlayer implements MediaPlayer {
        public void playMp3(String filename) {
                // assume mp3 is supported by MP4
                playMp4(filename);
        }
}

MediaPlayer player = new MediaAdapter();
player.playMp3("song.mp3");
```

**Object adapter** – The more common form uses composition instead of inheritance.  The adapter has a reference to the adaptee and delegates calls to it.  This approach is more flexible because the adapter can wrap any implementation of the adaptee interface:

```java
// Target interface
interface LightningPhone { void recharge(); }
// Adaptee class
class MicroUsbPhone {
        public void chargeViaMicroUsb() { System.out.println("Charging with MicroUSB"); }
}
// Object adapter using composition
class LightningToMicroUsbAdapter implements LightningPhone {
        private final MicroUsbPhone phone;
        public LightningToMicroUsbAdapter(MicroUsbPhone phone) { this.phone = phone; }
        public void recharge() { phone.chargeViaMicroUsb(); }
}

MicroUsbPhone android = new MicroUsbPhone();
LightningPhone adapter = new LightningToMicroUsbAdapter(android);
adapter.recharge(); // uses MicroUSB charging behind the scenes
```

Object adapters allow you to wrap existing instances at runtime, while class adapters require subclassing and cannot adapt classes that are final or third‑party.

### Decorator: layering behaviours

**Motivation** – The Decorator pattern attaches additional responsibilities to an object dynamically.  Unlike static inheritance, decorators can be composed at runtime in any combination, giving flexibility when adding features.  Decorators are often used to add responsibilities like logging, authentication or data compression.

**Coffee example revisited** – Suppose we have a `Coffee` interface and want to add milk, sugar and whipped cream as optional enhancements.  Without decorators, we would need a `Coffee` class and subclasses like `MilkCoffee`, `SugarCoffee`, `MilkSugarCoffee`, etc.

```java
interface Coffee {
        double cost();
        String ingredients();
}
class SimpleCoffee implements Coffee {
        public double cost() { return 2.0; }
        public String ingredients() { return "Coffee"; }
}
class MilkDecorator implements Coffee {
        private final Coffee coffee;
        public MilkDecorator(Coffee coffee) { this.coffee = coffee; }
        public double cost() { return coffee.cost() + 0.5; }
        public String ingredients() { return coffee.ingredients() + ", milk"; }
}
class SugarDecorator implements Coffee {
        private final Coffee coffee;
        public SugarDecorator(Coffee coffee) { this.coffee = coffee; }
        public double cost() { return coffee.cost() + 0.3; }
        public String ingredients() { return coffee.ingredients() + ", sugar"; }
}

// Usage
Coffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);
System.out.println("Ingredients: " + coffee.ingredients());
System.out.println("Total cost: " + coffee.cost());
```

Decorators implement the same interface as the component, so they can be composed recursively.  Each decorator adds its own behavior (e.g., additional cost, extra ingredients) and delegates to the wrapped coffee.

### Composite: transparent vs safe composites

**Transparent composites** – In the transparent approach, the component interface declares all methods for both leaf and composite objects.  Clients can treat all components uniformly, but leaf nodes must implement methods that are not relevant (e.g., `add()`/`remove()` in a file system).

**Safe composites** – The safe approach separates the interfaces for leaves and composites.  The composite interface includes methods for managing children (e.g., `add()`, `remove()`) and the leaf interface includes only the methods relevant to leaves.  Clients must check the type before calling child‑related methods.

**File system example** – The file system is a classic composite.  Files are leaves; directories are composites.  Clients can treat both as `FileSystemNode`s when traversing or calculating sizes.  A safe composite prevents files from having child management methods:

```java
interface FileSystemNode {
        long getSize();
        void print(String indent);
}
class File implements FileSystemNode {
        private final String name; private final long size;
        public File(String name, long size) { this.name = name; this.size = size; }
        public long getSize() { return size; }
        public void print(String indent) { System.out.println(indent + name + " (" + size + " bytes)"); }
}
class Directory implements FileSystemNode {
        private final String name;
        private final List<FileSystemNode> children = new ArrayList<>();
        public Directory(String name) { this.name = name; }
        public void add(FileSystemNode node) { children.add(node); }
        public long getSize() {
                long total = 0;
                for (FileSystemNode c : children) total += c.getSize();
                return total;
        }
        public void print(String indent) {
                System.out.println(indent + name + "/");
                for (FileSystemNode c : children) c.print(indent + "  ");
        }
}

Directory root = new Directory("root");
root.add(new File("readme.txt", 1200));
Directory docs = new Directory("docs");
docs.add(new File("guide.pdf", 56000));
docs.add(new File("faq.txt", 4000));
root.add(docs);
root.print("");
System.out.println("Total size: " + root.getSize());
```

Because files cannot contain children, the API prevents accidental misuse.  Clients still treat files and directories uniformly via the `FileSystemNode` interface.

### Proxy: types and real‑world analogies

**Varieties of proxies** – The Proxy pattern provides a surrogate or placeholder that controls access to an underlying object.  There are several flavours:

* **Virtual proxy** – Instantiates expensive objects on demand.  Useful when loading an object is resource intensive (e.g., loading an image from disk or a huge document from a remote server).
* **Protection proxy** – Controls access to the real object by checking permissions before delegating a request.  Often used in security contexts.
* **Remote proxy** – Represents an object in a different address space.  Remote proxies handle communication details (e.g., network calls) transparently.
* **Caching proxy** – Caches results of operations to reduce expensive calls (similar to virtual proxy but caches multiple results).

**Virtual proxy example** – Suppose we have a `BigImage` class that loads image data from disk in its constructor.  We want to avoid loading the image until it is actually needed:

```java
class BigImage implements Image {
        private final String filename;
        private byte[] data;
        public BigImage(String filename) {
                this.filename = filename;
                load();
        }
        private void load() {
                System.out.println("Loading image from disk: " + filename);
                data = new byte[10_000_000]; // simulate expensive load
        }
        public void display() { System.out.println("Displaying " + filename); }
}

class LazyImageProxy implements Image {
        private final String filename;
        private BigImage real;
        public LazyImageProxy(String filename) { this.filename = filename; }
        public void display() {
                if (real == null) real = new BigImage(filename);
                real.display();
        }
}

Image img = new LazyImageProxy("photo.jpg");
// First call downloads
img.display();
// Second call uses cached RealImage
img.display();
```

**Protection proxy example** – A database connection may be wrapped by a protection proxy that checks user credentials before executing queries:

```java
interface Database {
        void executeQuery(String sql);
}
class RealDatabase implements Database {
        public void executeQuery(String sql) { System.out.println("Executing: " + sql); }
}
class SecureDatabaseProxy implements Database {
        private final Database realDb;
        private final String userRole;
        public SecureDatabaseProxy(Database realDb, String userRole) {
                this.realDb = realDb; this.userRole = userRole;
        }
        public void executeQuery(String sql) {
                if (!"admin".equals(userRole)) {
                        throw new SecurityException("Only admin can execute queries");
                }
                realDb.executeQuery(sql);
        }
}

Database db = new SecureDatabaseProxy(new RealDatabase(), "user");
try { db.executeQuery("DROP TABLE users"); }
catch (SecurityException ex) { System.out.println(ex.getMessage()); }
```

In this example, the protection proxy enforces access control.  The client interacts with the proxy via the same interface (`Database`) as the real object.

### Observer: push vs pull notifications

**Push vs pull** – The Observer pattern establishes a one‑to‑many dependency.  There are two common notification styles.  In the **push** model, the subject sends the data along with the notification.  Observers simply consume the provided data.  In the **pull** model, the subject sends only a simple notification (e.g., “I changed”) and observers query the subject for details.

**Temperature sensor example** – We model a temperature sensor (`Subject`) and displays (`Observers`).  In the push approach, the sensor notifies observers with the temperature value.  In the pull approach, observers call back to the sensor to read the latest temperature.

```java
interface Observer { void update(float temp); }
interface Subject { void attach(Observer o); void detach(Observer o); void notifyObservers(); }
class TemperatureSensor implements Subject {
        private final List<Observer> observers = new ArrayList<>();
        private float temperature;
        public void attach(Observer o) { observers.add(o); }
        public void detach(Observer o) { observers.remove(o); }
        public void setTemperature(float temp) {
                this.temperature = temp;
                notifyObservers();
        }
        public void notifyObservers() {
                // push model: send temp
                for (Observer o : observers) o.update(temperature);
        }
}
class DigitalDisplay implements Observer {
        public void update(float temp) { System.out.println("Digital display: " + temp + "°C"); }
}

TemperatureSensor sensor = new TemperatureSensor();
sensor.attach(new DigitalDisplay());
sensor.setTemperature(22.5f);
```

In the **pull** model, the `update()` method may receive a reference to the subject, or the observer may hold a reference.  It queries the state on demand:

```java
interface Observer { void update(); }
interface Subject { void attach(Observer o); void detach(Observer o); void notifyObservers(); float getTemperature(); }
class PullTemperatureSensor implements Subject {
        private final List<Observer> observers = new ArrayList<>();
        private float temperature;
        public void attach(Observer o) { observers.add(o); }
        public void detach(Observer o) { observers.remove(o); }
        public void setTemperature(float temp) {
                this.temperature = temp; notifyObservers();
        }
        public void notifyObservers() { for (Observer o : observers) o.update(); }
        public float getTemperature() { return temperature; }
}
class CelsiusDisplay implements Observer {
        private final PullTemperatureSensor sensor;
        public CelsiusDisplay(PullTemperatureSensor s) { this.sensor = s; }
        public void update() {
                System.out.println("Celsius display: " + sensor.getTemperature() + "°C");
        }
}

PullTemperatureSensor s = new PullTemperatureSensor();
CelsiusDisplay disp = new CelsiusDisplay(s);
s.attach(disp);
s.setTemperature(19.0f);
```

The pull model reduces coupling because the subject does not need to know which data observers require.  Observers can pull only the data they need.

### Strategy: selecting algorithms at runtime

**Motivation** – The Strategy pattern encapsulates interchangeable algorithms.  Clients choose the appropriate strategy at runtime.  This pattern eliminates large conditional statements and promotes open/closed design.  Strategies are often combined with dependency injection to supply the desired algorithm.

**Sorting example** – Suppose we have multiple sorting algorithms (bubble sort, quick sort, merge sort).  Without strategy, a `Sorter` class might include a `switch` over algorithm names, cluttering the code.  Using strategies, each algorithm is a separate class implementing a common interface:

```java
interface SortStrategy {
        <T extends Comparable<T>> void sort(List<T> list);
}
class BubbleSortStrategy implements SortStrategy {
        public <T extends Comparable<T>> void sort(List<T> list) {
                for (int i = 0; i < list.size() - 1; i++) {
                        for (int j = 0; j < list.size() - i - 1; j++) {
                                if (list.get(j).compareTo(list.get(j+1)) > 0) {
                                        Collections.swap(list, j, j+1);
                                }
                        }
                }
        }
}
class QuickSortStrategy implements SortStrategy {
        public <T extends Comparable<T>> void sort(List<T> list) {
                quickSort(list, 0, list.size() - 1);
        }
        private <T extends Comparable<T>> void quickSort(List<T> list, int low, int high) {
                if (low < high) {
                        int pi = partition(list, low, high);
                        quickSort(list, low, pi - 1);
                        quickSort(list, pi + 1, high);
                }
        }
        private <T extends Comparable<T>> int partition(List<T> list, int low, int high) {
                T pivot = list.get(high);
                int i = low;
                for (int j = low; j < high; j++) {
                        if (list.get(j).compareTo(pivot) <= 0) {
                                Collections.swap(list, i, j);
                                i++;
                        }
                }
                Collections.swap(list, i, high);
                return i;
        }
}

class Sorter {
        private SortStrategy strategy;
        public void setStrategy(SortStrategy s) { this.strategy = s; }
        public <T extends Comparable<T>> void sort(List<T> list) { strategy.sort(list); }
}

Sorter sorter = new Sorter();
List<Integer> numbers = Arrays.asList(5, 2, 9, 1);
sorter.setStrategy(new BubbleSortStrategy());
sorter.sort(numbers);
System.out.println(numbers);
sorter.setStrategy(new QuickSortStrategy());
sorter.sort(numbers);
System.out.println(numbers);
```

**Using functional strategies** – In modern Java, you can use functional interfaces to represent strategies.  A `Comparator<T>` is essentially a strategy for comparison.  Lambdas enable concise strategy definitions:

```java
Comparator<String> byLength = (a, b) -> Integer.compare(a.length(), b.length());
Comparator<String> alphabetical = String::compareTo;

List<String> names = new ArrayList<>(List.of("Alice", "Bob", "Charles"));
names.sort(byLength);        // sort by length
names.sort(alphabetical);    // sort alphabetically
```

### Template Method: hooks and customization

**Hooks** – The Template Method pattern defines the skeleton of an algorithm and allows subclasses to refine certain steps.  To further customise the algorithm, the base class can define **hook methods**: optional override points that do nothing by default.  Subclasses can override hooks to inject behaviour at particular points.

**Game example** – Consider a game framework that defines the sequence: initialize, start play, end play.  Subclasses override core steps and can optionally hook into the end of the game to display a message:

```java
abstract class Game {
        public final void play() {
                initialize();
                startPlay();
                if (shouldShowScore()) { showScore(); }
                endPlay();
        }
        protected abstract void initialize();
        protected abstract void startPlay();
        protected abstract void endPlay();
        // hook
        protected boolean shouldShowScore() { return false; }
        protected void showScore() { System.out.println("Score: 0"); }
}
class Chess extends Game {
        protected void initialize() { System.out.println("Initialize chess board"); }
        protected void startPlay() { System.out.println("Start playing chess"); }
        protected void endPlay() { System.out.println("End chess game"); }
        protected boolean shouldShowScore() { return true; }
        protected void showScore() { System.out.println("Chess score: 1-0"); }
}
class Checkers extends Game {
        protected void initialize() { System.out.println("Setup checkers board"); }
        protected void startPlay() { System.out.println("Start playing checkers"); }
        protected void endPlay() { System.out.println("End checkers game"); }
}

Game chess = new Chess();
chess.play();
Game checkers = new Checkers();
checkers.play();
```

The `Chess` class overrides the hook `shouldShowScore()` to display the score, whereas `Checkers` uses the default behaviour (not showing a score).  Hooks allow subclass authors to control optional parts of the template without changing the algorithm’s structure.

### State: enumerations and dynamic transitions

**Using enums to represent states** – For simple finite‑state machines, an `enum` can encapsulate state transitions.  Each constant can override behaviour for events.  This approach removes the need to implement separate classes for each state and ensures that transitions are explicit:

```java
class Turnstile {
        private State state = State.LOCKED;
        public void coin() {
                state = state.onCoin();
        }
        public void push() {
                state = state.onPush();
        }
        public void setState(State state) { this.state = state; }
}

enum State {
        LOCKED {
                public State onCoin() {
                        System.out.println("Unlocking");
                        return UNLOCKED;
                }
                public State onPush() {
                        System.out.println("Locked: please insert coin");
                        return this;
                }
        },
        UNLOCKED {
                public State onCoin() {
                        System.out.println("Coin already inserted");
                        return this;
                }
                public State onPush() {
                        System.out.println("Dispensing item");
                        return LOCKED;
                }
        };

        public State onCoin() { return this; }
        public State onPush() { return this; }
}

Turnstile t = new Turnstile();
t.push(); // Locked: please insert coin
t.coin(); // Unlocking
t.push(); // Dispensing item
```

This example demonstrates how the state pattern encapsulates behaviour for each state and controls transitions.

### Mediator: centralized communication examples

**Air traffic control** – In an airport, multiple airplanes need to communicate about takeoff and landing.  Direct communication between every pair of airplanes would be chaotic.  Instead, they communicate through the control tower (mediator).  Each airplane notifies the tower when it wants to land or take off, and the tower coordinates:

```java
interface ControlTower {
    void requestLanding(Aircraft aircraft);
    void notifyRunwayFree();
}
class AirportControlTower implements ControlTower {
    private final Queue<Aircraft> queue = new LinkedList<>();
    private boolean runwayFree = true;
    public synchronized void requestLanding(Aircraft aircraft) {
        queue.offer(aircraft);
        manageQueue();
    }
    public synchronized void notifyRunwayFree() {
        runwayFree = true;
        manageQueue();
    }
    private void manageQueue() {
        if (runwayFree && !queue.isEmpty()) {
            runwayFree = false;
            Aircraft next = queue.poll();
            System.out.println("Tower: cleared " + next.getName() + " to land.");
            next.land();
        }
    }
}
class Aircraft {
    private final String name; private final ControlTower tower;
    public Aircraft(String name, ControlTower tower) { this.name = name; this.tower = tower; }
    public String getName() { return name; }
    public void requestLanding() { tower.requestLanding(this); }
    public void land() { System.out.println(name + " landing"); try { Thread.sleep(1000); } catch (InterruptedException ignored) {} tower.notifyRunwayFree(); }
}

ControlTower tower = new AirportControlTower();
Aircraft a1 = new Aircraft("Flight A", tower);
Aircraft a2 = new Aircraft("Flight B", tower);
tower.addUser(a1);
tower.addUser(a2);
a1.requestLanding();
a2.requestLanding();
```

The control tower ensures that only one aircraft lands at a time.  Neither aircraft knows about the other’s existence; they only know the mediator.

**UI dialog boxes** – In a graphical user interface, multiple widgets need to coordinate (e.g., selecting a list item enables buttons).  Without mediation, each widget references others, leading to tight coupling.  A mediator centralizes the interactions:

```java
interface DialogMediator {
    void widgetChanged(Widget w);
}
class LoginDialog implements DialogMediator {
    public final TextField username = new TextField(this);
    public final TextField password = new TextField(this);
    public final Button okButton = new Button(this);
    public void widgetChanged(Widget w) {
        boolean enable = !username.getText().isEmpty() && !password.getText().isEmpty();
        okButton.setEnabled(enable);
    }
}
abstract class Widget {
    protected final DialogMediator dialog;
    protected Widget(DialogMediator d) { this.dialog = d; }
    public void changed() { dialog.widgetChanged(this); }
}
class TextField extends Widget {
    private String text = "";
    public TextField(DialogMediator d) { super(d); }
    public void setText(String t) { text = t; changed(); }
    public String getText() { return text; }
}
class Button extends Widget {
    private boolean enabled;
    public Button(DialogMediator d) { super(d); }
    public void setEnabled(boolean e) { this.enabled = e; System.out.println("Button enabled = " + e); }
}

LoginDialog dialog = new LoginDialog();
dialog.okButton.setEnabled(false);
// Initial state disables button

dialog.username.setText("user");
dialog.password.setText("pass");
// both fields filled – button enabled automatically
```
