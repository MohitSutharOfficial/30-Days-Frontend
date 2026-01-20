# Day 18: State Management (Redux/Context API)

## 🎯 Learning Objectives

By the end of Day 18, you will:
- Understand advanced state management patterns in React
- Master useReducer for complex state logic
- Implement Redux for global state management
- Use Redux Toolkit for modern Redux development
- Compare Context API vs Redux for different use cases
- Implement middleware for side effects (Redux Thunk, Redux Saga)
- Structure scalable state management architecture
- Handle async actions and API integration
- Implement state normalization and selectors
- Understand immutability patterns and best practices

## 📚 Topics to Study

### 1. Understanding State Management

```javascript
/**
 * WHY STATE MANAGEMENT?
 * 
 * Problems with Local State:
 * - Prop drilling through multiple layers
 * - Shared state between distant components
 * - State duplication and synchronization
 * - Difficult to debug and test
 * 
 * Solutions:
 * 1. Context API - Built-in, simple, good for low-frequency updates
 * 2. Redux - Predictable, devtools, middleware, complex apps
 * 3. useReducer - Local complex state, reducer pattern
 */

// Problem: Prop Drilling
function App() {
  const [user, setUser] = useState(null);
  return <Layout user={user} setUser={setUser} />;
}

function Layout({ user, setUser }) {
  return <Sidebar user={user} setUser={setUser} />;
}

function Sidebar({ user, setUser }) {
  return <UserMenu user={user} setUser={setUser} />;
}

function UserMenu({ user, setUser }) {
  return <div>{user?.name}</div>;
}

// Solution: Context or Redux - direct access without drilling
```

### 2. useReducer Hook

```javascript
/**
 * useReducer - useState alternative for complex state logic
 * Similar to Redux reducer pattern
 * 
 * Syntax: const [state, dispatch] = useReducer(reducer, initialState)
 */

import { useReducer } from 'react';

// Basic Counter with useReducer
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return initialState;
    case 'setCount':
      return { count: action.payload };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
      <button onClick={() => dispatch({ type: 'setCount', payload: 100 })}>
        Set to 100
      </button>
    </div>
  );
}

// Complex Form with useReducer
const formInitialState = {
  username: '',
  email: '',
  password: '',
  confirmPassword: '',
  errors: {},
  isSubmitting: false
};

function formReducer(state, action) {
  switch (action.type) {
    case 'SET_FIELD':
      return {
        ...state,
        [action.field]: action.value,
        errors: { ...state.errors, [action.field]: null }
      };
    case 'SET_ERRORS':
      return { ...state, errors: action.errors };
    case 'SET_SUBMITTING':
      return { ...state, isSubmitting: action.value };
    case 'RESET_FORM':
      return formInitialState;
    default:
      return state;
  }
}

function RegistrationForm() {
  const [state, dispatch] = useReducer(formReducer, formInitialState);
  
  const handleChange = (field) => (e) => {
    dispatch({ type: 'SET_FIELD', field, value: e.target.value });
  };
  
  const validate = () => {
    const errors = {};
    if (!state.username) errors.username = 'Required';
    if (!state.email) errors.email = 'Required';
    if (state.password.length < 8) errors.password = 'Min 8 characters';
    if (state.password !== state.confirmPassword) {
      errors.confirmPassword = 'Passwords must match';
    }
    return errors;
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    const errors = validate();
    
    if (Object.keys(errors).length > 0) {
      dispatch({ type: 'SET_ERRORS', errors });
      return;
    }
    
    dispatch({ type: 'SET_SUBMITTING', value: true });
    
    try {
      await api.register(state);
      dispatch({ type: 'RESET_FORM' });
    } catch (error) {
      dispatch({ type: 'SET_ERRORS', errors: { submit: error.message } });
    } finally {
      dispatch({ type: 'SET_SUBMITTING', value: false });
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        value={state.username}
        onChange={handleChange('username')}
        placeholder="Username"
      />
      {state.errors.username && <span>{state.errors.username}</span>}
      
      <input
        value={state.email}
        onChange={handleChange('email')}
        type="email"
        placeholder="Email"
      />
      {state.errors.email && <span>{state.errors.email}</span>}
      
      <input
        value={state.password}
        onChange={handleChange('password')}
        type="password"
        placeholder="Password"
      />
      {state.errors.password && <span>{state.errors.password}</span>}
      
      <input
        value={state.confirmPassword}
        onChange={handleChange('confirmPassword')}
        type="password"
        placeholder="Confirm Password"
      />
      {state.errors.confirmPassword && <span>{state.errors.confirmPassword}</span>}
      
      <button type="submit" disabled={state.isSubmitting}>
        {state.isSubmitting ? 'Submitting...' : 'Register'}
      </button>
    </form>
  );
}

// Todo App with useReducer
function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        { id: Date.now(), text: action.payload, completed: false }
      ];
    case 'TOGGLE_TODO':
      return state.map(todo =>
        todo.id === action.payload
          ? { ...todo, completed: !todo.completed }
          : todo
      );
    case 'DELETE_TODO':
      return state.filter(todo => todo.id !== action.payload);
    case 'EDIT_TODO':
      return state.map(todo =>
        todo.id === action.payload.id
          ? { ...todo, text: action.payload.text }
          : todo
      );
    case 'CLEAR_COMPLETED':
      return state.filter(todo => !todo.completed);
    default:
      return state;
  }
}

function TodoApp() {
  const [todos, dispatch] = useReducer(todoReducer, []);
  const [input, setInput] = useState('');
  
  const addTodo = () => {
    if (input.trim()) {
      dispatch({ type: 'ADD_TODO', payload: input });
      setInput('');
    }
  };
  
  return (
    <div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && addTodo()}
      />
      <button onClick={addTodo}>Add</button>
      <button onClick={() => dispatch({ type: 'CLEAR_COMPLETED' })}>
        Clear Completed
      </button>
      
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
            />
            <span style={{
              textDecoration: todo.completed ? 'line-through' : 'none'
            }}>
              {todo.text}
            </span>
            <button onClick={() => dispatch({ type: 'DELETE_TODO', payload: todo.id })}>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 3. Advanced Context Patterns

```javascript
/**
 * CONTEXT + REDUCER PATTERN
 * Combine useContext and useReducer for scalable state management
 */

