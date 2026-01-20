# Day 20: Forms, Validation & Error Handling

## 🎯 Learning Objectives

By the end of Day 20, you will:
- Master controlled and uncontrolled components in React
- Implement comprehensive form validation patterns
- Handle form submission and async operations
- Create reusable form input components
- Implement client-side and server-side validation
- Use React Hook Form for complex forms
- Handle errors gracefully with error boundaries
- Implement loading states and user feedback
- Validate forms with schema validation (Yup, Zod)
- Create accessible and user-friendly forms

## 📚 Topics to Study

### 1. Controlled vs Uncontrolled Components

```javascript
/**
 * CONTROLLED COMPONENTS
 * - React state controls form element value
 * - Single source of truth
 * - Preferred approach in React
 */

import { useState } from 'react';

function ControlledForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    bio: '',
    country: '',
    newsletter: false,
    gender: ''
  });
  
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form data:', formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Text input */}
      <input
        type="text"
        name="username"
        value={formData.username}
        onChange={handleChange}
        placeholder="Username"
      />
      
      {/* Email input */}
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      
      {/* Password input */}
      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      
      {/* Textarea */}
      <textarea
        name="bio"
        value={formData.bio}
        onChange={handleChange}
        placeholder="Bio"
      />
      
      {/* Select dropdown */}
      <select name="country" value={formData.country} onChange={handleChange}>
        <option value="">Select Country</option>
        <option value="us">United States</option>
        <option value="uk">United Kingdom</option>
        <option value="ca">Canada</option>
      </select>
      
      {/* Checkbox */}
      <label>
        <input
          type="checkbox"
          name="newsletter"
          checked={formData.newsletter}
          onChange={handleChange}
        />
        Subscribe to newsletter
      </label>
      
      {/* Radio buttons */}
      <div>
        <label>
          <input
            type="radio"
            name="gender"
            value="male"
            checked={formData.gender === 'male'}
            onChange={handleChange}
          />
          Male
        </label>
        <label>
          <input
            type="radio"
            name="gender"
            value="female"
            checked={formData.gender === 'female'}
            onChange={handleChange}
          />
          Female
        </label>
      </div>
      
      <button type="submit">Submit</button>
    </form>
  );
}

/**
 * UNCONTROLLED COMPONENTS
 * - DOM controls element value
 * - Use refs to access values
 * - Useful for file inputs, integrating non-React code
 */

import { useRef } from 'react';

function UncontrolledForm() {
  const usernameRef = useRef();
  const emailRef = useRef();
  const fileRef = useRef();
  
  const handleSubmit = (e) => {
    e.preventDefault();
    
    const formData = {
      username: usernameRef.current.value,
      email: emailRef.current.value,
      file: fileRef.current.files[0]
    };
    
    console.log('Form data:', formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input ref={usernameRef} type="text" defaultValue="" />
      <input ref={emailRef} type="email" defaultValue="" />
      <input ref={fileRef} type="file" />
      <button type="submit">Submit</button>
    </form>
  );
}

/**
 * MIXED APPROACH - File Upload with Controlled Form
 */

function MixedForm() {
  const [formData, setFormData] = useState({
    title: '',
    description: ''
  });
  const [file, setFile] = useState(null);
  const fileInputRef = useRef();
  
  const handleChange = (e) => {
    setFormData(prev => ({ ...prev, [e.target.name]: e.target.value }));
  };
  
  const handleFileChange = (e) => {
    setFile(e.target.files[0]);
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    const data = new FormData();
    data.append('title', formData.title);
    data.append('description', formData.description);
    data.append('file', file);
    
    // Upload to server
    await fetch('/api/upload', {
      method: 'POST',
      body: data
    });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input name="title" value={formData.title} onChange={handleChange} />
      <textarea name="description" value={formData.description} onChange={handleChange} />
      <input ref={fileInputRef} type="file" onChange={handleFileChange} />
      {file && <p>Selected: {file.name}</p>}
      <button type="submit">Upload</button>
    </form>
  );
}
```

### 2. Form Validation

