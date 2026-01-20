# Day 21: Performance Optimization & React Best Practices

## 🎯 Learning Objectives

By the end of Day 21, you will:
- Master React performance optimization techniques
- Use React.memo, useMemo, and useCallback effectively
- Implement code splitting and lazy loading
- Profile and measure React application performance
- Understand React rendering behavior and optimization strategies
- Implement virtualization for large lists
- Follow React best practices and design patterns
- Avoid common React anti-patterns
- Structure scalable React applications
- Prepare production-ready React applications

## 📚 Topics to Study

### 1. Understanding React Rendering

```javascript
/**
 * HOW REACT RENDERS
 * 
 * 1. Component state/props change
 * 2. React re-renders component
 * 3. React compares new virtual DOM with previous
 * 4. React updates only changed parts in real DOM
 * 
 * WHEN COMPONENTS RE-RENDER:
 * - State changes (useState, useReducer)
 * - Props change
 * - Parent component re-renders
 * - Context value changes
 * - Force update (rarely used)
 */

// Example: Unnecessary Re-renders
function Parent() {
  const [count, setCount] = useState(0);
  
  console.log('Parent rendered');
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
      {/* Child re-renders even though its props don't change */}
      <Child />
    </div>
  );
}

function Child() {
  console.log('Child rendered'); // Logs on every parent render
  return <div>I am a child</div>;
}

/**
 * RENDER PHASES
 * 
 * 1. Render Phase (Pure, can be paused)
 *    - Call component functions
 *    - Calculate changes
 *    - Can be async
 * 
 * 2. Commit Phase (Synchronous)
 *    - Update DOM
 *    - Run effects (useEffect)
 *    - Update refs
 */
```

### 2. React.memo - Preventing Unnecessary Re-renders

```javascript
/**
 * React.memo - Higher-order component
 * Memoizes component, re-renders only if props change
 * Shallow comparison by default
 */

import { memo } from 'react';

// Without memo - re-renders on every parent render
function ExpensiveComponent({ data }) {
  console.log('ExpensiveComponent rendered');
  // Complex calculations or rendering
  const processedData = data.map(item => ({
    ...item,
    processed: heavyComputation(item)
  }));
  
  return (
    <div>
      {processedData.map(item => (
        <div key={item.id}>{item.processed}</div>
      ))}
    </div>
  );
}

// With memo - re-renders only when data prop changes
const MemoizedExpensiveComponent = memo(ExpensiveComponent);

function Parent() {
  const [count, setCount] = useState(0);
  const [data, setData] = useState([/* data */]);
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
      {/* Only re-renders when data changes, not when count changes */}
      <MemoizedExpensiveComponent data={data} />
    </div>
  );
}

/**
 * Custom Comparison Function
 * For complex props comparison
 */

const UserCard = memo(
  function UserCard({ user, onEdit }) {
    console.log('UserCard rendered');
    return (
      <div>
        <h3>{user.name}</h3>
        <p>{user.email}</p>
        <button onClick={() => onEdit(user.id)}>Edit</button>
      </div>
    );
  },
  (prevProps, nextProps) => {
    // Return true if props are equal (skip re-render)
    // Return false if props are different (re-render)
    return (
      prevProps.user.id === nextProps.user.id &&
      prevProps.user.name === nextProps.user.name &&
      prevProps.user.email === nextProps.user.email
      // Intentionally ignore onEdit function comparison
    );
  }
);

/**
 * WHEN TO USE React.memo
 * 
 * ✅ Use when:
 * - Pure functional component
 * - Renders often with same props
 * - Component is expensive to render
 * - Medium to large component
 * 
 * ❌ Don't use when:
 * - Props change frequently
 * - Component is simple/cheap to render
 * - Premature optimization
 */

// Good use case
const ProductList = memo(({ products }) => {
  return (
    <div>
      {products.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
});

// Bad use case (too simple)
const SimpleText = memo(({ text }) => <p>{text}</p>);
```

### 3. useMemo - Memoizing Expensive Calculations

