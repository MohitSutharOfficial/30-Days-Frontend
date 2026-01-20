# Day 15: React Fundamentals & JSX

## 🎯 Learning Objectives

By the end of Day 15, you will:
- Understand React's component-based architecture and virtual DOM
- Master JSX syntax and embedding expressions
- Create functional components using modern React patterns
- Comprehend React's declarative programming paradigm
- Set up a React development environment with Create React App and Vite
- Understand the difference between React elements and components
- Learn React's reconciliation algorithm and rendering behavior

## 📚 Topics to Study

### 1. What is React?

React is a declarative, component-based JavaScript library for building user interfaces. Key characteristics:

```javascript
/**
 * REACT CORE CONCEPTS
 * 
 * 1. Component-Based Architecture
 *    - UI is broken into reusable, independent pieces
 *    - Each component manages its own state and logic
 * 
 * 2. Virtual DOM
 *    - In-memory representation of real DOM
 *    - React calculates minimal changes needed
 *    - Efficient batch updates to actual DOM
 * 
 * 3. Declarative Programming
 *    - Describe WHAT the UI should look like
 *    - React handles HOW to update it
 * 
 * 4. Unidirectional Data Flow
 *    - Data flows down from parent to child
 *    - Predictable state management
 */
```

### 2. Setting Up React Environment

```bash
# Using Create React App (CRA)
npx create-react-app my-app
cd my-app
npm start

# Using Vite (Modern, Faster Alternative)
npm create vite@latest my-react-app -- --template react
cd my-react-app
npm install
npm run dev

# Project Structure
# my-app/
# ├── node_modules/
# ├── public/
# │   └── index.html          # HTML template
# ├── src/
# │   ├── App.jsx             # Root component
# │   ├── main.jsx            # Entry point
# │   └── index.css           # Global styles
# ├── package.json
# └── vite.config.js
```

### 3. JSX Fundamentals

```javascript
/**
 * JSX - JavaScript XML
 * A syntax extension that looks like HTML but is JavaScript
 */

// Basic JSX Element
const element = <h1>Hello, React!</h1>;

// JSX with Expressions
const name = 'Sarah';
const greeting = <h1>Hello, {name}!</h1>;

// JSX with JavaScript Expressions
const user = {
  firstName: 'John',
  lastName: 'Doe'
};

const fullName = (
  <h1>
    Welcome, {user.firstName} {user.lastName}!
  </h1>
);

// JSX with Conditional Rendering
const isLoggedIn = true;
const dashboard = (
  <div>
    {isLoggedIn ? <h1>Welcome Back!</h1> : <h1>Please Log In</h1>}
  </div>
);

// JSX with Arrays/Lists
const numbers = [1, 2, 3, 4, 5];
const listItems = (
  <ul>
    {numbers.map((number) => (
      <li key={number}>{number}</li>
    ))}
  </ul>
);

// JSX Attributes (camelCase)
const image = (
  <img 
    src="profile.jpg" 
    alt="Profile" 
    className="profile-image"  // class → className
    tabIndex="0"                // tabindex → tabIndex
  />
);

// JSX with Inline Styles (JavaScript object)
const styledDiv = (
  <div style={{
    backgroundColor: '#f0f0f0',
    padding: '20px',
    borderRadius: '8px'
  }}>
    Styled Content
  </div>
);

// JSX Must Return Single Parent Element
// WRONG:
// return (
//   <h1>Title</h1>
//   <p>Paragraph</p>
// );

// CORRECT - Using Fragment:
return (
  <>
    <h1>Title</h1>
    <p>Paragraph</p>
  </>
);
```

### 4. Functional Components

```javascript
/**
 * MODERN REACT: Functional Components
 * Recommended approach since React 16.8 (Hooks)
 */

// Simple Functional Component
function Welcome() {
  return <h1>Hello, World!</h1>;
}

// Arrow Function Component
const Greeting = () => {
  return <h1>Welcome to React</h1>;
};

// Component with Props
function UserCard(props) {
  return (
    <div className="user-card">
      <h2>{props.name}</h2>
      <p>Email: {props.email}</p>
      <p>Role: {props.role}</p>
    </div>
  );
}

// Component with Destructured Props
const ProductCard = ({ title, price, inStock }) => {
  return (
    <div className="product-card">
      <h3>{title}</h3>
      <p className="price">${price}</p>
      {inStock ? (
        <span className="badge success">In Stock</span>
      ) : (
        <span className="badge danger">Out of Stock</span>
      )}
    </div>
  );
};

// Component with Default Props
function Button({ text = 'Click Me', onClick = () => {} }) {
  return (
    <button onClick={onClick} className="btn">
      {text}
    </button>
  );
}

// Component with Children Prop
const Card = ({ children, title }) => {
  return (
    <div className="card">
      <h2 className="card-title">{title}</h2>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
};

// Usage:
// <Card title="My Card">
//   <p>This is the card content</p>
//   <button>Action</button>
// </Card>
```

