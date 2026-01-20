# Day 10: DOM Manipulation & Events

## 🎯 Learning Objectives

By the end of today, you will understand:
- The Document Object Model (DOM) structure and API
- Modern DOM selection methods and best practices
- Creating, modifying, and removing DOM elements
- Event handling, bubbling, and delegation
- Browser events and user interactions
- DOM traversal and navigation techniques
- Performance optimization for DOM operations

## 📚 Topics to Study

### 1. Understanding the DOM

```javascript
// The DOM Tree Structure
/*
document (root)
  └── html
      ├── head
      │   ├── title
      │   └── meta
      └── body
          ├── header
          ├── main
          │   ├── section
          │   └── article
          └── footer
*/

// Window vs Document
// window - Global browser object
// document - DOM entry point
// window.document === document // true

// DOM Node Types
// 1 - Element node (HTML tags)
// 3 - Text node (text content)
// 8 - Comment node (comments)
// 9 - Document node (document itself)

// Checking node types
const element = document.querySelector('div');
// element.nodeType // 1 (Element)
// element.nodeName // 'DIV'
// element.nodeValue // null for elements
```

### 2. Selecting DOM Elements

```javascript
// Modern Selection Methods (Preferred)

// querySelector - Returns first match (CSS selector)
const header = document.querySelector('.header');
const mainNav = document.querySelector('#main-nav');
const firstButton = document.querySelector('button');
const complexSelect = document.querySelector('.container > .item:first-child');

// querySelectorAll - Returns NodeList of all matches
const allButtons = document.querySelectorAll('button');
const cards = document.querySelectorAll('.card');
const links = document.querySelectorAll('a[href^="https"]');

// NodeList to Array conversion
const buttonsArray = Array.from(allButtons);
const cardsArray = [...cards]; // Spread operator

// Legacy Methods (Still used, but querySelector is preferred)

// getElementById - Returns single element
const myElement = document.getElementById('my-element');

// getElementsByClassName - Returns HTMLCollection (live)
const items = document.getElementsByClassName('item');

// getElementsByTagName - Returns HTMLCollection (live)
const paragraphs = document.getElementsByTagName('p');

// HTMLCollection vs NodeList
// HTMLCollection - Live, updates automatically
// NodeList - Usually static (except from childNodes)

// Selecting within elements (scoped selection)
const container = document.querySelector('.container');
const innerButtons = container.querySelectorAll('button');

// Checking element existence
const element = document.querySelector('.might-not-exist');
if (element) {
  // Element exists, safe to manipulate
}
```

### 3. DOM Traversal

```javascript
// Parent, Child, Sibling Navigation

const element = document.querySelector('.target');

// Parent Navigation
// element.parentNode        // Direct parent (includes all node types)
// element.parentElement     // Parent element (elements only)
// element.closest('.class') // Nearest ancestor matching selector

// Child Navigation
// element.children          // HTMLCollection of child elements
// element.childNodes        // NodeList (includes text, comment nodes)
// element.firstElementChild // First child element
// element.lastElementChild  // Last child element
// element.firstChild        // First child node (any type)
// element.lastChild         // Last child node (any type)

// Sibling Navigation
// element.nextElementSibling     // Next element sibling
// element.previousElementSibling // Previous element sibling
// element.nextSibling            // Next node sibling
// element.previousSibling        // Previous node sibling

// Practical Traversal Examples

// Find all siblings
const getAllSiblings = (elem) => {
  const siblings = [];
  let sibling = elem.parentElement.firstElementChild;
  
  while (sibling) {
    if (sibling !== elem) {
      siblings.push(sibling);
    }
    sibling = sibling.nextElementSibling;
  }
  
  return siblings;
};

// Find all parents until condition
const getParentsUntil = (elem, selector) => {
  const parents = [];
  let parent = elem.parentElement;
  
  while (parent && !parent.matches(selector)) {
    parents.push(parent);
    parent = parent.parentElement;
  }
  
  return parents;
};
```

### 4. Creating and Modifying Elements

