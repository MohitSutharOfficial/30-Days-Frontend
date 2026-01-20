# Day 22: TypeScript Fundamentals 🔷

## 🎯 Learning Objectives

By the end of Day 22, you will:
- Understand TypeScript's core type system and benefits over JavaScript
- Master primitive types, interfaces, type aliases, and union/intersection types
- Work confidently with generics, utility types, and advanced type features
- Configure TypeScript projects with proper tsconfig.json settings
- Apply type narrowing, type guards, and discriminated unions
- Understand when and how to use enums, tuples, and literal types
- Write type-safe code that catches errors at compile-time

---

## 📚 Topics to Study

### 1. **Introduction to TypeScript**

TypeScript is a statically-typed superset of JavaScript that compiles to plain JavaScript. It provides compile-time type checking, enhanced IDE support, and better code maintainability.

**Why TypeScript?**
```typescript
// JavaScript - Runtime errors
function addNumbers(a, b) {
  return a + b;
}
addNumbers(5, "10"); // "510" - unexpected behavior

// TypeScript - Compile-time error
function addNumbers(a: number, b: number): number {
  return a + b;
}
// addNumbers(5, "10"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

### 2. **Basic Types**

```typescript
// Primitive Types
let isDone: boolean = false;
let decimal: number = 6;
let color: string = "blue";
let notDefined: undefined = undefined;
let notPresent: null = null;

// Arrays
let list: number[] = [1, 2, 3];
let genericList: Array<number> = [1, 2, 3];

// Tuples - fixed-length arrays with specific types
let tuple: [string, number] = ["hello", 10];
// tuple = [10, "hello"]; // Error: Type 'number' is not assignable to type 'string'

// Enums
enum Color {
  Red,
  Green,
  Blue
}
let c: Color = Color.Green;

enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE",
  Pending = "PENDING"
}

// Any - opt-out of type checking (use sparingly)
let notSure: any = 4;
notSure = "maybe a string";
notSure = false;

// Unknown - type-safe counterpart of any
let value: unknown = "hello";
// value.toUpperCase(); // Error
if (typeof value === "string") {
  value.toUpperCase(); // OK
}

// Void - absence of return value
function warnUser(): void {
  console.log("This is a warning message");
}

// Never - represents values that never occur
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {}
}
```

### 3. **Interfaces and Type Aliases**

```typescript
// Interface - describes the shape of an object
interface User {
  id: number;
  name: string;
  email: string;
  age?: number; // Optional property
  readonly createdAt: Date; // Readonly property
}

const user: User = {
  id: 1,
  name: "John Doe",
  email: "john@example.com",
  createdAt: new Date()
};

// user.createdAt = new Date(); // Error: Cannot assign to 'createdAt'

// Interface extension
interface AdminUser extends User {
  permissions: string[];
  role: "admin" | "superadmin";
}

// Type Alias - creates a new name for a type
type Point = {
  x: number;
  y: number;
};

type ID = string | number;

type UserRole = "admin" | "user" | "guest";

// Union Types
type Result = Success | Failure;

interface Success {
  status: "success";
  data: any;
}

interface Failure {
  status: "error";
  error: string;
}

// Intersection Types
type Employee = {
  id: number;
  name: string;
};

type Manager = {
  department: string;
  teamSize: number;
};

type ManagerEmployee = Employee & Manager;

const manager: ManagerEmployee = {
  id: 1,
  name: "Alice",
  department: "Engineering",
  teamSize: 10
};
```

### 4. **Functions in TypeScript**

```typescript
// Function with typed parameters and return type
function add(a: number, b: number): number {
  return a + b;
}

// Optional parameters
function buildName(firstName: string, lastName?: string): string {
  return lastName ? `${firstName} ${lastName}` : firstName;
}

// Default parameters
function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

// Function type expressions
type GreetFunction = (name: string) => string;

const greetUser: GreetFunction = (name) => {
  return `Hello, ${name}`;
};

// Function overloads
function createDate(timestamp: number): Date;
function createDate(year: number, month: number, day: number): Date;
function createDate(yearOrTimestamp: number, month?: number, day?: number): Date {
  if (month !== undefined && day !== undefined) {
    return new Date(yearOrTimestamp, month, day);
  }
  return new Date(yearOrTimestamp);
}
```

### 5. **Generics**

```typescript
// Generic function
function identity<T>(arg: T): T {
  return arg;
}

