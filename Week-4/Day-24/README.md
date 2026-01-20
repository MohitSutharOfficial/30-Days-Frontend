# Day 24: Testing (Jest, React Testing Library) 🧪

## 🎯 Learning Objectives

By the end of Day 24, you will:
- Understand testing fundamentals and the testing pyramid
- Master Jest for unit testing JavaScript and TypeScript code
- Use React Testing Library for component testing
- Write effective tests for hooks, context, and async operations
- Implement test-driven development (TDD) practices
- Achieve meaningful test coverage without over-testing
- Mock dependencies, API calls, and external modules
- Debug and troubleshoot failing tests efficiently
- Apply best practices for maintainable test suites

---

## 📚 Topics to Study

### 1. **Testing Fundamentals**

```javascript
// The Testing Pyramid
/*
               /\
              /  \     E2E Tests (Few)
             /----\
            /      \   Integration Tests (Some)
           /--------\
          /          \ Unit Tests (Many)
         /____________\

Unit Tests: Test individual functions/components in isolation
Integration Tests: Test how multiple units work together
E2E Tests: Test entire user flows from start to finish
*/

// Test Structure: AAA Pattern
/*
Arrange: Set up test data and conditions
Act: Execute the code being tested
Assert: Verify the results
*/

// Example
function add(a, b) {
  return a + b;
}

// Test
test('add function adds two numbers', () => {
  // Arrange
  const num1 = 2;
  const num2 = 3;
  
  // Act
  const result = add(num1, num2);
  
  // Assert
  expect(result).toBe(5);
});
```

### 2. **Jest Basics**

```javascript
// Installation
// npm install --save-dev jest @types/jest
// npm install --save-dev ts-jest @testing-library/jest-dom

// jest.config.js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts'],
  moduleNameMapper: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
    '\\.(jpg|jpeg|png|gif|svg)$': '<rootDir>/__mocks__/fileMock.js'
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/index.tsx',
    '!src/reportWebVitals.ts'
  ],
  coverageThresholds: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};

// Basic Matchers
describe('Jest Matchers', () => {
  test('equality matchers', () => {
    expect(2 + 2).toBe(4);
    expect({ name: 'John' }).toEqual({ name: 'John' });
    expect({ name: 'John' }).not.toBe({ name: 'John' }); // Different references
  });
  
  test('truthiness', () => {
    expect(true).toBeTruthy();
    expect(false).toBeFalsy();
    expect(null).toBeNull();
    expect(undefined).toBeUndefined();
    expect('hello').toBeDefined();
  });
  
  test('numbers', () => {
    expect(5).toBeGreaterThan(3);
    expect(5).toBeGreaterThanOrEqual(5);
    expect(3).toBeLessThan(5);
    expect(0.1 + 0.2).toBeCloseTo(0.3); // Floating point
  });
  
  test('strings', () => {
    expect('team').not.toMatch(/I/);
    expect('hello world').toContain('world');
  });
  
  test('arrays and iterables', () => {
    const items = ['apple', 'banana', 'orange'];
    expect(items).toContain('banana');
    expect(items).toHaveLength(3);
  });
  
  test('exceptions', () => {
    function throwError() {
      throw new Error('Something went wrong');
    }
    expect(throwError).toThrow();
    expect(throwError).toThrow('Something went wrong');
  });
});

// Async Testing
describe('Async Tests', () => {
  // Promises
  test('async data fetch with promise', () => {
    return fetchData().then(data => {
      expect(data).toBe('peanut butter');
    });
  });
  
  // Async/Await
  test('async data fetch with async/await', async () => {
    const data = await fetchData();
    expect(data).toBe('peanut butter');
  });
  
  // Resolves/Rejects
  test('async resolves', async () => {
    await expect(fetchData()).resolves.toBe('peanut butter');
  });
  
  test('async rejects', async () => {
    await expect(fetchDataError()).rejects.toThrow('error');
  });
});

// Setup and Teardown
describe('Setup and Teardown', () => {
  let database;
  
  // Runs before all tests in this describe block
  beforeAll(() => {
    database = initializeDatabase();
  });
  
  // Runs after all tests in this describe block
  afterAll(() => {
    database.close();
  });
  
  // Runs before each test
  beforeEach(() => {
    database.clear();
  });
  
  // Runs after each test
  afterEach(() => {
    database.cleanup();
  });
  
  test('test 1', () => {
    // Test code
  });
  
  test('test 2', () => {
    // Test code
  });
});
```

