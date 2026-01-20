# Day 11: Async JavaScript: Promises & Async/Await

## 🎯 Learning Objectives

By the end of today, you will understand:
- Synchronous vs asynchronous JavaScript execution
- The JavaScript event loop and call stack
- Callbacks and the callback hell problem
- Promises: creation, chaining, and error handling
- Async/await syntax for cleaner asynchronous code
- Promise combinators (all, race, allSettled, any)
- Error handling patterns in asynchronous code
- Practical patterns for real-world async operations

## 📚 Topics to Study

### 1. Understanding Asynchronous JavaScript

```javascript
// Synchronous Code - Blocking
console.log('First');
console.log('Second');
console.log('Third');
// Output: First, Second, Third (in order)

// Asynchronous Code - Non-blocking
console.log('First');

setTimeout(() => {
  console.log('Second (after 1 second)');
}, 1000);

console.log('Third');
// Output: First, Third, Second (after 1 second)

// The Event Loop
/*
Call Stack: Executes code
Web APIs: Browser features (setTimeout, fetch, etc.)
Callback Queue: Waits for stack to be empty
Event Loop: Moves callbacks from queue to stack
*/

// Microtasks vs Macrotasks
console.log('1 - Sync');

setTimeout(() => {
  console.log('2 - Macrotask (setTimeout)');
}, 0);

Promise.resolve().then(() => {
  console.log('3 - Microtask (Promise)');
});

console.log('4 - Sync');

// Output: 1, 4, 3, 2
// Microtasks (Promises) execute before macrotasks (setTimeout)
```

### 2. Callbacks and Callback Hell

```javascript
// Simple Callback
function fetchData(callback) {
  setTimeout(() => {
    const data = { id: 1, name: 'John' };
    callback(data);
  }, 1000);
}

fetchData((data) => {
  console.log(data); // { id: 1, name: 'John' }
});

// Error-First Callback Pattern (Node.js style)
function readFile(filename, callback) {
  setTimeout(() => {
    const error = null; // or new Error('File not found')
    const data = 'File contents';
    callback(error, data);
  }, 1000);
}

readFile('file.txt', (error, data) => {
  if (error) {
    console.error('Error:', error);
    return;
  }
  console.log('Data:', data);
});

// Callback Hell (Pyramid of Doom)
// Multiple dependent async operations
getUser(userId, (userError, user) => {
  if (userError) {
    handleError(userError);
    return;
  }
  
  getPosts(user.id, (postsError, posts) => {
    if (postsError) {
      handleError(postsError);
      return;
    }
    
    getComments(posts[0].id, (commentsError, comments) => {
      if (commentsError) {
        handleError(commentsError);
        return;
      }
      
      // Finally do something with comments
      displayComments(comments);
    });
  });
});

// Problems with callbacks:
// 1. Hard to read (deeply nested)
// 2. Error handling is repetitive
// 3. Hard to compose operations
// 4. Difficult to handle concurrent operations
```

### 3. Promises - The Solution