```javascript
/**
 * useMemo - Memoize expensive computations
 * Only recalculates when dependencies change
 * 
 * Syntax: useMemo(() => computation, [dependencies])
 */

import { useMemo, useState } from 'react';

function DataDashboard({ data }) {
  const [filter, setFilter] = useState('all');
  const [sortOrder, setSortOrder] = useState('asc');
  
  // ❌ Without useMemo - recalculates on every render
  const expensiveCalculation = () => {
    console.log('Calculating...');
    return data
      .filter(item => filter === 'all' || item.category === filter)
      .sort((a, b) => sortOrder === 'asc' ? a.value - b.value : b.value - a.value)
      .reduce((acc, item) => acc + item.value, 0);
  };
  
  // ✅ With useMemo - only recalculates when data, filter, or sortOrder change
  const total = useMemo(() => {
    console.log('Calculating total...');
    return data
      .filter(item => filter === 'all' || item.category === filter)
      .sort((a, b) => sortOrder === 'asc' ? a.value - b.value : b.value - a.value)
      .reduce((acc, item) => acc + item.value, 0);
  }, [data, filter, sortOrder]);
  
  return (
    <div>
      <select value={filter} onChange={(e) => setFilter(e.target.value)}>
        <option value="all">All</option>
        <option value="sales">Sales</option>
        <option value="marketing">Marketing</option>
      </select>
      
      <button onClick={() => setSortOrder(order => order === 'asc' ? 'desc' : 'asc')}>
        Toggle Sort
      </button>
      
      <h2>Total: ${total}</h2>
    </div>
  );
}

/**
 * COMMON USE CASES
 */

// Filtering and sorting large lists
function ProductList({ products, searchQuery }) {
  const filteredProducts = useMemo(() => {
    return products.filter(product =>
      product.name.toLowerCase().includes(searchQuery.toLowerCase())
    );
  }, [products, searchQuery]);
  
  return (
    <ul>
      {filteredProducts.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}

// Expensive object creation
function Chart({ data, config }) {
  const chartData = useMemo(() => {
    return {
      labels: data.map(d => d.label),
      datasets: [{
        label: 'Sales',
        data: data.map(d => d.value),
        backgroundColor: config.colors
      }]
    };
  }, [data, config.colors]);
  
  return <ChartComponent data={chartData} />;
}

// Referential equality for props
function Parent() {
  const [count, setCount] = useState(0);
  
  // ❌ New object on every render
  const config = { theme: 'dark', size: 'large' };
  
  // ✅ Same object reference unless theme/size change
  const configMemo = useMemo(
    () => ({ theme: 'dark', size: 'large' }),
    [] // dependencies
  );
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Re-render</button>
      <MemoizedChild config={configMemo} />
    </div>
  );
}

/**
 * WHEN TO USE useMemo
 * 
 * ✅ Use when:
 * - Expensive calculations
 * - Preventing child re-renders (referential equality)
 * - Filtering/sorting large arrays
 * - Complex object transformations
 * 
 * ❌ Don't use when:
 * - Simple calculations (overhead > benefit)
 * - Premature optimization
 * - Every single computation
 */
```

### 4. useCallback - Memoizing Functions

```javascript
/**
 * useCallback - Memoize function references
 * Prevents creating new function on every render
 * 
 * Syntax: useCallback(() => { ... }, [dependencies])
 */

import { useCallback, useState } from 'react';

function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState('all');
  
  // ❌ New function on every render
  const handleDelete = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };
  
  // ✅ Same function reference unless todos change
  const handleDeleteMemo = useCallback((id) => {
    setTodos(todos => todos.filter(todo => todo.id !== id));
  }, []); // Empty deps because we use functional update
  
  const handleToggle = useCallback((id) => {
    setTodos(todos => todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  }, []);
  
  const handleAdd = useCallback((text) => {
    setTodos(todos => [
      ...todos,
      { id: Date.now(), text, completed: false }
    ]);
  }, []);
  
  return (
    <div>
      <AddTodoForm onAdd={handleAdd} />
      <TodoList
        todos={todos}
        onToggle={handleToggle}
        onDelete={handleDeleteMemo}
      />
    </div>
  );
}

// Child component with memo
const TodoList = memo(({ todos, onToggle, onDelete }) => {
  console.log('TodoList rendered');
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={onToggle}
          onDelete={onDelete}
        />
      ))}
    </ul>
  );
});

const TodoItem = memo(({ todo, onToggle, onDelete }) => {
  console.log('TodoItem rendered:', todo.id);
  return (
    <li>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => onToggle(todo.id)}
      />
      <span>{todo.text}</span>
      <button onClick={() => onDelete(todo.id)}>Delete</button>
    </li>
  );
});

/**
 * useCallback with useMemo
 */

function SearchableList({ items }) {
  const [query, setQuery] = useState('');
  
  // Memoize filtered items
  const filteredItems = useMemo(() => {
    return items.filter(item =>
      item.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [items, query]);
  
  // Memoize search handler
  const handleSearch = useCallback((e) => {
    setQuery(e.target.value);
  }, []);
  
  // Memoize item click handler
  const handleItemClick = useCallback((id) => {
    console.log('Item clicked:', id);
  }, []);
  
  return (
    <div>
      <input value={query} onChange={handleSearch} placeholder="Search..." />
      <ItemList items={filteredItems} onItemClick={handleItemClick} />
    </div>
  );
}

/**
 * WHEN TO USE useCallback
 * 
 * ✅ Use when:
 * - Passing callbacks to memoized child components
 * - Function is a dependency of useEffect/useMemo
 * - Creating event handlers for lists
 * - Optimizing context values
 * 
 * ❌ Don't use when:
 * - Function is not passed as prop
 * - Child component is not memoized
 * - Premature optimization
 */
```

