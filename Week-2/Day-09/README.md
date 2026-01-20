# Day 09: Functions, Scope, Closures & Hoisting

## 🎯 Learning Objectives

By the end of today, you will understand:
- Function types: declarations, expressions, IIFE, higher-order functions
- Scope chain: global, function, block, and lexical scope
- Closures and their practical applications
- Hoisting behavior for variables and functions
- The 'this' keyword and different binding methods
- Pure functions and function composition

## 📚 Topics to Study

### 1. Function Types and Patterns

```javascript
// Function Declaration - Hoisted
function greet(name) {
  return `Hello, ${name}!`;
}

// Function Expression - Not hoisted
const greetExpr = function(name) {
  return `Hello, ${name}!`;
};

// Arrow Function Expression
const greetArrow = (name) => `Hello, ${name}!`;

// Named Function Expression (useful for debugging)
const factorial = function fact(n) {
  return n <= 1 ? 1 : n * fact(n - 1);
};

// IIFE (Immediately Invoked Function Expression)
// Used for creating private scope
(function() {
  const privateVar = 'Cannot access outside';
  console.log('IIFE executed');
})();

// IIFE with parameters
const result = (function(a, b) {
  return a + b;
})(5, 10); // result = 15

// Higher-Order Functions
// Function that accepts function as argument
const filter = (arr, predicate) => {
  const result = [];
  for (let item of arr) {
    if (predicate(item)) {
      result.push(item);
    }
  }
  return result;
};

// Function that returns a function
const multiplier = (factor) => {
  return (number) => number * factor;
};
const double = multiplier(2);
const triple = multiplier(3);
// double(5) // 10
// triple(5) // 15
```

### 2. Scope and Scope Chain

```javascript
// Global Scope
const globalVar = 'I am global';

function outerFunction() {
  // Function Scope
  const outerVar = 'I am outer';
  
  function innerFunction() {
    // Nested Function Scope
    const innerVar = 'I am inner';
    
    // Can access all outer scopes
    console.log(globalVar);  // ✅ Accessible
    console.log(outerVar);   // ✅ Accessible
    console.log(innerVar);   // ✅ Accessible
  }
  
  // console.log(innerVar);  // ❌ Error: not defined
}

// Block Scope (let and const)
if (true) {
  const blockScoped = 'Only in this block';
  let alsoBlockScoped = 'Also only here';
  var functionScoped = 'Available in whole function';
}
// console.log(blockScoped);     // ❌ Error
// console.log(functionScoped);  // ✅ Works (var is function-scoped)

// Lexical Scope (Static Scope)
// Inner functions have access to outer function variables
function outer() {
  const message = 'outer';
  
  function inner() {
    console.log(message); // Looks up scope chain
  }
  
  return inner;
}

// Scope Chain Example
const a = 'global';
function first() {
  const b = 'first';
  function second() {
    const c = 'second';
    function third() {
      const d = 'third';
      // Can access: d, c, b, a (follows scope chain)
    }
  }
}
```

### 3. Closures - The Power Pattern

```javascript
// Basic Closure
// Inner function retains access to outer function's variables
function createCounter() {
  let count = 0; // Private variable
  
  return {
    increment() {
      return ++count;
    },
    decrement() {
      return --count;
    },
    getCount() {
      return count;
    }
  };
}

const counter = createCounter();
// counter.increment() // 1
// counter.increment() // 2
// counter.getCount()  // 2
// count is private, cannot access directly

// Closure in Loops (Common Interview Question)
// WRONG WAY - var is function-scoped
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // Will print 3, 3, 3
  }, 100);
}

// RIGHT WAY - let is block-scoped
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // Will print 0, 1, 2
  }, 100);
}

// ALTERNATIVE - Using IIFE to create closure
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // Will print 0, 1, 2
    }, 100);
  })(i);
}

// Factory Pattern with Closures
function createBankAccount(initialBalance) {
  let balance = initialBalance; // Private
  
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

const account = createBankAccount(1000);
// account.deposit(500)    // 1500
// account.withdraw(200)   // 1300
// account.balance         // undefined (private)

// Memoization with Closures
function memoize(fn) {
  const cache = {};
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (key in cache) {
      console.log('From cache');
      return cache[key];
    }
    
    console.log('Computing...');
    const result = fn.apply(this, args);
    cache[key] = result;
    return result;
  };
}

const expensiveOperation = (n) => {
  // Simulate expensive calculation
  return n * n;
};

const memoized = memoize(expensiveOperation);
// memoized(5) // Computing... 25
// memoized(5) // From cache 25
```

