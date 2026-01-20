# Day 17: React Hooks (useState, useEffect, useContext)

## 🎯 Learning Objectives

By the end of Day 17, you will:
- Master `useState` for managing component state
- Implement side effects using `useEffect` hook
- Understand the `useContext` hook for consuming context
- Learn proper hook rules and best practices
- Handle complex state updates and state batching
- Manage component lifecycle with hooks
- Implement data fetching and subscriptions
- Solve prop drilling using Context API
- Understand hook dependencies and cleanup functions
- Create custom hooks for reusable logic

## 📚 Topics to Study

### 1. Introduction to Hooks

```javascript
/**
 * WHAT ARE HOOKS?
 * - Functions that let you "hook into" React features
 * - Available in functional components only
 * - Enable state and lifecycle without classes
 * - Introduced in React 16.8
 * 
 * RULES OF HOOKS:
 * 1. Only call hooks at the top level (not in loops, conditions, or nested functions)
 * 2. Only call hooks from React functional components or custom hooks
 * 3. Hook calls must be in the same order on every render
 */

// ✅ CORRECT
function Component() {
  const [state, setState] = useState(0);
  useEffect(() => {}, []);
  
  return <div>{state}</div>;
}

// ❌ WRONG - Conditional hook
function WrongComponent({ condition }) {
  if (condition) {
    const [state, setState] = useState(0); // ERROR!
  }
  return <div>Content</div>;
}

// ❌ WRONG - Hook in loop
function WrongComponent2({ items }) {
  return items.map(item => {
    const [state, setState] = useState(0); // ERROR!
    return <div>{item}</div>;
  });
}
```

### 2. useState Hook

```javascript
/**
 * useState - Add state to functional components
 * Returns: [currentState, setStateFunction]
 */

import { useState } from 'react';

// Basic Usage
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}

// Multiple State Variables
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);
  const [agreed, setAgreed] = useState(false);
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ name, email, age, agreed });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input 
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input 
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
        type="email"
      />
      <input 
        value={age}
        onChange={(e) => setAge(Number(e.target.value))}
        type="number"
      />
      <label>
        <input 
          type="checkbox"
          checked={agreed}
          onChange={(e) => setAgreed(e.target.checked)}
        />
        I agree to terms
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

// Object State
function UserProfile() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    bio: '',
    preferences: {
      theme: 'light',
      notifications: true
    }
  });
  
  // Update nested object - must spread to maintain immutability
  const updateTheme = (theme) => {
    setUser(prevUser => ({
      ...prevUser,
      preferences: {
        ...prevUser.preferences,
        theme: theme
      }
    }));
  };
  
  // Update top-level property
  const updateName = (name) => {
    setUser(prevUser => ({
      ...prevUser,
      name: name
    }));
  };
  
  return (
    <div>
      <input 
        value={user.name}
        onChange={(e) => updateName(e.target.value)}
      />
      <button onClick={() => updateTheme('dark')}>Dark Mode</button>
    </div>
  );
}

// Array State
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');
  
  const addTodo = () => {
    setTodos([...todos, { id: Date.now(), text: input, completed: false }]);
    setInput('');
  };
  
  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };
  
  const deleteTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };
  
  return (
    <div>
      <input 
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && addTodo()}
      />
      <button onClick={addTodo}>Add</button>
      
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input 
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            <span style={{ 
              textDecoration: todo.completed ? 'line-through' : 'none' 
            }}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

// Functional Updates (when new state depends on previous)
function AdvancedCounter() {
  const [count, setCount] = useState(0);
  
  // ❌ Potential bug with multiple rapid clicks
  const incrementWrong = () => {
    setCount(count + 1);
    setCount(count + 1); // Both use same count value
    // Result: count + 1, not count + 2
  };
  
  // ✅ Correct way - functional update
  const incrementCorrect = () => {
    setCount(prevCount => prevCount + 1);
    setCount(prevCount => prevCount + 1);
    // Result: count + 2
  };
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={incrementCorrect}>+2</button>
    </div>
  );
}

// Lazy Initial State (expensive computation)
function ExpensiveComponent() {
  // ❌ Runs on every render
  const [state, setState] = useState(expensiveCalculation());
  
  // ✅ Runs only once on mount
  const [state2, setState2] = useState(() => expensiveCalculation());
  
  return <div>{state2}</div>;
}

function expensiveCalculation() {
  console.log('Calculating...');
  return Array.from({ length: 1000 }, (_, i) => i);
}
```