### 5. Code Splitting and Lazy Loading

```javascript
/**
 * CODE SPLITTING - Load code when needed
 * Reduces initial bundle size
 * Improves initial load time
 */

import { lazy, Suspense, useState } from 'react';

// Static import - loaded immediately
import Header from './Header';

// Dynamic import - loaded when needed
const Dashboard = lazy(() => import('./Dashboard'));
const Profile = lazy(() => import('./Profile'));
const Settings = lazy(() => import('./Settings'));

function App() {
  return (
    <div>
      <Header />
      
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
          <Route path="/settings" element={<Settings />} />
        </Routes>
      </Suspense>
    </div>
  );
}

/**
 * COMPONENT-BASED CODE SPLITTING
 */

function TabPanel() {
  const [activeTab, setActiveTab] = useState('home');
  
  // Lazy load tab components
  const HomeTab = lazy(() => import('./tabs/HomeTab'));
  const AnalyticsTab = lazy(() => import('./tabs/AnalyticsTab'));
  const ReportsTab = lazy(() => import('./tabs/ReportsTab'));
  
  const renderTab = () => {
    switch (activeTab) {
      case 'home':
        return <HomeTab />;
      case 'analytics':
        return <AnalyticsTab />;
      case 'reports':
        return <ReportsTab />;
      default:
        return <HomeTab />;
    }
  };
  
  return (
    <div>
      <nav>
        <button onClick={() => setActiveTab('home')}>Home</button>
        <button onClick={() => setActiveTab('analytics')}>Analytics</button>
        <button onClick={() => setActiveTab('reports')}>Reports</button>
      </nav>
      
      <Suspense fallback={<div className="spinner">Loading tab...</div>}>
        {renderTab()}
      </Suspense>
    </div>
  );
}

/**
 * PRELOADING COMPONENTS
 * Load before user interaction
 */

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function Navigation() {
  // Preload on hover
  const preloadHeavyComponent = () => {
    import('./HeavyComponent');
  };
  
  return (
    <nav>
      <Link to="/home">Home</Link>
      <Link
        to="/heavy"
        onMouseEnter={preloadHeavyComponent}
      >
        Heavy Page
      </Link>
    </nav>
  );
}

/**
 * RETRY LOGIC FOR FAILED CHUNKS
 */

function lazyWithRetry(componentImport) {
  return new Promise((resolve, reject) => {
    const hasRefreshed = JSON.parse(
      window.sessionStorage.getItem('retry-lazy-refreshed') || 'false'
    );
    
    componentImport()
      .then((component) => {
        window.sessionStorage.setItem('retry-lazy-refreshed', 'false');
        resolve(component);
      })
      .catch((error) => {
        if (!hasRefreshed) {
          window.sessionStorage.setItem('retry-lazy-refreshed', 'true');
          return window.location.reload();
        }
        reject(error);
      });
  });
}

const Dashboard = lazy(() => lazyWithRetry(() => import('./Dashboard')));
```

### 6. List Virtualization

