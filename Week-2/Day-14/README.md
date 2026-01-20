# Day 14: Object-Oriented JavaScript & Prototypes

## 🎯 Learning Objectives

By the end of today, you will understand:
- Object-oriented programming concepts in JavaScript
- Constructor functions and the 'new' keyword
- Prototypes and the prototype chain
- ES6 class syntax and modern OOP
- Inheritance and class hierarchies
- Private fields and methods
- Static methods and properties
- Common OOP design patterns
- When to use OOP vs functional programming

## 📚 Topics to Study

### 1. Objects and Constructor Functions

```javascript
// Object Literal (Simple objects)
const user = {
  name: 'John',
  age: 30,
  greet() {
    return `Hello, I'm ${this.name}`;
  }
};

// Factory Function Pattern
function createUser(name, age) {
  return {
    name,
    age,
    greet() {
      return `Hello, I'm ${this.name}`;
    }
  };
}

const user1 = createUser('John', 30);
const user2 = createUser('Jane', 25);

// Problem: Each object has its own greet function (memory inefficient)

// Constructor Function (Capitalized by convention)
function User(name, age) {
  this.name = name;
  this.age = age;
}

// Methods on prototype (shared by all instances)
User.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

User.prototype.getAge = function() {
  return this.age;
};

// Create instances with 'new' keyword
const john = new User('John', 30);
const jane = new User('Jane', 25);

// What 'new' does:
// 1. Creates empty object {}
// 2. Sets prototype to Constructor.prototype
// 3. Calls constructor with 'this' = new object
// 4. Returns the object (unless constructor returns object)

// Checking instance
console.log(john instanceof User); // true
console.log(typeof john); // 'object'
console.log(john.constructor === User); // true

// Prototype Chain
// john -> User.prototype -> Object.prototype -> null

// Without 'new' (common mistake)
const broken = User('Bob', 40); // undefined
// 'this' refers to global object, causes bugs!

// Safeguard against missing 'new'
function SafeUser(name, age) {
  if (!(this instanceof SafeUser)) {
    return new SafeUser(name, age);
  }
  this.name = name;
  this.age = age;
}

// Object.create - Create with specific prototype
const userPrototype = {
  greet() {
    return `Hello, I'm ${this.name}`;
  }
};

const alice = Object.create(userPrototype);
alice.name = 'Alice';
alice.age = 28;
```

### 2. Prototypes and Prototype Chain

```javascript
// Understanding Prototypes

// Every function has a prototype property
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function() {
  return `Hello, I'm ${this.name}`;
};

// Every object has internal [[Prototype]] (accessible via __proto__)
const person = new Person('John');

// Prototype chain lookup
person.sayHello(); // Found on Person.prototype
person.toString(); // Found on Object.prototype
// person.nonExistent // undefined (not found in chain)

// Checking prototype
console.log(Object.getPrototypeOf(person) === Person.prototype); // true
console.log(person.__proto__ === Person.prototype); // true (legacy)

// Setting prototype
const animal = {
  eat() {
    return 'eating';
  }
};

const dog = Object.create(animal);
dog.bark = function() {
  return 'woof!';
};

// dog.eat() // 'eating' (from animal prototype)
// dog.bark() // 'woof!' (own method)

// Prototype chain visualization
// dog -> animal -> Object.prototype -> null

// hasOwnProperty - Check if property is own (not inherited)
console.log(dog.hasOwnProperty('bark')); // true
console.log(dog.hasOwnProperty('eat')); // false

// Enumeration
for (let key in dog) {
  console.log(key); // 'bark', 'eat'
}

for (let key in dog) {
  if (dog.hasOwnProperty(key)) {
    console.log(key); // Only 'bark'
  }
}

// Modern way
const ownKeys = Object.keys(dog); // Only own properties
const allKeys = [...Object.keys(dog), ...Object.keys(animal)];

// Prototype pollution (security concern)
// Never do this:
// Object.prototype.customMethod = function() { ... }
// Affects ALL objects!

