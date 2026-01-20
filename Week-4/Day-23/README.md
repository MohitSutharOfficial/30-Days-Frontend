# Day 23: TypeScript with React ⚛️🔷

## 🎯 Learning Objectives

By the end of Day 23, you will:
- Type React functional components, props, and state with TypeScript
- Master typing React hooks (useState, useEffect, useContext, useRef, etc.)
- Understand typing patterns for events, forms, and children
- Work with generic components and advanced component patterns
- Type Context API and custom hooks properly
- Implement type-safe Redux/state management solutions
- Handle TypeScript with React Router and third-party libraries
- Apply best practices for scalable React TypeScript applications

---

## 📚 Topics to Study

### 1. **Setting Up React with TypeScript**

```bash
# Create new React + TypeScript project
# npx create-react-app my-app --template typescript

# Or with Vite (faster)
# npm create vite@latest my-app -- --template react-ts

# TypeScript React dependencies
# npm install --save-dev @types/react @types/react-dom
```

```typescript
// Project structure example
/*
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.types.ts
│   │   └── index.ts
│   └── ...
├── types/
│   ├── api.types.ts
│   ├── common.types.ts
│   └── index.ts
├── hooks/
│   ├── useAuth.ts
│   └── useFetch.ts
├── contexts/
│   └── AuthContext.tsx
└── App.tsx
*/
```

### 2. **Typing React Components**

```typescript
// Functional Component - Basic approach
import React from 'react';

interface ButtonProps {
  text: string;
  onClick: () => void;
  disabled?: boolean;
  variant?: 'primary' | 'secondary' | 'danger';
}

// Method 1: Using React.FC (Function Component)
const Button: React.FC<ButtonProps> = ({ text, onClick, disabled = false, variant = 'primary' }) => {
  return (
    <button 
      onClick={onClick} 
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {text}
    </button>
  );
};

// Method 2: Direct typing (preferred in modern React)
function Button2({ text, onClick, disabled = false }: ButtonProps) {
  return <button onClick={onClick} disabled={disabled}>{text}</button>;
}

// With children
interface CardProps {
  title: string;
  children: React.ReactNode;
  footer?: React.ReactNode;
}

const Card = ({ title, children, footer }: CardProps) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-content">{children}</div>
      {footer && <div className="card-footer">{footer}</div>}
    </div>
  );
};

// Component with render props
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item, index)}</li>
      ))}
    </ul>
  );
}

// Usage
// <List 
//   items={users} 
//   renderItem={(user) => <span>{user.name}</span>}
// />
```

### 3. **Typing React Hooks**

```typescript
// useState
import { useState } from 'react';

// Type inference
const [count, setCount] = useState(0); // type: number

// Explicit typing
const [user, setUser] = useState<User | null>(null);

interface User {
  id: number;
  name: string;
  email: string;
}

// useState with initial state function
const [data, setData] = useState<User[]>(() => {
  const stored = localStorage.getItem('users');
  return stored ? JSON.parse(stored) : [];
});

// useEffect
import { useEffect } from 'react';

useEffect(() => {
  // Effect logic
  const subscription = subscribeToSomething();
  
  // Cleanup function
  return () => {
    subscription.unsubscribe();
  };
}, [/* dependencies */]);

// useRef - DOM elements
import { useRef } from 'react';

function TextInput() {
  const inputRef = useRef<HTMLInputElement>(null);
  
  const focusInput = () => {
    inputRef.current?.focus(); // Optional chaining for null check
  };
  
  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}

// useRef - Mutable values
function Timer() {
  const intervalRef = useRef<NodeJS.Timeout | null>(null);
  const [count, setCount] = useState(0);
  
  const startTimer = () => {
    intervalRef.current = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
  };
  
  const stopTimer = () => {
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
    }
  };
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}

// useReducer
import { useReducer } from 'react';

type State = {
  count: number;
  error: string | null;
};

type Action =
  | { type: 'increment' }
  | { type: 'decrement' }
  | { type: 'reset' }
  | { type: 'setError'; payload: string };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 };
    case 'decrement':
      return { ...state, count: state.count - 1 };
    case 'reset':
      return { count: 0, error: null };
    case 'setError':
      return { ...state, error: action.payload };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0, error: null });
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}

// useContext
import { createContext, useContext } from 'react';

interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}
```

### 4. **Event Handling & Forms**