```javascript
/**
 * VIRTUALIZATION - Render only visible items
 * For large lists (1000+ items)
 * 
 * Library: react-window or react-virtualized
 * Installation: npm install react-window
 */

import { FixedSizeList } from 'react-window';

// Without virtualization - renders all 10,000 items
function SlowList({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index} style={{ height: 50 }}>
          {item.name}
        </li>
      ))}
    </ul>
  );
}

// With virtualization - renders only visible items
function FastList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );
  
  return (
    <FixedSizeList
      height={600}        // Height of scrollable area
      itemCount={items.length}  // Total number of items
      itemSize={50}       // Height of each item
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}

/**
 * VARIABLE SIZE LIST
 */

import { VariableSizeList } from 'react-window';

function VariableList({ items }) {
  const getItemSize = (index) => {
    // Dynamic height based on content
    return items[index].description ? 80 : 50;
  };
  
  const Row = ({ index, style }) => (
    <div style={style}>
      <h4>{items[index].title}</h4>
      {items[index].description && <p>{items[index].description}</p>}
    </div>
  );
  
  return (
    <VariableSizeList
      height={600}
      itemCount={items.length}
      itemSize={getItemSize}
      width="100%"
    >
      {Row}
    </VariableSizeList>
  );
}

/**
 * INFINITE SCROLL WITH VIRTUALIZATION
 */

function InfiniteList() {
  const [items, setItems] = useState(Array.from({ length: 50 }));
  const [hasMore, setHasMore] = useState(true);
  
  const loadMore = () => {
    if (items.length >= 1000) {
      setHasMore(false);
      return;
    }
    
    setTimeout(() => {
      setItems(prev => [...prev, ...Array.from({ length: 50 })]);
    }, 1000);
  };
  
  return (
    <InfiniteLoader
      isItemLoaded={index => index < items.length}
      itemCount={hasMore ? items.length + 1 : items.length}
      loadMoreItems={loadMore}
    >
      {({ onItemsRendered, ref }) => (
        <FixedSizeList
          height={600}
          itemCount={items.length}
          itemSize={50}
          onItemsRendered={onItemsRendered}
          ref={ref}
          width="100%"
        >
          {Row}
        </FixedSizeList>
      )}
    </InfiniteLoader>
  );
}
```

### 7. Performance Profiling

```javascript
/**
 * REACT DEVTOOLS PROFILER
 * 
 * 1. Install React DevTools browser extension
 * 2. Open DevTools > Profiler tab
 * 3. Click record button
 * 4. Interact with app
 * 5. Stop recording
 * 6. Analyze flame graph and ranked chart
 * 
 * Metrics:
 * - Commit duration
 * - Render duration
 * - Which components re-rendered
 * - Why components re-rendered
 */

/**
 * PROFILER API - Programmatic profiling
 */

import { Profiler } from 'react';

function onRenderCallback(
  id,        // Component identifier
  phase,     // "mount" or "update"
  actualDuration,  // Time spent rendering
  baseDuration,    // Estimated time without memoization
  startTime,
  commitTime,
  interactions  // Set of interactions
) {
  console.log(`${id} (${phase}) took ${actualDuration}ms`);
  
  // Send to analytics
  // analytics.track('component-render', {
  //   component: id,
  //   duration: actualDuration,
  //   phase
  // });
}

function App() {
  return (
    <Profiler id="App" onRender={onRenderCallback}>
      <Header />
      <Profiler id="Dashboard" onRender={onRenderCallback}>
        <Dashboard />
      </Profiler>
      <Footer />
    </Profiler>
  );
}

/**
 * PERFORMANCE METRICS
 */

function measurePerformance() {
  // Time to First Byte (TTFB)
  const ttfb = performance.timing.responseStart - performance.timing.requestStart;
  
  // DOM Content Loaded
  const dcl = performance.timing.domContentLoadedEventEnd - performance.timing.navigationStart;
  
  // Load Complete
  const loadComplete = performance.timing.loadEventEnd - performance.timing.navigationStart;
  
  console.log('TTFB:', ttfb);
  console.log('DOM Content Loaded:', dcl);
  console.log('Load Complete:', loadComplete);
}

// Web Vitals
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

getCLS(console.log);  // Cumulative Layout Shift
getFID(console.log);  // First Input Delay
getFCP(console.log);  // First Contentful Paint
getLCP(console.log);  // Largest Contentful Paint
getTTFB(console.log); // Time to First Byte
```

### 8. React Best Practices