```javascript
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
  // Async operation
  const success = true;
  
  setTimeout(() => {
    if (success) {
      resolve('Operation successful!'); // Fulfill
    } else {
      reject('Operation failed!'); // Reject
    }
  }, 1000);
});

// Promise States
// 1. Pending - Initial state
// 2. Fulfilled - Operation completed successfully
// 3. Rejected - Operation failed

// Consuming Promises

// then() - Handle fulfilled promise
myPromise.then((result) => {
  console.log(result); // 'Operation successful!'
});

// catch() - Handle rejected promise
myPromise.catch((error) => {
  console.error(error); // 'Operation failed!'
});

// finally() - Runs regardless of outcome
myPromise.finally(() => {
  console.log('Cleanup or loading state removal');
});

// Chaining Promises
fetch('/api/user/1')
  .then(response => response.json()) // Returns promise
  .then(user => {
    console.log('User:', user);
    return fetch(`/api/posts/${user.id}`); // Return new promise
  })
  .then(response => response.json())
  .then(posts => {
    console.log('Posts:', posts);
    return posts[0];
  })
  .then(firstPost => {
    console.log('First post:', firstPost);
  })
  .catch(error => {
    // Catches any error in the chain
    console.error('Error:', error);
  })
  .finally(() => {
    console.log('Request completed');
  });

// Creating resolved/rejected promises
const resolved = Promise.resolve('Immediate success');
const rejected = Promise.reject('Immediate failure');

// resolved.then(value => console.log(value));
// rejected.catch(error => console.error(error));

// Practical Promise Example
function fetchUser(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userId <= 0) {
        reject(new Error('Invalid user ID'));
        return;
      }
      
      resolve({
        id: userId,
        name: 'John Doe',
        email: 'john@example.com'
      });
    }, 1000);
  });
}

// Using the promise
fetchUser(1)
  .then(user => {
    console.log('User fetched:', user);
    return user.id;
  })
  .then(userId => {
    console.log('User ID:', userId);
  })
  .catch(error => {
    console.error('Failed to fetch user:', error.message);
  });
```

### 4. Promise Combinators

```javascript
// Promise.all() - Wait for all promises to resolve
// Rejects if ANY promise rejects
const promise1 = Promise.resolve(3);
const promise2 = new Promise(resolve => setTimeout(() => resolve(42), 1000));
const promise3 = fetch('/api/data').then(r => r.json());

Promise.all([promise1, promise2, promise3])
  .then(([result1, result2, result3]) => {
    console.log('All resolved:', result1, result2, result3);
  })
  .catch(error => {
    console.error('One or more failed:', error);
  });

// Use case: Fetch multiple resources concurrently
const fetchMultipleUsers = (userIds) => {
  const promises = userIds.map(id => fetch(`/api/user/${id}`));
  return Promise.all(promises);
};

// Promise.allSettled() - Wait for all, never rejects
// Returns array with status and value/reason for each
const promises = [
  Promise.resolve('Success 1'),
  Promise.reject('Error 1'),
  Promise.resolve('Success 2')
];

Promise.allSettled(promises)
  .then(results => {
    results.forEach((result, index) => {
      if (result.status === 'fulfilled') {
        console.log(`Promise ${index}: ${result.value}`);
      } else {
        console.log(`Promise ${index} failed: ${result.reason}`);
      }
    });
  });

// Output format:
// [
//   { status: 'fulfilled', value: 'Success 1' },
//   { status: 'rejected', reason: 'Error 1' },
//   { status: 'fulfilled', value: 'Success 2' }
// ]

// Promise.race() - Resolves/rejects with first settled promise
const slowPromise = new Promise(resolve => 
  setTimeout(() => resolve('Slow'), 3000)
);
const fastPromise = new Promise(resolve => 
  setTimeout(() => resolve('Fast'), 1000)
);

Promise.race([slowPromise, fastPromise])
  .then(result => {
    console.log(result); // 'Fast'
  });

// Use case: Timeout implementation
const fetchWithTimeout = (url, timeout) => {
  const fetchPromise = fetch(url);
  const timeoutPromise = new Promise((_, reject) => 
    setTimeout(() => reject(new Error('Timeout')), timeout)
  );
  
  return Promise.race([fetchPromise, timeoutPromise]);
};

// Promise.any() - Resolves with first fulfilled promise
// Rejects only if ALL promises reject
const promises2 = [
  Promise.reject('Error 1'),
  Promise.resolve('Success 1'),
  Promise.resolve('Success 2')
];

Promise.any(promises2)
  .then(result => {
    console.log('First success:', result); // 'Success 1'
  })
  .catch(error => {
    console.error('All failed:', error);
  });

// Use case: Fastest successful response from multiple servers
const fetchFromMultipleServers = (endpoints) => {
  const promises = endpoints.map(url => fetch(url));
  return Promise.any(promises);
};
```

### 5. Async/Await - Modern Syntax