```javascript
/**
 * BASIC CLIENT-SIDE VALIDATION
 */

function RegistrationForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: ''
  });
  
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: '' }));
    }
  };
  
  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    validateField(name, formData[name]);
  };
  
  const validateField = (name, value) => {
    let error = '';
    
    switch (name) {
      case 'username':
        if (!value) {
          error = 'Username is required';
        } else if (value.length < 3) {
          error = 'Username must be at least 3 characters';
        } else if (!/^[a-zA-Z0-9_]+$/.test(value)) {
          error = 'Username can only contain letters, numbers, and underscores';
        }
        break;
        
      case 'email':
        if (!value) {
          error = 'Email is required';
        } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          error = 'Invalid email format';
        }
        break;
        
      case 'password':
        if (!value) {
          error = 'Password is required';
        } else if (value.length < 8) {
          error = 'Password must be at least 8 characters';
        } else if (!/(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/.test(value)) {
          error = 'Password must contain uppercase, lowercase, and number';
        }
        break;
        
      case 'confirmPassword':
        if (!value) {
          error = 'Please confirm your password';
        } else if (value !== formData.password) {
          error = 'Passwords do not match';
        }
        break;
    }
    
    setErrors(prev => ({ ...prev, [name]: error }));
    return !error;
  };
  
  const validateForm = () => {
    const fieldNames = Object.keys(formData);
    const newErrors = {};
    let isValid = true;
    
    fieldNames.forEach(name => {
      if (!validateField(name, formData[name])) {
        isValid = false;
      }
    });
    
    return isValid;
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    // Mark all fields as touched
    const allTouched = Object.keys(formData).reduce((acc, key) => {
      acc[key] = true;
      return acc;
    }, {});
    setTouched(allTouched);
    
    if (!validateForm()) {
      return;
    }
    
    try {
      const response = await fetch('/api/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData)
      });
      
      if (!response.ok) {
        const error = await response.json();
        throw new Error(error.message);
      }
      
      alert('Registration successful!');
    } catch (error) {
      setErrors(prev => ({ ...prev, submit: error.message }));
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Username"
          className={touched.username && errors.username ? 'error' : ''}
        />
        {touched.username && errors.username && (
          <span className="error-message">{errors.username}</span>
        )}
      </div>
      
      <div>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Email"
          className={touched.email && errors.email ? 'error' : ''}
        />
        {touched.email && errors.email && (
          <span className="error-message">{errors.email}</span>
        )}
      </div>
      
      <div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Password"
          className={touched.password && errors.password ? 'error' : ''}
        />
        {touched.password && errors.password && (
          <span className="error-message">{errors.password}</span>
        )}
      </div>
      
      <div>
        <input
          type="password"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Confirm Password"
          className={touched.confirmPassword && errors.confirmPassword ? 'error' : ''}
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <span className="error-message">{errors.confirmPassword}</span>
        )}
      </div>
      
      {errors.submit && (
        <div className="error-message">{errors.submit}</div>
      )}
      
      <button type="submit">Register</button>
    </form>
  );
}

/**
 * REUSABLE INPUT COMPONENTS
 */

function FormInput({ label, error, touched, ...props }) {
  return (
    <div className="form-group">
      {label && <label>{label}</label>}
      <input
        {...props}
        className={`form-input ${touched && error ? 'input-error' : ''}`}
      />
      {touched && error && <span className="error-message">{error}</span>}
    </div>
  );
}

// Usage
<FormInput
  label="Email"
  type="email"
  name="email"
  value={formData.email}
  onChange={handleChange}
  onBlur={handleBlur}
  error={errors.email}
  touched={touched.email}
  placeholder="Enter your email"
/>
```

### 3. React Hook Form

