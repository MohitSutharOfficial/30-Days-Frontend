# Day 13: Array Methods & Functional Programming

## 🎯 Learning Objectives

By the end of today, you will understand:
- Essential array methods and their use cases
- Functional programming principles in JavaScript
- Pure functions and immutability
- Function composition and chaining
- Higher-order functions and callbacks
- Data transformation patterns
- Performance considerations for array operations
- Real-world array manipulation scenarios

## 📚 Topics to Study

### 1. Array Iteration Methods

```javascript
// forEach - Execute function for each element
const numbers = [1, 2, 3, 4, 5];

numbers.forEach((num, index, array) => {
  console.log(`Index ${index}: ${num}`);
});

// forEach doesn't return anything (returns undefined)
// Used for side effects only

// map - Transform each element, returns new array
const doubled = numbers.map(num => num * 2);
// [2, 4, 6, 8, 10]

const users = [
  { id: 1, name: 'John', age: 30 },
  { id: 2, name: 'Jane', age: 25 },
  { id: 3, name: 'Bob', age: 35 }
];

// Extract specific properties
const names = users.map(user => user.name);
// ['John', 'Jane', 'Bob']

// Transform objects
const userSummaries = users.map(user => ({
  id: user.id,
  label: `${user.name} (${user.age})`
}));

// IMPORTANT: map always returns array of same length
// For filtering and transforming, use filter then map

// filter - Keep elements that pass test
const evenNumbers = numbers.filter(num => num % 2 === 0);
// [2, 4]

const adults = users.filter(user => user.age >= 30);
// [{ id: 1, name: 'John', age: 30 }, { id: 3, name: 'Bob', age: 35 }]

// Complex filtering
const activeAdults = users.filter(user => {
  return user.age >= 30 && user.active === true;
});

// Chaining filter and map
const adultNames = users
  .filter(user => user.age >= 30)
  .map(user => user.name);
// ['John', 'Bob']

// find - Returns first element that matches
const firstAdult = users.find(user => user.age >= 30);
// { id: 1, name: 'John', age: 30 }

// Returns undefined if not found
const notFound = users.find(user => user.age > 100);
// undefined

// findIndex - Returns index of first match
const johnIndex = users.findIndex(user => user.name === 'John');
// 0

// Returns -1 if not found
const notFoundIndex = users.findIndex(user => user.name === 'NonExistent');
// -1

// some - Check if at least one element passes test
const hasAdults = users.some(user => user.age >= 30);
// true

const hasTeens = users.some(user => user.age < 18);
// false

// every - Check if all elements pass test
const allAdults = users.every(user => user.age >= 18);
// true

const allSeniors = users.every(user => user.age >= 65);
// false

// includes - Check if array contains value (primitive values)
const hasThree = numbers.includes(3);
// true

const hasTen = numbers.includes(10);
// false

// indexOf - Find index of value
const indexOfThree = numbers.indexOf(3);
// 2

// Returns -1 if not found
const indexOfTen = numbers.indexOf(10);
// -1

// lastIndexOf - Find last index of value
const duplicates = [1, 2, 3, 2, 1];
const lastTwo = duplicates.lastIndexOf(2);
// 3
```

### 2. The Mighty reduce() Method

