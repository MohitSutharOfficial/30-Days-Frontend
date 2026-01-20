# Day 08: JavaScript Fundamentals & ES6+ Syntax

## 🎯 Learning Objectives

By the end of today, you will understand:
- JavaScript data types, variables, and type coercion
- ES6+ features: let, const, arrow functions, template literals
- Destructuring, spread/rest operators, and default parameters
- Modern JavaScript syntax and best practices
- String, number, and boolean operations
- Type checking and conversion techniques

## 📚 Topics to Study

### 1. Variables and Data Types

```javascript
// VAR, LET, CONST - Understanding the differences
// var - function-scoped, hoisted, can be redeclared
var oldWay = 'avoid using var';

// let - block-scoped, not hoisted, cannot be redeclared
let userName = 'John';
userName = 'Jane'; // ✅ Can reassign

// const - block-scoped, not hoisted, cannot be reassigned
const API_KEY = 'abc123';
// API_KEY = 'new'; // ❌ Error: Assignment to constant variable

// Primitive Data Types
const stringType = 'Hello World';
const numberType = 42;
const bigIntType = 9007199254740991n;
const booleanType = true;
const undefinedType = undefined;
const nullType = null;
const symbolType = Symbol('unique');

// Reference Types
const arrayType = [1, 2, 3];
const objectType = { name: 'John', age: 30 };
const functionType = function() { return 'Hello'; };
```

### 2. Template Literals and String Methods

```javascript
// Template Literals - Modern string interpolation
const firstName = 'John';
const lastName = 'Doe';
const age = 30;

// ES6+ way (preferred)
const greeting = `Hello, ${firstName} ${lastName}!`;
const multiLine = `
  Name: ${firstName}
  Age: ${age}
  Status: ${age >= 18 ? 'Adult' : 'Minor'}
`;

// String Methods
const text = 'JavaScript is Amazing';
// text.toLowerCase() // 'javascript is amazing'
// text.toUpperCase() // 'JAVASCRIPT IS AMAZING'
// text.includes('Script') // true
// text.startsWith('Java') // true
// text.endsWith('ing') // true
// text.slice(0, 10) // 'JavaScript'
// text.split(' ') // ['JavaScript', 'is', 'Amazing']
// text.trim() // removes whitespace
// text.padStart(30, '*') // padding
// text.repeat(3) // repeat string
```

### 3. Arrow Functions

```javascript
// Traditional Function
function add(a, b) {
  return a + b;
}

// Arrow Function - concise syntax
const addArrow = (a, b) => a + b;

// Single parameter (parentheses optional)
const double = num => num * 2;

// No parameters
const greet = () => 'Hello!';

// Multiple statements (need curly braces and return)
const calculate = (x, y) => {
  const sum = x + y;
  const product = x * y;
  return { sum, product };
};

// Arrow functions and 'this' binding
const person = {
  name: 'John',
  // Regular function - 'this' refers to person object
  sayHi: function() {
    return `Hi, I'm ${this.name}`;
  },
  // Arrow function - 'this' is lexically bound
  sayHello: () => {
    // 'this' refers to outer scope, NOT person
    return `Hello`;
  }
};
```

### 4. Destructuring

```javascript
// Array Destructuring
const colors = ['red', 'green', 'blue', 'yellow'];
const [primary, secondary, ...others] = colors;
// primary = 'red'
// secondary = 'green'
// others = ['blue', 'yellow']

// Skip elements
const [first, , third] = colors;
// first = 'red', third = 'blue'

// Default values
const [a, b, c, d, e = 'default'] = colors;
// e = 'default' if not present

// Object Destructuring
const user = {
  id: 1,
  username: 'johndoe',
  email: 'john@example.com',
  age: 30,
  address: {
    city: 'New York',
    country: 'USA'
  }
};

// Basic destructuring
const { username, email } = user;

// Rename variables
const { username: name, age: userAge } = user;

// Nested destructuring
const { address: { city, country } } = user;

// Default values
const { role = 'user' } = user;
// role = 'user' (default value)

// Function parameter destructuring
const printUser = ({ username, email }) => {
  return `${username}: ${email}`;
};
```

### 5. Spread and Rest Operators

```javascript
// SPREAD OPERATOR (...) - Expands elements

// Array spreading
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]
const copied = [...arr1]; // shallow copy