```javascript
// Creating Elements

// createElement - Create new element
const div = document.createElement('div');
const button = document.createElement('button');
const img = document.createElement('img');

// Setting Content

// textContent - Plain text (safe, no HTML parsing)
div.textContent = 'Hello World';

// innerHTML - HTML string (use cautiously, XSS risk)
div.innerHTML = '<strong>Bold text</strong>';

// innerText - Like textContent but considers CSS (slower)
div.innerText = 'Visible text';

// Setting Attributes

// setAttribute - Set any attribute
img.setAttribute('src', 'image.jpg');
img.setAttribute('alt', 'Description');

// Direct property access (preferred when available)
img.src = 'image.jpg';
img.alt = 'Description';
button.disabled = true;

// getAttribute - Get attribute value
const src = img.getAttribute('src');

// removeAttribute - Remove attribute
img.removeAttribute('alt');

// hasAttribute - Check if attribute exists
if (img.hasAttribute('src')) {
  // Has src attribute
}

// Data Attributes
// <div data-user-id="123" data-role="admin"></div>
const element = document.querySelector('[data-user-id]');
// element.dataset.userId // '123'
// element.dataset.role   // 'admin'

// Setting data attributes
element.dataset.status = 'active';
// Results in: data-status="active"

// Class Manipulation

// classList - Modern way (preferred)
div.classList.add('active');
div.classList.add('item', 'featured'); // Multiple classes
div.classList.remove('active');
div.classList.toggle('visible'); // Add if absent, remove if present
div.classList.replace('old-class', 'new-class');
div.classList.contains('active'); // true/false

// className - String of all classes (legacy)
div.className = 'item active featured';

// Style Manipulation

// Inline styles (use sparingly)
div.style.color = 'red';
div.style.backgroundColor = 'blue'; // camelCase for CSS properties
div.style.fontSize = '16px';

// Setting multiple styles
Object.assign(div.style, {
  width: '200px',
  height: '100px',
  border: '1px solid black'
});

// cssText - Set multiple styles as string
div.style.cssText = 'color: red; background: blue; font-size: 16px;';

// Getting computed styles
const styles = window.getComputedStyle(div);
const color = styles.color;
const fontSize = styles.fontSize;
```

### 5. Adding and Removing Elements

```javascript
// Inserting Elements

const parent = document.querySelector('.container');
const newElement = document.createElement('div');

// appendChild - Add as last child (moves if already in DOM)
parent.appendChild(newElement);

// append - More flexible, can append multiple items/text
parent.append(newElement, 'Some text', anotherElement);

// prepend - Add as first child
parent.prepend(newElement);

// insertBefore - Insert before reference node
const reference = document.querySelector('.reference');
parent.insertBefore(newElement, reference);

// Modern Insertion Methods

// before - Insert before element (as sibling)
reference.before(newElement);

// after - Insert after element (as sibling)
reference.after(newElement);

// replaceWith - Replace element
const oldElement = document.querySelector('.old');
oldElement.replaceWith(newElement);

// insertAdjacentHTML - Insert HTML at position
element.insertAdjacentHTML('beforebegin', '<div>Before</div>');
// 'beforebegin' - Before element
// 'afterbegin'  - First child
// 'beforeend'   - Last child
// 'afterend'    - After element

// insertAdjacentElement - Insert element at position
element.insertAdjacentElement('afterend', newElement);

// Removing Elements

// remove - Modern way (removes element)
element.remove();

// removeChild - Legacy way (from parent)
parent.removeChild(element);

// Removing all children
parent.innerHTML = ''; // Fast but loses event listeners
// OR
while (parent.firstChild) {
  parent.removeChild(parent.firstChild);
}
// OR (modern)
parent.replaceChildren(); // Removes all children

// Cloning Elements

// cloneNode - Create copy
const clone = element.cloneNode(); // Shallow (element only)
const deepClone = element.cloneNode(true); // Deep (with children)
```

### 6. Event Handling