```javascript
/**
 * REACT HOOK FORM - Performant form library
 * 
 * Installation: npm install react-hook-form
 * 
 * Benefits:
 * - Minimal re-renders
 * - Built-in validation
 * - Easy integration with validation schemas
 * - Small bundle size
 */

import { useForm } from 'react-hook-form';

function HookForm() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors, isSubmitting, isDirty, isValid }
  } = useForm({
    mode: 'onBlur', // Validation mode
    defaultValues: {
      username: '',
      email: '',
      age: 0
    }
  });
  
  const onSubmit = async (data) => {
    console.log('Form data:', data);
    
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 2000));
    
    alert('Form submitted!');
  };
  
  // Watch specific field
  const password = watch('password');
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <input
          {...register('username', {
            required: 'Username is required',
            minLength: {
              value: 3,
              message: 'Username must be at least 3 characters'
            },
            pattern: {
              value: /^[a-zA-Z0-9_]+$/,
              message: 'Only letters, numbers, and underscores allowed'
            }
          })}
          placeholder="Username"
        />
        {errors.username && <span>{errors.username.message}</span>}
      </div>
      
      <div>
        <input
          {...register('email', {
            required: 'Email is required',
            pattern: {
              value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
              message: 'Invalid email address'
            }
          })}
          type="email"
          placeholder="Email"
        />
        {errors.email && <span>{errors.email.message}</span>}
      </div>
      
      <div>
        <input
          {...register('age', {
            required: 'Age is required',
            min: { value: 18, message: 'Must be at least 18' },
            max: { value: 100, message: 'Must be under 100' },
            valueAsNumber: true
          })}
          type="number"
          placeholder="Age"
        />
        {errors.age && <span>{errors.age.message}</span>}
      </div>
      
      <div>
        <input
          {...register('password', {
            required: 'Password is required',
            minLength: {
              value: 8,
              message: 'Password must be at least 8 characters'
            }
          })}
          type="password"
          placeholder="Password"
        />
        {errors.password && <span>{errors.password.message}</span>}
      </div>
      
      <div>
        <input
          {...register('confirmPassword', {
            required: 'Please confirm password',
            validate: value =>
              value === password || 'Passwords do not match'
          })}
          type="password"
          placeholder="Confirm Password"
        />
        {errors.confirmPassword && <span>{errors.confirmPassword.message}</span>}
      </div>
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
}

/**
 * REACT HOOK FORM WITH YUP VALIDATION
 * 
 * Installation: npm install @hookform/resolvers yup
 */

import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

// Validation Schema
const schema = yup.object({
  username: yup
    .string()
    .required('Username is required')
    .min(3, 'Minimum 3 characters')
    .matches(/^[a-zA-Z0-9_]+$/, 'Only alphanumeric and underscore'),
  email: yup
    .string()
    .required('Email is required')
    .email('Invalid email format'),
  password: yup
    .string()
    .required('Password is required')
    .min(8, 'Minimum 8 characters')
    .matches(/[a-z]/, 'Must contain lowercase')
    .matches(/[A-Z]/, 'Must contain uppercase')
    .matches(/[0-9]/, 'Must contain number'),
  confirmPassword: yup
    .string()
    .required('Please confirm password')
    .oneOf([yup.ref('password')], 'Passwords must match'),
  age: yup
    .number()
    .required('Age is required')
    .positive('Must be positive')
    .integer('Must be integer')
    .min(18, 'Must be at least 18'),
  terms: yup
    .boolean()
    .oneOf([true], 'You must accept terms and conditions')
}).required();

function FormWithYup() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm({
    resolver: yupResolver(schema)
  });
  
  const onSubmit = async (data) => {
    try {
      const response = await fetch('/api/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
      });
      
      if (!response.ok) throw new Error('Registration failed');
      
      alert('Success!');
    } catch (error) {
      console.error(error);
    }
  };
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username')} placeholder="Username" />
      {errors.username && <span>{errors.username.message}</span>}
      
      <input {...register('email')} type="email" placeholder="Email" />
      {errors.email && <span>{errors.email.message}</span>}
      
      <input {...register('password')} type="password" placeholder="Password" />
      {errors.password && <span>{errors.password.message}</span>}
      
      <input {...register('confirmPassword')} type="password" placeholder="Confirm" />
      {errors.confirmPassword && <span>{errors.confirmPassword.message}</span>}
      
      <input {...register('age')} type="number" placeholder="Age" />
      {errors.age && <span>{errors.age.message}</span>}
      
      <label>
        <input {...register('terms')} type="checkbox" />
        I accept the terms
      </label>
      {errors.terms && <span>{errors.terms.message}</span>}
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
}

/**
 * DYNAMIC FORM FIELDS
 */

import { useForm, useFieldArray } from 'react-hook-form';

function DynamicForm() {
  const { register, control, handleSubmit } = useForm({
    defaultValues: {
      name: '',
      emails: [{ value: '' }]
    }
  });
  
  const { fields, append, remove } = useFieldArray({
    control,
    name: 'emails'
  });
  
  const onSubmit = (data) => console.log(data);
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} placeholder="Name" />
      
      {fields.map((field, index) => (
        <div key={field.id}>
          <input
            {...register(`emails.${index}.value`)}
            placeholder="Email"
          />
          <button type="button" onClick={() => remove(index)}>
            Remove
          </button>
        </div>
      ))}
      
      <button type="button" onClick={() => append({ value: '' })}>
        Add Email
      </button>
      
      <button type="submit">Submit</button>
    </form>
  );
}
```

### 4. Error Boundaries