```javascript
// Async Function - Always returns a Promise
async function fetchData() {
  return 'Data'; // Automatically wrapped in Promise.resolve()
}

// fetchData().then(data => console.log(data)); // 'Data'

// Await - Pauses execution until promise resolves
async function getUser(userId) {
  // Must use await inside async function
  const response = await fetch(`/api/user/${userId}`);
  const user = await response.json();
  return user;
}

// Using async/await
async function displayUserPosts() {
  try {
    // Sequential execution (one after another)
    const user = await fetchUser(1);
    console.log('User:', user);
    
    const posts = await fetchPosts(user.id);
    console.log('Posts:', posts);
    
    const comments = await fetchComments(posts[0].id);
    console.log('Comments:', comments);
    
  } catch (error) {
    console.error('Error:', error);
  } finally {
    console.log('Cleanup');
  }
}

// Parallel Execution with async/await
async function fetchAllData() {
  try {
    // Start all requests simultaneously
    const userPromise = fetch('/api/user/1');
    const postsPromise = fetch('/api/posts');
    const settingsPromise = fetch('/api/settings');
    
    // Wait for all to complete
    const [userResponse, postsResponse, settingsResponse] = await Promise.all([
      userPromise,
      postsPromise,
      settingsPromise
    ]);
    
    // Process responses
    const user = await userResponse.json();
    const posts = await postsResponse.json();
    const settings = await settingsResponse.json();
    
    return { user, posts, settings };
  } catch (error) {
    console.error('One or more requests failed:', error);
  }
}

// Async/await with Promise.all - Cleaner parallel execution
async function fetchMultipleUsers(userIds) {
  try {
    const users = await Promise.all(
      userIds.map(id => fetchUser(id))
    );
    return users;
  } catch (error) {
    console.error('Failed to fetch users:', error);
  }
}

// Error Handling Patterns

// Try-catch for each operation
async function robustFetch() {
  let user, posts;
  
  try {
    user = await fetchUser(1);
  } catch (error) {
    console.error('User fetch failed:', error);
    user = null; // Provide default
  }
  
  try {
    posts = await fetchPosts(user?.id);
  } catch (error) {
    console.error('Posts fetch failed:', error);
    posts = []; // Provide default
  }
  
  return { user, posts };
}

// Top-level await (ES2022) - In modules only
// await can be used at top level without async function
// const config = await fetch('/api/config').then(r => r.json());

// Converting callback to Promise
function promisify(callbackFn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      callbackFn(...args, (error, result) => {
        if (error) {
          reject(error);
        } else {
          resolve(result);
        }
      });
    });
  };
}

// Usage
// const readFilePromise = promisify(readFile);
// const content = await readFilePromise('file.txt');
```

### 6. Real-World Async Patterns