let output1 = identity<string>("myString");
let output2 = identity<number>(100);

// Generic array function
function getFirstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

// Generic interface
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface Product {
  id: number;
  name: string;
  price: number;
}

const response: ApiResponse<Product> = {
  data: { id: 1, name: "Laptop", price: 999 },
  status: 200,
  message: "Success"
};

// Generic constraints
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): void {
  console.log(arg.length);
}

logLength("hello"); // OK
logLength([1, 2, 3]); // OK
// logLength(10); // Error: Argument of type 'number' is not assignable

// Generic classes
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = (x, y) => x + y;
```

### 6. **Type Narrowing and Type Guards**

```typescript
// typeof type guard
function padLeft(value: string, padding: string | number) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + value;
  }
  return padding + value;
}

// instanceof type guard
class Bird {
  fly() {
    console.log("Flying...");
  }
}

class Fish {
  swim() {
    console.log("Swimming...");
  }
}

function move(animal: Bird | Fish) {
  if (animal instanceof Bird) {
    animal.fly();
  } else {
    animal.swim();
  }
}

// Custom type guards
interface Cat {
  meow(): void;
}

interface Dog {
  bark(): void;
}

function isCat(pet: Cat | Dog): pet is Cat {
  return (pet as Cat).meow !== undefined;
}

function makeSound(pet: Cat | Dog) {
  if (isCat(pet)) {
    pet.meow();
  } else {
    pet.bark();
  }
}

// Discriminated unions
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; sideLength: number }
  | { kind: "rectangle"; width: number; height: number };

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}
```

### 7. **Utility Types**

```typescript
// Partial - makes all properties optional
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>): Todo {
  return { ...todo, ...fieldsToUpdate };
}

// Required - makes all properties required
type RequiredTodo = Required<Partial<Todo>>;

// Readonly - makes all properties readonly
const todo: Readonly<Todo> = {
  title: "Learn TypeScript",
  description: "Study all the fundamentals",
  completed: false
};
// todo.completed = true; // Error

// Pick - creates a type by picking specific properties
type TodoPreview = Pick<Todo, "title" | "completed">;

const preview: TodoPreview = {
  title: "Learn TypeScript",
  completed: false
};

// Omit - creates a type by omitting specific properties
type TodoInfo = Omit<Todo, "completed">;

// Record - constructs an object type with keys K and values T
type PageInfo = {
  title: string;
  url: string;
};

type Pages = "home" | "about" | "contact";

const pages: Record<Pages, PageInfo> = {
  home: { title: "Home", url: "/" },
  about: { title: "About", url: "/about" },
  contact: { title: "Contact", url: "/contact" }
};

// ReturnType - extracts the return type of a function
function getUser() {
  return { id: 1, name: "John", email: "john@example.com" };
}

type UserReturnType = ReturnType<typeof getUser>;

// Parameters - extracts parameter types of a function
function createUser(name: string, age: number, email: string) {
  return { name, age, email };
}