```javascript
// Adding Event Listeners

const button = document.querySelector('button');

// addEventListener - Modern approach (preferred)
button.addEventListener('click', function(event) {
  console.log('Button clicked!');
  console.log(event); // Event object
});

// Arrow function
button.addEventListener('click', (event) => {
  console.log('Clicked with arrow function');
});

// Named function (better for removing listeners)
function handleClick(event) {
  console.log('Button clicked');
}
button.addEventListener('click', handleClick);

// Removing event listener (must use same function reference)
button.removeEventListener('click', handleClick);

// Event object properties
button.addEventListener('click', (event) => {
  // event.type           // 'click'
  // event.target         // Element that triggered event
  // event.currentTarget  // Element with listener attached
  // event.clientX        // Mouse X coordinate
  // event.clientY        // Mouse Y coordinate
  // event.preventDefault() // Prevent default action
  // event.stopPropagation() // Stop bubbling
});

// Common Event Types

// Mouse Events
element.addEventListener('click', handler);
element.addEventListener('dblclick', handler);
element.addEventListener('mousedown', handler);
element.addEventListener('mouseup', handler);
element.addEventListener('mousemove', handler);
element.addEventListener('mouseenter', handler);
element.addEventListener('mouseleave', handler);
element.addEventListener('mouseover', handler);
element.addEventListener('mouseout', handler);

// Keyboard Events
document.addEventListener('keydown', (e) => {
  // e.key     // 'a', 'Enter', 'ArrowUp', etc.
  // e.code    // 'KeyA', 'Enter', 'ArrowUp', etc.
  // e.ctrlKey // true if Ctrl pressed
  // e.shiftKey // true if Shift pressed
  // e.altKey   // true if Alt pressed
});
document.addEventListener('keyup', handler);
document.addEventListener('keypress', handler); // Deprecated

// Form Events
input.addEventListener('input', handler);   // Every change
input.addEventListener('change', handler);  // After blur/enter
form.addEventListener('submit', handler);
input.addEventListener('focus', handler);
input.addEventListener('blur', handler);

// Document Events
document.addEventListener('DOMContentLoaded', handler); // DOM ready
window.addEventListener('load', handler);     // All resources loaded
window.addEventListener('resize', handler);   // Window resized
window.addEventListener('scroll', handler);   // Page scrolled

// Event Options

// Once - Remove after first trigger
button.addEventListener('click', handler, { once: true });

// Passive - Improves scroll performance
window.addEventListener('scroll', handler, { passive: true });

// Capture - Use capture phase instead of bubbling
element.addEventListener('click', handler, { capture: true });
// OR (legacy boolean)
element.addEventListener('click', handler, true);
```

### 7. Event Bubbling and Delegation

```javascript
// Event Bubbling
// Events propagate from target up to document
/*
<div class="outer">
  <div class="inner">
    <button>Click Me</button>
  </div>
</div>
*/

// Clicking button triggers:
// 1. button click handler
// 2. inner div click handler (bubbles)
// 3. outer div click handler (bubbles)
// 4. body click handler (bubbles)
// 5. document click handler (bubbles)

// Stopping propagation
button.addEventListener('click', (event) => {
  event.stopPropagation(); // Stops bubbling
  // Event won't reach parent handlers
});

// Preventing default behavior
const link = document.querySelector('a');
link.addEventListener('click', (event) => {
  event.preventDefault(); // Doesn't navigate
  // Custom behavior instead
});

// Event Delegation Pattern
// Attach listener to parent, handle child events

// Instead of this (inefficient):
const buttons = document.querySelectorAll('.item button');
buttons.forEach(button => {
  button.addEventListener('click', handleButtonClick);
});

// Do this (efficient):
const container = document.querySelector('.container');
container.addEventListener('click', (event) => {
  // Check if clicked element matches selector
  if (event.target.matches('.item button')) {
    handleButtonClick(event);
  }
});

// Delegation with closest() - handles nested elements
container.addEventListener('click', (event) => {
  const button = event.target.closest('.item button');
  if (button) {
    // Handle button click
    console.log('Button in item clicked');
  }
});

// Practical delegation example
const list = document.querySelector('.todo-list');
list.addEventListener('click', (event) => {
  const deleteBtn = event.target.closest('.delete-btn');
  const completeBtn = event.target.closest('.complete-btn');
  
  if (deleteBtn) {
    const item = deleteBtn.closest('.todo-item');
    item.remove();
  }
  
  if (completeBtn) {
    const item = completeBtn.closest('.todo-item');
    item.classList.toggle('completed');
  }
});
```

### 8. DOM Performance Optimization

