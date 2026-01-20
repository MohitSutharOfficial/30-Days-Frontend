# Day 16: React Components & Props

## 🎯 Learning Objectives

By the end of Day 16, you will:
- Master props as the primary method of passing data between components
- Implement prop validation using PropTypes and TypeScript
- Understand the `children` prop and composition patterns
- Learn component communication patterns (parent-to-child, child-to-parent)
- Recognize and address prop drilling challenges
- Differentiate between presentational and container components
- Implement default props and prop destructuring effectively
- Create reusable, configurable components

## 📚 Topics to Study

### 1. Understanding Props

```javascript
/**
 * PROPS (Properties)
 * - Read-only data passed from parent to child
 * - Immutable within the component
 * - Enable component reusability and configuration
 * - Flow unidirectionally (one-way data flow)
 */

// Basic Props Usage
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Usage: <Greeting name="Alice" />

// Props Destructuring (Preferred)
function UserCard({ name, email, role, avatarUrl }) {
  return (
    <div className="user-card">
      <img src={avatarUrl} alt={name} />
      <h2>{name}</h2>
      <p>{email}</p>
      <span className="role">{role}</span>
    </div>
  );
}

// Nested Object Props
function Address({ address }) {
  return (
    <div>
      <p>{address.street}</p>
      <p>{address.city}, {address.state} {address.zip}</p>
    </div>
  );
}

// Destructuring Nested Props
function Profile({ user: { name, email, address } }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
      <p>{address.city}</p>
    </div>
  );
}

// Array Props
function TagList({ tags }) {
  return (
    <ul className="tags">
      {tags.map((tag, index) => (
        <li key={index} className="tag">{tag}</li>
      ))}
    </ul>
  );
}

// Usage: <TagList tags={['React', 'JavaScript', 'Web Dev']} />

// Function Props (Callbacks)
function Button({ label, onClick, variant = 'primary' }) {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {label}
    </button>
  );
}

// Usage:
// <Button 
//   label="Submit" 
//   onClick={() => console.log('Clicked!')}
//   variant="success"
// />
```

### 2. Default Props

```javascript
/**
 * DEFAULT PROPS
 * Fallback values when props are not provided
 */

// Modern Approach: Default Parameters
function Card({ 
  title = 'Untitled',
  description = 'No description available',
  imageUrl = '/default-image.png',
  isActive = false 
}) {
  return (
    <div className={`card ${isActive ? 'active' : ''}`}>
      <img src={imageUrl} alt={title} />
      <h3>{title}</h3>
      <p>{description}</p>
    </div>
  );
}

// Legacy Approach: defaultProps (still valid)
function Alert({ message, type }) {
  return (
    <div className={`alert alert-${type}`}>
      {message}
    </div>
  );
}

Alert.defaultProps = {
  message: 'Alert message',
  type: 'info'
};

// Conditional Default with Nullish Coalescing
function Product({ name, price, discount }) {
  const finalPrice = price - (discount ?? 0);
  
  return (
    <div className="product">
      <h3>{name}</h3>
      <p className="price">${finalPrice}</p>
      {discount && <span className="discount">-${discount}</span>}
    </div>
  );
}
```

### 3. Children Prop

```javascript
/**
 * CHILDREN PROP
 * Special prop for content between component tags
 * Enables powerful composition patterns
 */

// Basic Children Usage
function Container({ children }) {
  return (
    <div className="container">
      {children}
    </div>
  );
}

// Usage:
// <Container>
//   <h1>Title</h1>
//   <p>Content</p>
// </Container>

// Card with Children
function Card({ title, children, footer }) {
  return (
    <div className="card">
      <div className="card-header">
        <h2>{title}</h2>
      </div>
      <div className="card-body">
        {children}
      </div>
      {footer && (
        <div className="card-footer">
          {footer}
        </div>
      )}
    </div>
  );
}

// Usage:
// <Card 
//   title="User Profile" 
//   footer={<button>Edit</button>}
// >
//   <p>Name: John Doe</p>
//   <p>Email: john@example.com</p>
// </Card>

// Modal Component Pattern
function Modal({ isOpen, onClose, title, children }) {
  if (!isOpen) return null;
  
  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        <div className="modal-header">
          <h2>{title}</h2>
          <button onClick={onClose}>×</button>
        </div>
        <div className="modal-body">
          {children}
        </div>
      </div>
    </div>
  );
}

// Layout Component Pattern
function PageLayout({ sidebar, children }) {
  return (
    <div className="page-layout">
      <aside className="sidebar">
        {sidebar}
      </aside>
      <main className="main-content">
        {children}
      </main>
    </div>
  );
}

// Multiple Children Slots Pattern
function Dialog({ header, children, actions }) {
  return (
    <div className="dialog">
      <div className="dialog-header">{header}</div>
      <div className="dialog-content">{children}</div>
      <div className="dialog-actions">{actions}</div>
    </div>
  );
}
```