```javascript
// Retry Logic
async function fetchWithRetry(url, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url);
      if (!response.ok) throw new Error('Request failed');
      return await response.json();
    } catch (error) {
      if (i === retries - 1) throw error;
      console.log(`Retry ${i + 1}/${retries}`);
      await delay(1000 * (i + 1)); // Exponential backoff
    }
  }
}

// Delay utility
const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));

// Sequential Processing
async function processItemsSequentially(items) {
  const results = [];
  
  for (const item of items) {
    const result = await processItem(item);
    results.push(result);
  }
  
  return results;
}

// Concurrent Processing with Limit
async function processWithLimit(items, limit = 3) {
  const results = [];
  const executing = [];
  
  for (const item of items) {
    const promise = processItem(item).then(result => {
      executing.splice(executing.indexOf(promise), 1);
      return result;
    });
    
    results.push(promise);
    executing.push(promise);
    
    if (executing.length >= limit) {
      await Promise.race(executing);
    }
  }
  
  return Promise.all(results);
}

// Queue System
class AsyncQueue {
  constructor() {
    this.queue = [];
    this.processing = false;
  }
  
  async add(asyncFn) {
    this.queue.push(asyncFn);
    if (!this.processing) {
      await this.process();
    }
  }
  
  async process() {
    this.processing = true;
    
    while (this.queue.length > 0) {
      const asyncFn = this.queue.shift();
      try {
        await asyncFn();
      } catch (error) {
        console.error('Queue item failed:', error);
      }
    }
    
    this.processing = false;
  }
}

// Cache with TTL (Time To Live)
class AsyncCache {
  constructor(ttl = 60000) {
    this.cache = new Map();
    this.ttl = ttl;
  }
  
  async get(key, fetchFn) {
    const cached = this.cache.get(key);
    
    if (cached && Date.now() - cached.timestamp < this.ttl) {
      return cached.value;
    }
    
    const value = await fetchFn();
    this.cache.set(key, {
      value,
      timestamp: Date.now()
    });
    
    return value;
  }
  
  clear() {
    this.cache.clear();
  }
}

// Usage
const cache = new AsyncCache(5000); // 5 second TTL
// const user = await cache.get('user:1', () => fetchUser(1));

// Debounced Async Function
function debounceAsync(fn, delay) {
  let timeoutId;
  let latestResolve;
  let latestReject;
  
  return function(...args) {
    clearTimeout(timeoutId);
    
    return new Promise((resolve, reject) => {
      latestResolve = resolve;
      latestReject = reject;
      
      timeoutId = setTimeout(async () => {
        try {
          const result = await fn.apply(this, args);
          latestResolve(result);
        } catch (error) {
          latestReject(error);
        }
      }, delay);
    });
  };
}

// Usage - debounced search
const debouncedSearch = debounceAsync(async (query) => {
  const response = await fetch(`/api/search?q=${query}`);
  return response.json();
}, 300);
```

## 💡 Key Concepts

### Promise Best Practices

1. **Always return promises in chains**: Enables proper chaining
2. **Use async/await for readability**: Cleaner than promise chains
3. **Handle errors properly**: Use catch() or try-catch
4. **Avoid mixing patterns**: Don't mix callbacks and promises
5. **Use Promise combinators**: Efficient concurrent operations

### Async/Await Guidelines

- Always use try-catch for error handling
- Return promises from async functions for chaining
- Await in parallel when operations are independent
- Use Promise.all for concurrent operations
- Don't forget finally for cleanup

### Common Pitfalls

- Forgetting to return in promise chains
- Not handling promise rejections (unhandled rejection)
- Sequential awaits when parallel is possible
- Mixing async/await with .then()
- Not understanding microtasks vs macrotasks

### Performance Considerations

- Parallel > Sequential when possible
- Use Promise.all for concurrent requests
- Implement request timeouts
- Cache async results when appropriate
- Limit concurrent operations to avoid overload

## 🛠️ Practical Exercises

### Exercise 1: Promise Creation and Chaining
Create custom promises that:
- Simulate API calls with random delays
- Chain multiple dependent operations
- Handle errors at different stages
- Implement proper finally cleanup
- Return transformed data

### Exercise 2: Async/Await Conversion
Convert callback-based code to:
- Promise-based implementations
- Async/await syntax
- Proper error handling
- Parallel execution where possible
- Cleaner, more readable code

### Exercise 3: Promise Combinators
Implement scenarios using:
- Promise.all for multiple parallel requests
- Promise.race for timeout logic
- Promise.allSettled for batch operations
- Promise.any for fallback servers
- Custom combinator functions

### Exercise 4: Error Handling
Build robust error handling for:
- Network request failures
- Retry logic with exponential backoff
- Fallback data sources
- Partial failure handling
- User-friendly error messages

### Exercise 5: Real-World Patterns
Implement:
- Request queue system
- Async cache with TTL
- Debounced async search
- Polling with cancellation
- Progress tracking for multiple operations

## 🎨 Project: Advanced Data Fetcher

Build a comprehensive data fetching library with modern async patterns.

**Core Features:**
- Fetch data from multiple endpoints
- Parallel and sequential request modes
- Automatic retry on failure
- Request timeout handling
- Response caching with TTL
- Loading and error states
- Request cancellation

