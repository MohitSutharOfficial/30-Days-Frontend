# Day 19: React Router & Navigation

## 🎯 Learning Objectives

By the end of Day 19, you will:
- Implement client-side routing with React Router v6
- Create dynamic routes with URL parameters
- Implement nested routes and layouts
- Handle navigation programmatically
- Create protected/private routes with authentication
- Implement route-based code splitting and lazy loading
- Use route hooks (useParams, useNavigate, useLocation, useSearchParams)
- Handle 404 pages and redirects
- Implement active navigation links
- Understand browser history and navigation patterns

## 📚 Topics to Study

### 1. Introduction to React Router

```javascript
/**
 * REACT ROUTER - Declarative routing for React applications
 * 
 * Installation:
 * npm install react-router-dom
 * 
 * Core Concepts:
 * - BrowserRouter: Uses HTML5 history API
 * - Routes: Container for Route definitions
 * - Route: Defines path-to-component mapping
 * - Link: Navigation without page reload
 * - Navigate: Programmatic navigation component
 */

import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

// Basic Setup
function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>
      
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </BrowserRouter>
  );
}

function Home() {
  return <h1>Home Page</h1>;
}

function About() {
  return <h1>About Page</h1>;
}

function Contact() {
  return <h1>Contact Page</h1>;
}

/**
 * ROUTE TYPES
 */

function AppRoutes() {
  return (
    <Routes>
      {/* Exact path */}
      <Route path="/" element={<Home />} />
      
      {/* Simple path */}
      <Route path="/about" element={<About />} />
      
      {/* Dynamic route with parameter */}
      <Route path="/users/:id" element={<UserProfile />} />
      
      {/* Multiple parameters */}
      <Route path="/posts/:category/:id" element={<Post />} />
      
      {/* Optional parameter */}
      <Route path="/search/:query?" element={<Search />} />
      
      {/* Wildcard - catch all */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}

function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>
      <Link to="/">Go Home</Link>
    </div>
  );
}
```

### 2. Route Parameters and Query Strings

```javascript
/**
 * URL PARAMETERS - Dynamic values in route path
 */

import { useParams, useSearchParams, useLocation } from 'react-router-dom';

// Route: /users/:userId
function UserProfile() {
  const { userId } = useParams();
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(setUser);
  }, [userId]);
  
  if (!user) return <div>Loading...</div>;
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
      <p>User ID: {userId}</p>
    </div>
  );
}

// Route: /posts/:category/:postId
function Post() {
  const { category, postId } = useParams();
  
  return (
    <div>
      <h1>Post {postId}</h1>
      <p>Category: {category}</p>
    </div>
  );
}

/**
 * QUERY PARAMETERS (Search Params)
 */

// URL: /search?q=react&sort=latest&page=1
function SearchResults() {
  const [searchParams, setSearchParams] = useSearchParams();
  const [results, setResults] = useState([]);
  
  const query = searchParams.get('q');
  const sort = searchParams.get('sort') || 'relevant';
  const page = searchParams.get('page') || '1';
  
  useEffect(() => {
    // Fetch results based on params
    fetch(`/api/search?q=${query}&sort=${sort}&page=${page}`)
      .then(res => res.json())
      .then(setResults);
  }, [query, sort, page]);
  
  const handleSortChange = (newSort) => {
    // Update only the sort parameter
    setSearchParams({ q: query, sort: newSort, page: '1' });
  };
  
  const handlePageChange = (newPage) => {
    setSearchParams({ q: query, sort, page: newPage });
  };
  
  return (
    <div>
      <h1>Search Results for: {query}</h1>
      
      <select value={sort} onChange={(e) => handleSortChange(e.target.value)}>
        <option value="relevant">Most Relevant</option>
        <option value="latest">Latest</option>
        <option value="popular">Most Popular</option>
      </select>
      
      <ul>
        {results.map(result => (
          <li key={result.id}>{result.title}</li>
        ))}
      </ul>
      
      <button onClick={() => handlePageChange(String(Number(page) - 1))}>
        Previous
      </button>
      <span>Page {page}</span>
      <button onClick={() => handlePageChange(String(Number(page) + 1))}>
        Next
      </button>
    </div>
  );
}

/**
 * LOCATION HOOK - Access full location object
 */

function LocationInfo() {
  const location = useLocation();
  
  console.log(location.pathname);  // '/search'
  console.log(location.search);    // '?q=react'
  console.log(location.hash);      // '#results'
  console.log(location.state);     // Passed via navigate
  console.log(location.key);       // Unique identifier
  
  return (
    <div>
      <p>Current Path: {location.pathname}</p>
      <p>Query String: {location.search}</p>
    </div>
  );
}
```