### 4. PropTypes Validation

```javascript
/**
 * PROP TYPES
 * Runtime type checking for props
 * Development-only warnings
 */

import PropTypes from 'prop-types';

// Basic PropTypes
function UserProfile({ name, age, email, isAdmin }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
      {isAdmin && <span className="badge">Admin</span>}
    </div>
  );
}

UserProfile.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired,
  email: PropTypes.string.isRequired,
  isAdmin: PropTypes.bool
};

// Complex PropTypes
function Product({ product, onAddToCart, category }) {
  return (
    <div className="product">
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <button onClick={() => onAddToCart(product.id)}>
        Add to Cart
      </button>
    </div>
  );
}

Product.propTypes = {
  product: PropTypes.shape({
    id: PropTypes.string.isRequired,
    name: PropTypes.string.isRequired,
    price: PropTypes.number.isRequired,
    inStock: PropTypes.bool
  }).isRequired,
  onAddToCart: PropTypes.func.isRequired,
  category: PropTypes.oneOf(['electronics', 'clothing', 'books'])
};

// Array PropTypes
function CommentList({ comments }) {
  return (
    <ul>
      {comments.map(comment => (
        <li key={comment.id}>{comment.text}</li>
      ))}
    </ul>
  );
}

CommentList.propTypes = {
  comments: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.number.isRequired,
      text: PropTypes.string.isRequired,
      author: PropTypes.string
    })
  ).isRequired
};

// Custom Validators
function RatingStars({ rating }) {
  return <div>{'⭐'.repeat(rating)}</div>;
}

RatingStars.propTypes = {
  rating: function(props, propName, componentName) {
    const value = props[propName];
    if (value < 1 || value > 5) {
      return new Error(
        `Invalid prop ${propName} of value ${value} supplied to ${componentName}. ` +
        'Rating must be between 1 and 5.'
      );
    }
  }
};
```

### 5. Component Communication Patterns

```javascript
/**
 * PARENT TO CHILD: Via Props
 */

function ParentComponent() {
  const userData = {
    name: 'Alice Johnson',
    email: 'alice@example.com'
  };
  
  return (
    <div>
      <ChildComponent user={userData} />
    </div>
  );
}

function ChildComponent({ user }) {
  return <p>{user.name} - {user.email}</p>;
}

/**
 * CHILD TO PARENT: Via Callback Props
 */

function Parent() {
  const handleDataFromChild = (data) => {
    console.log('Received from child:', data);
  };
  
  return (
    <div>
      <Child onDataSend={handleDataFromChild} />
    </div>
  );
}

function Child({ onDataSend }) {
  const sendData = () => {
    onDataSend({ message: 'Hello from child!', timestamp: Date.now() });
  };
  
  return <button onClick={sendData}>Send Data to Parent</button>;
}

/**
 * LIFTING STATE UP
 * Sharing state between components by moving it to common parent
 */

function TemperatureConverter() {
  const [celsius, setCelsius] = React.useState('');
  
  const handleCelsiusChange = (value) => {
    setCelsius(value);
  };
  
  const handleFahrenheitChange = (value) => {
    setCelsius(((value - 32) * 5/9).toString());
  };
  
  const fahrenheit = celsius ? (celsius * 9/5 + 32).toFixed(2) : '';
  
  return (
    <div>
      <TemperatureInput 
        temperature={celsius}
        scale="Celsius"
        onTemperatureChange={handleCelsiusChange}
      />
      <TemperatureInput 
        temperature={fahrenheit}
        scale="Fahrenheit"
        onTemperatureChange={handleFahrenheitChange}
      />
    </div>
  );
}

function TemperatureInput({ temperature, scale, onTemperatureChange }) {
  return (
    <div>
      <label>Enter temperature in {scale}:</label>
      <input 
        value={temperature}
        onChange={(e) => onTemperatureChange(e.target.value)}
      />
    </div>
  );
}

/**
 * SIBLING COMMUNICATION
 * Via shared parent state
 */

function Dashboard() {
  const [selectedItem, setSelectedItem] = React.useState(null);
  
  return (
    <div className="dashboard">
      <Sidebar onItemSelect={setSelectedItem} />
      <MainContent selectedItem={selectedItem} />
    </div>
  );
}

function Sidebar({ onItemSelect }) {
  const items = ['Home', 'Profile', 'Settings'];
  
  return (
    <nav>
      {items.map(item => (
        <button key={item} onClick={() => onItemSelect(item)}>
          {item}
        </button>
      ))}
    </nav>
  );
}

function MainContent({ selectedItem }) {
  return (
    <main>
      <h1>{selectedItem || 'Select an item'}</h1>
    </main>
  );
}
```

### 6. Prop Drilling