```javascript
// reduce - Reduce array to single value
const numbers = [1, 2, 3, 4, 5];

// Sum all numbers
const sum = numbers.reduce((accumulator, current) => {
  return accumulator + current;
}, 0); // 0 is initial value
// 15

// Shorter version
const sum2 = numbers.reduce((acc, cur) => acc + cur, 0);

// Product of all numbers
const product = numbers.reduce((acc, cur) => acc * cur, 1);
// 120

// Find maximum
const max = numbers.reduce((max, current) => {
  return current > max ? current : max;
}, numbers[0]);
// 5

// Find minimum
const min = numbers.reduce((min, current) => {
  return current < min ? current : min;
}, numbers[0]);
// 1

// Count occurrences
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const fruitCount = fruits.reduce((counts, fruit) => {
  counts[fruit] = (counts[fruit] || 0) + 1;
  return counts;
}, {});
// { apple: 3, banana: 2, orange: 1 }

// Group by property
const users = [
  { name: 'John', department: 'IT' },
  { name: 'Jane', department: 'HR' },
  { name: 'Bob', department: 'IT' },
  { name: 'Alice', department: 'HR' }
];

const byDepartment = users.reduce((groups, user) => {
  const dept = user.department;
  if (!groups[dept]) {
    groups[dept] = [];
  }
  groups[dept].push(user);
  return groups;
}, {});
// {
//   IT: [{ name: 'John', ... }, { name: 'Bob', ... }],
//   HR: [{ name: 'Jane', ... }, { name: 'Alice', ... }]
// }

// Flatten array of arrays
const nested = [[1, 2], [3, 4], [5, 6]];
const flattened = nested.reduce((acc, arr) => acc.concat(arr), []);
// [1, 2, 3, 4, 5, 6]

// Modern alternative: flat()
const flattened2 = nested.flat();
// [1, 2, 3, 4, 5, 6]

// Create object from array
const keyValuePairs = [
  ['name', 'John'],
  ['age', 30],
  ['city', 'New York']
];

const obj = keyValuePairs.reduce((acc, [key, value]) => {
  acc[key] = value;
  return acc;
}, {});
// { name: 'John', age: 30, city: 'New York' }

// Pipeline of transformations
const pipeline = [
  arr => arr.filter(x => x > 0),
  arr => arr.map(x => x * 2),
  arr => arr.reduce((sum, x) => sum + x, 0)
];

const data = [-1, 0, 1, 2, 3];
const result = pipeline.reduce((acc, fn) => fn(acc), data);
// 12 (filter: [1,2,3], map: [2,4,6], reduce: 12)

// reduceRight - Same as reduce but right to left
const letters = ['a', 'b', 'c'];
const reversed = letters.reduceRight((acc, letter) => acc + letter, '');
// 'cba'
```

### 3. Array Transformation Methods

```javascript
// concat - Merge arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const merged = arr1.concat(arr2);
// [1, 2, 3, 4, 5, 6]

// Modern alternative: spread operator
const merged2 = [...arr1, ...arr2];

// slice - Extract portion (non-mutating)
const numbers = [1, 2, 3, 4, 5];
const sliced = numbers.slice(1, 4);
// [2, 3, 4] (index 1 to 3, excludes 4)

const lastTwo = numbers.slice(-2);
// [4, 5]

const copy = numbers.slice();
// [1, 2, 3, 4, 5] (shallow copy)

// splice - Add/remove elements (MUTATES array)
const fruits = ['apple', 'banana', 'orange', 'grape'];

// Remove elements
const removed = fruits.splice(1, 2);
// removed: ['banana', 'orange']
// fruits: ['apple', 'grape']

// Insert elements
fruits.splice(1, 0, 'kiwi', 'mango');
// fruits: ['apple', 'kiwi', 'mango', 'grape']

// Replace elements
fruits.splice(1, 2, 'pear');
// fruits: ['apple', 'pear', 'grape']

// join - Create string from array
const words = ['Hello', 'World', '!'];
const sentence = words.join(' ');
// 'Hello World !'

const csv = ['John', 'Doe', '30'].join(',');
// 'John,Doe,30'

// split - Create array from string (String method)
const sentence2 = 'Hello World !';
const wordsArray = sentence2.split(' ');
// ['Hello', 'World', '!']

// reverse - Reverse array order (MUTATES)
const numbers2 = [1, 2, 3, 4, 5];
numbers2.reverse();
// [5, 4, 3, 2, 1]

// Non-mutating reverse
const reversed = [...numbers2].reverse();
// OR
const reversed2 = numbers2.slice().reverse();

// sort - Sort array (MUTATES)
const names = ['Charlie', 'Alice', 'Bob'];
names.sort();
// ['Alice', 'Bob', 'Charlie']

// Numbers need compare function
const nums = [10, 5, 40, 25, 1000];
nums.sort(); // WRONG: ['10', '1000', '25', '40', '5']

// Correct numeric sort
nums.sort((a, b) => a - b);
// [5, 10, 25, 40, 1000] (ascending)

nums.sort((a, b) => b - a);
// [1000, 40, 25, 10, 5] (descending)

// Sorting objects
const users = [
  { name: 'John', age: 30 },
  { name: 'Jane', age: 25 },
  { name: 'Bob', age: 35 }
];

users.sort((a, b) => a.age - b.age);
// Sorted by age ascending

users.sort((a, b) => a.name.localeCompare(b.name));
// Sorted by name alphabetically

// flat - Flatten nested arrays
const nested = [1, [2, 3], [4, [5, 6]]];
const flat1 = nested.flat();
// [1, 2, 3, 4, [5, 6]] (default depth: 1)

const flat2 = nested.flat(2);
// [1, 2, 3, 4, 5, 6] (depth: 2)

const flatAll = nested.flat(Infinity);
// [1, 2, 3, 4, 5, 6] (all levels)

// flatMap - Map then flatten
const sentences = ['Hello world', 'How are you'];
const words = sentences.flatMap(sentence => sentence.split(' '));
// ['Hello', 'world', 'How', 'are', 'you']

// Equivalent to:
const words2 = sentences.map(s => s.split(' ')).flat();
```