### 3. Programmatic Navigation

```javascript
/**
 * useNavigate - Navigate programmatically
 */

import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();
  const [credentials, setCredentials] = useState({ email: '', password: '' });
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(credentials)
      });
      
      const data = await response.json();
      
      if (response.ok) {
        localStorage.setItem('token', data.token);
        // Navigate to dashboard after successful login
        navigate('/dashboard');
      } else {
        alert('Login failed');
      }
    } catch (error) {
      console.error('Login error:', error);
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={credentials.email}
        onChange={(e) => setCredentials({ ...credentials, email: e.target.value })}
      />
      <input
        type="password"
        value={credentials.password}
        onChange={(e) => setCredentials({ ...credentials, password: e.target.value })}
      />
      <button type="submit">Login</button>
    </form>
  );
}

/**
 * NAVIGATION OPTIONS
 */

function NavigationExamples() {
  const navigate = useNavigate();
  
  // Basic navigation
  const goToAbout = () => {
    navigate('/about');
  };
  
  // Navigate with replace (no history entry)
  const replaceRoute = () => {
    navigate('/new-page', { replace: true });
  };
  
  // Navigate with state
  const navigateWithState = () => {
    navigate('/profile', { 
      state: { from: 'dashboard', userId: 123 }
    });
  };
  
  // Go back
  const goBack = () => {
    navigate(-1);
  };
  
  // Go forward
  const goForward = () => {
    navigate(1);
  };
  
  // Go back 2 pages
  const goBack2 = () => {
    navigate(-2);
  };
  
  return (
    <div>
      <button onClick={goToAbout}>Go to About</button>
      <button onClick={replaceRoute}>Replace Route</button>
      <button onClick={navigateWithState}>Navigate with State</button>
      <button onClick={goBack}>Go Back</button>
      <button onClick={goForward}>Go Forward</button>
    </div>
  );
}

// Accessing passed state
function ProfilePage() {
  const location = useLocation();
  const { from, userId } = location.state || {};
  
  return (
    <div>
      <h1>Profile</h1>
      {from && <p>Navigated from: {from}</p>}
      {userId && <p>User ID: {userId}</p>}
    </div>
  );
}

/**
 * REDIRECT COMPONENT
 */

import { Navigate } from 'react-router-dom';

function OldProfilePage() {
  // Redirect old route to new route
  return <Navigate to="/profile/new" replace />;
}

function ConditionalRedirect() {
  const isAuthenticated = checkAuth();
  
  // Redirect if not authenticated
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  return <div>Protected Content</div>;
}
```

### 4. Nested Routes and Layouts