```javascript
/**
 * COMPONENT STRUCTURE BEST PRACTICES
 */

// ✅ Good: Single responsibility
function UserProfile({ userId }) {
  const { user, loading, error } = useUser(userId);
  
  if (loading) return <Loading />;
  if (error) return <Error message={error} />;
  if (!user) return <NotFound />;
  
  return (
    <div>
      <UserHeader user={user} />
      <UserDetails user={user} />
      <UserActivity user={user} />
    </div>
  );
}

// ❌ Bad: Too many responsibilities
function UserProfile({ userId }) {
  // Fetching logic
  // Rendering logic
  // Business logic
  // All in one component (500+ lines)
}

/**
 * STATE MANAGEMENT BEST PRACTICES
 */

// ✅ Good: Lift state to appropriate level
function ShoppingCart() {
  const [items, setItems] = useState([]);
  
  return (
    <>
      <ProductList onAddToCart={(item) => setItems([...items, item])} />
      <Cart items={items} onRemove={(id) => setItems(items.filter(i => i.id !== id))} />
    </>
  );
}

// ❌ Bad: State duplication
function ProductList() {
  const [cart, setCart] = useState([]);  // Duplicated
}

function Cart() {
  const [items, setItems] = useState([]);  // Duplicated
}

/**
 * PROP DRILLING SOLUTIONS
 */

// ✅ Good: Use Context for deeply nested data
const UserContext = createContext();

function App() {
  const [user, setUser] = useState(null);
  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Dashboard />
    </UserContext.Provider>
  );
}

// ❌ Bad: Prop drilling through many levels
<Level1 user={user}>
  <Level2 user={user}>
    <Level3 user={user}>
      <Level4 user={user}>
        <UserProfile user={user} />

/**
 * ERROR HANDLING
 */

// ✅ Good: Graceful error handling
function DataComponent() {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetchData()
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, []);
  
  if (loading) return <Spinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!data) return <NoData />;
  
  return <DataDisplay data={data} />;
}

// ❌ Bad: No error handling
function DataComponent() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetchData().then(setData);  // What if this fails?
  }, []);
  
  return <div>{data.value}</div>;  // Crashes if data is null
}

/**
 * HOOKS BEST PRACTICES
 */

// ✅ Good: Custom hooks for reusable logic
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);
  
  return { user, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { user, loading, error } = useUser(userId);
  // Clean and reusable
}

// ❌ Bad: Repeated logic in every component
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  useEffect(() => { /* fetch logic */ }, [userId]);
  // Repeat in every component
}

/**
 * KEY PROP USAGE
 */

// ✅ Good: Stable, unique keys
<ul>
  {users.map(user => (
    <li key={user.id}>{user.name}</li>
  ))}
</ul>

// ❌ Bad: Index as key (when order can change)
<ul>
  {users.map((user, index) => (
    <li key={index}>{user.name}</li>
  ))}
</ul>

/**
 * CONDITIONAL RENDERING
 */

// ✅ Good: Early returns for cleaner code
function UserProfile({ user }) {
  if (!user) return <NotFound />;
  if (user.isBlocked) return <Blocked />;
  
  return <Profile user={user} />;
}

// ❌ Bad: Nested ternaries
function UserProfile({ user }) {
  return (
    <>
      {user ? (
        user.isBlocked ? (
          <Blocked />
        ) : (
          <Profile user={user} />
        )
      ) : (
        <NotFound />
      )}
    </>
  );
}

/**
 * FOLDER STRUCTURE
 */

/*
src/
├── components/
│   ├── common/           # Reusable components
│   │   ├── Button/
│   │   ├── Input/
│   │   └── Modal/
│   └── features/         # Feature-specific components
│       ├── auth/
│       ├── dashboard/
│       └── profile/
├── hooks/                # Custom hooks
│   ├── useAuth.js
│   ├── useFetch.js
│   └── useLocalStorage.js
├── context/              # Context providers
│   ├── AuthContext.jsx
│   └── ThemeContext.jsx
├── utils/                # Helper functions
│   ├── validators.js
│   └── formatters.js
├── services/             # API services
│   └── api.js
├── styles/               # Global styles
│   └── global.css
├── App.jsx
└── main.jsx
*/
```

## 💡 Key Concepts

1. **Optimize Wisely**: Profile first, optimize bottlenecks, avoid premature optimization

2. **Memoization**: Use React.memo, useMemo, useCallback judiciously

3. **Code Splitting**: Load code when needed, reduce initial bundle size

4. **Virtualization**: Render only visible items for large lists

5. **Component Design**: Single responsibility, proper abstraction, reusability