// Property descriptors
const obj = { name: 'John' };

Object.defineProperty(obj, 'age', {
  value: 30,
  writable: false,    // Cannot be changed
  enumerable: true,   // Shows in for...in
  configurable: false // Cannot be deleted or redefined
});

// obj.age = 31; // Fails silently (strict mode: error)
console.log(obj.age); // 30

// Get descriptor
const descriptor = Object.getOwnPropertyDescriptor(obj, 'age');
// { value: 30, writable: false, enumerable: true, configurable: false }
```

### 3. ES6 Classes - Modern Syntax

```javascript
// Class Declaration
class User {
  // Constructor - called with 'new'
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  // Instance methods (on prototype)
  greet() {
    return `Hello, I'm ${this.name}`;
  }
  
  getAge() {
    return this.age;
  }
  
  // Getter
  get info() {
    return `${this.name}, ${this.age} years old`;
  }
  
  // Setter
  set info(value) {
    const [name, age] = value.split(', ');
    this.name = name;
    this.age = parseInt(age);
  }
  
  // Static methods (on class itself, not instances)
  static create(name, age) {
    return new User(name, age);
  }
  
  static isAdult(user) {
    return user.age >= 18;
  }
}

// Usage
const john = new User('John', 30);
john.greet(); // 'Hello, I'm John'

// Getter/Setter
console.log(john.info); // 'John, 30 years old'
john.info = 'Jane, 25';
console.log(john.name); // 'Jane'

// Static methods
const jane = User.create('Jane', 25);
User.isAdult(john); // true

// Class Expression
const Person = class {
  constructor(name) {
    this.name = name;
  }
};

// Named class expression
const Employee = class Emp {
  constructor(name) {
    this.name = name;
  }
  
  whoAmI() {
    return Emp.name; // 'Emp'
  }
};

// Classes are NOT hoisted
// const instance = new MyClass(); // Error
class MyClass {
  constructor() {}
}

// Classes are strict mode by default
class StrictClass {
  constructor() {
    // 'use strict' is automatic
    // undeclaredVar = 5; // Error
  }
}

// Computed property names
const methodName = 'dynamicMethod';
class Dynamic {
  [methodName]() {
    return 'Dynamic!';
  }
}

const d = new Dynamic();
// d.dynamicMethod() // 'Dynamic!'
```

### 4. Inheritance and Class Hierarchies

```javascript
// Extends - Class inheritance
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    return `${this.name} makes a sound`;
  }
  
  move() {
    return `${this.name} moves`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Must call super() before using 'this'
    this.breed = breed;
  }
  
  // Override parent method
  speak() {
    return `${this.name} barks`;
  }
  
  // Call parent method
  makeSound() {
    return super.speak();
  }
  
  // New method
  fetch() {
    return `${this.name} fetches the ball`;
  }
}

const dog = new Dog('Rex', 'Labrador');
dog.speak(); // 'Rex barks'
dog.makeSound(); // 'Rex makes a sound'
dog.move(); // 'Rex moves' (inherited)
dog.fetch(); // 'Rex fetches the ball'

// Instance checks
console.log(dog instanceof Dog); // true
console.log(dog instanceof Animal); // true
console.log(dog instanceof Object); // true

// Prototype chain
// dog -> Dog.prototype -> Animal.prototype -> Object.prototype -> null

// Multi-level inheritance
class Pet extends Animal {
  constructor(name, owner) {
    super(name);
    this.owner = owner;
  }
  
  getOwner() {
    return this.owner;
  }
}

class Cat extends Pet {
  constructor(name, owner, indoor) {
    super(name, owner);
    this.indoor = indoor;
  }
  
  speak() {
    return `${this.name} meows`;
  }
}

const cat = new Cat('Whiskers', 'Alice', true);

// Static inheritance
class Vehicle {
  static info() {
    return 'This is a vehicle';
  }
}

class Car extends Vehicle {
  static info() {
    return `${super.info()} - specifically a car`;
  }
}

Car.info(); // 'This is a vehicle - specifically a car'