```typescript
// Event Handlers
import React, { ChangeEvent, FormEvent, MouseEvent, KeyboardEvent } from 'react';

function EventExamples() {
  // Click events
  const handleClick = (event: MouseEvent<HTMLButtonElement>) => {
    console.log('Button clicked:', event.currentTarget);
  };
  
  // Change events
  const handleChange = (event: ChangeEvent<HTMLInputElement>) => {
    console.log('Input value:', event.target.value);
  };
  
  // Keyboard events
  const handleKeyPress = (event: KeyboardEvent<HTMLInputElement>) => {
    if (event.key === 'Enter') {
      console.log('Enter pressed');
    }
  };
  
  return (
    <div>
      <button onClick={handleClick}>Click me</button>
      <input onChange={handleChange} onKeyPress={handleKeyPress} />
    </div>
  );
}

// Form Handling
interface FormData {
  username: string;
  email: string;
  password: string;
}

interface FormErrors {
  username?: string;
  email?: string;
  password?: string;
}

function RegistrationForm() {
  const [formData, setFormData] = useState<FormData>({
    username: '',
    email: '',
    password: ''
  });
  
  const [errors, setErrors] = useState<FormErrors>({});
  
  const handleInputChange = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };
  
  const validateForm = (): boolean => {
    const newErrors: FormErrors = {};
    
    if (formData.username.length < 3) {
      newErrors.username = 'Username must be at least 3 characters';
    }
    
    if (!formData.email.includes('@')) {
      newErrors.email = 'Invalid email address';
    }
    
    if (formData.password.length < 8) {
      newErrors.password = 'Password must be at least 8 characters';
    }
    
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };
  
  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    
    if (validateForm()) {
      console.log('Form submitted:', formData);
      // API call here
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="username"
        value={formData.username}
        onChange={handleInputChange}
        placeholder="Username"
      />
      {errors.username && <span className="error">{errors.username}</span>}
      
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleInputChange}
        placeholder="Email"
      />
      {errors.email && <span className="error">{errors.email}</span>}
      
      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleInputChange}
        placeholder="Password"
      />
      {errors.password && <span className="error">{errors.password}</span>}
      
      <button type="submit">Register</button>
    </form>
  );
}
```

### 5. **Custom Hooks with TypeScript**

```typescript
// Fetch hook
interface UseFetchResult<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
  refetch: () => void;
}

function useFetch<T>(url: string): UseFetchResult<T> {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  const fetchData = useCallback(async () => {
    try {
      setLoading(true);
      const response = await fetch(url);
      if (!response.ok) throw new Error('Network response was not ok');
      const json = await response.json();
      setData(json);
      setError(null);
    } catch (err) {
      setError(err as Error);
    } finally {
      setLoading(false);
    }
  }, [url]);
  
  useEffect(() => {
    fetchData();
  }, [fetchData]);
  
  return { data, loading, error, refetch: fetchData };
}

// Usage
// const { data, loading, error } = useFetch<User[]>('/api/users');

// Local Storage hook
function useLocalStorage<T>(key: string, initialValue: T): [T, (value: T) => void] {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });
  
  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };
  
  return [storedValue, setValue];
}

// Previous value hook
function usePrevious<T>(value: T): T | undefined {
  const ref = useRef<T>();
  
  useEffect(() => {
    ref.current = value;
  }, [value]);
  
  return ref.current;
}

// Debounce hook
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);
  
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
```

### 6. **Context API with TypeScript**

```typescript
// Auth Context Example
import React, { createContext, useContext, useState, useCallback } from 'react';

interface User {
  id: string;
  username: string;
  email: string;
  role: 'admin' | 'user';
}

interface AuthContextType {
  user: User | null;
  isAuthenticated: boolean;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  updateUser: (user: Partial<User>) => void;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}

interface AuthProviderProps {
  children: React.ReactNode;
}

export function AuthProvider({ children }: AuthProviderProps) {
  const [user, setUser] = useState<User | null>(null);
  
  const login = useCallback(async (email: string, password: string) => {
    // API call
    const response = await fetch('/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
    
    const userData = await response.json();
    setUser(userData);
  }, []);
  
  const logout = useCallback(() => {
    setUser(null);
  }, []);
  
  const updateUser = useCallback((updates: Partial<User>) => {
    setUser(prev => prev ? { ...prev, ...updates } : null);
  }, []);
  
  const value: AuthContextType = {
    user,
    isAuthenticated: !!user,
    login,
    logout,
    updateUser
  };
  
  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

// Usage in component
// function ProfilePage() {
//   const { user, logout } = useAuth();
//   
//   return (
//     <div>
//       <h1>Welcome, {user?.username}</h1>
//       <button onClick={logout}>Logout</button>
//     </div>
//   );
// }
```

