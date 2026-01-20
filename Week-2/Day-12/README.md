# Day 12: Fetch API, AJAX & HTTP Methods

## 🎯 Learning Objectives

By the end of today, you will understand:
- The Fetch API and modern HTTP requests
- HTTP methods: GET, POST, PUT, PATCH, DELETE
- Request and response handling
- Working with JSON, FormData, and other formats
- Headers, authentication, and CORS
- Error handling for network requests
- XMLHttpRequest (legacy) vs Fetch API
- Building RESTful API clients

## 📚 Topics to Study

### 1. The Fetch API Basics

```javascript
// Basic GET Request
fetch('https://api.example.com/users')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));

// With async/await (preferred)
async function fetchUsers() {
  try {
    const response = await fetch('https://api.example.com/users');
    const users = await response.json();
    console.log(users);
  } catch (error) {
    console.error('Error:', error);
  }
}

// Response Object Properties
async function exploreResponse() {
  const response = await fetch('https://api.example.com/data');
  
  // Status information
  console.log(response.status);      // 200, 404, 500, etc.
  console.log(response.statusText);  // 'OK', 'Not Found', etc.
  console.log(response.ok);          // true if 200-299
  
  // Headers
  console.log(response.headers.get('Content-Type'));
  
  // URL information
  console.log(response.url);
  console.log(response.redirected); // true if redirected
  
  // Body reading methods (can only use ONE)
  // const json = await response.json();      // Parse as JSON
  // const text = await response.text();      // Plain text
  // const blob = await response.blob();      // Binary data
  // const buffer = await response.arrayBuffer(); // ArrayBuffer
  // const formData = await response.formData();  // FormData
}

// Checking Response Status
async function fetchWithStatusCheck(url) {
  const response = await fetch(url);
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  
  return response.json();
}

// Alternative status handling
async function handleDifferentStatuses(url) {
  const response = await fetch(url);
  
  switch (response.status) {
    case 200:
      return response.json();
    case 404:
      throw new Error('Resource not found');
    case 500:
      throw new Error('Server error');
    default:
      throw new Error(`Unexpected status: ${response.status}`);
  }
}
```

### 2. HTTP Methods and Request Configuration

```javascript
// GET Request (default)
const getUsers = async () => {
  const response = await fetch('https://api.example.com/users');
  return response.json();
};

// GET with Query Parameters
const searchUsers = async (query) => {
  const params = new URLSearchParams({
    q: query,
    page: 1,
    limit: 10,
    sort: 'name'
  });
  
  const url = `https://api.example.com/users?${params}`;
  const response = await fetch(url);
  return response.json();
};

// POST Request - Create Resource
const createUser = async (userData) => {
  const response = await fetch('https://api.example.com/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(userData)
  });
  
  if (!response.ok) {
    throw new Error('Failed to create user');
  }
  
  return response.json();
};

// PUT Request - Replace Resource
const updateUser = async (userId, userData) => {
  const response = await fetch(`https://api.example.com/users/${userId}`, {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(userData)
  });
  
  return response.json();
};

// PATCH Request - Partial Update
const patchUser = async (userId, updates) => {
  const response = await fetch(`https://api.example.com/users/${userId}`, {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(updates)
  });
  
  return response.json();
};

// DELETE Request
const deleteUser = async (userId) => {
  const response = await fetch(`https://api.example.com/users/${userId}`, {
    method: 'DELETE'
  });
  
  if (!response.ok) {
    throw new Error('Failed to delete user');
  }
  
  // Some APIs return 204 No Content (empty response)
  if (response.status === 204) {
    return { success: true };
  }
  
  return response.json();
};

// Request Configuration Options
const advancedRequest = async () => {
  const response = await fetch('https://api.example.com/data', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': 'Bearer token123',
      'Custom-Header': 'value'
    },
    body: JSON.stringify({ data: 'value' }),
    mode: 'cors',        // 'cors', 'no-cors', 'same-origin'
    credentials: 'include', // 'include', 'same-origin', 'omit'
    cache: 'no-cache',   // 'default', 'no-store', 'reload', etc.
    redirect: 'follow',  // 'follow', 'manual', 'error'
    referrerPolicy: 'no-referrer' // Various policies
  });
  
  return response.json();
};
```

### 3. Working with Different Data Formats

```javascript
// JSON (Most Common)
const sendJSON = async (data) => {
  const response = await fetch('/api/data', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(data)
  });
  
  return response.json();
};