import { createContext, useContext, useReducer } from 'react';

// Actions
const ACTIONS = {
  ADD_ITEM: 'ADD_ITEM',
  REMOVE_ITEM: 'REMOVE_ITEM',
  UPDATE_QUANTITY: 'UPDATE_QUANTITY',
  CLEAR_CART: 'CLEAR_CART'
};

// Reducer
function cartReducer(state, action) {
  switch (action.type) {
    case ACTIONS.ADD_ITEM: {
      const existing = state.items.find(item => item.id === action.payload.id);
      if (existing) {
        return {
          ...state,
          items: state.items.map(item =>
            item.id === action.payload.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          )
        };
      }
      return {
        ...state,
        items: [...state.items, { ...action.payload, quantity: 1 }]
      };
    }
    case ACTIONS.REMOVE_ITEM:
      return {
        ...state,
        items: state.items.filter(item => item.id !== action.payload)
      };
    case ACTIONS.UPDATE_QUANTITY:
      return {
        ...state,
        items: state.items.map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: action.payload.quantity }
            : item
        )
      };
    case ACTIONS.CLEAR_CART:
      return { ...state, items: [] };
    default:
      return state;
  }
}

// Context
const CartContext = createContext();

// Provider
function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, { items: [] });
  
  // Derived state
  const total = state.items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );
  
  const itemCount = state.items.reduce(
    (count, item) => count + item.quantity,
    0
  );
  
  // Actions
  const addItem = (item) => {
    dispatch({ type: ACTIONS.ADD_ITEM, payload: item });
  };
  
  const removeItem = (id) => {
    dispatch({ type: ACTIONS.REMOVE_ITEM, payload: id });
  };
  
  const updateQuantity = (id, quantity) => {
    dispatch({ type: ACTIONS.UPDATE_QUANTITY, payload: { id, quantity } });
  };
  
  const clearCart = () => {
    dispatch({ type: ACTIONS.CLEAR_CART });
  };
  
  const value = {
    items: state.items,
    total,
    itemCount,
    addItem,
    removeItem,
    updateQuantity,
    clearCart
  };
  
  return <CartContext.Provider value={value}>{children}</CartContext.Provider>;
}

// Custom Hook
function useCart() {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error('useCart must be used within CartProvider');
  }
  return context;
}

// Usage
function ProductCard({ product }) {
  const { addItem } = useCart();
  
  return (
    <div>
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <button onClick={() => addItem(product)}>Add to Cart</button>
    </div>
  );
}