### 7. **Advanced Patterns**

```typescript
// Higher-Order Component (HOC)
function withLoading<P extends object>(
  Component: React.ComponentType<P>
) {
  return function WithLoadingComponent(
    props: P & { loading: boolean }
  ) {
    const { loading, ...rest } = props;
    
    if (loading) {
      return <div>Loading...</div>;
    }
    
    return <Component {...(rest as P)} />;
  };
}

// Generic Component with Constraints
interface DataTableProps<T extends { id: string | number }> {
  data: T[];
  columns: Array<{
    key: keyof T;
    header: string;
    render?: (value: T[keyof T], item: T) => React.ReactNode;
  }>;
  onRowClick?: (item: T) => void;
}

function DataTable<T extends { id: string | number }>({
  data,
  columns,
  onRowClick
}: DataTableProps<T>) {
  return (
    <table>
      <thead>
        <tr>
          {columns.map(col => (
            <th key={String(col.key)}>{col.header}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map(item => (
          <tr 
            key={item.id} 
            onClick={() => onRowClick?.(item)}
          >
            {columns.map(col => (
              <td key={String(col.key)}>
                {col.render 
                  ? col.render(item[col.key], item)
                  : String(item[col.key])}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}

// Compound Components Pattern
interface TabsContextType {
  activeTab: string;
  setActiveTab: (tab: string) => void;
}

const TabsContext = createContext<TabsContextType | undefined>(undefined);

interface TabsProps {
  defaultTab: string;
  children: React.ReactNode;
}

function Tabs({ defaultTab, children }: TabsProps) {
  const [activeTab, setActiveTab] = useState(defaultTab);
  
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

interface TabListProps {
  children: React.ReactNode;
}

Tabs.TabList = function TabList({ children }: TabListProps) {
  return <div className="tab-list">{children}</div>;
};

interface TabProps {
  id: string;
  children: React.ReactNode;
}

Tabs.Tab = function Tab({ id, children }: TabProps) {
  const context = useContext(TabsContext);
  if (!context) throw new Error('Tab must be used within Tabs');
  
  const { activeTab, setActiveTab } = context;
  
  return (
    <button
      className={activeTab === id ? 'active' : ''}
      onClick={() => setActiveTab(id)}
    >
      {children}
    </button>
  );
};

Tabs.TabPanel = function TabPanel({ id, children }: TabProps) {
  const context = useContext(TabsContext);
  if (!context) throw new Error('TabPanel must be used within Tabs');
  
  const { activeTab } = context;
  
  if (activeTab !== id) return null;
  
  return <div className="tab-panel">{children}</div>;
};
```

### 8. **Typing Third-Party Libraries**

```typescript
// React Router v6
import { useNavigate, useParams, useLocation } from 'react-router-dom';

interface RouteParams {
  id: string;
}

function UserDetail() {
  const { id } = useParams<RouteParams>();
  const navigate = useNavigate();
  const location = useLocation();
  
  // id is string | undefined
  if (!id) {
    return <div>No user ID provided</div>;
  }
  
  return <div>User ID: {id}</div>;
}

// Axios with TypeScript
import axios, { AxiosResponse } from 'axios';

interface ApiResponse<T> {
  data: T;
  message: string;
  status: number;
}

async function fetchUsers(): Promise<User[]> {
  const response: AxiosResponse<ApiResponse<User[]>> = await axios.get('/api/users');
  return response.data.data;
}

// Styled Components
import styled from 'styled-components';

interface ButtonStyledProps {
  $variant?: 'primary' | 'secondary';
  $size?: 'small' | 'medium' | 'large';
}

const StyledButton = styled.button<ButtonStyledProps>`
  padding: ${props => props.$size === 'small' ? '8px' : '12px'};
  background: ${props => props.$variant === 'primary' ? 'blue' : 'gray'};
`;
```

---

## 💡 Key Concepts