### 3. **Mocking with Jest**

```javascript
// Mock Functions
describe('Mock Functions', () => {
  test('mock function calls', () => {
    const mockFn = jest.fn();
    mockFn('hello');
    mockFn('world');
    
    expect(mockFn).toHaveBeenCalledTimes(2);
    expect(mockFn).toHaveBeenCalledWith('hello');
    expect(mockFn).toHaveBeenLastCalledWith('world');
  });
  
  test('mock return values', () => {
    const mockFn = jest.fn()
      .mockReturnValueOnce(10)
      .mockReturnValueOnce(20)
      .mockReturnValue(30);
    
    expect(mockFn()).toBe(10);
    expect(mockFn()).toBe(20);
    expect(mockFn()).toBe(30);
  });
  
  test('mock implementation', () => {
    const mockFn = jest.fn((x, y) => x + y);
    expect(mockFn(1, 2)).toBe(3);
  });
});

// Mocking Modules
// __mocks__/axios.js
const mockAxios = {
  get: jest.fn(() => Promise.resolve({ data: {} })),
  post: jest.fn(() => Promise.resolve({ data: {} })),
  put: jest.fn(() => Promise.resolve({ data: {} })),
  delete: jest.fn(() => Promise.resolve({ data: {} }))
};

export default mockAxios;

// In test file
import axios from 'axios';
jest.mock('axios');

describe('API calls', () => {
  test('fetches successfully', async () => {
    const mockData = { users: [{ id: 1, name: 'John' }] };
    axios.get.mockResolvedValue({ data: mockData });
    
    const result = await fetchUsers();
    
    expect(axios.get).toHaveBeenCalledWith('/api/users');
    expect(result).toEqual(mockData);
  });
  
  test('handles errors', async () => {
    const errorMessage = 'Network Error';
    axios.get.mockRejectedValue(new Error(errorMessage));
    
    await expect(fetchUsers()).rejects.toThrow(errorMessage);
  });
});

// Spy on Methods
describe('Spying', () => {
  test('spy on console.log', () => {
    const spy = jest.spyOn(console, 'log');
    console.log('Hello World');
    
    expect(spy).toHaveBeenCalledWith('Hello World');
    spy.mockRestore(); // Clean up
  });
  
  test('spy on object method', () => {
    const calculator = {
      add: (a, b) => a + b
    };
    
    const spy = jest.spyOn(calculator, 'add');
    calculator.add(2, 3);
    
    expect(spy).toHaveBeenCalledWith(2, 3);
    expect(spy).toHaveReturnedWith(5);
  });
});

// Timer Mocks
describe('Timer Mocks', () => {
  beforeEach(() => {
    jest.useFakeTimers();
  });
  
  afterEach(() => {
    jest.useRealTimers();
  });
  
  test('calls callback after 1 second', () => {
    const callback = jest.fn();
    setTimeout(callback, 1000);
    
    expect(callback).not.toHaveBeenCalled();
    jest.advanceTimersByTime(1000);
    expect(callback).toHaveBeenCalled();
  });
  
  test('runs all timers', () => {
    const callback = jest.fn();
    setTimeout(callback, 1000);
    setTimeout(callback, 2000);
    
    jest.runAllTimers();
    expect(callback).toHaveBeenCalledTimes(2);
  });
});
```

### 4. **React Testing Library Fundamentals**