### 3. useEffect Hook

```javascript
/**
 * useEffect - Perform side effects in functional components
 * Replaces componentDidMount, componentDidUpdate, componentWillUnmount
 * 
 * Syntax: useEffect(effectFunction, dependencyArray)
 */

import { useState, useEffect } from 'react';

// Run on Every Render
function ComponentA() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    console.log('Effect runs on every render');
  }); // No dependency array
  
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}

// Run Only Once (on mount)
function ComponentB() {
  useEffect(() => {
    console.log('Component mounted - runs once');
    
    // Cleanup on unmount
    return () => {
      console.log('Component unmounted');
    };
  }, []); // Empty dependency array
  
  return <div>Component B</div>;
}

// Run When Dependencies Change
function ComponentC() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  useEffect(() => {
    console.log('Count changed:', count);
  }, [count]); // Runs when count changes
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <input value={name} onChange={(e) => setName(e.target.value)} />
    </div>
  );
}

// Data Fetching
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    // Reset states when userId changes
    setLoading(true);
    setError(null);
    
    fetch(`https://api.example.com/users/${userId}`)
      .then(response => {
        if (!response.ok) throw new Error('Failed to fetch');
        return response.json();
      })
      .then(data => {
        setUser(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, [userId]); // Re-fetch when userId changes
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;
  
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}

// Data Fetching with Async/Await
function Posts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await fetch('https://api.example.com/posts');
        const data = await response.json();
        setPosts(data);
      } catch (error) {
        console.error('Error fetching posts:', error);
      } finally {
        setLoading(false);
      }
    };
    
    fetchPosts();
  }, []);
  
  if (loading) return <div>Loading posts...</div>;
  
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}

// Cleanup Function (Subscriptions, Timers, Event Listeners)
function Timer() {
  const [seconds, setSeconds] = useState(0);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);
    
    // Cleanup function - clear interval on unmount
    return () => {
      clearInterval(interval);
    };
  }, []);
  
  return <div>Seconds: {seconds}</div>;
}

// Event Listeners
function WindowSize() {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });
  
  useEffect(() => {
    const handleResize = () => {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };
    
    window.addEventListener('resize', handleResize);
    
    // Cleanup - remove listener
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);
  
  return (
    <div>
      Window: {windowSize.width} x {windowSize.height}
    </div>
  );
}

// Multiple Effects for Separation of Concerns
function Dashboard({ userId }) {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  
  // Effect 1: Fetch user data
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(setUser);
  }, [userId]);
  
  // Effect 2: Fetch user's posts
  useEffect(() => {
    fetch(`/api/users/${userId}/posts`)
      .then(res => res.json())
      .then(setPosts);
  }, [userId]);
  
  // Effect 3: Update document title
  useEffect(() => {
    document.title = user ? `${user.name}'s Dashboard` : 'Dashboard';
  }, [user]);
  
  return <div>Dashboard content...</div>;
}

// Abort Controller for Cleanup
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  
  useEffect(() => {
    const abortController = new AbortController();
    
    const fetchResults = async () => {
      if (!query) return;
      
      setLoading(true);
      try {
        const response = await fetch(`/api/search?q=${query}`, {
          signal: abortController.signal
        });
        const data = await response.json();
        setResults(data);
      } catch (error) {
        if (error.name !== 'AbortError') {
          console.error('Fetch error:', error);
        }
      } finally {
        setLoading(false);
      }
    };
    
    fetchResults();
    
    // Cleanup: abort pending request
    return () => {
      abortController.abort();
    };
  }, [query]);
  
  return loading ? <div>Searching...</div> : <div>{results.length} results</div>;
}
```

### 4. useContext Hook

```javascript
/**
 * useContext - Consume context values
 * Solves prop drilling problem
 * Provides global state accessible to component tree
 */

import { createContext, useContext, useState } from 'react';

// 1. Create Context
const ThemeContext = createContext();

// 2. Provider Component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  const value = {
    theme,
    toggleTheme
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Custom Hook for Consuming Context
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// 4. Consumer Components
function ThemedButton() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button 
      onClick={toggleTheme}
      style={{
        backgroundColor: theme === 'light' ? '#fff' : '#333',
        color: theme === 'light' ? '#333' : '#fff'
      }}
    >
      Toggle Theme (Current: {theme})
    </button>
  );
}