```javascript
/**
 * ERROR BOUNDARIES - Catch JavaScript errors in component tree
 * 
 * Note: Error boundaries are class components (no hook equivalent yet)
 * They catch errors during:
 * - Rendering
 * - Lifecycle methods
 * - Constructors of child components
 * 
 * They DON'T catch:
 * - Event handlers (use try-catch)
 * - Async code
 * - Server-side rendering
 * - Errors in error boundary itself
 */

import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }
  
  static getDerivedStateFromError(error) {
    // Update state so next render shows fallback UI
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    // Log error to error reporting service
    console.error('Error caught by boundary:', error, errorInfo);
    this.setState({ error, errorInfo });
    
    // Log to external service
    // logErrorToService(error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      // Custom fallback UI
      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <details style={{ whiteSpace: 'pre-wrap' }}>
            {this.state.error && this.state.error.toString()}
            <br />
            {this.state.errorInfo && this.state.errorInfo.componentStack}
          </details>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <MyComponent />
    </ErrorBoundary>
  );
}

// Multiple error boundaries for different sections
function Dashboard() {
  return (
    <div>
      <ErrorBoundary>
        <Sidebar />
      </ErrorBoundary>
      
      <ErrorBoundary>
        <MainContent />
      </ErrorBoundary>
      
      <ErrorBoundary>
        <Analytics />
      </ErrorBoundary>
    </div>
  );
}

/**
 * ERROR HANDLING IN EVENT HANDLERS
 * (Error boundaries don't catch these)
 */

function EventHandlerErrors() {
  const [error, setError] = useState(null);
  
  const handleClick = async () => {
    try {
      setError(null);
      const response = await fetch('/api/data');
      if (!response.ok) throw new Error('Failed to fetch');
      const data = await response.json();
      // Process data
    } catch (err) {
      setError(err.message);
      console.error('Error in event handler:', err);
    }
  };
  
  return (
    <div>
      {error && <div className="error-alert">{error}</div>}
      <button onClick={handleClick}>Fetch Data</button>
    </div>
  );
}

/**
 * GLOBAL ERROR HANDLER
 */

function GlobalErrorHandler({ children }) {
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const handleError = (event) => {
      console.error('Global error:', event.error);
      setError(event.error.message);
    };
    
    const handleRejection = (event) => {
      console.error('Unhandled promise rejection:', event.reason);
      setError(event.reason.message || 'An error occurred');
    };
    
    window.addEventListener('error', handleError);
    window.addEventListener('unhandledrejection', handleRejection);
    
    return () => {
      window.removeEventListener('error', handleError);
      window.removeEventListener('unhandledrejection', handleRejection);
    };
  }, []);
  
  if (error) {
    return (
      <div className="global-error">
        <h1>Application Error</h1>
        <p>{error}</p>
        <button onClick={() => window.location.reload()}>
          Reload Application
        </button>
      </div>
    );
  }
  
  return children;
}
```

### 5. Loading States and User Feedback

```javascript
/**
 * COMPREHENSIVE FORM WITH LOADING STATES
 */

function CompleteForm() {
  const [formData, setFormData] = useState({ email: '', password: '' });
  const [status, setStatus] = useState('idle'); // idle, loading, success, error
  const [error, setError] = useState(null);
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    setStatus('loading');
    setError(null);
    
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData)
      });
      
      if (!response.ok) {
        const errorData = await response.json();
        throw new Error(errorData.message || 'Login failed');
      }
      
      const data = await response.json();
      setStatus('success');
      
      // Redirect or update app state
      setTimeout(() => {
        window.location.href = '/dashboard';
      }, 1500);
      
    } catch (err) {
      setStatus('error');
      setError(err.message);
    }
  };
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    // Clear error when user starts typing
    if (error) setError(null);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {status === 'success' && (
        <div className="alert alert-success">
          Login successful! Redirecting...
        </div>
      )}
      
      {status === 'error' && error && (
        <div className="alert alert-error">
          {error}
        </div>
      )}
      
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        disabled={status === 'loading' || status === 'success'}
        placeholder="Email"
        required
      />
      
      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
        disabled={status === 'loading' || status === 'success'}
        placeholder="Password"
        required
      />
      
      <button
        type="submit"
        disabled={status === 'loading' || status === 'success'}
      >
        {status === 'loading' && 'Logging in...'}
        {status === 'success' && '✓ Success'}
        {(status === 'idle' || status === 'error') && 'Login'}
      </button>
      
      {status === 'loading' && (
        <div className="spinner">Loading...</div>
      )}
    </form>
  );
}
```