### 5. React Elements vs Components

```javascript
/**
 * REACT ELEMENTS
 * - Plain objects describing what to render
 * - Immutable
 * - Created by JSX or React.createElement()
 */

// JSX creates React elements
const element = <h1>Hello</h1>;

// Equivalent to:
const elementObject = React.createElement(
  'h1',
  null,
  'Hello'
);

/**
 * REACT COMPONENTS
 * - Functions or classes that return React elements
 * - Reusable
 * - Can accept props and manage state
 */

// Component (Function that returns elements)
function Header() {
  return <h1>Hello</h1>;
}

// Using the component creates an element
const headerElement = <Header />;
```

### 6. Rendering and Composition

```javascript
/**
 * ROOT RENDERING
 * Entry point for React application
 */

// main.jsx or index.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

/**
 * COMPONENT COMPOSITION
 * Building complex UIs from smaller components
 */

// Small, focused components
const Logo = () => <img src="/logo.svg" alt="Logo" />;

const Navigation = () => (
  <nav>
    <a href="#home">Home</a>
    <a href="#about">About</a>
    <a href="#contact">Contact</a>
  </nav>
);

const UserMenu = ({ username }) => (
  <div className="user-menu">
    <span>Welcome, {username}</span>
    <button>Logout</button>
  </div>
);

// Composed larger component
const Header = ({ username }) => (
  <header className="app-header">
    <Logo />
    <Navigation />
    <UserMenu username={username} />
  </header>
);

// Main App composition
const App = () => (
  <div className="app">
    <Header username="John Doe" />
    <main>
      <h1>Main Content</h1>
    </main>
    <footer>© 2024 My App</footer>
  </div>
);
```

## 💡 Key Concepts

1. **Virtual DOM**: React maintains a lightweight copy of the DOM in memory, calculating the most efficient way to update the actual DOM

2. **JSX Compilation**: JSX is transpiled to `React.createElement()` calls by Babel

3. **Component Naming**: Components must start with capital letters (PascalCase) to distinguish from HTML elements

4. **Fragments**: Use `<>...</>` or `<React.Fragment>` to group elements without adding extra DOM nodes

5. **Keys in Lists**: Always provide unique `key` prop when rendering arrays to help React identify changes

6. **Immutability**: React relies on immutable data patterns for efficient change detection

7. **One-Way Data Flow**: Data flows from parent to child components via props

## 🛠️ Practical Exercises

### Exercise 1: Create Your First Component
Create a `Profile` component that displays user information with name, bio, and avatar.

### Exercise 2: JSX Expressions
Build a `Clock` component that displays current time and greeting based on time of day.

### Exercise 3: Conditional Rendering
Create a `StatusBadge` component that shows different colors and text based on user status (online/offline/away).

### Exercise 4: List Rendering
Build a `TodoList` component that renders an array of todo items with unique keys.

### Exercise 5: Component Composition
Create a `Dashboard` component composed of smaller components: `Header`, `Sidebar`, `MainContent`, and `Footer`.

### Exercise 6: Event Handling
Build a `Counter` component with increment/decrement buttons (use inline handlers for now).

### Exercise 7: Styling Components
Create a `Card` component with inline styles and className props for custom styling.

## 🎨 Project: Personal Portfolio Landing Page

Build a single-page React portfolio with the following components:

**Requirements:**
- `Header` component with navigation links
- `Hero` section with name, title, and call-to-action
- `About` section with bio and skills list
- `Projects` section displaying project cards (array of 3+ projects)
- `Contact` section with social media links
- `Footer` component with copyright info

**Technical Specifications:**
- Use functional components only
- Implement proper JSX syntax with expressions
- Use component composition effectively
- Apply conditional rendering where appropriate
- Use array mapping for repeated elements (skills, projects)
- Include inline styles and className attributes
- Ensure all list items have unique keys