```javascript
// DocumentFragment - Build DOM off-screen
const fragment = document.createDocumentFragment();

for (let i = 0; i < 1000; i++) {
  const li = document.createElement('li');
  li.textContent = `Item ${i}`;
  fragment.appendChild(li); // Not triggering reflow yet
}

list.appendChild(fragment); // Single reflow

// Batch DOM updates
const container = document.querySelector('.container');

// BAD - Multiple reflows
for (let i = 0; i < 100; i++) {
  const div = document.createElement('div');
  container.appendChild(div); // Reflow on each append
}

// GOOD - Single reflow
const elements = [];
for (let i = 0; i < 100; i++) {
  elements.push(document.createElement('div'));
}
container.append(...elements);

// Read and write separately
// BAD - Thrashing (interleaved reads/writes)
const height1 = element1.offsetHeight; // Read (triggers layout)
element1.style.height = height1 + 10 + 'px'; // Write
const height2 = element2.offsetHeight; // Read (triggers layout)
element2.style.height = height2 + 10 + 'px'; // Write

// GOOD - Batch reads, then writes
const height1 = element1.offsetHeight; // Read
const height2 = element2.offsetHeight; // Read
element1.style.height = height1 + 10 + 'px'; // Write
element2.style.height = height2 + 10 + 'px'; // Write

// Debouncing expensive operations
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

const expensiveOperation = debounce(() => {
  // Heavy DOM manipulation
}, 300);

window.addEventListener('resize', expensiveOperation);

// Throttling
function throttle(func, limit) {
  let inThrottle;
  return function(...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

const handleScroll = throttle(() => {
  // Scroll handling logic
}, 100);

window.addEventListener('scroll', handleScroll);
```

## 💡 Key Concepts

### DOM Manipulation Best Practices

1. **Use Modern APIs**: querySelector over getElementById
2. **Cache Selectors**: Store references, don't query repeatedly
3. **Event Delegation**: Better performance for dynamic content
4. **Minimize Reflows**: Batch DOM updates
5. **Use DocumentFragment**: Build complex structures off-screen

### Event Handling Principles

- One listener on parent > many listeners on children
- Use named functions for removable listeners
- Prevent default cautiously (breaks expected behavior)
- Stop propagation sparingly (breaks delegation)

### Performance Considerations

- Reading layout properties triggers reflow
- Batch reads together, writes together
- Use CSS classes over inline styles
- Debounce/throttle frequent events
- Remove event listeners when not needed

### Common Pitfalls

- innerHTML with user input (XSS vulnerability)
- Not removing event listeners (memory leaks)
- Querying DOM inside loops
- Excessive inline styling
- Not checking if element exists before manipulating

## 🛠️ Practical Exercises

### Exercise 1: Element Manipulation
Create a script that:
- Selects elements using various methods
- Modifies content, attributes, and styles
- Adds, removes, and toggles classes
- Clones elements and inserts them
- Removes elements based on conditions

### Exercise 2: Dynamic List Builder
Build functionality to:
- Create list items dynamically
- Add items with input field
- Remove items individually
- Clear all items
- Filter/search items
- Sort items alphabetically

### Exercise 3: Event Handling Practice
Implement:
- Multiple event types on same element
- Event delegation for dynamic content
- Keyboard navigation (arrow keys)
- Form validation with real-time feedback
- Custom events and event dispatching

### Exercise 4: Interactive Gallery
Create an image gallery with:
- Thumbnail grid
- Lightbox on click
- Keyboard navigation (arrows, ESC)
- Click outside to close
- Smooth transitions
- Lazy loading images

### Exercise 5: Performance Optimization
Optimize:
- Rendering 1000+ list items efficiently
- Scroll event handling with throttle
- Window resize with debounce
- DOM measurement batching
- Memory leak prevention

## 🎨 Project: Interactive TODO Application

Build a fully functional TODO app with advanced DOM manipulation.

**Core Features:**
- Add new tasks with input field
- Mark tasks as complete (toggle)
- Edit task text inline
- Delete individual tasks
- Clear completed tasks
- Filter: All, Active, Completed
- Count remaining tasks
- Persist to localStorage