## 💡 Key Concepts

1. **Controlled Components**: React state as single source of truth for form values

2. **Validation Strategies**: Client-side (UX), server-side (security), both recommended

3. **Error Boundaries**: Catch and handle rendering errors gracefully

4. **Progressive Enhancement**: HTML5 validation + JavaScript validation

5. **Accessibility**: Proper labels, error messages, ARIA attributes

6. **User Feedback**: Loading states, success messages, error handling

7. **Form Libraries**: Reduce boilerplate, improve performance, add features

## 🛠️ Practical Exercises

### Exercise 1: Basic Form
Create contact form with validation (name, email, message).

### Exercise 2: Registration Form
Multi-field registration with password strength and confirmation.

### Exercise 3: Dynamic Form
Add/remove form fields dynamically (e.g., multiple phone numbers).

### Exercise 4: File Upload
Form with file upload and preview functionality.

### Exercise 5: React Hook Form
Rebuild registration form using React Hook Form.

### Exercise 6: Error Boundary
Create error boundary and trigger errors to test it.

### Exercise 7: Async Validation
Implement username availability check with debouncing.

## 🎨 Project: Multi-Step Registration Wizard

Build a complete multi-step registration form:

**Steps:**
1. Personal Information (name, email, phone)
2. Account Details (username, password, confirm password)
3. Address Information (street, city, state, zip, country)
4. Preferences (newsletter, notifications, theme)
5. Review and Confirm

**Features:**
- Step-by-step wizard with progress indicator
- Field validation on blur
- Form-level validation on submit
- Previous/Next navigation
- Can't proceed with errors
- Review all data before final submit
- Loading state during submission
- Success/error messages
- Data persistence (localStorage)
- Form reset option

**Technical Requirements:**
- Use React Hook Form
- Implement Yup validation schema
- Error boundary for error handling
- Responsive design
- Accessibility (ARIA labels, keyboard navigation)
- Loading indicators
- Toast notifications for feedback
- Save draft functionality
- Edit capability from review step

## 📖 Resources to Explore

### Official Documentation
- [React Forms](https://react.dev/learn/sharing-state-between-components#controlled-and-uncontrolled-components)
- [React Hook Form](https://react-hook-form.com/)
- [Yup Validation](https://github.com/jquense/yup)
- [Error Boundaries](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary)

### Libraries
- React Hook Form - Performance-focused forms
- Formik - Popular form library
- Yup - Schema validation
- Zod - TypeScript-first validation
- React Select - Advanced select inputs
- React Datepicker - Date/time inputs

### Articles
- "Controlled vs Uncontrolled Components"
- "Form Validation in React"
- "Error Handling in React"
- "Accessible Forms"

## 🔍 Interview Questions

1. **What's the difference between controlled and uncontrolled components?**
2. **How do you validate forms in React?**
3. **What are error boundaries and their limitations?**
4. **How do you handle async form submission?**
5. **What is React Hook Form and its benefits?**
6. **How do you implement multi-step forms?**
7. **What's the best way to handle form errors?**
8. **How do you optimize form performance?**
9. **How do you handle file uploads in React?**
10. **What are validation schemas?**
11. **How do you implement field-level validation?**
12. **What's the difference between onChange and onBlur validation?**
13. **How do you show loading states during form submission?**
14. **How do you make forms accessible?**
15. **What's the purpose of the touched state in forms?**

## ✅ Success Criteria

- [ ] Implement controlled and uncontrolled components
- [ ] Create comprehensive form validation
- [ ] Use React Hook Form effectively
- [ ] Implement Yup/Zod schema validation
- [ ] Create error boundary components
- [ ] Handle async form submissions
- [ ] Implement loading and success states
- [ ] Build accessible forms with proper ARIA
- [ ] Complete multi-step wizard project
- [ ] Handle file uploads
- [ ] Answer 12/15 interview questions correctly

## 🚀 Next Steps

Tomorrow (Day 21), you'll learn:
- **Performance Optimization**: React.memo, useMemo, useCallback
- **Code Splitting**: Dynamic imports, lazy loading
- **Profiling**: React DevTools Profiler
- **Best Practices**: React patterns and anti-patterns

### Preparation
- Review JavaScript memoization
- Understand React rendering behavior
- Practice identifying performance bottlenecks
- Read about virtual DOM diffing

---

**Remember**: Forms are the gateway to user data. Make them user-friendly, validated, and accessible!