// Object spreading
const defaults = { theme: 'light', lang: 'en' };
const userSettings = { theme: 'dark' };
const settings = { ...defaults, ...userSettings };
// { theme: 'dark', lang: 'en' } - userSettings overrides

// Spread in function calls
const numbers = [5, 1, 9, 3];
const max = Math.max(...numbers); // 9

// REST OPERATOR (...) - Collects elements

// Rest in function parameters
const sum = (...numbers) => {
  return numbers.reduce((total, num) => total + num, 0);
};
// sum(1, 2, 3, 4, 5) // 15

// Rest in destructuring
const { id, ...restOfUser } = user;
// restOfUser contains all properties except 'id'

const [head, ...tail] = [1, 2, 3, 4, 5];
// head = 1, tail = [2, 3, 4, 5]
```

### 6. Default Parameters and Enhanced Object Literals

```javascript
// Default Parameters
const greetUser = (name = 'Guest', greeting = 'Hello') => {
  return `${greeting}, ${name}!`;
};
// greetUser() // 'Hello, Guest!'
// greetUser('John') // 'Hello, John!'

// Enhanced Object Literals
const name = 'MacBook Pro';
const price = 2499;
const stock = 15;

// Shorthand property names
const product = {
  name,    // same as name: name
  price,   // same as price: price
  stock,   // same as stock: stock
  
  // Shorthand method syntax
  getInfo() {
    return `${this.name}: $${this.price}`;
  },
  
  // Computed property names
  [`is${name.replace(' ', '')}`]: true,
  
  // Dynamic keys
  [Date.now()]: 'timestamp'
};
```

### 7. Type Coercion and Comparison

```javascript
// Type Coercion - Implicit conversion
const result1 = '5' + 3;    // '53' (string concatenation)
const result2 = '5' - 3;    // 2 (numeric subtraction)
const result3 = '5' * '2';  // 10 (numeric multiplication)
const result4 = true + 1;   // 2 (true becomes 1)

// Loose Equality (==) vs Strict Equality (===)
5 == '5';    // true (coercion happens)
5 === '5';   // false (different types)
null == undefined;   // true
null === undefined;  // false

// Always use === and !== to avoid unexpected behavior

// Type Checking
typeof 'hello';        // 'string'
typeof 42;             // 'number'
typeof true;           // 'boolean'
typeof undefined;      // 'undefined'
typeof null;           // 'object' (historical bug)
typeof {};             // 'object'
typeof [];             // 'object'
Array.isArray([]);     // true (proper array check)

// Truthy and Falsy Values
// Falsy: false, 0, '', null, undefined, NaN
// Everything else is truthy