```javascript
/**
 * PROP DRILLING PROBLEM
 * Passing props through multiple component layers
 * Components in the middle don't use the props
 */

// Problem Example
function App() {
  const user = { name: 'John', role: 'Admin' };
  
  return <Dashboard user={user} />;
}

function Dashboard({ user }) {
  // Dashboard doesn't use user, just passes it down
  return (
    <div>
      <Sidebar user={user} />
    </div>
  );
}

function Sidebar({ user }) {
  // Sidebar doesn't use user either
  return (
    <nav>
      <UserMenu user={user} />
    </nav>
  );
}

function UserMenu({ user }) {
  // Finally used here!
  return <div>Welcome, {user.name}</div>;
}

/**
 * SOLUTION 1: Component Composition
 * Pass components instead of data
 */

function App() {
  const user = { name: 'John', role: 'Admin' };
  
  return (
    <Dashboard>
      <Sidebar>
        <UserMenu user={user} />
      </Sidebar>
    </Dashboard>
  );
}

function Dashboard({ children }) {
  return <div className="dashboard">{children}</div>;
}

function Sidebar({ children }) {
  return <nav className="sidebar">{children}</nav>;
}

/**
 * SOLUTION 2: Context API (covered in Day 17)
 * SOLUTION 3: State Management (covered in Day 18)
 */
```

### 7. Presentational vs Container Components

```javascript
/**
 * PRESENTATIONAL COMPONENTS
 * - Focused on UI/appearance
 * - Receive data via props
 * - No state management (mostly)
 * - Reusable and testable
 */

// Presentational: Button
function Button({ children, variant, onClick, disabled }) {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
}

// Presentational: UserCard
function UserCard({ user, onEdit, onDelete }) {
  return (
    <div className="user-card">
      <img src={user.avatar} alt={user.name} />
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      <div className="actions">
        <Button variant="primary" onClick={onEdit}>Edit</Button>
        <Button variant="danger" onClick={onDelete}>Delete</Button>
      </div>
    </div>
  );
}

/**
 * CONTAINER COMPONENTS
 * - Handle logic and data
 * - Manage state
 * - Fetch data from APIs
 * - Pass data to presentational components
 */

// Container: UserCardContainer
function UserCardContainer({ userId }) {
  const [user, setUser] = React.useState(null);
  const [loading, setLoading] = React.useState(true);
  
  React.useEffect(() => {
    // Fetch user data
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);
  
  const handleEdit = () => {
    console.log('Editing user:', user.id);
    // Edit logic
  };
  
  const handleDelete = () => {
    console.log('Deleting user:', user.id);
    // Delete logic
  };
  
  if (loading) return <div>Loading...</div>;
  
  return (
    <UserCard 
      user={user}
      onEdit={handleEdit}
      onDelete={handleDelete}
    />
  );
}

// Container: TodoListContainer
function TodoListContainer() {
  const [todos, setTodos] = React.useState([]);
  const [filter, setFilter] = React.useState('all');
  
  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text, completed: false }]);
  };
  
  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };
  
  const filteredTodos = todos.filter(todo => {
    if (filter === 'active') return !todo.completed;
    if (filter === 'completed') return todo.completed;
    return true;
  });
  
  return (
    <TodoList 
      todos={filteredTodos}
      onToggle={toggleTodo}
      onAdd={addTodo}
      filter={filter}
      onFilterChange={setFilter}
    />
  );
}
```

## 💡 Key Concepts

1. **Props are Read-Only**: Never modify props directly; they should be treated as immutable

2. **Props Flow Downward**: Data flows from parent to child, creating a predictable data flow

3. **Composition over Inheritance**: React favors composition using the children prop and component nesting

4. **Prop Drilling**: Passing props through multiple layers can become cumbersome; solved by Context API or state management

5. **PropTypes for Safety**: Runtime validation helps catch bugs during development

6. **Callback Props**: Functions passed as props enable child-to-parent communication

7. **Separation of Concerns**: Container components handle logic, presentational components handle UI

## 🛠️ Practical Exercises

### Exercise 1: Reusable Components
Create `Button`, `Input`, and `Card` components that accept various props for customization.

### Exercise 2: Prop Validation
Add comprehensive PropTypes to all components from Exercise 1.

### Exercise 3: Children Prop Mastery
Build a `Tabs` component that accepts multiple `Tab` components as children.

### Exercise 4: Callback Props
Create a `SearchBar` component that sends search queries to parent component.

### Exercise 5: Lifting State Up
Build a `ShoppingCart` where `ProductList` and `Cart` share state through parent.

### Exercise 6: Component Composition
Create a `Modal` component that accepts header, content, and footer as props or children.

### Exercise 7: Container/Presentational Pattern
Refactor a component into separate container and presentational components.

## 🎨 Project: Reusable Component Library

Build a mini component library with reusable, configurable components:

**Components to Create:**

1. **Button Component**
   - Props: `variant` (primary, secondary, danger), `size` (small, medium, large), `disabled`, `onClick`, `children`
   - Default props for variant and size
   - PropTypes validation

2. **Card Component**
   - Props: `title`, `children`, `footer`, `image`, `imagePosition`
   - Support for header, body, and footer sections
   - Image placement options (top, left, right)

3. **Alert Component**
   - Props: `type` (success, warning, error, info), `message`, `onClose`, `dismissible`
   - Icon based on type
   - Optional close button

4. **Form Input Component**
   - Props: `label`, `type`, `value`, `onChange`, `error`, `placeholder`, `required`
   - Error message display
   - Validation state styling

5. **Modal Component**
   - Props: `isOpen`, `onClose`, `title`, `children`, `actions`
   - Backdrop click to close
   - Escape key to close

6. **Badge Component**
   - Props: `text`, `color`, `variant` (solid, outline), `size`

7. **Avatar Component**
   - Props: `src`, `name`, `size`, `fallbackText`
   - Fallback to initials if no image

**Demo Page:**
- Create `App.jsx` showcasing all components
- Multiple variants of each component
- Interactive examples (buttons that work, modals that open)

**Technical Requirements:**
- All components must have PropTypes
- Use default props where appropriate
- Implement proper children prop usage
- Create container components for stateful demos
- Use callback props for event handling
- Apply consistent styling (CSS modules or styled components)

## 📖 Resources to Explore

### Official Documentation
- [Components and Props](https://react.dev/learn/passing-props-to-a-component) - React.dev
- [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html) - Type checking
- [Composition vs Inheritance](https://react.dev/learn/passing-props-to-a-component#passing-jsx-as-children)

### Articles
- "Presentational and Container Components" by Dan Abramov
- "React Component Patterns" - Medium
- "Avoiding Prop Drilling in React" - Blog posts
- "Advanced React Component Patterns" - Kent C. Dodds

### Libraries
- [PropTypes Package](https://www.npmjs.com/package/prop-types)
- [TypeScript](https://www.typescriptlang.org/) - Alternative to PropTypes
- [Storybook](https://storybook.js.org/) - Component development tool

### Component Libraries (for inspiration)
- Material-UI (MUI)
- Chakra UI
- Ant Design
- React Bootstrap

## 🔍 Interview Questions

### Conceptual Questions

1. **What are props in React?**
   - Immutable data passed from parent to child, enable component configuration and reusability

2. **Can you modify props inside a component?**
   - No, props are read-only; modifying them would break React's unidirectional data flow

3. **What is the children prop?**
   - Special prop containing content between component opening and closing tags

4. **Explain prop drilling and its drawbacks.**
   - Passing props through many layers of components; makes code harder to maintain and refactor

5. **What's the difference between props and state?**
   - Props are passed from parent (immutable), state is managed within component (mutable)

### Technical Questions

6. **How do you validate props in React?**
   - Using PropTypes library or TypeScript for type checking

7. **What are default props and how do you define them?**
   - Fallback values for props; use default parameters or Component.defaultProps

8. **How can a child component communicate with its parent?**
   - Through callback functions passed as props from parent to child

9. **What is the difference between presentational and container components?**
   - Presentational: UI-focused, receive props; Container: logic-focused, manage state

10. **What is "lifting state up"?**
    - Moving shared state to common parent component to enable sibling communication

### Coding Questions

11. **Create a component that accepts a render prop.**

12. **Write PropTypes validation for a complex nested object.**

13. **Implement a reusable Modal component using children prop.**

14. **Create a SearchBar component that debounces input and calls parent callback.**

15. **Refactor a component suffering from prop drilling using composition.**

## ✅ Success Criteria

- [ ] Create components that properly receive and use props
- [ ] Implement default props for component flexibility
- [ ] Use children prop for component composition
- [ ] Add PropTypes validation to all custom components
- [ ] Implement parent-child communication using callback props
- [ ] Lift state up to share data between sibling components
- [ ] Identify and refactor prop drilling situations
- [ ] Separate presentational and container component logic
- [ ] Build a library of reusable, configurable components
- [ ] Pass all PropTypes validations without console warnings
- [ ] Answer at least 12/15 interview questions correctly

## 🚀 Next Steps

Tomorrow (Day 17), you'll explore:
- **React Hooks**: useState, useEffect, useContext
- **State Management**: Managing component state with Hooks
- **Side Effects**: API calls, subscriptions, manual DOM manipulation
- **Context API**: Solving prop drilling with global state

### Preparation
- Review JavaScript closures and scope
- Understand asynchronous JavaScript (Promises, async/await)
- Practice array and object immutability patterns
- Read about React Hooks introduction

---

**Remember**: Props make components reusable and configurable. Think of them as function parameters for your UI components!