1. **Type Safety**: Catch React-specific errors at compile time
2. **Props Validation**: Replace PropTypes with TypeScript interfaces
3. **Generic Components**: Create reusable components that work with multiple types
4. **Event Typing**: Properly type all DOM events and handlers
5. **Hook Typing**: Type hooks correctly for safe state management
6. **Context Typing**: Create type-safe context providers and consumers
7. **Ref Handling**: Properly type refs for DOM elements and values
8. **Children Typing**: Use React.ReactNode for flexible children props

---

## 🛠️ Practical Exercises

### Exercise 1: Type-Safe Form Builder
Create a dynamic form component with validation and TypeScript.

### Exercise 2: Generic Data Grid
Build a reusable data grid with sorting, filtering, and pagination.

### Exercise 3: Custom Hooks Library
Develop a collection of type-safe custom hooks (useDebounce, useThrottle, useAsync).

### Exercise 4: Context-Based State Management
Implement a shopping cart using Context API with full TypeScript support.

### Exercise 5: Higher-Order Component
Create a HOC for authentication and authorization with proper typing.

---

## 🎨 Project: E-Commerce Product Dashboard

Build a comprehensive product management dashboard with TypeScript:

**Requirements:**
1. Product list with type-safe filtering and sorting
2. Type-safe forms for adding/editing products
3. Custom hooks for data fetching and caching
4. Context for cart management
5. Generic table component with custom renderers
6. Type-safe routing with React Router
7. Error boundaries with proper typing
8. Fully typed API client with Axios

**Features:**
- CRUD operations with type safety
- Real-time search with debouncing
- Form validation with TypeScript
- Role-based access control
- Shopping cart with calculations
- Responsive design

---

## 📖 Resources to Explore

### Documentation
- **React TypeScript Cheatsheet**: https://react-typescript-cheatsheet.netlify.app/
- **React Docs - TypeScript**: https://react.dev/learn/typescript
- **TypeScript React Handbook**: Official Microsoft guide

### Video Courses
- **React & TypeScript** by Jack Herrington
- **React TypeScript Tutorial** by Ben Awad
- **Full Stack React TypeScript** by freeCodeCamp

### Articles
- "Advanced React TypeScript Patterns"
- "React Hooks with TypeScript Best Practices"
- "Type-Safe React Redux"

### GitHub Repositories
- **react-typescript-samples**: Real-world examples
- **typescript-react-starter**: Official starter template
- **awesome-react-typescript**: Curated resources

---

## 🔍 Interview Questions

### Basic
1. How do you type React component props?
2. What's the difference between React.FC and direct function typing?
3. How do you handle optional props in TypeScript React?
4. How do you type useState with complex objects?
5. How do you type React children?

### Intermediate
1. How do you create a generic React component?
2. Explain typing custom hooks with TypeScript.
3. How do you type Context API properly?
4. How do you handle form events in TypeScript React?
5. What's the best way to type useRef?
6. How do you type HOCs in React?
7. Explain typing render props patterns.

### Advanced
1. How do you implement type-safe compound components?
2. Create a type-safe form builder system.
3. How do you type recursive React components?
4. Explain typing polymorphic components ("as" prop pattern).
5. How would you type a complex state machine with useReducer?
6. Implement a type-safe event emitter for React.
7. How do you handle discriminated unions in React components?

### Practical
1. Design a type-safe API layer for a React application.
2. Implement a type-safe routing system.
3. Create a type-safe form validation library.
4. Build a generic data table with advanced features.

---

## ✅ Success Criteria

- [ ] Can type all React components and props confidently
- [ ] Successfully type all React hooks
- [ ] Handle events and forms with proper typing
- [ ] Create type-safe custom hooks
- [ ] Implement Context API with TypeScript
- [ ] Build generic and reusable components
- [ ] Apply advanced patterns (HOC, render props, compound components)
- [ ] Integrate TypeScript with third-party React libraries
- [ ] Write production-ready TypeScript React code
- [ ] Debug TypeScript errors in React applications

---

## 🚀 Next Steps

Tomorrow (Day 24), you'll learn:
- **Testing with Jest and React Testing Library**
- **Writing unit and integration tests**
- **Testing TypeScript React components**
- **Test-driven development (TDD) principles**

**Preparation:**
- Review testing fundamentals
- Understand assertion libraries
- Explore Jest documentation
- Practice writing testable code

---

**Remember**: TypeScript + React = Type-safe, maintainable, and scalable applications. Embrace the type system! ⚛️🔷