function CartSummary() {
  const { items, total, itemCount, removeItem } = useCart();
  
  return (
    <div>
      <h2>Cart ({itemCount} items)</h2>
      <ul>
        {items.map(item => (
          <li key={item.id}>
            {item.name} x {item.quantity} = ${item.price * item.quantity}
            <button onClick={() => removeItem(item.id)}>Remove</button>
          </li>
        ))}
      </ul>
      <p>Total: ${total.toFixed(2)}</p>
    </div>
  );
}

/**
 * SPLITTING CONTEXTS FOR PERFORMANCE
 * Separate frequently changing state from static state
 */

// State Context - frequently changes
const StateContext = createContext();
// Dispatch Context - never changes
const DispatchContext = createContext();

function AppProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <StateContext.Provider value={state}>
      <DispatchContext.Provider value={dispatch}>
        {children}
      </DispatchContext.Provider>
    </StateContext.Provider>
  );
}

// Components that only need dispatch won't re-render on state changes
function useAppState() {
  const context = useContext(StateContext);
  if (!context) throw new Error('Must be within AppProvider');
  return context;
}

function useAppDispatch() {
  const context = useContext(DispatchContext);
  if (!context) throw new Error('Must be within AppProvider');
  return context;
}
```

### 4. Redux Fundamentals

```javascript
/**
 * REDUX - Predictable state container
 * 
 * Core Concepts:
 * 1. Store - Single source of truth
 * 2. Actions - Plain objects describing what happened
 * 3. Reducers - Pure functions that specify state changes
 * 4. Dispatch - Method to send actions to store
 * 
 * Principles:
 * 1. Single source of truth (one store)
 * 2. State is read-only (dispatch actions to change)
 * 3. Changes made with pure functions (reducers)
 */

// Installation:
// npm install @reduxjs/toolkit react-redux

import { createStore } from 'redux';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Action Types
const INCREMENT = 'counter/increment';
const DECREMENT = 'counter/decrement';
const INCREMENT_BY = 'counter/incrementBy';

// Action Creators
const increment = () => ({ type: INCREMENT });
const decrement = () => ({ type: DECREMENT });
const incrementBy = (amount) => ({ type: INCREMENT_BY, payload: amount });

// Reducer
const initialState = { value: 0 };

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { ...state, value: state.value + 1 };
    case DECREMENT:
      return { ...state, value: state.value - 1 };
    case INCREMENT_BY:
      return { ...state, value: state.value + action.payload };
    default:
      return state;
  }
}

// Store
const store = createStore(counterReducer);

// App Setup
function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}

// Component using Redux
function Counter() {
  const count = useSelector(state => state.value);
  const dispatch = useDispatch();
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(incrementBy(5))}>+5</button>
    </div>
  );
}

/**
 * COMBINING REDUCERS
 * Split state into multiple reducers
 */

import { combineReducers } from 'redux';

// User Reducer
const userInitialState = { currentUser: null, isAuthenticated: false };

function userReducer(state = userInitialState, action) {
  switch (action.type) {
    case 'user/login':
      return { currentUser: action.payload, isAuthenticated: true };
    case 'user/logout':
      return { currentUser: null, isAuthenticated: false };
    default:
      return state;
  }
}

// Products Reducer
const productsInitialState = { items: [], loading: false, error: null };

function productsReducer(state = productsInitialState, action) {
  switch (action.type) {
    case 'products/fetchStart':
      return { ...state, loading: true };
    case 'products/fetchSuccess':
      return { items: action.payload, loading: false, error: null };
    case 'products/fetchError':
      return { ...state, loading: false, error: action.payload };
    default:
      return state;
  }
}

// Root Reducer
const rootReducer = combineReducers({
  user: userReducer,
  products: productsReducer,
  cart: cartReducer
});