### 4. Functional Programming Concepts

```javascript
// Pure Functions
// - Same input always produces same output
// - No side effects (doesn't modify external state)

// PURE ✅
function add(a, b) {
  return a + b;
}

function calculatePrice(price, tax) {
  return price * (1 + tax);
}

// IMPURE ❌
let total = 0;
function addToTotal(value) {
  total += value; // Modifies external state
  return total;
}

function getRandomPrice(base) {
  return base * Math.random(); // Non-deterministic
}

// Immutability - Never modify original data

// BAD - Mutating
const user = { name: 'John', age: 30 };
user.age = 31; // Mutation

const numbers = [1, 2, 3];
numbers.push(4); // Mutation

// GOOD - Creating new values
const updatedUser = { ...user, age: 31 };
const withFour = [...numbers, 4];

// Immutable array operations
const original = [1, 2, 3, 4, 5];

// Add to beginning (immutable)
const withZero = [0, ...original];

// Add to end (immutable)
const withSix = [...original, 6];

// Remove item (immutable)
const without3 = original.filter(n => n !== 3);

// Update item (immutable)
const doubled = original.map(n => n * 2);

// Immutable object updates
const person = {
  name: 'John',
  age: 30,
  address: {
    city: 'New York',
    country: 'USA'
  }
};

// Shallow update
const updated = { ...person, age: 31 };

// Deep update (nested)
const movedPerson = {
  ...person,
  address: {
    ...person.address,
    city: 'Boston'
  }
};

// Higher-Order Functions
// Functions that take or return functions

// Function that returns function
const multiplier = (factor) => {
  return (number) => number * factor;
};

const double = multiplier(2);
const triple = multiplier(3);
// double(5) // 10
// triple(5) // 15

// Function that takes function
const operate = (arr, operation) => {
  return arr.map(operation);
};

const numbers = [1, 2, 3, 4, 5];
// operate(numbers, x => x * 2) // [2, 4, 6, 8, 10]
// operate(numbers, x => x ** 2) // [1, 4, 9, 16, 25]

// Currying - Transform multi-arg function to sequence of single-arg
const add = (a, b, c) => a + b + c;

const curriedAdd = (a) => (b) => (c) => a + b + c;
// curriedAdd(1)(2)(3) // 6

// Partial application
const add5 = curriedAdd(5);
const add5and3 = add5(3);
// add5and3(2) // 10

// Function Composition
// Combine simple functions to build complex ones

const compose = (...fns) => (value) =>
  fns.reduceRight((acc, fn) => fn(acc), value);

const pipe = (...fns) => (value) =>
  fns.reduce((acc, fn) => fn(acc), value);

// Example functions
const addOne = (x) => x + 1;
const double = (x) => x * 2;
const square = (x) => x * x;

// Compose (right to left)
const compute = compose(square, double, addOne);
// compute(2) // square(double(addOne(2))) = 36

// Pipe (left to right)
const process = pipe(addOne, double, square);
// process(2) // square(double(addOne(2))) = 36

// Practical composition
const toLowerCase = (str) => str.toLowerCase();
const removeSpaces = (str) => str.replace(/\s/g, '');
const addDashes = (str) => str.replace(/(.{4})/g, '$1-').slice(0, -1);

const formatCreditCard = pipe(
  removeSpaces,
  addDashes
);
// formatCreditCard('1234 5678 9012 3456') // '1234-5678-9012-3456'
```