function ThemedCard() {
  const { theme } = useTheme();
  
  return (
    <div style={{
      backgroundColor: theme === 'light' ? '#f0f0f0' : '#222',
      color: theme === 'light' ? '#000' : '#fff',
      padding: '20px'
    }}>
      Card with {theme} theme
    </div>
  );
}

// 5. App with Provider
function App() {
  return (
    <ThemeProvider>
      <div>
        <ThemedButton />
        <ThemedCard />
      </div>
    </ThemeProvider>
  );
}

/**
 * ADVANCED CONTEXT PATTERN - Authentication
 */

const AuthContext = createContext();

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    // Check for existing session
    const token = localStorage.getItem('token');
    if (token) {
      // Validate token and fetch user
      fetch('/api/me', {
        headers: { Authorization: `Bearer ${token}` }
      })
        .then(res => res.json())
        .then(setUser)
        .catch(() => localStorage.removeItem('token'))
        .finally(() => setLoading(false));
    } else {
      setLoading(false);
    }
  }, []);
  
  const login = async (email, password) => {
    const response = await fetch('/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
    const data = await response.json();
    localStorage.setItem('token', data.token);
    setUser(data.user);
  };
  
  const logout = () => {
    localStorage.removeItem('token');
    setUser(null);
  };
  
  const value = {
    user,
    loading,
    login,
    logout,
    isAuthenticated: !!user
  };
  
  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}

// Protected Route Component
function ProtectedPage() {
  const { user, loading } = useAuth();
  
  if (loading) return <div>Loading...</div>;
  if (!user) return <div>Please log in</div>;
  
  return <div>Welcome, {user.name}!</div>;
}

/**
 * MULTIPLE CONTEXTS COMPOSITION
 */

function App() {
  return (
    <AuthProvider>
      <ThemeProvider>
        <LanguageProvider>
          <MainApp />
        </LanguageProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}

/**
 * CONTEXT WITH REDUCER (Preview for Day 18)
 */

const CartContext = createContext();

function CartProvider({ children }) {
  const [cart, setCart] = useState([]);
  
  const addToCart = (product) => {
    setCart(prev => {
      const existing = prev.find(item => item.id === product.id);
      if (existing) {
        return prev.map(item =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      }
      return [...prev, { ...product, quantity: 1 }];
    });
  };
  
  const removeFromCart = (productId) => {
    setCart(prev => prev.filter(item => item.id !== productId));
  };
  
  const updateQuantity = (productId, quantity) => {
    setCart(prev => prev.map(item =>
      item.id === productId ? { ...item, quantity } : item
    ));
  };
  
  const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
  
  const value = {
    cart,
    addToCart,
    removeFromCart,
    updateQuantity,
    total,
    itemCount: cart.length
  };
  
  return (
    <CartContext.Provider value={value}>
      {children}
    </CartContext.Provider>
  );
}
```

### 5. Custom Hooks

```javascript
/**
 * CUSTOM HOOKS
 * Extract reusable logic into custom hooks
 * Must start with "use"
 */

// Custom Hook: useLocalStorage
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });
  
  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };
  
  return [storedValue, setValue];
}

// Usage
function App() {
  const [name, setName] = useLocalStorage('name', '');
  
  return (
    <input 
      value={name}
      onChange={(e) => setName(e.target.value)}
      placeholder="Your name (persisted)"
    />
  );
}

// Custom Hook: useFetch
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        if (!response.ok) throw new Error('Network response was not ok');
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    
    fetchData();
  }, [url]);
  
  return { data, loading, error };
}

// Usage
function UserList() {
  const { data: users, loading, error } = useFetch('/api/users');
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// Custom Hook: useToggle
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  
  const toggle = () => setValue(prev => !prev);
  const setTrue = () => setValue(true);
  const setFalse = () => setValue(false);
  
  return [value, { toggle, setTrue, setFalse }];
}

// Usage
function Modal() {
  const [isOpen, { toggle, setTrue, setFalse }] = useToggle(false);
  
  return (
    <>
      <button onClick={setTrue}>Open Modal</button>
      {isOpen && (
        <div className="modal">
          <p>Modal Content</p>
          <button onClick={setFalse}>Close</button>
        </div>
      )}
    </>
  );
}

// Custom Hook: useDebounce
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  
  return debouncedValue;
}