// FormData (File Uploads, Multi-part Forms)
const uploadFile = async (file, additionalData) => {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('title', additionalData.title);
  formData.append('description', additionalData.description);
  
  const response = await fetch('/api/upload', {
    method: 'POST',
    // Don't set Content-Type header - browser sets it with boundary
    body: formData
  });
  
  return response.json();
};

// URL-encoded Form Data
const sendFormData = async (formElement) => {
  const formData = new FormData(formElement);
  const params = new URLSearchParams(formData);
  
  const response = await fetch('/api/form', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded'
    },
    body: params
  });
  
  return response.json();
};

// Plain Text
const sendText = async (text) => {
  const response = await fetch('/api/text', {
    method: 'POST',
    headers: {
      'Content-Type': 'text/plain'
    },
    body: text
  });
  
  return response.text();
};

// Blob (Binary Data)
const downloadImage = async (url) => {
  const response = await fetch(url);
  const blob = await response.blob();
  
  // Create download link
  const objectURL = URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = objectURL;
  link.download = 'image.jpg';
  link.click();
  
  URL.revokeObjectURL(objectURL); // Clean up
};

// Streaming Response
async function streamResponse(url) {
  const response = await fetch(url);
  const reader = response.body.getReader();
  
  while (true) {
    const { done, value } = await reader.read();
    if (done) break;
    
    // Process chunk
    console.log('Received chunk:', value);
  }
}
```

### 4. Headers and Authentication

```javascript
// Setting Headers
const headers = new Headers();
headers.append('Content-Type', 'application/json');
headers.append('Authorization', 'Bearer token123');

const response = await fetch('/api/data', {
  headers: headers
});

// Or use object literal
const response2 = await fetch('/api/data', {
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  }
});

// Reading Response Headers
const checkHeaders = async () => {
  const response = await fetch('/api/data');
  
  // Get specific header
  const contentType = response.headers.get('Content-Type');
  
  // Check if header exists
  const hasAuth = response.headers.has('Authorization');
  
  // Iterate all headers
  for (const [key, value] of response.headers) {
    console.log(`${key}: ${value}`);
  }
};