```javascript
/**
 * NESTED ROUTES - Routes within routes
 * Outlet - Placeholder for child routes
 */

import { Outlet } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<Home />} />
          <Route path="about" element={<About />} />
          <Route path="contact" element={<Contact />} />
        </Route>
        
        <Route path="/dashboard" element={<DashboardLayout />}>
          <Route index element={<DashboardHome />} />
          <Route path="profile" element={<Profile />} />
          <Route path="settings" element={<Settings />} />
          <Route path="analytics" element={<Analytics />} />
        </Route>
        
        <Route path="/products" element={<ProductsLayout />}>
          <Route index element={<ProductList />} />
          <Route path=":productId" element={<ProductDetail />} />
          <Route path="new" element={<NewProduct />} />
          <Route path=":productId/edit" element={<EditProduct />} />
        </Route>
        
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

// Main Layout Component
function Layout() {
  return (
    <div className="app-container">
      <header>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/about">About</Link>
          <Link to="/contact">Contact</Link>
          <Link to="/dashboard">Dashboard</Link>
        </nav>
      </header>
      
      <main>
        {/* Child routes render here */}
        <Outlet />
      </main>
      
      <footer>
        <p>© 2024 My App</p>
      </footer>
    </div>
  );
}

// Dashboard Layout with Sidebar
function DashboardLayout() {
  return (
    <div className="dashboard">
      <aside className="sidebar">
        <nav>
          <Link to="/dashboard">Dashboard Home</Link>
          <Link to="/dashboard/profile">Profile</Link>
          <Link to="/dashboard/settings">Settings</Link>
          <Link to="/dashboard/analytics">Analytics</Link>
        </nav>
      </aside>
      
      <div className="dashboard-content">
        <Outlet />
      </div>
    </div>
  );
}

// Products Layout
function ProductsLayout() {
  return (
    <div className="products-page">
      <h1>Products</h1>
      <nav>
        <Link to="/products">All Products</Link>
        <Link to="/products/new">Add Product</Link>
      </nav>
      <Outlet />
    </div>
  );
}

/**
 * INDEX ROUTES
 * Default child route when parent path matches exactly
 */

// Route: /dashboard → renders DashboardHome
// Route: /dashboard/profile → renders Profile
<Route path="/dashboard" element={<DashboardLayout />}>
  <Route index element={<DashboardHome />} />
  <Route path="profile" element={<Profile />} />
</Route>
```

### 5. Protected Routes

```javascript
/**
 * PROTECTED/PRIVATE ROUTES
 * Restrict access based on authentication
 */

import { Navigate, Outlet } from 'react-router-dom';

// Protected Route Component
function ProtectedRoute({ children }) {
  const isAuthenticated = checkAuth(); // Your auth check logic
  
  if (!isAuthenticated) {
    // Redirect to login if not authenticated
    return <Navigate to="/login" replace />;
  }
  
  return children ? children : <Outlet />;
}

// With Role-Based Access
function ProtectedRoute({ allowedRoles }) {
  const { user, isAuthenticated } = useAuth();
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  if (allowedRoles && !allowedRoles.includes(user.role)) {
    return <Navigate to="/unauthorized" replace />;
  }
  
  return <Outlet />;
}

// Usage in Routes
function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* Public routes */}
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        
        {/* Protected routes */}
        <Route element={<ProtectedRoute />}>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
        </Route>
        
        {/* Admin only routes */}
        <Route element={<ProtectedRoute allowedRoles={['admin']} />}>
          <Route path="/admin" element={<AdminPanel />} />
          <Route path="/admin/users" element={<UserManagement />} />
        </Route>
        
        <Route path="/unauthorized" element={<Unauthorized />} />
      </Routes>
    </BrowserRouter>
  );
}

/**
 * REDIRECT AFTER LOGIN
 */

function Login() {
  const navigate = useNavigate();
  const location = useLocation();
  
  const from = location.state?.from?.pathname || '/dashboard';
  
  const handleLogin = async (credentials) => {
    const success = await login(credentials);
    if (success) {
      navigate(from, { replace: true });
    }
  };
  
  return <LoginForm onSubmit={handleLogin} />;
}

// In ProtectedRoute, save original location
function ProtectedRoute() {
  const location = useLocation();
  const isAuthenticated = checkAuth();
  
  if (!isAuthenticated) {
    // Save where user was trying to go
    return <Navigate to="/login" state={{ from: location }} replace />;
  }
  
  return <Outlet />;
}
```

### 6. Active Links and Navigation

```javascript
/**
 * NavLink - Link with active state styling
 */

import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      {/*ClassName as string */}
      <NavLink
        to="/"
        className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}
      >
        Home
      </NavLink>
      
      {/* Style object */}
      <NavLink
        to="/about"
        style={({ isActive }) => ({
          color: isActive ? 'red' : 'black',
          fontWeight: isActive ? 'bold' : 'normal'
        })}
      >
        About
      </NavLink>
      
      {/* With end prop - only active when exact match */}
      <NavLink
        to="/products"
        className={({ isActive }) => isActive ? 'active' : ''}
        end
      >
        Products
      </NavLink>
    </nav>
  );
}

// Sidebar with Active Links
function Sidebar() {
  const navItems = [
    { path: '/dashboard', label: 'Dashboard', icon: '📊' },
    { path: '/dashboard/profile', label: 'Profile', icon: '👤' },
    { path: '/dashboard/settings', label: 'Settings', icon: '⚙️' },
    { path: '/dashboard/analytics', label: 'Analytics', icon: '📈' }
  ];
  
  return (
    <aside className="sidebar">
      {navItems.map(item => (
        <NavLink
          key={item.path}
          to={item.path}
          className={({ isActive }) => 
            `sidebar-link ${isActive ? 'sidebar-link-active' : ''}`
          }
        >
          <span className="icon">{item.icon}</span>
          <span className="label">{item.label}</span>
        </NavLink>
      ))}
    </aside>
  );
}
```