const checkValue = (val) => {
  if (val) {
    return 'Truthy';
  }
  return 'Falsy';
};
```

## 💡 Key Concepts

### ES6+ Best Practices

1. **Use const by default**: Immutability prevents bugs
2. **Use let when reassignment needed**: Block scoping prevents issues
3. **Avoid var entirely**: Function scoping causes confusion
4. **Prefer arrow functions**: Cleaner syntax, lexical this binding
5. **Use template literals**: More readable string interpolation
6. **Destructure when possible**: Cleaner code, less repetition

### Type Coercion Rules

- String + anything = string concatenation
- Number operations convert to numbers
- Boolean context: use falsy/truthy values
- Always use strict equality (===) for comparisons

### Common Mistakes to Avoid

- Mutating const objects/arrays (const prevents reassignment, not mutation)
- Using arrow functions for methods needing 'this'
- Forgetting return in arrow functions with {}
- Comparing with == instead of ===
- Not understanding reference vs primitive types

## 🛠️ Practical Exercises

### Exercise 1: Data Transformation
Create functions using ES6+ syntax to:
- Convert user data from API format to display format
- Use destructuring to extract nested properties
- Use template literals for formatted output
- Apply default parameters for optional fields

### Exercise 2: Array and Object Manipulation
Build utility functions that:
- Merge multiple objects with spread operator
- Clone arrays and objects deeply
- Extract specific properties using destructuring
- Use rest parameters to handle variable arguments

### Exercise 3: String Processing
Implement functions to:
- Validate email format using string methods
- Format phone numbers consistently
- Create URL slugs from titles (lowercase, hyphens)
- Truncate text with ellipsis at word boundaries

### Exercise 4: Type Checking and Validation
Create validators for:
- Checking if value is a valid number (not NaN)
- Determining if object is empty
- Validating array contains only certain types
- Type-safe comparison functions

### Exercise 5: Modern Syntax Refactoring
Take legacy JavaScript code and refactor to:
- Replace var with const/let appropriately
- Convert function expressions to arrow functions
- Use template literals instead of string concatenation
- Apply destructuring to reduce repetition
- Add default parameters where applicable

## 🎨 Project: Personal Profile Card Generator

Build a JavaScript module that generates formatted profile cards from user data.

**Requirements:**
- Accept user data object (name, email, age, skills, address)
- Use destructuring to extract properties
- Provide default values for optional fields
- Format output using template literals
- Create helper functions with arrow syntax
- Use spread operator to merge default and custom settings
- Implement data validation with type checking
- Return formatted HTML string or console output

**Features to Implement:**
- Name formatting (title case)
- Email validation
- Age calculation from birthdate
- Skills array formatting with commas
- Address formatting (city, state, country)
- Custom theme support (colors, layout)
- Export/import using ES6 modules

**Bonus Challenges:**
- Add computed properties for display names
- Implement card templating system
- Create multiple card layouts
- Add sanitization for HTML output

## 📖 Resources to Explore

### Documentation
- [MDN: JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [MDN: Template Literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
- [MDN: Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
- [MDN: Destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

### Articles & Tutorials
- JavaScript.info - Modern JavaScript Tutorial
- ES6 Features Overview
- Understanding 'this' in JavaScript
- Type Coercion in JavaScript Explained

### Video Content
- ES6 JavaScript: The Complete Developer's Guide
- JavaScript Fundamentals for Beginners
- Modern JavaScript From The Beginning

### Practice Platforms
- JavaScript30.com - 30 Day Vanilla JS Challenge
- freeCodeCamp JavaScript Algorithms
- Exercism JavaScript Track
- Codewars JavaScript Challenges

## 🔍 Interview Questions

### Conceptual Questions

1. **What are the differences between var, let, and const?**
   - Scope (function vs block)
   - Hoisting behavior
   - Reassignment rules
   - Temporal dead zone

2. **Explain arrow functions and when NOT to use them.**
   - Lexical this binding
   - No arguments object
   - Cannot be used as constructors
   - Not suitable for object methods

3. **What is destructuring and why is it useful?**
   - Extract values from arrays/objects
   - Cleaner code, less repetition
   - Default values support
   - Function parameter destructuring

4. **Difference between spread and rest operators?**
   - Same syntax (...), different contexts
   - Spread expands, rest collects
   - Use cases for each

5. **What are template literals and their advantages?**
   - String interpolation
   - Multi-line strings
   - Expression embedding
   - Tagged templates

### Practical Questions

6. **Write a function that swaps two variables using destructuring.**

7. **Create a function that merges objects with nested properties.**

8. **Implement a utility to check if two objects are equal (deep comparison).**

9. **Write a function that returns only unique values from multiple arrays.**

10. **Create a type-safe function that handles different parameter types.**

### Advanced Questions

11. **Explain type coercion with examples of unexpected behavior.**

12. **What is the temporal dead zone in JavaScript?**

13. **How do you create immutable objects in JavaScript?**

14. **Explain the difference between shallow and deep copying.**

15. **What are symbols and when would you use them?**

## ✅ Success Criteria

By the end of Day 08, you should be able to:

- [ ] Choose appropriate variable declarations (const, let, never var)
- [ ] Write clean arrow functions for various use cases
- [ ] Use template literals for all string interpolation
- [ ] Apply destructuring to simplify code
- [ ] Understand when to use spread vs rest operators
- [ ] Implement default parameters effectively
- [ ] Use enhanced object literal syntax
- [ ] Perform proper type checking and comparisons
- [ ] Avoid common type coercion pitfalls
- [ ] Refactor ES5 code to modern ES6+ syntax
- [ ] Write type-safe, predictable JavaScript code
- [ ] Complete the profile card generator project

## 🚀 Next Steps

Tomorrow (Day 09), you'll dive into:
- Functions in depth: pure functions, higher-order functions
- Scope: global, function, block, and lexical scope
- Closures: creating private variables and factory functions
- Hoisting: understanding JavaScript's execution context
- The 'this' keyword and binding methods

**Preparation:**
- Review function basics and scope concepts
- Practice writing functions that return functions
- Think about data encapsulation scenarios

**Keep practicing** - Mastery of ES6+ fundamentals is crucial for modern JavaScript development!