// Bearer Token Authentication
const fetchWithAuth = async (token) => {
  const response = await fetch('/api/protected', {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};

// Basic Authentication
const basicAuth = async (username, password) => {
  const credentials = btoa(`${username}:${password}`);
  
  const response = await fetch('/api/login', {
    headers: {
      'Authorization': `Basic ${credentials}`
    }
  });
  
  return response.json();
};

// API Key Authentication
const fetchWithApiKey = async (apiKey) => {
  const response = await fetch('/api/data', {
    headers: {
      'X-API-Key': apiKey
    }
  });
  
  return response.json();
};

// Custom Header Interceptor Pattern
class APIClient {
  constructor(baseURL, defaultHeaders = {}) {
    this.baseURL = baseURL;
    this.defaultHeaders = defaultHeaders;
  }
  
  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`;
    const config = {
      ...options,
      headers: {
        ...this.defaultHeaders,
        ...options.headers
      }
    };
    
    const response = await fetch(url, config);
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    return response.json();
  }
  
  get(endpoint, options) {
    return this.request(endpoint, { ...options, method: 'GET' });
  }
  
  post(endpoint, data, options) {
    return this.request(endpoint, {
      ...options,
      method: 'POST',
      body: JSON.stringify(data)
    });
  }
}

// Usage
const api = new APIClient('https://api.example.com', {
  'Authorization': 'Bearer token123',
  'Content-Type': 'application/json'
});

// const users = await api.get('/users');
// const newUser = await api.post('/users', { name: 'John' });
```

### 5. Error Handling and Network Errors

```javascript
// Comprehensive Error Handling
async function robustFetch(url, options = {}) {
  try {
    const response = await fetch(url, options);
    
    // Network request succeeded, check HTTP status
    if (!response.ok) {
      // Try to get error details from response
      let errorMessage = `HTTP ${response.status}: ${response.statusText}`;
      
      try {
        const errorData = await response.json();
        errorMessage = errorData.message || errorMessage;
      } catch {
        // Response wasn't JSON, use default message
      }
      
      throw new Error(errorMessage);
    }
    
    return response.json();
    
  } catch (error) {
    // Network error (no connection, DNS failure, etc.)
    if (error.name === 'TypeError' && error.message === 'Failed to fetch') {
      throw new Error('Network error - check your connection');
    }
    
    // Timeout error
    if (error.name === 'AbortError') {
      throw new Error('Request timeout');
    }
    
    // Re-throw other errors
    throw error;
  }
}

// Timeout Implementation
async function fetchWithTimeout(url, timeout = 5000) {
  const controller = new AbortController();
  const signal = controller.signal;
  
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, { signal });
    clearTimeout(timeoutId);
    return response.json();
  } catch (error) {
    clearTimeout(timeoutId);
    
    if (error.name === 'AbortError') {
      throw new Error('Request timeout');
    }
    
    throw error;
  }
}

// Retry Logic
async function fetchWithRetry(url, options = {}, retries = 3) {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url, options);
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }
      
      return response.json();
      
    } catch (error) {
      const isLastAttempt = i === retries - 1;
      
      if (isLastAttempt) {
        throw error;
      }
      
      // Exponential backoff
      const delay = Math.pow(2, i) * 1000;
      await new Promise(resolve => setTimeout(resolve, delay));
      
      console.log(`Retry ${i + 1}/${retries}`);
    }
  }
}

// Error Response Handling
class APIError extends Error {
  constructor(message, status, data) {
    super(message);
    this.name = 'APIError';
    this.status = status;
    this.data = data;
  }
}

async function fetchWithCustomErrors(url) {
  const response = await fetch(url);
  
  if (!response.ok) {
    const errorData = await response.json();
    throw new APIError(
      errorData.message,
      response.status,
      errorData
    );
  }
  
  return response.json();
}

// Usage with custom error handling
try {
  await fetchWithCustomErrors('/api/data');
} catch (error) {
  if (error instanceof APIError) {
    console.error(`API Error ${error.status}:`, error.message);
    console.error('Error details:', error.data);
  } else {
    console.error('Network error:', error);
  }
}
```

### 6. CORS and Security

```javascript
// CORS - Cross-Origin Resource Sharing

// Simple Request (automatically includes credentials if same-origin)
fetch('https://api.example.com/data', {
  credentials: 'same-origin' // default
});

// Include credentials in cross-origin requests (cookies, auth)
fetch('https://api.example.com/data', {
  credentials: 'include'
});

// Never send credentials
fetch('https://api.example.com/data', {
  credentials: 'omit'
});

// Preflight Requests
// Browser automatically sends OPTIONS request for:
// - Methods other than GET, HEAD, POST
// - Custom headers
// - Content-Type other than application/x-www-form-urlencoded,
//   multipart/form-data, or text/plain

// Server must respond with appropriate CORS headers:
// Access-Control-Allow-Origin: *
// Access-Control-Allow-Methods: GET, POST, PUT, DELETE
// Access-Control-Allow-Headers: Content-Type, Authorization
// Access-Control-Allow-Credentials: true

// Security Best Practices

// 1. Validate and sanitize user input
const searchUsers = async (userInput) => {
  // Sanitize input
  const sanitized = userInput.replace(/[^\w\s]/gi, '');
  
  const params = new URLSearchParams({ q: sanitized });
  const response = await fetch(`/api/search?${params}`);
  return response.json();
};

// 2. Use HTTPS in production
const secureRequest = async () => {
  if (location.protocol !== 'https:' && location.hostname !== 'localhost') {
    throw new Error('Insecure connection - use HTTPS');
  }
  
  return fetch('https://api.example.com/sensitive-data');
};

// 3. Handle tokens securely
// DON'T store tokens in localStorage (XSS vulnerable)
// DO use httpOnly cookies or secure session storage

// 4. Implement CSRF protection
const postWithCSRF = async (data) => {
  const csrfToken = document.querySelector('meta[name="csrf-token"]').content;
  
  return fetch('/api/action', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': csrfToken
    },
    body: JSON.stringify(data)
  });
};
```

### 7. XMLHttpRequest (Legacy)

```javascript
// XMLHttpRequest - Legacy approach (for reference)

// GET Request
const xhr = new XMLHttpRequest();

xhr.onload = function() {
  if (xhr.status === 200) {
    const data = JSON.parse(xhr.responseText);
    console.log(data);
  } else {
    console.error('Error:', xhr.status);
  }
};

xhr.onerror = function() {
  console.error('Network error');
};

xhr.open('GET', 'https://api.example.com/users');
xhr.send();

// POST Request with XMLHttpRequest
const xhrPost = new XMLHttpRequest();

xhrPost.onload = function() {
  if (xhrPost.status === 200) {
    console.log(JSON.parse(xhrPost.responseText));
  }
};

xhrPost.open('POST', 'https://api.example.com/users');
xhrPost.setRequestHeader('Content-Type', 'application/json');
xhrPost.send(JSON.stringify({ name: 'John' }));

// Progress Events
xhr.upload.onprogress = function(event) {
  if (event.lengthComputable) {
    const percentComplete = (event.loaded / event.total) * 100;
    console.log(`Upload: ${percentComplete}%`);
  }
};

// Modern alternative: Fetch with progress (requires workaround)
async function fetchWithProgress(url, onProgress) {
  const response = await fetch(url);
  const reader = response.body.getReader();
  const contentLength = +response.headers.get('Content-Length');
  
  let receivedLength = 0;
  const chunks = [];
  
  while (true) {
    const { done, value } = await reader.read();
    
    if (done) break;
    
    chunks.push(value);
    receivedLength += value.length;
    
    onProgress(receivedLength, contentLength);
  }
  
  const blob = new Blob(chunks);
  return blob;
}

// Why use Fetch over XMLHttpRequest:
// - Promise-based (better async handling)
// - Cleaner, more modern syntax
// - Better error handling
// - Standardized across browsers
// - Easier to compose and test
```

## 💡 Key Concepts

### HTTP Methods Understanding

- **GET**: Retrieve data (idempotent, safe)
- **POST**: Create new resource (not idempotent)
- **PUT**: Replace entire resource (idempotent)
- **PATCH**: Partial update (idempotent)
- **DELETE**: Remove resource (idempotent)
- **HEAD**: Like GET but response has no body
- **OPTIONS**: Check allowed methods (CORS preflight)

### RESTful API Principles

1. **Resources**: Everything is a resource with unique URI
2. **HTTP Methods**: Use appropriate method for action
3. **Stateless**: Each request contains all needed information
4. **Uniform Interface**: Consistent API design
5. **JSON**: Standard data format for APIs

### Status Code Ranges

- **2xx**: Success (200 OK, 201 Created, 204 No Content)
- **3xx**: Redirection (301 Moved, 304 Not Modified)
- **4xx**: Client Error (400 Bad Request, 401 Unauthorized, 404 Not Found)
- **5xx**: Server Error (500 Internal Error, 503 Service Unavailable)

### Common Pitfalls

- Not checking response.ok before parsing
- Using response.json() multiple times
- Not handling network errors
- Forgetting Content-Type header
- Not implementing timeouts
- Storing tokens insecurely

## 🛠️ Practical Exercises

### Exercise 1: CRUD Operations
Build complete CRUD functionality:
- Create new resources with POST
- Read resources with GET
- Update with PUT/PATCH
- Delete resources
- Handle all error cases
- Implement optimistic updates

### Exercise 2: Search and Filter
Implement advanced search:
- Query parameter building
- Debounced search input
- Filter combinations
- Sorting options
- Pagination handling
- Loading and empty states

### Exercise 3: File Upload
Create file upload system:
- Single and multiple file uploads
- Progress tracking
- File type validation
- Size limits
- Preview before upload
- Error handling and retry

### Exercise 4: API Client Library
Build reusable API client:
- Base URL configuration
- Default headers
- Request/response interceptors
- Automatic retry logic
- Error handling
- Type-safe methods

### Exercise 5: Real-time Data
Implement data synchronization:
- Polling for updates
- WebSocket fallback consideration
- Conflict resolution
- Offline support
- Queue failed requests
- Background sync

## 🎨 Project: API Dashboard

Build a comprehensive dashboard consuming multiple API endpoints.

**Core Features:**
- User authentication (login/logout)
- Dashboard with multiple data widgets
- CRUD operations for user resources
- Search and filter functionality
- Data visualization from API
- Real-time updates (polling)
- Error handling and retry logic

**API Integration:**
- Multiple endpoints (users, posts, comments, stats)
- Parallel requests for dashboard data
- Sequential requests for dependent data
- Proper loading states
- Error boundaries
- Success/error notifications

**Data Management:**
- Local state cache
- Optimistic updates
- Background refresh
- Pagination implementation
- Sorting and filtering
- Data export functionality

**Advanced Features:**
- Request cancellation (AbortController)
- Request deduplication
- Response caching with TTL
- Offline mode support
- Request queue for failed requests
- Upload progress tracking
- Download with progress
- Authentication token refresh
- Rate limiting handling

**UI Requirements:**
- Loading skeletons
- Error messages
- Empty states
- Retry buttons
- Success feedback
- Form validation
- Responsive design

## 📖 Resources to Explore

### Documentation
- [MDN: Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [MDN: Using Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)
- [MDN: HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [MDN: CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

### Essential Reading
- REST API Tutorial
- HTTP: The Definitive Guide
- Understanding CORS
- API Security Best Practices

### Articles
- Fetch API vs Axios vs XMLHttpRequest
- Handling API Errors Effectively
- Building a Robust API Client
- CORS Explained Simply
- Authentication Methods Comparison

### Video Content
- Fetch API Crash Course
- REST API Concepts
- Building API Clients
- CORS and Security

### Public APIs for Practice
- JSONPlaceholder (fake REST API)
- PokéAPI (Pokémon data)
- OpenWeather API
- GitHub API
- NewsAPI

## 🔍 Interview Questions

### Fundamental Questions

1. **What is the Fetch API and how does it differ from XMLHttpRequest?**
   - Promise-based vs callback
   - Modern vs legacy
   - Advantages of Fetch

2. **Explain the different HTTP methods and when to use each.**
   - GET, POST, PUT, PATCH, DELETE
   - Idempotency
   - Safe methods

3. **What is CORS and why does it exist?**
   - Cross-origin security
   - Preflight requests
   - CORS headers

4. **How do you handle errors in Fetch API?**
   - Network errors
   - HTTP errors
   - Response parsing errors

5. **What are HTTP status codes? Name common ones.**
   - 2xx, 3xx, 4xx, 5xx ranges
   - Specific codes and meanings

### Practical Questions

6. **Implement a fetch request with timeout.**

7. **Create a POST request with JSON body and error handling.**

8. **Build a function to upload a file with progress tracking.**

9. **Implement retry logic for failed requests.**

10. **Create an API client with default headers and base URL.**

### Advanced Questions

11. **How would you implement request cancellation?**

12. **Explain request/response interceptors and their use cases.**

13. **How do you handle authentication token refresh?**

14. **Design a caching strategy for API responses.**

15. **Implement request deduplication for identical concurrent requests.**

## ✅ Success Criteria

By the end of Day 12, you should be able to:

- [ ] Make HTTP requests with Fetch API
- [ ] Use all HTTP methods appropriately
- [ ] Handle different data formats (JSON, FormData)
- [ ] Set and read request/response headers
- [ ] Implement proper error handling
- [ ] Understand CORS and security concerns
- [ ] Build API client with retry logic
- [ ] Implement request timeout
- [ ] Handle file uploads
- [ ] Work with authentication
- [ ] Parse and validate responses
- [ ] Complete the API dashboard project

## 🚀 Next Steps

Tomorrow (Day 13), you'll master:
- Array methods: map, filter, reduce, find, etc.
- Functional programming concepts
- Immutability and pure functions
- Function composition and chaining
- Higher-order functions
- Real-world data transformation patterns

**Preparation:**
- Review array basics
- Practice with array iteration
- Think about data transformation scenarios

**Keep building** - API integration is crucial for modern web applications!