6. **State Management**: Keep state close to where it's used, lift when needed

7. **Error Boundaries**: Gracefully handle errors, prevent app crashes

## 🛠️ Practical Exercises

### Exercise 1: Optimize Component
Profile and optimize a slow component with unnecessary re-renders.

### Exercise 2: Implement Memoization
Add React.memo, useMemo, and useCallback to optimize a todo app.

### Exercise 3: Code Splitting
Implement route-based code splitting with lazy loading.

### Exercise 4: Virtualize List
Create virtualized list displaying 10,000+ items.

### Exercise 5: Custom Hooks
Extract common logic into reusable custom hooks.

### Exercise 6: Error Boundaries
Add error boundaries to different app sections.

### Exercise 7: Performance Audit
Use React DevTools Profiler to identify and fix performance issues.

## 🎨 Project: High-Performance Dashboard

Build an optimized dashboard application:

**Features:**
1. Multiple widgets with real-time data
2. Large data tables with virtualization
3. Charts and visualizations
4. Search and filtering
5. User preferences (theme, layout)
6. Notifications system

**Performance Requirements:**
- Code splitting for all major sections
- Memoized components and calculations
- Virtualized lists for large datasets
- Lazy loaded modals and dialogs
- Optimized re-renders (< 16ms)
- Bundle size < 200KB (gzipped)
- Lighthouse score > 90

**Technical Implementation:**
- Use React.memo for all presentational components
- Implement useMemo for expensive calculations
- Use useCallback for event handlers
- Add route-based code splitting
- Implement virtualization for tables
- Create custom hooks for data fetching
- Add error boundaries
- Implement loading states
- Use Context for global state
- Profile with React DevTools

## 📖 Resources to Explore

### Official Documentation
- [Optimizing Performance](https://react.dev/learn/render-and-commit)
- [React.memo](https://react.dev/reference/react/memo)
- [useMemo](https://react.dev/reference/react/useMemo)
- [useCallback](https://react.dev/reference/react/useCallback)
- [Code Splitting](https://react.dev/reference/react/lazy)

### Tools
- React DevTools Profiler
- Lighthouse
- Web Vitals
- webpack-bundle-analyzer
- React Window

### Articles
- "Optimizing React Performance" - React docs
- "Before You memo()" by Dan Abramov
- "React Performance Best Practices"
- "Web Vitals" by Google

## 🔍 Interview Questions

1. **When would you use React.memo?**
2. **What's the difference between useMemo and useCallback?**
3. **How do you identify performance bottlenecks in React?**
4. **What is code splitting and why is it important?**
5. **Explain the virtual DOM and reconciliation.**
6. **What causes unnecessary re-renders in React?**
7. **How do you optimize a large list in React?**
8. **What are the best practices for using keys?**
9. **How do you profile React applications?**
10. **What is lazy loading in React?**
11. **When should you NOT use useMemo?**
12. **How do you prevent prop drilling?**
13. **What are React best practices for folder structure?**
14. **How do you handle errors in React?**
15. **What optimization techniques do you know?**

## ✅ Success Criteria

- [ ] Understand React rendering behavior
- [ ] Use React.memo effectively
- [ ] Implement useMemo and useCallback appropriately
- [ ] Set up code splitting with lazy loading
- [ ] Implement list virtualization
- [ ] Profile applications with React DevTools
- [ ] Create custom hooks for reusable logic
- [ ] Implement error boundaries
- [ ] Follow React best practices
- [ ] Complete high-performance dashboard project
- [ ] Answer 12/15 interview questions correctly
- [ ] Optimize app to Lighthouse score > 90

## 🚀 Next Steps

**Week 3 Complete!** You've mastered:
- React fundamentals and JSX
- Components and props
- Hooks (useState, useEffect, useContext)
- State management (Redux/Context)
- React Router
- Forms and validation
- Performance optimization

**Next Week (Week 4):**
- Modern CSS (Tailwind, CSS-in-JS)
- Build tools and bundlers
- Testing React applications
- TypeScript with React
- Advanced patterns
- Real-world projects

**Continue Learning:**
- Build more projects
- Contribute to open source
- Read React source code
- Follow React core team
- Join React communities

---

**Congratulations!** You've completed Week 3 of your 30-Day Frontend Mastery journey. You're now proficient in React, the most popular frontend framework. Keep building, keep learning! 🚀