```typescript
// Installation
// npm install --save-dev @testing-library/react @testing-library/jest-dom
// npm install --save-dev @testing-library/user-event

// setupTests.ts
import '@testing-library/jest-dom';

// Basic Component Test
import { render, screen } from '@testing-library/react';
import Button from './Button';

describe('Button Component', () => {
  test('renders button with text', () => {
    render(<Button>Click me</Button>);
    const buttonElement = screen.getByText(/click me/i);
    expect(buttonElement).toBeInTheDocument();
  });
  
  test('applies correct class based on variant', () => {
    render(<Button variant="primary">Submit</Button>);
    const button = screen.getByRole('button');
    expect(button).toHaveClass('btn-primary');
  });
  
  test('button is disabled when prop is passed', () => {
    render(<Button disabled>Click me</Button>);
    const button = screen.getByRole('button');
    expect(button).toBeDisabled();
  });
});

// Querying Elements
describe('Query Methods', () => {
  test('getBy* - throws error if not found', () => {
    render(<div>Hello</div>);
    expect(screen.getByText('Hello')).toBeInTheDocument();
    // screen.getByText('Goodbye'); // Would throw error
  });
  
  test('queryBy* - returns null if not found', () => {
    render(<div>Hello</div>);
    expect(screen.queryByText('Goodbye')).not.toBeInTheDocument();
  });
  
  test('findBy* - async query', async () => {
    render(<AsyncComponent />);
    const element = await screen.findByText('Loaded');
    expect(element).toBeInTheDocument();
  });
  
  test('getAllBy* - multiple elements', () => {
    render(
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
      </ul>
    );
    const items = screen.getAllByRole('listitem');
    expect(items).toHaveLength(3);
  });
});

// Priority of Queries (from most to least preferred)
/*
1. getByRole (most accessible)
2. getByLabelText (form elements)
3. getByPlaceholderText
4. getByText (non-interactive elements)
5. getByDisplayValue (form inputs)
6. getByAltText (images)
7. getByTitle
8. getByTestId (last resort)
*/
```

### 5. **User Interactions**

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