### 5. Practical Array Patterns

```javascript
// Remove duplicates
const numbers = [1, 2, 2, 3, 4, 4, 5];

// Using Set
const unique = [...new Set(numbers)];
// [1, 2, 3, 4, 5]

// Using filter
const unique2 = numbers.filter((num, index, arr) => {
  return arr.indexOf(num) === index;
});

// Remove duplicates from array of objects
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 1, name: 'John' } // duplicate
];

const uniqueUsers = users.filter((user, index, arr) => {
  return arr.findIndex(u => u.id === user.id) === index;
});

// Intersection - Common elements
const arr1 = [1, 2, 3, 4, 5];
const arr2 = [3, 4, 5, 6, 7];

const intersection = arr1.filter(value => arr2.includes(value));
// [3, 4, 5]

// Difference - Elements in arr1 but not arr2
const difference = arr1.filter(value => !arr2.includes(value));
// [1, 2]

// Union - All unique elements
const union = [...new Set([...arr1, ...arr2])];
// [1, 2, 3, 4, 5, 6, 7]

// Chunk array - Split into smaller arrays
const chunk = (arr, size) => {
  return Array.from({ length: Math.ceil(arr.length / size) }, (_, i) =>
    arr.slice(i * size, i * size + size)
  );
};

const numbers = [1, 2, 3, 4, 5, 6, 7, 8];
const chunks = chunk(numbers, 3);
// [[1, 2, 3], [4, 5, 6], [7, 8]]

// Partition - Split based on condition
const partition = (arr, predicate) => {
  return arr.reduce(
    ([pass, fail], item) => {
      return predicate(item)
        ? [[...pass, item], fail]
        : [pass, [...fail, item]];
    },
    [[], []]
  );
};

const [evens, odds] = partition([1, 2, 3, 4, 5], n => n % 2 === 0);
// evens: [2, 4]
// odds: [1, 3, 5]

// Pluck - Extract property from objects
const pluck = (arr, key) => arr.map(obj => obj[key]);

const users = [
  { id: 1, name: 'John', age: 30 },
  { id: 2, name: 'Jane', age: 25 }
];

const names = pluck(users, 'name');
// ['John', 'Jane']

// Shuffle array
const shuffle = (arr) => {
  const copy = [...arr];
  for (let i = copy.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [copy[i], copy[j]] = [copy[j], copy[i]];
  }
  return copy;
};

// Sample - Get random items
const sample = (arr, n = 1) => {
  const shuffled = shuffle(arr);
  return shuffled.slice(0, n);
};

// Zip - Combine arrays
const zip = (...arrays) => {
  const length = Math.max(...arrays.map(arr => arr.length));
  return Array.from({ length }, (_, i) => arrays.map(arr => arr[i]));
};

const zipped = zip([1, 2, 3], ['a', 'b', 'c'], [true, false, true]);
// [[1, 'a', true], [2, 'b', false], [3, 'c', true]]

// Object from arrays
const fromPairs = (pairs) => {
  return pairs.reduce((obj, [key, value]) => {
    obj[key] = value;
    return obj;
  }, {});
};

const obj = fromPairs([['name', 'John'], ['age', 30]]);
// { name: 'John', age: 30 }

// Deep flatten
const deepFlatten = (arr) => {
  return arr.reduce((acc, item) => {
    return acc.concat(Array.isArray(item) ? deepFlatten(item) : item);
  }, []);
};

const nested = [1, [2, [3, [4, 5]]], 6];
const flat = deepFlatten(nested);
// [1, 2, 3, 4, 5, 6]
```

### 6. Performance Considerations