### 4. Hoisting Explained

```javascript
// Variable Hoisting

// var - Declaration is hoisted, initialization is not
console.log(x); // undefined (not error)
var x = 5;
console.log(x); // 5

// Equivalent to:
// var x;
// console.log(x); // undefined
// x = 5;

// let and const - Temporal Dead Zone (TDZ)
// console.log(y); // ❌ ReferenceError: Cannot access before initialization
let y = 10;

// const z;  // ❌ SyntaxError: Missing initializer
const z = 20;

// Function Hoisting

// Function Declaration - Fully hoisted
hoistedFunction(); // ✅ Works!
function hoistedFunction() {
  return 'I am hoisted';
}

// Function Expression - NOT hoisted
// notHoisted(); // ❌ TypeError: notHoisted is not a function
const notHoisted = function() {
  return 'I am not hoisted';
};

// Class Hoisting - TDZ applies
// const instance = new MyClass(); // ❌ ReferenceError
class MyClass {
  constructor() {
    this.name = 'MyClass';
  }
}

// Real-world hoisting scenario
function processUser() {
  console.log(user); // undefined (var hoisting)
  
  if (false) {
    var user = 'John'; // Declaration hoisted to function scope
  }
  
  console.log(user); // undefined
}
```

### 5. The 'this' Keyword

```javascript
// Global Context
console.log(this); // Window (browser) or global (Node.js)

// Object Method
const person = {
  name: 'John',
  greet: function() {
    return `Hello, I'm ${this.name}`;
  }
};
// person.greet() // 'Hello, I'm John'

// Arrow Functions - Lexical 'this'
const person2 = {
  name: 'Jane',
  greet: () => {
    return `Hello, I'm ${this.name}`; // 'this' from outer scope
  }
};
// person2.greet() // 'Hello, I'm undefined'

// Constructor Function
function User(name, email) {
  this.name = name;
  this.email = email;
  this.greet = function() {
    return `Hi, I'm ${this.name}`;
  };
}
const user1 = new User('John', 'john@example.com');

// 'this' binding methods

// call() - Invoke function with specified 'this'
function introduce(greeting, punctuation) {
  return `${greeting}, I'm ${this.name}${punctuation}`;
}
const person3 = { name: 'Alice' };
// introduce.call(person3, 'Hello', '!') // 'Hello, I'm Alice!'

// apply() - Same as call(), but arguments as array
// introduce.apply(person3, ['Hi', '.']) // 'Hi, I'm Alice.'

// bind() - Returns new function with bound 'this'
const boundIntroduce = introduce.bind(person3, 'Hey');
// boundIntroduce('?') // 'Hey, I'm Alice?'

// Common 'this' pitfall
const counter = {
  count: 0,
  increment: function() {
    setTimeout(function() {
      this.count++; // 'this' is not counter!
      console.log(this.count); // NaN
    }, 100);
  }
};

// Solution 1: Arrow function
const counter2 = {
  count: 0,
  increment: function() {
    setTimeout(() => {
      this.count++; // Lexical 'this', refers to counter2
    }, 100);
  }
};

// Solution 2: Store 'this' reference
const counter3 = {
  count: 0,
  increment: function() {
    const self = this;
    setTimeout(function() {
      self.count++;
    }, 100);
  }
};
```

### 6. Pure Functions and Function Composition

```javascript
// Pure Function
// Same input always produces same output
// No side effects (doesn't modify external state)

// PURE ✅
function add(a, b) {
  return a + b;
}

function multiply(a, b) {
  return a * b;
}