// Click Events
describe('User Interactions - Click', () => {
  test('handles button click', async () => {
    const handleClick = jest.fn();
    const user = userEvent.setup();
    
    render(<button onClick={handleClick}>Click me</button>);
    
    await user.click(screen.getByRole('button'));
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
  
  test('double click', async () => {
    const handleDoubleClick = jest.fn();
    const user = userEvent.setup();
    
    render(<button onDoubleClick={handleDoubleClick}>Double click</button>);
    
    await user.dblClick(screen.getByRole('button'));
    
    expect(handleDoubleClick).toHaveBeenCalled();
  });
});

// Form Testing
describe('Form Testing', () => {
  test('typing in input field', async () => {
    const user = userEvent.setup();
    render(<input type="text" />);
    
    const input = screen.getByRole('textbox');
    await user.type(input, 'Hello World');
    
    expect(input).toHaveValue('Hello World');
  });
  
  test('form submission', async () => {
    const handleSubmit = jest.fn((e) => e.preventDefault());
    const user = userEvent.setup();
    
    render(
      <form onSubmit={handleSubmit}>
        <input name="username" />
        <input name="email" />
        <button type="submit">Submit</button>
      </form>
    );
    
    await user.type(screen.getByRole('textbox', { name: /username/i }), 'john');
    await user.type(screen.getByRole('textbox', { name: /email/i }), 'john@example.com');
    await user.click(screen.getByRole('button'));
    
    expect(handleSubmit).toHaveBeenCalled();
  });
  
  test('select dropdown', async () => {
    const user = userEvent.setup();
    
    render(
      <select>
        <option value="">Select...</option>
        <option value="1">Option 1</option>
        <option value="2">Option 2</option>
      </select>
    );
    
    const select = screen.getByRole('combobox');
    await user.selectOptions(select, '1');
    
    expect(select).toHaveValue('1');
  });
  
  test('checkbox toggle', async () => {
    const user = userEvent.setup();
    render(<input type="checkbox" />);
    
    const checkbox = screen.getByRole('checkbox');
    
    expect(checkbox).not.toBeChecked();
    await user.click(checkbox);
    expect(checkbox).toBeChecked();
  });
});

// Keyboard Events
describe('Keyboard Events', () => {
  test('keyboard navigation', async () => {
    const user = userEvent.setup();
    
    render(
      <div>
        <button>First</button>
        <button>Second</button>
        <button>Third</button>
      </div>
    );
    
    await user.tab();
    expect(screen.getByText('First')).toHaveFocus();
    
    await user.tab();
    expect(screen.getByText('Second')).toHaveFocus();
  });
  
  test('keyboard shortcuts', async () => {
    const user = userEvent.setup();
    const handleKeyDown = jest.fn();
    
    render(<input onKeyDown={handleKeyDown} />);
    
    const input = screen.getByRole('textbox');
    await user.type(input, '{Enter}');
    
    expect(handleKeyDown).toHaveBeenCalled();
  });
});
```

### 6. **Testing Hooks**

```typescript
import { renderHook, waitFor } from '@testing-library/react';
import { act } from 'react-dom/test-utils';

// Testing Custom Hooks
describe('useCounter Hook', () => {
  test('initializes with default value', () => {
    const { result } = renderHook(() => useCounter());
    
    expect(result.current.count).toBe(0);
  });
  
  test('increments counter', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
  
  test('decrements counter', () => {
    const { result } = renderHook(() => useCounter(5));
    
    act(() => {
      result.current.decrement();
    });
    
    expect(result.current.count).toBe(4);
  });
});

// Testing Async Hooks
describe('useFetch Hook', () => {
  test('fetches data successfully', async () => {
    const mockData = { users: [{ id: 1, name: 'John' }] };
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve(mockData)
      })
    );
    
    const { result } = renderHook(() => useFetch('/api/users'));
    
    expect(result.current.loading).toBe(true);
    
    await waitFor(() => {
      expect(result.current.loading).toBe(false);
    });
    
    expect(result.current.data).toEqual(mockData);
    expect(result.current.error).toBeNull();
  });
  
  test('handles fetch errors', async () => {
    global.fetch = jest.fn(() =>
      Promise.reject(new Error('Network error'))
    );
    
    const { result } = renderHook(() => useFetch('/api/users'));
    
    await waitFor(() => {
      expect(result.current.loading).toBe(false);
    });
    
    expect(result.current.error).toBeTruthy();
    expect(result.current.data).toBeNull();
  });
});
```

### 7. **Testing Context and Providers**

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

// Testing Components with Context
describe('Component with Context', () => {
  test('renders with theme context', () => {
    render(
      <ThemeProvider value={{ theme: 'dark' }}>
        <ThemedButton />
      </ThemeProvider>
    );
    
    const button = screen.getByRole('button');
    expect(button).toHaveClass('theme-dark');
  });
  
  test('updates context value', async () => {
    const user = userEvent.setup();
    
    render(
      <ThemeProvider>
        <ThemeToggle />
        <ThemedButton />
      </ThemeProvider>
    );
    
    const toggle = screen.getByRole('button', { name: /toggle theme/i });
    await user.click(toggle);
    
    const button = screen.getByRole('button', { name: /themed button/i });
    expect(button).toHaveClass('theme-dark');
  });
});

// Custom Render with Providers
const AllTheProviders = ({ children }) => {
  return (
    <ThemeProvider>
      <AuthProvider>
        <Router>
          {children}
        </Router>
      </AuthProvider>
    </ThemeProvider>
  );
};

const customRender = (ui, options) =>
  render(ui, { wrapper: AllTheProviders, ...options });

// Re-export everything
export * from '@testing-library/react';
export { customRender as render };

// Usage
test('component with all providers', () => {
  customRender(<MyComponent />);
  // Test assertions
});
```

### 8. **Testing Async Components**

```typescript
describe('Async Component Testing', () => {
  test('shows loading state', () => {
    render(<AsyncUserList />);
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
  });
  
  test('displays users after loading', async () => {
    const mockUsers = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ];
    
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve(mockUsers)
      })
    );
    
    render(<AsyncUserList />);
    
    const users = await screen.findAllByRole('listitem');
    expect(users).toHaveLength(2);
  });
  
  test('displays error message on fetch failure', async () => {
    global.fetch = jest.fn(() =>
      Promise.reject(new Error('Failed to fetch'))
    );
    
    render(<AsyncUserList />);
    
    const errorMessage = await screen.findByText(/error/i);
    expect(errorMessage).toBeInTheDocument();
  });
});
```

---

## 💡 Key Concepts

1. **Test Isolation**: Each test should be independent and not affect others
2. **Arrange-Act-Assert**: Clear test structure for readability
3. **Query Priority**: Use accessible queries (getByRole) over test IDs
4. **User-Centric Testing**: Test from the user's perspective
5. **Async Handling**: Properly handle async operations with waitFor
6. **Mocking**: Mock external dependencies for reliable tests
7. **Coverage**: Aim for meaningful coverage, not 100%
8. **Maintainability**: Write tests that are easy to understand and update