### 7. Route-Based Code Splitting

```javascript
/**
 * LAZY LOADING - Load components only when needed
 * Improves initial load time
 */

import { lazy, Suspense } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Lazy load components
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Products = lazy(() => import('./pages/Products'));
const ProductDetail = lazy(() => import('./pages/ProductDetail'));

// Loading component
function LoadingSpinner() {
  return (
    <div className="loading-spinner">
      <div className="spinner"></div>
      <p>Loading...</p>
    </div>
  );
}

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<LoadingSpinner />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/products" element={<Products />} />
          <Route path="/products/:id" element={<ProductDetail />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

/**
 * PRELOADING ROUTES
 * Load route before navigation for better UX
 */

const AboutPage = lazy(() => import('./pages/About'));

function Navigation() {
  const preloadAbout = () => {
    // Preload on hover
    import('./pages/About');
  };
  
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about" onMouseEnter={preloadAbout}>
        About
      </Link>
    </nav>
  );
}
```

### 8. Advanced Patterns

```javascript
/**
 * BREADCRUMBS
 */

function Breadcrumbs() {
  const location = useLocation();
  const pathnames = location.pathname.split('/').filter(x => x);
  
  return (
    <nav className="breadcrumbs">
      <Link to="/">Home</Link>
      {pathnames.map((name, index) => {
        const routeTo = `/${pathnames.slice(0, index + 1).join('/')}`;
        const isLast = index === pathnames.length - 1;
        
        return (
          <span key={routeTo}>
            {' / '}
            {isLast ? (
              <span>{name}</span>
            ) : (
              <Link to={routeTo}>{name}</Link>
            )}
          </span>
        );
      })}
    </nav>
  );
}

/**
 * SCROLL TO TOP ON ROUTE CHANGE
 */

function ScrollToTop() {
  const { pathname } = useLocation();
  
  useEffect(() => {
    window.scrollTo(0, 0);
  }, [pathname]);
  
  return null;
}

// Usage
function App() {
  return (
    <BrowserRouter>
      <ScrollToTop />
      <Routes>
        {/* routes */}
      </Routes>
    </BrowserRouter>
  );
}

/**
 * ROUTE GUARDS
 */

function RouteGuard({ condition, redirectTo, children }) {
  if (!condition) {
    return <Navigate to={redirectTo} replace />;
  }
  return children;
}

// Usage
<Route 
  path="/admin" 
  element={
    <RouteGuard condition={isAdmin} redirectTo="/unauthorized">
      <AdminPanel />
    </RouteGuard>
  } 
/>

/**
 * MODAL ROUTES
 * Background location pattern
 */

function App() {
  const location = useLocation();
  const background = location.state?.background;
  
  return (
    <>
      <Routes location={background || location}>
        <Route path="/" element={<Home />} />
        <Route path="/gallery" element={<Gallery />} />
      </Routes>
      
      {background && (
        <Routes>
          <Route path="/gallery/:id" element={<ImageModal />} />
        </Routes>
      )}
    </>
  );
}

function Gallery() {
  const location = useLocation();
  
  return (
    <div>
      {images.map(image => (
        <Link
          key={image.id}
          to={`/gallery/${image.id}`}
          state={{ background: location }}
        >
          <img src={image.thumbnail} alt={image.title} />
        </Link>
      ))}
    </div>
  );
}
```

## 💡 Key Concepts

1. **Client-Side Routing**: Navigation without page reload using history API

2. **Route Matching**: React Router matches routes from most to least specific

3. **Nested Routes**: Compose complex layouts with parent-child route relationships