// IMPURE ❌
let total = 0;
function addToTotal(value) {
  total += value; // Modifies external state
  return total;
}

// Pure function benefits
const numbers = [1, 2, 3, 4, 5];

// Pure - returns new array
const doubled = numbers.map(n => n * 2);

// Impure - modifies original
// numbers.push(6);

// Function Composition
// Combining simple functions to build complex ones

const compose = (...fns) => (value) => 
  fns.reduceRight((acc, fn) => fn(acc), value);

const pipe = (...fns) => (value) =>
  fns.reduce((acc, fn) => fn(acc), value);

// Example functions
const addOne = (x) => x + 1;
const double = (x) => x * 2;
const square = (x) => x * x;

// Compose (right to left)
const composedFunc = compose(square, double, addOne);
// composedFunc(2) // square(double(addOne(2))) = square(double(3)) = square(6) = 36

// Pipe (left to right)
const pipedFunc = pipe(addOne, double, square);
// pipedFunc(2) // square(double(addOne(2))) = square(double(3)) = square(6) = 36

// Practical composition example
const toLowerCase = (str) => str.toLowerCase();
const removeSpaces = (str) => str.replace(/\s/g, '');
const addPrefix = (str) => `user_${str}`;

const createUsername = pipe(
  toLowerCase,
  removeSpaces,
  addPrefix
);
// createUsername('John Doe') // 'user_johndoe'
```

## 💡 Key Concepts

### Scope Best Practices

1. **Use const by default**: Prevents accidental reassignment
2. **Minimize global scope pollution**: Keep variables local
3. **Leverage block scope**: Use {} to create scope when needed
4. **Avoid var**: Use let/const for predictable scoping

### Closure Use Cases

- **Data Privacy**: Hide implementation details
- **Factory Functions**: Create multiple instances with private state
- **Memoization**: Cache expensive function results
- **Event Handlers**: Maintain state across callbacks
- **Partial Application**: Pre-fill function arguments

### Understanding 'this'

- Regular functions: 'this' determined by how function is called
- Arrow functions: 'this' determined by where function is defined
- Methods: 'this' refers to the object
- Constructors: 'this' refers to new instance
- Event handlers: 'this' refers to element (unless arrow function)

### Common Mistakes to Avoid

- Relying on hoisting (declare at top)
- Using var in loops with async operations
- Arrow functions for object methods
- Not understanding 'this' context
- Modifying state in pure functions
- Creating unnecessary closures (memory leaks)

## 🛠️ Practical Exercises

### Exercise 1: Build a Module Pattern
Create a module using closures that:
- Manages a private collection of items
- Provides public methods: add, remove, find, getAll
- Implements validation before adding items
- Tracks modification count privately
- Cannot directly access internal data

### Exercise 2: Function Composition Utilities
Implement utility functions:
- compose() for right-to-left composition
- pipe() for left-to-right composition
- curry() to convert multi-arg function to curried version
- partial() for partial application
- Create practical examples using these utilities

### Exercise 3: Advanced Counter System
Build counters with different behaviors:
- Basic counter (increment, decrement, reset)
- Limited counter (min/max boundaries)
- Step counter (custom increment value)
- History tracking (undo/redo capability)
- All using closures for private state

### Exercise 4: Debounce and Throttle
Implement these important patterns:
- debounce(fn, delay): Execute after delay of inactivity
- throttle(fn, interval): Execute at most once per interval
- Test with scroll/resize events simulation
- Use closures to maintain timer state

### Exercise 5: this Binding Challenges
Create examples demonstrating:
- Losing 'this' context and fixing it
- call/apply/bind usage scenarios
- Arrow function vs regular function for methods
- Event listener 'this' binding
- Constructor function vs class 'this'

## 🎨 Project: Task Manager with Closures

Build a sophisticated task management system using closures and advanced function concepts.

**Requirements:**
- Create tasks with private properties (id, title, status, priority)
- Factory function to create task instances
- Public methods: update, complete, delete, getPriority
- Private validation logic
- Task collection manager with closures
- Search and filter functionality
- Undo/redo operations using closure history

**Core Features:**
- Add tasks with auto-generated IDs
- Update task properties safely
- Filter tasks by status, priority, date
- Sort tasks by various criteria
- Track completed vs pending count
- Statistics calculator (private state)
- Event system for task changes

**Advanced Features:**
- Memoization for expensive filters
- Debounced search functionality
- Composed validation pipelines
- Pure utility functions
- Higher-order filter/map/reduce operations
- Private state management throughout

**Bonus Challenges:**
- Implement observer pattern with closures
- Create task dependencies system
- Add recurring tasks with closure-based scheduling
- Build time-tracking with private timers

## 📖 Resources to Explore

### Documentation
- [MDN: Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)
- [MDN: Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
- [MDN: Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)
- [MDN: this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)

### Essential Reading
- You Don't Know JS: Scope & Closures
- JavaScript: The Good Parts (Douglas Crockford)
- Eloquent JavaScript - Functions chapter
- Understanding JavaScript Closures with Ease

### Articles
- Master the JavaScript Interview: What is a Closure?
- JavaScript Scope and Closures
- Understanding Hoisting in JavaScript
- The Complete Guide to 'this' in JavaScript

### Video Tutorials
- JavaScript: Understanding the Weird Parts
- Advanced JavaScript Concepts
- Closures and Scope Explained
- Mastering 'this' in JavaScript

### Practice
- JavaScript Garden - Scopes and Closures
- Closure exercises on Exercism
- HackerRank JavaScript challenges
- LeetCode JavaScript problems

## 🔍 Interview Questions

### Fundamental Questions

1. **What is a closure? Provide a practical example.**
   - Function + lexical environment
   - Access to outer scope
   - Common use cases

2. **Explain the difference between function declaration and expression.**
   - Hoisting differences
   - When to use each
   - Named vs anonymous

3. **What is hoisting? How does it work for variables and functions?**
   - Declaration hoisting
   - var vs let/const
   - Function hoisting behavior

4. **Explain scope chain in JavaScript.**
   - Lexical scoping
   - Variable lookup process
   - Global, function, block scope

5. **What is the 'this' keyword? How does its value get determined?**
   - Different contexts
   - Binding rules
   - Arrow function behavior

### Practical Questions

6. **Fix the closure in loop problem (classic interview question).**

7. **Create a function that remembers previous calls (memoization).**

8. **Implement a private counter using closures.**

9. **Write a curry function that handles any number of arguments.**

10. **Create a debounce function from scratch.**

### Advanced Questions

11. **What is the temporal dead zone (TDZ)?**

12. **Explain call, apply, and bind. When would you use each?**

13. **What are higher-order functions? Provide examples.**

14. **How do arrow functions differ from regular functions regarding 'this'?**

15. **What is a pure function and why are they important?**

16. **Explain the module pattern and its benefits.**

17. **How can closures lead to memory leaks? How to prevent them?**

18. **What is function composition and why is it useful?**

## ✅ Success Criteria

By the end of Day 09, you should be able to:

- [ ] Understand and use different function types appropriately
- [ ] Explain scope chain and lexical scoping
- [ ] Create and use closures for data privacy
- [ ] Understand hoisting for variables and functions
- [ ] Master 'this' binding in different contexts
- [ ] Use call, apply, and bind correctly
- [ ] Implement higher-order functions
- [ ] Write pure functions and compose them
- [ ] Avoid common closure pitfalls
- [ ] Implement debounce and throttle
- [ ] Create factory functions with private state
- [ ] Complete the task manager project

## 🚀 Next Steps

Tomorrow (Day 10), you'll learn:
- DOM (Document Object Model) manipulation
- Selecting and traversing DOM elements
- Creating, modifying, and removing elements
- Event handling and event delegation
- Browser APIs and DOM performance

**Preparation:**
- Review HTML structure and elements
- Understand parent-child relationships in HTML
- Practice with browser DevTools console

**Keep building** - Functions, scope, and closures are fundamental to mastering JavaScript!
