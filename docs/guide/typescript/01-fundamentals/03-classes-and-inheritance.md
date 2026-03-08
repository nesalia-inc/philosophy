# 1.3 Classes and Inheritance

> **Learning Goal**: Master object-oriented programming in TypeScript with classes, inheritance, abstract classes, and access modifiers.

## Class Basics

### Basic Class Declaration

```typescript
class User {
  id: string;
  name: string;
  email: string;

  constructor(id: string, name: string, email: string) {
    this.id = id;
    this.name = name;
    this.email = email;
  }

  greet(): string {
    return `Hello, I'm ${this.name}!`;
  }
}

const user = new User("123", "Alice", "alice@example.com");
console.log(user.greet()); // "Hello, I'm Alice!"
```

### Constructor Parameter Properties

Simplify class declaration by declaring properties in the constructor:

```typescript
class User {
  // Equivalent to:
  // id: string;
  // name: string;
  // email: string;

  constructor(
    public id: string,
    public name: string,
    public email: string
  ) {}

  greet(): string {
    return `Hello, I'm ${this.name}!`;
  }
}
```

### Readonly Properties

```typescript
class User {
  constructor(
    public id: string,
    public readonly name: string, // Cannot be modified after creation
    private readonly createdAt: Date = new Date()
  ) {}

  // Error: Cannot assign to 'name' because it is a read-only property
  // rename(newName: string) {
  //   this.name = newName;
  // }
}
```

### Access Modifiers

TypeScript provides four access modifiers:

```typescript
class BankAccount {
  public owner: string;        // Accessible everywhere
  protected balance: number;   // Accessible in class and subclasses
  private pin: string;         // Accessible only in this class
  readonly accountNumber: string; // Cannot be modified

  constructor(owner: string, pin: string) {
    this.owner = owner;
    this.balance = 0;
    this.pin = pin;
    this.accountNumber = this.generateAccountNumber();
  }

  private generateAccountNumber(): string {
    return Math.random().toString(36).substring(7);
  }

  public deposit(amount: number): void {
    this.balance += amount;
  }

  protected deductFee(fee: number): void {
    this.balance -= fee;
  }

  public getBalance(): number {
    return this.balance;
  }
}

const account = new BankAccount("Alice", "1234");
account.owner; // OK
// account.balance; // Error: Property 'balance' is protected
// account.pin; // Error: Property 'pin' is private
// account.accountNumber = "new"; // Error: Cannot assign to readonly
```

## Inheritance

### Extending Classes

```typescript
class Animal {
  constructor(
    public name: string,
    protected age: number
  ) {}

  speak(): void {
    console.log(`${this.name} makes a sound`);
  }

  move(): void {
    console.log(`${this.name} is moving`);
  }
}

class Dog extends Animal {
  breed: string;

  constructor(name: string, age: number, breed: string) {
    super(name, age); // Must call super() first
    this.breed = breed;
  }

  speak(): void {
    console.log(`${this.name} barks!`);
  }

  fetch(): void {
    console.log(`${this.name} fetches the ball`);
  }
}

const dog = new Dog("Buddy", 5, "Golden Retriever");
dog.speak(); // "Buddy barks!"
dog.move(); // "Buddy is moving" (inherited)
dog.fetch(); // "Buddy fetches the ball"
```

### Method Overriding

```typescript
class Parent {
  greet(): string {
    return "Hello from Parent";
  }
}

class Child extends Parent {
  greet(): string {
    return "Hello from Child";
  }

  greetWithSuper(): string {
    // Call parent method using super
    return super.greet() + " and " + this.greet();
  }
}

const child = new Child();
console.log(child.greet()); // "Hello from Child"
console.log(child.greetWithSuper()); // "Hello from Parent and Hello from Child"
```

### Protected Members

```typescript
class Employee {
  constructor(
    public name: string,
    protected salary: number
  ) {}

  getSalaryInfo(): string {
    return `Salary: ${this.salary}`;
  }
}

class Manager extends Employee {
  reportTeamSize(): void {
    // Can access protected member from parent
    console.log(`Manager ${this.name} earns ${this.salary}`);
  }
}

const manager = new Manager("Alice", 100000);
manager.getSalaryInfo(); // OK
manager.reportTeamSize(); // OK
// manager.salary; // Error: Property 'salary' is protected
```

## Abstract Classes

### Abstract Classes and Methods

Abstract classes cannot be instantiated and may contain abstract methods that must be implemented by subclasses:

```typescript
abstract class Shape {
  constructor(
    protected color: string
  ) {}

  // Abstract method: must be implemented by subclasses
  abstract calculateArea(): number;

  // Concrete method: has implementation
  getColor(): string {
    return this.color;
  }

  abstract getPerimeter(): number; // Another abstract method
}

class Circle extends Shape {
  constructor(
    color: string,
    private radius: number
  ) {
    super(color);
  }

  calculateArea(): number {
    return Math.PI * this.radius * this.radius;
  }

  getPerimeter(): number {
    return 2 * Math.PI * this.radius;
  }
}