**Bonus Challenges:**
- Add a theme toggle button (light/dark mode preview)
- Implement smooth scroll navigation
- Create reusable `Button` and `Card` components
- Add hover effects using inline styles

## 📖 Resources to Explore

### Official Documentation
- [React Official Docs](https://react.dev) - New React documentation
- [JSX In Depth](https://react.dev/learn/writing-markup-with-jsx) - Comprehensive JSX guide
- [Create React App](https://create-react-app.dev/) - Official CRA documentation
- [Vite Guide](https://vitejs.dev/guide/) - Modern build tool

### Video Tutorials
- React in 100 Seconds by Fireship
- React Tutorial for Beginners by Codevolution
- Modern React Crash Course by Traversy Media

### Articles & Guides
- "Thinking in React" - React.dev
- "React JSX Deep Dive" - Medium
- "Virtual DOM and Internals" - React Blog

### Tools
- [React DevTools](https://react.dev/learn/react-developer-tools) - Browser extension
- [Babel REPL](https://babeljs.io/repl) - See JSX compilation
- [CodeSandbox](https://codesandbox.io/s/react) - Online React playground

## 🔍 Interview Questions

### Conceptual Questions

1. **What is React and what problems does it solve?**
   - Component reusability, efficient DOM updates via Virtual DOM, declarative UI, large ecosystem

2. **Explain the Virtual DOM and how React uses it.**
   - Lightweight copy of real DOM, diffing algorithm, batch updates, performance optimization

3. **What is JSX? Is it required to use React?**
   - JavaScript XML syntax extension, syntactic sugar for React.createElement(), not required but recommended

4. **What are the differences between React elements and React components?**
   - Elements are immutable objects, components are functions/classes that return elements

5. **Why must component names start with capital letters?**
   - React distinguishes components from HTML elements; lowercase = HTML, uppercase = component

### Technical Questions

6. **What's the difference between `class` and `className` in JSX?**
   - JSX uses camelCase for attributes; `class` is reserved JavaScript keyword

7. **Can you embed any JavaScript expression in JSX?**
   - Yes, using curly braces {}, but only expressions (not statements like if/for)

8. **What is a Fragment and why would you use it?**
   - Grouping element without extra DOM node, cleaner markup, better performance

9. **Why do we need keys when rendering lists in React?**
   - Help React identify changes, additions, removals efficiently; improve reconciliation performance

10. **What is React.StrictMode?**
    - Development-only wrapper highlighting potential problems, double-renders components intentionally

### Coding Questions

11. **Write a component that conditionally renders content based on props.**

12. **Create a component that maps over an array and renders list items with keys.**

13. **What's wrong with this code?**
    ```jsx
    function Component() {
      return (
        <h1>Title</h1>
        <p>Description</p>
      )
    }
    ```

14. **Convert this to JSX:**
    ```javascript
    React.createElement('div', {className: 'container'}, 
      React.createElement('h1', null, 'Hello')
    )
    ```

15. **How would you conditionally apply a CSS class in JSX?**
    ```jsx
    <div className={isActive ? 'active' : 'inactive'}>
    ```

## ✅ Success Criteria

- [ ] Successfully set up React development environment (CRA or Vite)
- [ ] Create multiple functional components with proper syntax
- [ ] Write valid JSX with embedded JavaScript expressions
- [ ] Implement conditional rendering in components
- [ ] Render lists from arrays with unique keys
- [ ] Compose complex UIs from smaller components
- [ ] Apply both inline styles and CSS classes
- [ ] Use Fragments to group elements efficiently
- [ ] Understand props flow from parent to child
- [ ] Complete the portfolio landing page project
- [ ] Can explain Virtual DOM and React's rendering process
- [ ] Answer at least 10/15 interview questions correctly

## 🚀 Next Steps

Tomorrow (Day 16), you'll dive deeper into:
- **React Components & Props**: Props validation, prop types, children prop patterns
- **Component Communication**: Parent-child data flow, callback props
- **Prop Drilling**: Understanding the challenge and basic solutions
- **Component Patterns**: Presentational vs Container components

### Preparation
- Review JavaScript destructuring and spread operators
- Practice component composition
- Understand function parameters and callbacks
- Read about one-way data flow in React

---

**Remember**: React is all about breaking down complex UIs into simple, reusable components. Think in components, not pages!