// Usage
function SearchComponent() {
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);
  
  useEffect(() => {
    if (debouncedSearchTerm) {
      // Perform search
      console.log('Searching for:', debouncedSearchTerm);
    }
  }, [debouncedSearchTerm]);
  
  return (
    <input
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

## 💡 Key Concepts

1. **Hooks Rules**: Only call at top level, only in functional components/custom hooks

2. **State Updates are Asynchronous**: Use functional updates when new state depends on previous

3. **useEffect Dependencies**: Always include all values used inside effect in dependency array

4. **Cleanup Functions**: Return cleanup function from useEffect to prevent memory leaks

5. **Context Performance**: Context changes re-render all consumers; split contexts when needed

6. **Custom Hooks**: Extract reusable logic, must start with "use", can use other hooks

7. **Stale Closures**: Be careful with outdated values in closures; use dependency arrays correctly

## 🛠️ Practical Exercises

### Exercise 1: State Management
Build a shopping cart with add, remove, update quantity using useState.

### Exercise 2: Data Fetching
Create a component that fetches and displays data from JSONPlaceholder API.

### Exercise 3: Form Handling
Build a multi-step form with useState managing each step's data.

### Exercise 4: Cleanup Effects
Create a chat component with WebSocket connection using useEffect cleanup.

### Exercise 5: Context Implementation
Build theme switcher (light/dark) using useContext.

### Exercise 6: Custom Hook
Create `useWindowSize` custom hook that tracks window dimensions.

### Exercise 7: Combined Hooks
Build a search feature with debouncing using multiple hooks.

## 🎨 Project: Task Management Application

Build a full-featured task manager using hooks:

**Features:**
1. Add, edit, delete tasks
2. Mark tasks as complete
3. Filter tasks (all, active, completed)
4. Persist data to localStorage
5. Search/filter tasks
6. Dark/light theme toggle
7. Task categories

**Technical Requirements:**
- Use useState for task list, filters, search
- Use useEffect for localStorage persistence
- Use useContext for theme management
- Create custom hooks: `useLocalStorage`, `useTaskFilter`
- Implement cleanup in useEffect
- Proper dependency arrays
- Loading and error states

**Bonus:**
- Drag and drop reordering
- Due dates and reminders
- Statistics dashboard
- Export/import tasks

## 📖 Resources to Explore

### Official Documentation
- [Hooks API Reference](https://react.dev/reference/react)
- [useState](https://react.dev/reference/react/useState)
- [useEffect](https://react.dev/reference/react/useEffect)
- [useContext](https://react.dev/reference/react/useContext)

### Articles
- "A Complete Guide to useEffect" by Dan Abramov
- "How to use React Context effectively" by Kent C. Dodds
- "Custom Hooks" - React docs
- "Rules of Hooks" - React docs

### Videos
- "React Hooks Course" by Web Dev Simplified
- "useEffect Explained" by Fireship
- "Custom Hooks Tutorial" by Codevolution

## 🔍 Interview Questions

1. **What are React Hooks and why were they introduced?**
2. **Explain the rules of hooks.**
3. **What's the difference between useState and useRef?**
4. **When does useEffect run?**
5. **What is the purpose of the dependency array in useEffect?**
6. **How do you prevent infinite loops in useEffect?**
7. **What's the difference between componentDidMount and useEffect?**
8. **How do you clean up side effects?**
9. **What is useContext and when would you use it?**
10. **How do you optimize context to prevent unnecessary re-renders?**
11. **What are custom hooks?**
12. **Can you conditionally call hooks?**
13. **What is a stale closure in React?**
14. **How do you fetch data with useEffect?**
15. **What happens if you omit the dependency array in useEffect?**

## ✅ Success Criteria

- [ ] Use useState for various data types (primitives, objects, arrays)
- [ ] Implement useEffect with proper dependencies
- [ ] Create cleanup functions for subscriptions and timers
- [ ] Build and consume context with useContext
- [ ] Create at least 3 custom hooks
- [ ] Handle async operations with useEffect
- [ ] Prevent common hook pitfalls (stale closures, infinite loops)
- [ ] Complete task management project with all features
- [ ] Answer 12/15 interview questions correctly

## 🚀 Next Steps

Tomorrow (Day 18), you'll learn:
- **State Management**: Redux and advanced Context patterns
- **useReducer Hook**: Complex state logic
- **Redux Toolkit**: Modern Redux development
- **Global State Architecture**: Scalable state management

### Preparation
- Review reducer pattern in JavaScript
- Understand immutability deeply
- Practice array and object transformations
- Read about Flux architecture

---

**Remember**: Hooks make React functional components powerful. Master these three hooks, and you've mastered modern React!