**Implementation Requirements:**
- Use async/await exclusively
- Implement all Promise combinators
- Proper error handling throughout
- Type-safe response handling
- Configurable options (timeout, retries, cache)

**API Design:**
```javascript
// const fetcher = new DataFetcher({
//   baseURL: 'https://api.example.com',
//   timeout: 5000,
//   retries: 3,
//   cache: true
// });

// Single request
// const user = await fetcher.get('/user/1');

// Multiple parallel requests
// const [users, posts] = await fetcher.all([
//   '/users',
//   '/posts'
// ]);

// With retry and timeout
// const data = await fetcher.get('/data', {
//   retries: 5,
//   timeout: 10000
// });
```

**Advanced Features:**
- Request/response interceptors
- Automatic JSON parsing
- Query parameter building
- Request deduplication
- Progress callbacks
- Abort controller integration
- Rate limiting
- Request priority queue

**Error Handling:**
- Network errors
- Timeout errors
- HTTP error statuses
- Retry exhausted
- Cache errors
- Validation errors

## 📖 Resources to Explore

### Documentation
- [MDN: Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN: async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN: Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)

### Essential Reading
- JavaScript Promises: An Introduction
- Async/Await: Modern Asynchronous JavaScript
- Understanding the Event Loop
- You Don't Know JS: Async & Performance

### Articles
- Promises vs Async/Await
- Error Handling in Asynchronous JavaScript
- Promise Anti-Patterns
- Mastering JavaScript Promises
- Common Async Patterns

### Video Content
- The Event Loop Explained (Philip Roberts)
- JavaScript Promises Crash Course
- Async JavaScript Tutorial
- Promise Combinators Deep Dive

### Practice Platforms
- JavaScript Promises Practice
- Async JavaScript Challenges
- Promise-based Coding Exercises

## 🔍 Interview Questions

### Fundamental Questions

1. **What is a Promise and what states can it have?**
   - Pending, fulfilled, rejected
   - Immutable once settled
   - Error handling

2. **Explain the event loop and how async code executes.**
   - Call stack, web APIs, callback queue
   - Microtasks vs macrotasks
   - Execution order

3. **What's the difference between Promise.all and Promise.allSettled?**
   - Rejection behavior
   - Use cases for each
   - Return value format

4. **How does async/await work under the hood?**
   - Syntactic sugar over promises
   - Generator functions
   - Error propagation

5. **What is callback hell and how do Promises solve it?**
   - Nested callbacks problem
   - Promise chaining
   - Flattened error handling

### Practical Questions

6. **Write a function that fetches data with retry logic.**

7. **Implement Promise.all from scratch.**

8. **Create a sleep function using Promises.**

9. **Convert a callback-based function to use Promises.**

10. **Implement request timeout using Promise.race.**

### Advanced Questions

11. **How do you handle errors in promise chains vs async/await?**

12. **What are the risks of not handling promise rejections?**

13. **Explain the difference between returning a promise and awaiting it.**

14. **How would you implement request cancellation?**

15. **Design a rate limiter for API requests using Promises.**

## ✅ Success Criteria

By the end of Day 11, you should be able to:

- [ ] Understand synchronous vs asynchronous execution
- [ ] Explain the JavaScript event loop
- [ ] Create and consume Promises
- [ ] Chain promises effectively
- [ ] Use all Promise combinators appropriately
- [ ] Write clean async/await code
- [ ] Handle errors in async operations
- [ ] Implement retry logic
- [ ] Understand parallel vs sequential execution
- [ ] Build request timeout logic
- [ ] Create async utility functions
- [ ] Complete the data fetcher project

## 🚀 Next Steps

Tomorrow (Day 12), you'll learn:
- Fetch API for HTTP requests
- AJAX and XMLHttpRequest
- HTTP methods (GET, POST, PUT, DELETE)
- Request and response headers
- Working with JSON and form data
- CORS and security considerations

**Preparation:**
- Review HTTP basics (methods, status codes)
- Understand REST API concepts
- Practice with public APIs

**Keep coding** - Async JavaScript is essential for modern web development!