**DOM Operations Required:**
- Dynamic element creation
- Event delegation for all interactions
- Class toggling for states
- Inline editing with contenteditable
- Conditional rendering based on filters
- Real-time counter updates

**Event Handling:**
- Enter key to add task
- Click to toggle completion
- Double-click to edit
- ESC to cancel edit
- Filter button clicks
- Delete button clicks

**Advanced Features:**
- Drag and drop reordering
- Due dates with date picker
- Priority levels (high, medium, low)
- Categories/tags
- Bulk actions (select all, delete selected)
- Undo/redo functionality
- Search/filter by text
- Sort by different criteria

**Performance Requirements:**
- Handle 100+ tasks smoothly
- Debounced search
- Efficient re-rendering on filter
- No memory leaks
- Optimized event listeners

## 📖 Resources to Explore

### Documentation
- [MDN: DOM API](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)
- [MDN: Element API](https://developer.mozilla.org/en-US/docs/Web/API/Element)
- [MDN: Event Reference](https://developer.mozilla.org/en-US/docs/Web/Events)
- [MDN: EventTarget](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget)

### Essential Reading
- DOM Enlightenment by Cody Lindley
- JavaScript DOM Manipulation Master Class
- Event Bubbling and Delegation Explained
- High Performance JavaScript (Nicholas Zakas)

### Articles
- Understanding the DOM Tree and Nodes
- JavaScript Event Loop and Event Handling
- Best Practices for DOM Manipulation
- Optimizing JavaScript for Performance
- Event Delegation Pattern Explained

### Video Tutorials
- JavaScript DOM Crash Course
- Events and Event Listeners Deep Dive
- Building Projects with Vanilla JavaScript
- DOM Performance Optimization

### Interactive Learning
- JavaScript30 - DOM Projects
- freeCodeCamp - DOM Manipulation
- Scrimba - Interactive DOM Course
- Frontend Masters - JavaScript Path

## 🔍 Interview Questions

### Fundamental Questions

1. **What is the DOM? How is it different from HTML?**
   - Tree-like structure
   - API for manipulation
   - HTML is text, DOM is objects

2. **Explain event bubbling and capturing.**
   - Three phases: capture, target, bubble
   - Event propagation direction
   - stopPropagation()

3. **What's the difference between innerHTML and textContent?**
   - Security implications
   - Performance differences
   - When to use each

4. **What is event delegation and why use it?**
   - Efficiency with many elements
   - Works with dynamic content
   - Memory benefits

5. **Difference between querySelector and getElementById?**
   - Flexibility vs specificity
   - Performance considerations
   - Return types

### Practical Questions

6. **How would you select all elements with a specific data attribute?**

7. **Create a function to remove all child elements efficiently.**

8. **Implement event delegation for a dynamic list.**

9. **Write code to get all parents of an element.**

10. **How do you prevent default form submission?**

### Advanced Questions

11. **What causes browser reflow/repaint? How to minimize?**

12. **Explain the difference between target and currentTarget.**

13. **How do you create a custom event and dispatch it?**

14. **What are memory leaks in DOM manipulation? How to prevent?**

15. **How would you implement virtual scrolling for 10,000 items?**

## ✅ Success Criteria

By the end of Day 10, you should be able to:

- [ ] Select elements using modern methods
- [ ] Traverse DOM tree efficiently
- [ ] Create and modify elements
- [ ] Manipulate attributes, classes, and styles
- [ ] Add and remove elements from DOM
- [ ] Handle various event types
- [ ] Implement event delegation
- [ ] Understand event bubbling and capturing
- [ ] Optimize DOM operations for performance
- [ ] Prevent common security issues (XSS)
- [ ] Build interactive UI components
- [ ] Complete the TODO app project

## 🚀 Next Steps

Tomorrow (Day 11), you'll explore:
- Asynchronous JavaScript fundamentals
- Callbacks and callback hell
- Promises: creation and chaining
- Async/await syntax
- Error handling in async code
- Concurrent operations and Promise combinators

**Preparation:**
- Review synchronous vs asynchronous concepts
- Understand the JavaScript event loop
- Practice with setTimeout and setInterval

**Keep practicing** - DOM manipulation is the foundation of interactive web development!