---

## 🛠️ Practical Exercises

### Exercise 1: Form Validation Testing
Write comprehensive tests for a registration form with validation.

### Exercise 2: API Integration Testing
Test components that fetch and display data from an API.

### Exercise 3: Custom Hook Testing
Create and test a complex custom hook with multiple features.

### Exercise 4: Context Testing
Test a shopping cart context with add, remove, and update operations.

### Exercise 5: Async Operations
Test components with debouncing, throttling, and delayed actions.

---

## 🎨 Project: Tested Todo Application

Build a fully tested todo application:

**Components to Test:**
1. TodoList - displays all todos with filtering
2. TodoItem - individual todo with edit/delete
3. TodoForm - add new todos with validation
4. TodoFilter - filter by status
5. useTodos hook - custom hook for todo management

**Test Coverage:**
- Unit tests for utility functions
- Component rendering tests
- User interaction tests
- Form validation tests
- Async operations (add, update, delete)
- Context state management
- Error handling
- Edge cases (empty list, long text, etc.)

**Requirements:**
- Minimum 80% code coverage
- All user flows tested
- Error scenarios covered
- Accessibility testing included

---

## 📖 Resources to Explore

### Documentation
- **Jest Documentation**: https://jestjs.io/
- **React Testing Library**: https://testing-library.com/react
- **Testing Library Guides**: https://testing-library.com/docs/guiding-principles

### Video Tutorials
- **JavaScript Testing with Jest** by Traversy Media
- **React Testing Library Tutorial** by Codevolution
- **TDD with React** by Ben Awad

### Articles & Guides
- "Common Testing Mistakes" by Kent C. Dodds
- "Write Tests. Not Too Many. Mostly Integration."
- "Testing Implementation Details"

### Tools
- **Jest**: Testing framework
- **React Testing Library**: React component testing
- **User Event**: Simulate user interactions
- **MSW (Mock Service Worker)**: API mocking
- **Cypress**: E2E testing alternative

---

## 🔍 Interview Questions

### Basic
1. What is the testing pyramid?
2. Explain the AAA pattern in testing.
3. What's the difference between unit and integration tests?
4. What are Jest matchers?
5. How do you test async code in Jest?

### Intermediate
1. Explain the difference between getBy, queryBy, and findBy.
2. How do you mock API calls in tests?
3. What is the purpose of setupTests file?
4. How do you test custom hooks?
5. Explain userEvent vs fireEvent.
6. How do you test components with Context?
7. What is test coverage and how do you measure it?

### Advanced
1. How do you implement TDD in a React project?
2. What are the best practices for testing React components?
3. How do you test components with third-party libraries?
4. Explain snapshot testing and when to use it.
5. How would you test a complex form with multiple validation rules?
6. What's your strategy for testing Redux-connected components?
7. How do you handle flaky tests?
8. Explain testing accessibility in React.

### Practical
1. Design a testing strategy for a large-scale application.
2. How would you refactor untested legacy code?
3. Write tests for a real-time chat component.
4. Test a component with infinite scroll.

---

## ✅ Success Criteria

- [ ] Understand Jest fundamentals and matchers
- [ ] Write unit tests for JavaScript functions
- [ ] Test React components with React Testing Library
- [ ] Simulate user interactions effectively
- [ ] Test forms and user inputs
- [ ] Handle async operations in tests
- [ ] Mock API calls and external dependencies
- [ ] Test custom hooks and Context
- [ ] Achieve meaningful test coverage
- [ ] Debug and fix failing tests
- [ ] Apply TDD principles to new features

---

## 🚀 Next Steps

Tomorrow (Day 25), you'll learn:
- **Build Tools**: Webpack, Vite, and modern bundlers
- **Module bundling and code splitting**
- **Performance optimization techniques**
- **Build configuration and optimization**

**Preparation:**
- Understand JavaScript modules (ES6, CommonJS)
- Review npm scripts basics
- Explore build tool documentation
- Learn about tree shaking and minification

---

**Remember**: Good tests give you confidence to refactor and ship features faster! 🧪