// Extending built-in classes
class MyArray extends Array {
  first() {
    return this[0];
  }
  
  last() {
    return this[this.length - 1];
  }
  
  // Override built-in method
  map(callback) {
    const result = super.map(callback);
    return new MyArray(...result);
  }
}

const arr = new MyArray(1, 2, 3, 4, 5);
arr.first(); // 1
arr.last(); // 5
const doubled = arr.map(x => x * 2); // Still MyArray instance
```

### 5. Private Fields and Methods (ES2022)

```javascript
// Private fields (start with #)
class BankAccount {
  // Private fields must be declared
  #balance = 0;
  #accountNumber;
  
  constructor(accountNumber, initialBalance = 0) {
    this.#accountNumber = accountNumber;
    this.#balance = initialBalance;
  }
  
  // Public method accessing private field
  deposit(amount) {
    if (amount > 0) {
      this.#balance += amount;
      return this.#balance;
    }
    throw new Error('Invalid amount');
  }
  
  withdraw(amount) {
    if (amount > 0 && amount <= this.#balance) {
      this.#balance -= amount;
      return this.#balance;
    }
    throw new Error('Invalid amount or insufficient funds');
  }
  
  getBalance() {
    return this.#balance;
  }
  
  // Private method
  #validateTransaction(amount) {
    return amount > 0 && amount <= this.#balance;
  }
  
  // Using private method
  transfer(amount, targetAccount) {
    if (this.#validateTransaction(amount)) {
      this.withdraw(amount);
      targetAccount.deposit(amount);
      return true;
    }
    return false;
  }
  
  // Private static field
  static #bankName = 'MyBank';
  
  // Private static method
  static #generateAccountNumber() {
    return Math.random().toString(36).substr(2, 9);
  }
  
  static createAccount(initialBalance) {
    const accountNumber = this.#generateAccountNumber();
    return new BankAccount(accountNumber, initialBalance);
  }
}

const account = new BankAccount('ACC123', 1000);
account.deposit(500); // 1500
account.withdraw(200); // 1300
account.getBalance(); // 1300

// account.#balance // SyntaxError: Private field
// account.#validateTransaction(100) // SyntaxError

// Weak encapsulation alternative (using closures)
function createBankAccount(accountNumber, initialBalance = 0) {
  let balance = initialBalance; // Private via closure
  
  return {
    deposit(amount) {
      if (amount > 0) {
        balance += amount;
        return balance;
      }
      throw new Error('Invalid amount');
    },
    
    withdraw(amount) {
      if (amount > 0 && amount <= balance) {
        balance -= amount;
        return balance;
      }
      throw new Error('Invalid amount or insufficient funds');
    },
    
    getBalance() {
      return balance;
    }
  };
}

// Private conventions (before # syntax)
class OldStylePrivate {
  constructor() {
    this._privateField = 'convention: treat as private';
  }
  
  _privateMethod() {
    // Convention: underscore prefix means private
  }
}
```

### 6. Mixins and Composition

```javascript
// Mixins - Add functionality to classes

// Mixin object
const canEat = {
  eat(food) {
    return `${this.name} is eating ${food}`;
  }
};

const canWalk = {
  walk() {
    return `${this.name} is walking`;
  }
};

const canSwim = {
  swim() {
    return `${this.name} is swimming`;
  }
};

// Apply mixins
class Person {
  constructor(name) {
    this.name = name;
  }
}

Object.assign(Person.prototype, canEat, canWalk);

const person = new Person('John');
person.eat('pizza'); // 'John is eating pizza'
person.walk(); // 'John is walking'
// person.swim() // Error: not defined

// Mixin function
function applyMixins(targetClass, ...mixins) {
  mixins.forEach(mixin => {
    Object.assign(targetClass.prototype, mixin);
  });
}

class Fish {
  constructor(name) {
    this.name = name;
  }
}

applyMixins(Fish, canEat, canSwim);

const fish = new Fish('Nemo');
fish.swim(); // 'Nemo is swimming'