4. **URL Parameters**: Dynamic route segments for data identification

5. **Navigation State**: Pass data between routes using location state

6. **Code Splitting**: Load route components only when needed

7. **Protected Routes**: Guard routes based on authentication/authorization

## 🛠️ Practical Exercises

### Exercise 1: Basic Routing
Create app with Home, About, Contact pages and navigation.

### Exercise 2: Dynamic Routes
Build blog with post list and individual post pages using URL parameters.

### Exercise 3: Nested Routes
Create dashboard with sidebar navigation and nested routes.

### Exercise 4: Protected Routes
Implement authentication flow with login and protected dashboard.

### Exercise 5: Search with Query Params
Build search page that uses query parameters for filtering.

### Exercise 6: 404 Handling
Create custom 404 page with navigation back to home.

### Exercise 7: Code Splitting
Implement lazy loading for all route components.

## 🎨 Project: Multi-Page Blog Application

Build a complete blog application with routing:

**Pages:**
1. Home - List of recent posts
2. Post List - Paginated posts with filters
3. Post Detail - Individual post view
4. Author Profile - Author info and their posts
5. Category - Posts filtered by category
6. Search Results - Search functionality
7. Admin Dashboard - Protected routes
8. Login/Register - Authentication pages

**Features:**
- Nested routes for admin section
- Dynamic routes for posts and authors
- Query parameters for search and filters
- Protected admin routes
- Active navigation highlighting
- Breadcrumb navigation
- 404 error page
- Route-based code splitting
- Scroll to top on navigation

**Technical Requirements:**
- Use React Router v6
- Implement all route hooks
- Create layout components with Outlet
- Use NavLink for active states
- Implement ProtectedRoute component
- Add lazy loading for route components
- Handle loading and error states
- Persist auth state across refreshes

## 📖 Resources to Explore

### Official Documentation
- [React Router Official Docs](https://reactrouter.com/)
- [React Router Tutorial](https://reactrouter.com/en/main/start/tutorial)
- [Migration Guide](https://reactrouter.com/en/main/upgrading/v5) - v5 to v6

### Articles
- "React Router Tutorial" - Official guide
- "Protected Routes in React Router"
- "Lazy Loading Routes in React"
- "React Router Best Practices"

### Videos
- "React Router v6 Tutorial" by Web Dev Simplified
- "React Router Crash Course" by Traversy Media
- "Advanced React Router Patterns"

## 🔍 Interview Questions

1. **What is React Router and why is it needed?**
2. **What's the difference between BrowserRouter and HashRouter?**
3. **Explain the difference between Link and NavLink.**
4. **What is the purpose of the Outlet component?**
5. **How do you access URL parameters in React Router?**
6. **What's the difference between useNavigate and Navigate component?**
7. **How do you implement protected routes?**
8. **What are nested routes and when would you use them?**
9. **How do you handle 404 pages in React Router?**
10. **What's the difference between replace and push navigation?**
11. **How do you implement route-based code splitting?**
12. **What are query parameters and how do you access them?**
13. **How do you pass state between routes?**
14. **What is the purpose of the index route?**
15. **How do you programmatically navigate in React Router v6?**

## ✅ Success Criteria

- [ ] Set up React Router with multiple routes
- [ ] Implement dynamic routes with parameters
- [ ] Create nested routes with layouts
- [ ] Build protected routes with authentication
- [ ] Use all major route hooks (useParams, useNavigate, etc.)
- [ ] Implement active navigation with NavLink
- [ ] Add query parameter handling
- [ ] Create 404 error page
- [ ] Implement route-based code splitting
- [ ] Complete multi-page blog project
- [ ] Answer 12/15 interview questions correctly

## 🚀 Next Steps

Tomorrow (Day 20), you'll learn:
- **Forms in React**: Controlled vs uncontrolled components
- **Form Validation**: Client-side validation patterns
- **Error Handling**: Form errors, API errors, error boundaries
- **Form Libraries**: React Hook Form, Formik

### Preparation
- Review HTML form elements
- Practice form validation with JavaScript
- Understand controlled components
- Read about error boundaries in React

---

**Remember**: React Router makes building SPAs feel like multi-page apps. Master routing, and you'll build better user experiences!