```javascript
// Method Selection Performance

// For simple iteration
// forEach - fastest for simple operations
// for loop - fastest overall, but less readable

// For finding
// find - stops at first match (efficient)
// filter - checks all elements (less efficient for finding one)

// For existence check
// includes - fastest for primitives
// some - fastest for complex conditions

// Chain optimization

// BAD - Multiple iterations
const result = array
  .filter(item => item.active)
  .map(item => item.value)
  .filter(value => value > 10)
  .map(value => value * 2);

// BETTER - Combined conditions
const result2 = array
  .filter(item => item.active && item.value > 10)
  .map(item => item.value * 2);

// BEST - Single reduce (one iteration)
const result3 = array.reduce((acc, item) => {
  if (item.active && item.value > 10) {
    acc.push(item.value * 2);
  }
  return acc;
}, []);

// Break early when possible

// BAD - Checks all elements
const hasInvalid = items.filter(item => !item.valid).length > 0;

// GOOD - Stops at first match
const hasInvalid2 = items.some(item => !item.valid);

// Avoid creating functions in loops
const arr = [1, 2, 3, 4, 5];

// BAD
arr.forEach(item => {
  someFunction(() => item * 2); // Creates new function each iteration
});

// GOOD
const multiplier = (item) => item * 2;
arr.forEach(item => {
  someFunction(multiplier);
});

// Memory considerations

// Large arrays - consider streaming/chunking
const processLargeArray = (largeArray, chunkSize = 1000) => {
  const chunks = chunk(largeArray, chunkSize);
  return chunks.map(chunk => {
    return chunk.map(item => processItem(item));
  }).flat();
};

// Lazy evaluation (generators)
function* filterMap(array, filterFn, mapFn) {
  for (const item of array) {
    if (filterFn(item)) {
      yield mapFn(item);
    }
  }
}

// Only processes what's needed
const result = filterMap(
  [1, 2, 3, 4, 5],
  n => n > 2,
  n => n * 2
);
// Use: for (const value of result) { ... }
```

## 💡 Key Concepts

### When to Use Which Method

- **map**: Transform every element
- **filter**: Keep some elements
- **reduce**: Combine to single value
- **find**: Get first match
- **some**: Check if any match
- **every**: Check if all match
- **forEach**: Side effects only

### Functional Programming Benefits

1. **Predictable**: Pure functions always return same output
2. **Testable**: Easy to test pure functions
3. **Composable**: Build complex from simple
4. **Maintainable**: Easier to understand and modify
5. **Parallelizable**: Pure functions can run concurrently

### Immutability Benefits

- Prevents bugs from unexpected mutations
- Easier to track changes
- Enables time-travel debugging
- Better for React/Redux state management
- Facilitates undo/redo functionality

### Common Mistakes

- Mutating arrays with push, splice, reverse, sort
- Using forEach when map/filter is appropriate
- Forgetting to return in map/reduce
- Not handling empty arrays
- Inefficient chaining (multiple iterations)
- Creating functions inside loops

## 🛠️ Practical Exercises

### Exercise 1: Data Transformation Pipeline
Build transformations that:
- Filter, map, and reduce complex data
- Handle nested structures
- Remove duplicates
- Group and aggregate data
- Compose transformations

### Exercise 2: Array Utilities Library
Create utility functions for:
- Unique values
- Intersection, union, difference
- Chunking and partitioning
- Shuffling and sampling
- Deep flattening
- Zipping and unzipping

### Exercise 3: Functional Programming Practice
Implement:
- Pure functions for common operations
- Higher-order functions
- Function composition utilities
- Curried functions
- Memoization with closures

### Exercise 4: Real-World Data Processing
Process datasets that include:
- Filtering by multiple criteria
- Sorting by multiple fields
- Aggregating statistics
- Transforming structures
- Handling missing data
- Combining from multiple sources

### Exercise 5: Performance Optimization
Optimize code by:
- Reducing iterations
- Breaking early when possible
- Using appropriate methods
- Avoiding unnecessary allocations
- Implementing lazy evaluation

## 🎨 Project: Data Analytics Dashboard

Build a data processing system for analytics dashboard.