// Functional mixin (returns class)
const Timestamped = (Base) => class extends Base {
  constructor(...args) {
    super(...args);
    this.createdAt = new Date();
  }
  
  age() {
    return Date.now() - this.createdAt.getTime();
  }
};

const Nameable = (Base) => class extends Base {
  constructor(name, ...args) {
    super(...args);
    this.name = name;
  }
  
  toString() {
    return this.name;
  }
};

// Compose mixins
class Entity {
  constructor() {
    this.id = Math.random();
  }
}

class User extends Timestamped(Nameable(Entity)) {
  constructor(name, email) {
    super(name);
    this.email = email;
  }
}

const user = new User('John', 'john@example.com');
user.name; // 'John'
user.toString(); // 'John'
user.age(); // milliseconds since creation
user.createdAt; // Date object

// Composition over inheritance
// Instead of deep class hierarchies, compose behaviors

class Task {
  constructor(title) {
    this.title = title;
    this.completed = false;
  }
  
  complete() {
    this.completed = true;
  }
}

// Add behaviors via composition
const withPriority = (task, priority) => ({
  ...task,
  priority,
  getPriority() {
    return this.priority;
  }
});

const withDueDate = (task, dueDate) => ({
  ...task,
  dueDate,
  isOverdue() {
    return Date.now() > this.dueDate.getTime();
  }
});

const task = new Task('Learn OOP');
const priorityTask = withPriority(task, 'high');
const fullTask = withDueDate(priorityTask, new Date('2024-12-31'));
```

### 7. Common Design Patterns

```javascript
// Singleton Pattern - Single instance
class Singleton {
  static #instance = null;
  
  constructor() {
    if (Singleton.#instance) {
      return Singleton.#instance;
    }
    Singleton.#instance = this;
    this.data = [];
  }
  
  static getInstance() {
    if (!Singleton.#instance) {
      Singleton.#instance = new Singleton();
    }
    return Singleton.#instance;
  }
}

const s1 = new Singleton();
const s2 = new Singleton();
console.log(s1 === s2); // true (same instance)

// Factory Pattern - Create objects
class UserFactory {
  static createUser(type, data) {
    switch (type) {
      case 'admin':
        return new Admin(data.name, data.permissions);
      case 'customer':
        return new Customer(data.name, data.email);
      case 'guest':
        return new Guest();
      default:
        throw new Error('Unknown user type');
    }
  }
}

class Admin {
  constructor(name, permissions) {
    this.name = name;
    this.permissions = permissions;
    this.role = 'admin';
  }
}

class Customer {
  constructor(name, email) {
    this.name = name;
    this.email = email;
    this.role = 'customer';
  }
}

class Guest {
  constructor() {
    this.role = 'guest';
  }
}

const admin = UserFactory.createUser('admin', {
  name: 'John',
  permissions: ['read', 'write', 'delete']
});

// Observer Pattern - Event system
class EventEmitter {
  #listeners = {};
  
  on(event, callback) {
    if (!this.#listeners[event]) {
      this.#listeners[event] = [];
    }
    this.#listeners[event].push(callback);
  }
  