type CreateUserParams = Parameters<typeof createUser>;
```

### 8. **TypeScript Configuration (tsconfig.json)**

```json
// tsconfig.json - basic configuration
{
  "compilerOptions": {
    // Language and Environment
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",
    
    // Modules
    "module": "ESNext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    
    // Emit
    "outDir": "./dist",
    "sourceMap": true,
    "declaration": true,
    "declarationMap": true,
    "removeComments": true,
    
    // Type Checking
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    
    // Interop Constraints
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    
    // Skip type checking of declaration files
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts"]
}
```

---

## 💡 Key Concepts

1. **Static Type Checking**: Catch errors at compile-time rather than runtime
2. **Type Inference**: TypeScript automatically infers types when possible
3. **Structural Typing**: Types are compatible based on their structure, not names
4. **Type Safety**: Prevents common errors like accessing undefined properties
5. **Code Documentation**: Types serve as inline documentation
6. **IDE Support**: Enhanced autocomplete, refactoring, and navigation
7. **Gradual Adoption**: Can be added incrementally to existing JavaScript projects

---

## 🛠️ Practical Exercises

### Exercise 1: Type-Safe API Client
Create a type-safe API client with proper error handling and response typing.

### Exercise 2: Generic Data Structure
Implement a generic Stack data structure with push, pop, and peek methods.

### Exercise 3: Form Validation
Build a type-safe form validation system using discriminated unions and type guards.

### Exercise 4: Advanced Types Challenge
Create a complex type system for a shopping cart with products, discounts, and calculations.

### Exercise 5: Utility Types Practice
Refactor a JavaScript codebase by adding TypeScript types using utility types.

---

## 🎨 Project: Task Management System Types

Create a comprehensive type system for a task management application:

**Requirements:**
1. Define interfaces for User, Task, Project, and Comment
2. Create union types for TaskStatus and Priority
3. Implement generic functions for filtering and sorting
4. Use utility types for partial updates and readonly views
5. Create type guards for different user roles
6. Define discriminated unions for different task types
7. Implement a type-safe event system
8. Add proper error types and result types

**Type System Features:**
- User roles (Admin, Manager, Member)
- Task states (Todo, InProgress, Review, Done)
- Priority levels (Low, Medium, High, Critical)
- Generic CRUD operations
- Type-safe event emitters
- Immutable data structures

---

## 📖 Resources to Explore

### Official Documentation
- **TypeScript Handbook**: https://www.typescriptlang.org/docs/handbook/
- **TypeScript Deep Dive**: https://basarat.gitbook.io/typescript/
- **TypeScript Playground**: https://www.typescriptlang.org/play

### Video Tutorials
- **TypeScript Course for Beginners** by freeCodeCamp
- **No BS TS** by Jack Herrington
- **TypeScript Tutorial** by Net Ninja

### Articles & Guides
- "TypeScript Best Practices" on Medium
- "Advanced TypeScript Patterns" articles
- Microsoft TypeScript Blog

### Books
- **Programming TypeScript** by Boris Cherny
- **Effective TypeScript** by Dan Vanderkam

### Tools
- **TS Node**: Execute TypeScript directly
- **TSC**: TypeScript compiler
- **TypeDoc**: Generate documentation from TypeScript

---

## 🔍 Interview Questions

### Basic
1. What is TypeScript and what are its advantages over JavaScript?
2. Explain the difference between `interface` and `type` alias.
3. What is type inference in TypeScript?
4. What are the primitive types in TypeScript?
5. Explain the `any` vs `unknown` types.

### Intermediate
1. What are generics and why are they useful?
2. Explain type narrowing and type guards.
3. What are discriminated unions?
4. How do you handle null and undefined in TypeScript?
5. What is the purpose of utility types like Partial, Pick, and Omit?
6. Explain the `never` type and when to use it.
7. What are index signatures in TypeScript?

### Advanced
1. Explain conditional types and provide examples.
2. How do mapped types work in TypeScript?
3. What is the difference between structural and nominal typing?
4. Explain covariance and contravariance in TypeScript.
5. How would you implement a type-safe event emitter?
6. What are template literal types?
7. Explain the `infer` keyword in TypeScript.
8. How do you handle circular type references?

### Practical Scenarios
1. How would you type a Redux store with TypeScript?
2. Design a type-safe API client with proper error handling.
3. How would you implement exhaustive checking in switch statements?
4. Create a type system for a form builder with validation.

---

## ✅ Success Criteria

- [ ] Understand all primitive and complex TypeScript types
- [ ] Can write interfaces and type aliases confidently
- [ ] Successfully use generics for reusable code
- [ ] Apply type narrowing and type guards appropriately
- [ ] Utilize utility types to simplify type definitions
- [ ] Configure TypeScript projects with proper settings
- [ ] Write type-safe functions with proper signatures
- [ ] Handle union and intersection types effectively
- [ ] Understand and apply discriminated unions
- [ ] Can migrate JavaScript code to TypeScript

---

## 🚀 Next Steps

Tomorrow (Day 23), you'll learn:
- **TypeScript with React**: Typing React components, hooks, props, and context
- **Advanced React TypeScript Patterns**: HOCs, render props, and compound components
- **Type-safe State Management**: Redux, Zustand with TypeScript
- **Best Practices**: Project structure and naming conventions

**Preparation:**
- Review React fundamentals from Week 3
- Practice writing generic functions
- Explore TypeScript React starter templates
- Set up a React + TypeScript project

---

**Remember**: TypeScript's power comes from its type system. Embrace strict mode, avoid `any`, and let the compiler help you write better code! 🔷