// Usage in components
function ProductList() {
  const products = useSelector(state => state.products.items);
  const loading = useSelector(state => state.products.loading);
  const dispatch = useDispatch();
  
  useEffect(() => {
    dispatch({ type: 'products/fetchStart' });
    fetch('/api/products')
      .then(res => res.json())
      .then(data => dispatch({ type: 'products/fetchSuccess', payload: data }))
      .catch(err => dispatch({ type: 'products/fetchError', payload: err.message }));
  }, [dispatch]);
  
  if (loading) return <div>Loading...</div>;
  
  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

### 5. Redux Toolkit (Modern Redux)

```javascript
/**
 * REDUX TOOLKIT - Official recommended way to write Redux
 * 
 * Benefits:
 * - Less boilerplate
 * - Built-in Immer for immutability
 * - Better TypeScript support
 * - Includes Redux DevTools
 * - Built-in thunk for async
 */

import { configureStore, createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Slice (combines actions, action creators, and reducer)
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      // Immer allows "mutating" code
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    },
    reset: (state) => {
      state.value = 0;
    }
  }
});

// Export actions
export const { increment, decrement, incrementByAmount, reset } = counterSlice.actions;

// Async Thunk for API calls
export const fetchProducts = createAsyncThunk(
  'products/fetch',
  async (category) => {
    const response = await fetch(`/api/products?category=${category}`);
    return response.json();
  }
);

// Products Slice
const productsSlice = createSlice({
  name: 'products',
  initialState: {
    items: [],
    status: 'idle', // 'idle' | 'loading' | 'succeeded' | 'failed'
    error: null
  },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchProducts.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchProducts.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.items = action.payload;
      })
      .addCase(fetchProducts.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message;
      });
  }
});

// User Slice with Login
const userSlice = createSlice({
  name: 'user',
  initialState: { currentUser: null, token: null },
  reducers: {
    setUser: (state, action) => {
      state.currentUser = action.payload.user;
      state.token = action.payload.token;
    },
    logout: (state) => {
      state.currentUser = null;
      state.token = null;
    }
  }
});

export const loginUser = createAsyncThunk(
  'user/login',
  async ({ email, password }) => {
    const response = await fetch('/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
    const data = await response.json();
    localStorage.setItem('token', data.token);
    return data;
  }
);

// Configure Store
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
    products: productsSlice.reducer,
    user: userSlice.reducer
  }
});

// App Setup
function App() {
  return (
    <Provider store={store}>
      <MainApp />
    </Provider>
  );
}

// Using Redux Toolkit in Components
function Counter() {
  const count = useSelector(state => state.counter.value);
  const dispatch = useDispatch();
  
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(incrementByAmount(10))}>+10</button>
    </div>
  );
}

function ProductList() {
  const { items, status, error } = useSelector(state => state.products);
  const dispatch = useDispatch();
  
  useEffect(() => {
    dispatch(fetchProducts('electronics'));
  }, [dispatch]);
  
  if (status === 'loading') return <div>Loading...</div>;
  if (status === 'failed') return <div>Error: {error}</div>;
  
  return (
    <ul>
      {items.map(product => (
        <li key={product.id}>{product.name} - ${product.price}</li>
      ))}
    </ul>
  );
}

/**
 * SELECTORS - Compute derived state
 */

// Memoized selectors with reselect (included in RTK)
import { createSelector } from '@reduxjs/toolkit';

// Basic selector
const selectCart = state => state.cart.items;

// Derived selector
const selectCartTotal = createSelector(
  [selectCart],
  (items) => items.reduce((total, item) => total + item.price * item.quantity, 0)
);

const selectCartItemCount = createSelector(
  [selectCart],
  (items) => items.reduce((count, item) => count + item.quantity, 0)
);

// Usage
function CartSummary() {
  const total = useSelector(selectCartTotal);
  const itemCount = useSelector(selectCartItemCount);
  
  return (
    <div>
      <p>Items: {itemCount}</p>
      <p>Total: ${total.toFixed(2)}</p>
    </div>
  );
}
```

### 6. Context API vs Redux

```javascript
/**
 * WHEN TO USE CONTEXT API:
 * - Theme, locale, user preferences
 * - Low to medium frequency updates
 * - Simple state structure
 * - Small to medium apps
 * - No need for middleware
 * 
 * WHEN TO USE REDUX:
 * - Large applications
 * - Complex state logic
 * - High frequency updates
 * - Need for time-travel debugging
 * - Middleware for async actions
 * - State persistence
 * - Multiple developers
 */

// Context API Example - Theme
const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Redux Example - E-commerce
const store = configureStore({
  reducer: {
    products: productsReducer,
    cart: cartReducer,
    orders: ordersReducer,
    user: userReducer,
    ui: uiReducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(logger, apiMiddleware)
});

// Hybrid Approach - Use Both
function App() {
  return (
    <Provider store={store}>
      <ThemeProvider>
        <LocaleProvider>
          <MainApp />
        </LocaleProvider>
      </ThemeProvider>
    </Provider>
  );
}
```

## 💡 Key Concepts

1. **Immutability**: Never mutate state directly; always return new objects/arrays

2. **Single Source of Truth**: Redux maintains one store for entire app state

3. **Pure Reducers**: Reducers must be pure functions without side effects

4. **Action Types**: Use constants to avoid typos and enable better tooling

5. **Middleware**: Extend Redux with custom functionality (logging, async, etc.)

6. **Normalization**: Flatten nested data for easier updates and access

7. **Derived State**: Compute values from state using selectors, don't store them

## 🛠️ Practical Exercises

### Exercise 1: useReducer Todo App
Build todo application using useReducer with add, toggle, delete, filter.

### Exercise 2: Context + Reducer
Create global notification system with Context and useReducer.

### Exercise 3: Redux Counter
Implement counter with Redux including increment, decrement, reset.

### Exercise 4: Redux Toolkit Async
Build product list with async data fetching using createAsyncThunk.

### Exercise 5: Selectors
Create memoized selectors for filtering and sorting data.

### Exercise 6: Multiple Reducers
Build app with separate reducers for users, posts, and comments.

### Exercise 7: State Persistence
Implement localStorage persistence for Redux state.

## 🎨 Project: E-Commerce Store with Redux

Build a complete e-commerce application:

**Features:**
1. Product listing with categories
2. Product search and filters
3. Shopping cart (add, remove, update quantity)
4. User authentication (login/logout)
5. Order history
6. Wishlist functionality

**State Structure:**
```javascript
{
  products: { items: [], status: '', filters: {} },
  cart: { items: [], total: 0 },
  user: { currentUser: null, token: null },
  orders: { list: [], status: '' },
  wishlist: { items: [] },
  ui: { modal: null, notifications: [] }
}
```

**Technical Requirements:**
- Use Redux Toolkit
- Implement async thunks for API calls
- Create selectors for derived state
- Add loading and error states
- Implement optimistic updates
- Add Redux DevTools integration
- Use middleware for API calls
- Normalize data structure

## 📖 Resources to Explore

### Official Documentation
- [Redux Official Docs](https://redux.js.org/)
- [Redux Toolkit](https://redux-toolkit.js.org/)
- [React-Redux](https://react-redux.js.org/)
- [useReducer Hook](https://react.dev/reference/react/useReducer)

### Articles
- "You Might Not Need Redux" by Dan Abramov
- "Redux vs Context API" comparisons
- "Normalizing State Shape" - Redux docs
- "Redux Toolkit Tutorial" - Official guide

### Videos
- "Redux Tutorial" by Net Ninja
- "Redux Toolkit Crash Course" by Traversy Media
- "Modern Redux with Redux Toolkit" by Academind

### Tools
- Redux DevTools Extension
- Redux Toolkit CLI

## 🔍 Interview Questions

1. **What is Redux and why would you use it?**
2. **Explain the three principles of Redux.**
3. **What's the difference between Redux and Context API?**
4. **What is a reducer in Redux?**
5. **What are Redux actions and action creators?**
6. **What is Redux middleware? Give examples.**
7. **What is Redux Thunk and why is it needed?**
8. **How do you handle async operations in Redux?**
9. **What is the difference between useReducer and Redux?**
10. **What are Redux selectors and why use them?**
11. **How do you structure a Redux store?**
12. **What is state normalization in Redux?**
13. **How does Redux DevTools help in development?**
14. **What is the difference between createSlice and createReducer?**
15. **How do you test Redux reducers and actions?**

## ✅ Success Criteria

- [ ] Implement complex state with useReducer
- [ ] Create Context + Reducer patterns
- [ ] Set up Redux store with multiple reducers
- [ ] Use Redux Toolkit for modern Redux development
- [ ] Implement async actions with createAsyncThunk
- [ ] Create memoized selectors
- [ ] Handle loading and error states
- [ ] Complete e-commerce project with full state management
- [ ] Integrate Redux DevTools
- [ ] Answer 12/15 interview questions correctly

## 🚀 Next Steps

Tomorrow (Day 19), you'll learn:
- **React Router**: Client-side routing
- **Navigation**: Programmatic navigation, route parameters
- **Protected Routes**: Authentication-based routing
- **Code Splitting**: Route-based lazy loading

### Preparation
- Review browser routing concepts
- Understand URL parameters and query strings
- Practice with history API
- Read about SPA navigation

---

**Remember**: State management is about choosing the right tool for the job. Start simple with useState/Context, scale to Redux when needed!