  off(event, callback) {
    if (!this.#listeners[event]) return;
    this.#listeners[event] = this.#listeners[event]
      .filter(cb => cb !== callback);
  }
  
  emit(event, data) {
    if (!this.#listeners[event]) return;
    this.#listeners[event].forEach(callback => callback(data));
  }
}

class DataStore extends EventEmitter {
  #data = {};
  
  set(key, value) {
    this.#data[key] = value;
    this.emit('change', { key, value });
  }
  
  get(key) {
    return this.#data[key];
  }
}

const store = new DataStore();
store.on('change', ({ key, value }) => {
  console.log(`${key} changed to ${value}`);
});

store.set('name', 'John'); // Triggers listener

// Builder Pattern - Complex object construction
class QueryBuilder {
  #query = {
    select: [],
    from: '',
    where: [],
    orderBy: [],
    limit: null
  };
  
  select(...fields) {
    this.#query.select.push(...fields);
    return this; // Method chaining
  }
  
  from(table) {
    this.#query.from = table;
    return this;
  }
  
  where(condition) {
    this.#query.where.push(condition);
    return this;
  }
  
  orderBy(field, direction = 'ASC') {
    this.#query.orderBy.push({ field, direction });
    return this;
  }
  
  limit(count) {
    this.#query.limit = count;
    return this;
  }
  
  build() {
    // Convert to SQL string
    const parts = [];
    
    if (this.#query.select.length) {
      parts.push(`SELECT ${this.#query.select.join(', ')}`);
    }
    
    if (this.#query.from) {
      parts.push(`FROM ${this.#query.from}`);
    }
    
    if (this.#query.where.length) {
      parts.push(`WHERE ${this.#query.where.join(' AND ')}`);
    }
    
    if (this.#query.orderBy.length) {
      const orderClauses = this.#query.orderBy
        .map(({ field, direction }) => `${field} ${direction}`);
      parts.push(`ORDER BY ${orderClauses.join(', ')}`);
    }
    
    if (this.#query.limit) {
      parts.push(`LIMIT ${this.#query.limit}`);
    }
    
    return parts.join(' ');
  }
}

const query = new QueryBuilder()
  .select('id', 'name', 'email')
  .from('users')
  .where('age >= 18')
  .where('active = true')
  .orderBy('name', 'ASC')
  .limit(10)
  .build();

// SELECT id, name, email FROM users WHERE age >= 18 AND active = true ORDER BY name ASC LIMIT 10
```

## 💡 Key Concepts

### OOP Principles

1. **Encapsulation**: Bundle data and methods, hide internals
2. **Inheritance**: Reuse code through class hierarchies
3. **Polymorphism**: Same interface, different implementations
4. **Abstraction**: Hide complexity, show only essentials

### When to Use OOP

- **Good for:**
  - Modeling real-world entities
  - Stateful applications
  - Large applications with shared behavior
  - Frameworks and libraries
  - UI components

- **Less suitable for:**
  - Simple data transformations
  - Stateless utilities
  - Highly concurrent operations
  - Functional data pipelines

### Prototypes vs Classes

- Classes are syntactic sugar over prototypes
- Prototypes more flexible, classes more familiar
- Both create prototype chain
- Classes enforce certain patterns (no hoisting, strict mode)

### Common Pitfalls

- Forgetting 'new' keyword
- Deep inheritance hierarchies (prefer composition)
- Overusing classes (not everything needs to be a class)
- Not understanding 'this' binding
- Mutating shared prototype objects
- Creating too many private fields (performance)

## 🛠️ Practical Exercises

### Exercise 1: Build Class Hierarchy
Create inheritance chain:
- Base Vehicle class
- Car, Motorcycle, Truck subclasses
- Shared and unique methods
- Method overriding
- Static utilities

### Exercise 2: Encapsulation Practice
Implement with private fields:
- Shopping cart with private items
- User authentication with private token
- Database connection with private config
- Game character with private health

### Exercise 3: Design Patterns
Implement patterns:
- Singleton for configuration
- Factory for creating different user types
- Observer for event system
- Builder for complex queries
- Strategy for different algorithms

### Exercise 4: Composition vs Inheritance
Refactor:
- Deep class hierarchy to composition
- Mixins for shared behavior
- Functional composition
- Compare approaches

### Exercise 5: Real-World Modeling
Model systems:
- Library management (books, members, loans)
- E-commerce (products, cart, orders)
- Task management (tasks, projects, users)
- Social media (posts, comments, likes)

## 🎨 Project: Task Management System

Build a comprehensive OOP-based task management system.

**Class Structure:**
- `Task` - Individual task with properties and methods
- `Project` - Container for related tasks
- `User` - System user with permissions
- `TaskManager` - Main coordinator

**Core Features:**
- Create, update, delete tasks
- Assign tasks to users
- Set priorities and due dates
- Mark tasks complete
- Filter and search tasks
- Project organization
- User authentication
- Activity logging

**OOP Concepts Applied:**
- Encapsulation with private fields
- Inheritance (different task types)
- Polymorphism (task behaviors)
- Composition (tasks contain subtasks)
- Static utilities
- Getters/setters
- Method chaining

**Design Patterns:**
- Singleton for TaskManager
- Factory for creating tasks
- Observer for task updates
- Builder for complex task creation
- Strategy for different sorting

**Advanced Features:**
- Task dependencies
- Recurring tasks
- Task templates
- Notifications system
- Undo/redo operations
- Import/export functionality
- Statistics and reports

## 📖 Resources to Explore

### Documentation
- [MDN: Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
- [MDN: Inheritance and Prototype Chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
- [MDN: Private Class Features](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields)

### Essential Reading
- You Don't Know JS: this & Object Prototypes
- JavaScript: The Good Parts (Douglas Crockford)
- Object-Oriented JavaScript
- Design Patterns: Elements of Reusable Code

### Articles
- Understanding Prototypes in JavaScript
- ES6 Classes vs Constructor Functions
- Composition Over Inheritance
- JavaScript Design Patterns
- When to Use Classes in JavaScript

### Video Content
- JavaScript OOP Crash Course
- Prototypes and Inheritance Explained
- ES6 Classes Deep Dive
- Design Patterns in JavaScript

### Practice
- OOP exercises on Exercism
- Design pattern implementations
- Refactoring exercises
- LeetCode OOP problems

## 🔍 Interview Questions

### Fundamental Questions

1. **What is the prototype chain in JavaScript?**
   - How inheritance works
   - Prototype vs __proto__
   - Lookup mechanism

2. **Explain the difference between classical and prototypal inheritance.**
   - Class-based vs prototype-based
   - JavaScript's approach
   - Advantages/disadvantages

3. **What does the 'new' keyword do?**
   - Four steps of object creation
   - 'this' binding
   - Return value

4. **How do ES6 classes differ from constructor functions?**
   - Syntactic sugar
   - Hoisting differences
   - Strict mode

5. **What are private fields and how do they work?**
   - # syntax
   - True privacy
   - Vs underscore convention

### Practical Questions

6. **Implement inheritance without using 'extends' keyword.**

7. **Create a mixin to add functionality to multiple classes.**

8. **Implement a singleton pattern.**

9. **Build a factory function that creates different object types.**

10. **Create a class with getter/setter that validates values.**

### Advanced Questions

11. **Explain composition vs inheritance. When to use each?**

12. **How would you implement multiple inheritance in JavaScript?**

13. **What are the drawbacks of deep class hierarchies?**

14. **Implement the observer pattern from scratch.**

15. **How do you ensure a constructor is called with 'new'?**

## ✅ Success Criteria

By the end of Day 14, you should be able to:

- [ ] Create classes with proper encapsulation
- [ ] Understand prototype chain
- [ ] Implement inheritance hierarchies
- [ ] Use private fields and methods
- [ ] Apply static methods appropriately
- [ ] Understand this binding in classes
- [ ] Choose between OOP and functional approaches
- [ ] Implement common design patterns
- [ ] Use composition effectively
- [ ] Create mixins for shared behavior
- [ ] Model real-world systems with OOP
- [ ] Complete the task management project

## 🚀 Next Steps

**Week 2 Complete!** You've mastered JavaScript & DOM fundamentals.

**Week 3 Preview** - Modern Frontend Frameworks:
- Day 15-21: React, components, hooks, state management

**What You've Learned This Week:**
- JavaScript fundamentals and ES6+
- Functions, scope, and closures
- DOM manipulation and events
- Asynchronous JavaScript
- API integration with Fetch
- Functional programming with arrays
- Object-oriented programming

**Keep building** - You now have the JavaScript foundation for modern web development!

**Review and Practice:**
- Build projects combining all week's concepts
- Contribute to open source JavaScript projects
- Practice coding challenges
- Prepare for Week 3's framework deep-dive