**Data Processing Requirements:**
- Parse and validate raw data
- Clean and normalize data
- Filter by date ranges and categories
- Group by dimensions (time, category, user)
- Calculate aggregates (sum, avg, min, max, count)
- Sort by multiple criteria
- Paginate results

**Transformations:**
- Convert API responses to display format
- Denormalize related data
- Calculate derived fields
- Create data summaries
- Format for charts and graphs

**Functional Approach:**
- All transformations as pure functions
- Composable transformation pipeline
- Immutable data throughout
- Type-safe operations
- Reusable utility functions

**Features:**
- Sales data analysis
  - Total revenue by period
  - Top products by sales
  - Growth trends
  - Customer segments
  
- User analytics
  - Active users count
  - User behavior patterns
  - Retention metrics
  - Conversion funnels
  
- Product metrics
  - Inventory levels
  - Product performance
  - Category analysis
  - Trend detection

**Advanced Features:**
- Custom aggregation functions
- Multi-dimensional grouping
- Rolling averages/windows
- Percentile calculations
- Correlation analysis
- Data export (CSV, JSON)
- Caching computed results
- Incremental updates

**Performance:**
- Handle 10,000+ records
- Optimize chained operations
- Lazy evaluation where beneficial
- Memoize expensive calculations
- Efficient sorting algorithms

## 📖 Resources to Explore

### Documentation
- [MDN: Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN: Array Methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#instance_methods)
- Functional Programming in JavaScript

### Essential Reading
- Eloquent JavaScript - Higher-Order Functions
- Functional-Light JavaScript
- Professor Frisby's Mostly Adequate Guide to FP
- JavaScript Allongé

### Articles
- Map, Filter, Reduce Explained
- Functional Programming Concepts
- Immutability in JavaScript
- Function Composition Guide
- Array Method Performance

### Video Content
- Fun Fun Function - Functional Programming Series
- JavaScript Array Methods Explained
- Functional Programming in JavaScript
- Array Reduce Mastery

### Practice Platforms
- Codewars - Functional Programming Katas
- Exercism - JavaScript Track
- HackerRank - Functional Programming
- LeetCode - Array Problems

## 🔍 Interview Questions

### Fundamental Questions

1. **Explain map, filter, and reduce with examples.**
   - When to use each
   - Chaining methods
   - Performance implications

2. **What is a pure function? Why are they important?**
   - Definition and characteristics
   - Benefits for testing
   - Predictability

3. **How do you remove duplicates from an array?**
   - Multiple approaches
   - Object arrays
   - Performance trade-offs

4. **What's the difference between forEach and map?**
   - Return values
   - Use cases
   - Side effects

5. **Explain immutability in JavaScript.**
   - Why it matters
   - How to achieve it
   - Benefits

### Practical Questions

6. **Implement your own map function.**

7. **Use reduce to group objects by property.**

8. **Flatten a nested array without flat().**

9. **Find intersection of two arrays.**

10. **Sort array of objects by multiple fields.**

### Advanced Questions

11. **Implement function composition from scratch.**

12. **What is function currying? Implement it.**

13. **How would you implement lazy evaluation for arrays?**

14. **Optimize this chain: .filter().map().filter().map()**

15. **Explain when reduce is better than forEach.**

## ✅ Success Criteria

By the end of Day 13, you should be able to:

- [ ] Use all essential array methods effectively
- [ ] Chain array methods for complex transformations
- [ ] Write pure functions
- [ ] Implement immutable data operations
- [ ] Compose functions for reusability
- [ ] Use reduce for complex aggregations
- [ ] Remove duplicates and find intersections
- [ ] Group and aggregate data
- [ ] Optimize array operations
- [ ] Understand performance implications
- [ ] Apply functional programming concepts
- [ ] Complete the analytics dashboard project

## 🚀 Next Steps

Tomorrow (Day 14), you'll explore:
- Object-Oriented JavaScript
- Constructor functions and classes
- Prototypes and inheritance
- ES6 class syntax
- Private fields and methods
- Design patterns in JavaScript

**Preparation:**
- Review object basics
- Understand 'this' binding
- Think about code organization

**Keep practicing** - Functional programming makes code more maintainable and testable!