class Rectangle extends Shape {
  constructor(
    color: string,
    private width: number,
    private height: number
  ) {
    super(color);
  }

  calculateArea(): number {
    return this.width * this.height;
  }

  getPerimeter(): number {
    return 2 * (this.width + this.height);
  }
}

// Error: Cannot create an instance of an abstract class
// const shape = new Shape("red");

const circle = new Circle("red", 5);
const rectangle = new Rectangle("blue", 10, 20);
```

## Implements Clause

Classes can implement interfaces to enforce a contract:

```typescript
interface Serializable {
  toJSON(): string;
  fromJSON(json: string): void;
}

interface Loggable {
  log(message: string): void;
}

class User implements Serializable, Loggable {
  constructor(
    public id: string,
    public name: string
  ) {}

  toJSON(): string {
    return JSON.stringify({ id: this.id, name: this.name });
  }

  fromJSON(json: string): void {
    const data = JSON.parse(json);
    this.id = data.id;
    this.name = data.name;
  }

  log(message: string): void {
    console.log(`[${this.name}] ${message}`);
  }
}
```

## Getters and Setters

```typescript
class Temperature {
  private _celsius: number = 0;

  get celsius(): number {
    return this._celsius;
  }

  set celsius(value: number) {
    if (value < -273.15) {
      throw new Error("Temperature below absolute zero");
    }
    this._celsius = value;
  }

  get fahrenheit(): number {
    return (this._celsius * 9 / 5) + 32;
  }

  set fahrenheit(value: number) {
    this._celsius = (value - 32) * 5 / 9;
  }
}

const temp = new Temperature();
temp.celsius = 25;
console.log(temp.fahrenheit); // 77

temp.fahrenheit = 100;
console.log(temp.celsius); // 37.78
```

## Static Members

```typescript
class MathUtils {
  static PI: number = 3.14159;

  static add(a: number, b: number): number {
    return a + b;
  }

  static circleArea(radius: number): number {
    return this.PI * radius * radius;
  }
}

// Access static members without instantiation
console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.add(1, 2)); // 3
console.log(MathUtils.circleArea(5)); // 78.54
```

## Property Initialization

### Definite Assignment Assertion

```typescript
class User {
  name: string; // Must be initialized in constructor
  age!: number; // ! tells TypeScript we'll initialize it ourselves

  constructor(name: string) {
    this.name = name;
    this.initializeAge();
  }

  private initializeAge() {
    this.age = 0;
  }
}
```

### Optional Properties

```typescript
class User {
  constructor(
    public name: string,
    public email?: string // Optional property
  ) {}
}

const user1 = new User("Alice");
const user2 = new User("Bob", "bob@example.com");
```

## Best Practices

1. **Use constructor parameter properties** to reduce boilerplate
2. **Make properties `readonly`** when they shouldn't change after initialization
3. **Use `protected`** for properties that subclasses need but external code doesn't
4. **Prefer `private`** over `public` for internal implementation details
5. **Use abstract classes** when you have shared implementation but want to enforce a contract
6. **Use interfaces** for pure contracts without implementation
7. **Avoid inheritance for composition** - prefer composition over inheritance

## Composition Over Inheritance

```typescript
// Bad: Deep inheritance hierarchy
class Animal {}
class FlyingAnimal extends Animal {}
class Bird extends FlyingAnimal {}
class Eagle extends Bird {}

// Good: Composition
class Animal {
  constructor(private abilities: Flyable & Swimmable) {}
}

interface Flyable {
  fly(): void;
}

interface Swimmable {
  swim(): void;
}

class FlyingSwimming implements Flyable, Swimmable {
  fly(): void { console.log("Flying"); }
  swim(): void { console.log("Swimming"); }
}

const duck = new Animal(new FlyingSwimming());
```

## Common Mistakes

### Mistake 1: Forgetting super()

```typescript
class Parent {
  constructor(public name: string) {}
}

class Child extends Parent {
  constructor(public age: number) {
    // Error: Must call super() first
    // super();
  }
}

// Correct
class Child extends Parent {
  constructor(
    public name: string,
    public age: number
  ) {
    super(name); // Must call super() first
  }
}
```

### Mistake 2: Using Private When Protected is Better

```typescript
class Parent {
  private value: number = 10;

  getValue(): number {
    return this.value;
  }
}

class Child extends Parent {
  doubleValue(): number {
    // Error: Property 'value' is private
    // return this.value * 2;
    return this.getValue() * 2;
  }
}

// Better: use protected
class Parent {
  protected value: number = 10;
}
```

## Exercises

1. Create an abstract `Vehicle` class with subclasses `Car` and `Motorcycle`
2. Create a `BankAccount` class with protected balance and public deposit/withdraw methods
3. Create a `Logger` class with static methods for different log levels
4. Create a class that implements multiple interfaces
5. Refactor a deep inheritance hierarchy to use composition

## Next Lesson

Now that you understand classes and inheritance, let's explore **[1.4 Type Inference and Control Flow](./04-type-inference.md)** to learn how TypeScript infers types and narrows them automatically.
