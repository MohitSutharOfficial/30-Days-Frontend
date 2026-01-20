# Day 27: CI/CD, Deployment & Performance 🚀

## 🎯 Learning Objectives

By the end of Day 27, you will:
- Understand CI/CD principles and benefits
- Configure automated testing and deployment pipelines
- Deploy applications to various hosting platforms
- Implement performance optimization strategies
- Set up monitoring and error tracking
- Configure environment-specific builds
- Understand caching strategies and CDN usage
- Implement progressive web app (PWA) features
- Monitor and analyze application performance

---

## 📚 Topics to Study

### 1. **CI/CD Fundamentals**

```yaml
# Continuous Integration (CI)
# - Automatically build and test code changes
# - Catch bugs early
# - Ensure code quality
# - Fast feedback loop

# Continuous Deployment (CD)
# - Automatically deploy to production
# - Faster time to market
# - Reduce manual errors
# - Consistent deployments

# CI/CD Pipeline Stages:
# 1. Source: Code commit triggers pipeline
# 2. Build: Compile/bundle application
# 3. Test: Run automated tests
# 4. Deploy: Deploy to staging/production
# 5. Monitor: Track performance and errors

# Benefits:
# - Faster development cycles
# - Higher code quality
# - Reduced manual work
# - Consistent deployments
# - Early bug detection
# - Better collaboration
```

### 2. **GitHub Actions**

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

# Trigger conditions
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

# Environment variables
env:
  NODE_VERSION: '18'

jobs:
  # Lint and format check
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run ESLint
        run: npm run lint
      
      - name: Check formatting
        run: npm run format:check
  
  # Run tests
  test:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
  
  # Build application
  build:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
        env:
          REACT_APP_API_URL: ${{ secrets.API_URL }}
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: build/
  
  # Deploy to staging
  deploy-staging:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/develop'
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - name: Download build
        uses: actions/download-artifact@v3
        with:
          name: build
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
  
  # Deploy to production
  deploy-production:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://example.com
    steps:
      - name: Download build
        uses: actions/download-artifact@v3
        with:
          name: build
      
      - name: Deploy to Netlify
        uses: nwtgck/actions-netlify@v2.0
        with:
          publish-dir: './build'
          production-deploy: true
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}

# Matrix strategy for multiple environments
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        node-version: [16, 18, 20]
    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm test
```

### 3. **Deployment Platforms**

```yaml
# Vercel Deployment
# vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "build"
      }
    }
  ],
  "routes": [
    {
      "src": "/api/(.*)",
      "dest": "/api/$1"
    },
    {
      "src": "/(.*)",
      "dest": "/$1"
    }
  ],
  "env": {
    "REACT_APP_API_URL": "@api-url"
  }
}

# Netlify Configuration
# netlify.toml
[build]
  command = "npm run build"
  publish = "build"

[build.environment]
  NODE_VERSION = "18"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

[[redirects]]
  from = "/api/*"
  to = "https://api.example.com/:splat"
  status = 200

[context.production]
  environment = { REACT_APP_ENV = "production" }

[context.deploy-preview]
  environment = { REACT_APP_ENV = "staging" }

# AWS S3 + CloudFront
# Deploy script
/*
aws s3 sync build/ s3://my-bucket --delete
aws cloudfront create-invalidation --distribution-id DISTID --paths "/*"
*/

# Docker Deployment
# Dockerfile
FROM node:18-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

# docker-compose.yml
version: '3.8'
services:
  frontend:
    build: .
    ports:
      - "80:80"
    environment:
      - REACT_APP_API_URL=https://api.example.com
```

### 4. **Performance Optimization**

```javascript
// 1. Code Splitting
// Route-based splitting
import { lazy, Suspense } from 'react';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  );
}

// Component-based splitting
const HeavyComponent = lazy(() => 
  import(/* webpackChunkName: "heavy" */ './HeavyComponent')
);

// 2. Image Optimization
/*
- Use WebP format with fallbacks
- Implement lazy loading
- Use responsive images
- Compress images
- Use CDN for image delivery
*/

// Lazy loading images
function LazyImage({ src, alt }) {
  const [imageSrc, setImageSrc] = useState(null);
  const imgRef = useRef();
  
  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setImageSrc(src);
          observer.disconnect();
        }
      },
      { rootMargin: '100px' }
    );
    
    if (imgRef.current) {
      observer.observe(imgRef.current);
    }
    
    return () => observer.disconnect();
  }, [src]);
  
  return (
    <img
      ref={imgRef}
      src={imageSrc || 'placeholder.jpg'}
      alt={alt}
      loading="lazy"
    />
  );
}

// 3. Memoization
import { memo, useMemo, useCallback } from 'react';

// Memoize component
const ExpensiveComponent = memo(({ data }) => {
  return <div>{/* Render data */}</div>;
});

// Memoize computed values
function Component({ items }) {
  const sortedItems = useMemo(() => {
    return items.sort((a, b) => a.value - b.value);
  }, [items]);
  
  return <List items={sortedItems} />;
}

// Memoize callbacks
function Parent() {
  const handleClick = useCallback(() => {
    console.log('Clicked');
  }, []);
  
  return <Child onClick={handleClick} />;
}

// 4. Virtual Scrolling
// Using react-window
import { FixedSizeList } from 'react-window';

function VirtualList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>{items[index].name}</div>
  );
  
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}

// 5. Debouncing and Throttling
function SearchComponent() {
  const [query, setQuery] = useState('');
  
  // Debounce search
  const debouncedSearch = useMemo(
    () =>
      debounce((value) => {
        // API call
        fetchResults(value);
      }, 500),
    []
  );
  
  const handleChange = (e) => {
    setQuery(e.target.value);
    debouncedSearch(e.target.value);
  };
  
  return <input value={query} onChange={handleChange} />;
}

// 6. Web Workers for Heavy Computations
// worker.js
self.addEventListener('message', (e) => {
  const result = heavyComputation(e.data);
  self.postMessage(result);
});

// Component
function Component() {
  const [result, setResult] = useState(null);
  
  useEffect(() => {
    const worker = new Worker('worker.js');
    
    worker.onmessage = (e) => {
      setResult(e.data);
    };
    
    worker.postMessage(data);
    
    return () => worker.terminate();
  }, [data]);
}
```

### 5. **Caching Strategies**

```javascript
// Service Worker for PWA
// public/service-worker.js
const CACHE_NAME = 'my-app-v1';
const urlsToCache = [
  '/',
  '/static/css/main.css',
  '/static/js/main.js'
];

// Install service worker
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
  );
});

// Fetch with cache-first strategy
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => {
        if (response) {
          return response; // Return cached version
        }
        return fetch(event.request); // Fetch from network
      })
  );
});

// Network-first strategy (for API calls)
self.addEventListener('fetch', (event) => {
  event.respondWith(
    fetch(event.request)
      .then((response) => {
        // Clone and cache the response
        const responseClone = response.clone();
        caches.open(CACHE_NAME).then((cache) => {
          cache.put(event.request, responseClone);
        });
        return response;
      })
      .catch(() => {
        // Fallback to cache
        return caches.match(event.request);
      })
  );
});

// HTTP Caching Headers
/*
Cache-Control: public, max-age=31536000, immutable
  - Cache static assets for 1 year

Cache-Control: no-cache
  - Always revalidate with server

Cache-Control: no-store
  - Don't cache at all

ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
  - Conditional requests based on content hash
*/

// CDN Configuration
/*
- Use CDN for static assets
- Enable Gzip/Brotli compression
- Set proper cache headers
- Use HTTP/2 or HTTP/3
- Enable edge caching
*/
```

### 6. **Monitoring and Error Tracking**

```javascript
// Sentry Integration
// npm install @sentry/react

import * as Sentry from '@sentry/react';
import { BrowserTracing } from '@sentry/tracing';

Sentry.init({
  dsn: 'YOUR_SENTRY_DSN',
  integrations: [new BrowserTracing()],
  tracesSampleRate: 1.0,
  environment: process.env.NODE_ENV
});

// Error boundary with Sentry
function App() {
  return (
    <Sentry.ErrorBoundary fallback={<ErrorFallback />}>
      <YourApp />
    </Sentry.ErrorBoundary>
  );
}

// Custom error tracking
Sentry.captureException(new Error('Something went wrong'));
Sentry.captureMessage('User action failed', 'warning');

// Google Analytics / Google Tag Manager
// npm install react-ga4

import ReactGA from 'react-ga4';

ReactGA.initialize('G-XXXXXXXXXX');

// Track page views
useEffect(() => {
  ReactGA.send({ hitType: 'pageview', page: window.location.pathname });
}, []);

// Track events
const handleClick = () => {
  ReactGA.event({
    category: 'User',
    action: 'Clicked Button',
    label: 'Purchase'
  });
};

// Performance Monitoring
// Web Vitals
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

function sendToAnalytics(metric) {
  const body = JSON.stringify(metric);
  // Send to analytics endpoint
  navigator.sendBeacon('/analytics', body);
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getFCP(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);

// Custom performance marks
performance.mark('component-render-start');
// Component renders
performance.mark('component-render-end');
performance.measure(
  'component-render',
  'component-render-start',
  'component-render-end'
);
```

### 7. **Environment Configuration**

```javascript
// .env files
/*
.env                 # Default
.env.local          # Local overrides (gitignored)
.env.development    # Development
.env.test           # Test
.env.production     # Production
*/

// .env.development
REACT_APP_API_URL=http://localhost:3001
REACT_APP_ENV=development
REACT_APP_ENABLE_MOCK=true

// .env.production
REACT_APP_API_URL=https://api.example.com
REACT_APP_ENV=production
REACT_APP_ENABLE_MOCK=false

// config.js
const config = {
  apiUrl: process.env.REACT_APP_API_URL,
  environment: process.env.REACT_APP_ENV,
  enableMock: process.env.REACT_APP_ENABLE_MOCK === 'true',
  version: process.env.REACT_APP_VERSION || '1.0.0'
};

export default config;

// Feature flags
const features = {
  newDashboard: process.env.REACT_APP_FEATURE_NEW_DASHBOARD === 'true',
  betaFeatures: process.env.REACT_APP_ENABLE_BETA === 'true'
};

function App() {
  return (
    <div>
      {features.newDashboard ? <NewDashboard /> : <OldDashboard />}
    </div>
  );
}
```

### 8. **Progressive Web App (PWA)**

```javascript
// manifest.json
{
  "short_name": "My App",
  "name": "My Progressive Web App",
  "icons": [
    {
      "src": "favicon.ico",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    },
    {
      "src": "logo192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "logo512.png",
      "type": "image/png",
      "sizes": "512x512"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#000000",
  "background_color": "#ffffff",
  "orientation": "portrait"
}

// Register service worker
// src/index.js
import * as serviceWorkerRegistration from './serviceWorkerRegistration';

serviceWorkerRegistration.register({
  onUpdate: (registration) => {
    // Show update available notification
    if (registration && registration.waiting) {
      registration.waiting.postMessage({ type: 'SKIP_WAITING' });
    }
  }
});

// Offline capability
// Cache API responses
const cache = new Map();

async function fetchWithCache(url) {
  if (cache.has(url)) {
    return cache.get(url);
  }
  
  try {
    const response = await fetch(url);
    const data = await response.json();
    cache.set(url, data);
    return data;
  } catch (error) {
    // Return cached data if offline
    if (!navigator.onLine && cache.has(url)) {
      return cache.get(url);
    }
    throw error;
  }
}

// Install prompt
let deferredPrompt;

window.addEventListener('beforeinstallprompt', (e) => {
  e.preventDefault();
  deferredPrompt = e;
  // Show install button
});

function handleInstallClick() {
  if (deferredPrompt) {
    deferredPrompt.prompt();
    deferredPrompt.userChoice.then((choiceResult) => {
      if (choiceResult.outcome === 'accepted') {
        console.log('User accepted install');
      }
      deferredPrompt = null;
    });
  }
}
```

---

## 💡 Key Concepts

1. **CI/CD**: Automate testing and deployment
2. **Performance**: Optimize load time and runtime performance
3. **Caching**: Reduce network requests and improve speed
4. **Monitoring**: Track errors and performance metrics
5. **PWA**: Offline capability and app-like experience
6. **Deployment**: Multiple platform options and strategies
7. **Environment Management**: Separate configs for different stages
8. **Error Tracking**: Catch and fix issues quickly

---

## 🛠️ Practical Exercises

### Exercise 1: CI/CD Pipeline
Set up a complete CI/CD pipeline with GitHub Actions.

### Exercise 2: Multi-Platform Deployment
Deploy the same app to Vercel, Netlify, and AWS.

### Exercise 3: Performance Audit
Use Lighthouse to audit and optimize application performance.

### Exercise 4: PWA Implementation
Convert a React app to a Progressive Web App with offline support.

### Exercise 5: Monitoring Setup
Integrate Sentry and Google Analytics for error and usage tracking.

---

## 🎨 Project: Production-Ready Deployment

Deploy a fully optimized application with monitoring:

**Requirements:**
1. Automated CI/CD pipeline
2. Multi-environment deployment (staging, production)
3. Performance optimization (Lighthouse score > 90)
4. PWA capabilities
5. Error tracking with Sentry
6. Analytics integration
7. Proper caching strategies
8. CDN configuration
9. Environment-specific configurations
10. Monitoring dashboard

**Deliverables:**
- Deployed application (staging + production)
- CI/CD pipeline configuration
- Performance report
- Monitoring setup
- Documentation

---

## 📖 Resources to Explore

### Documentation
- **GitHub Actions**: https://docs.github.com/actions
- **Vercel Docs**: https://vercel.com/docs
- **Netlify Docs**: https://docs.netlify.com
- **Web.dev**: Performance guides

### Video Tutorials
- **GitHub Actions Tutorial** by freeCodeCamp
- **Deployment Strategies** by Traversy Media
- **Web Performance** by Google Chrome Developers

### Tools
- **Lighthouse**: Performance auditing
- **WebPageTest**: Performance testing
- **Sentry**: Error tracking
- **New Relic**: Application monitoring
- **Vercel/Netlify**: Deployment platforms

---

## 🔍 Interview Questions

### Basic
1. What is CI/CD?
2. Why is performance optimization important?
3. What is a service worker?
4. How do you deploy a React application?
5. What is lazy loading?

### Intermediate
1. Explain different caching strategies.
2. How do you optimize bundle size?
3. What are the Core Web Vitals?
4. How does code splitting improve performance?
5. Explain the difference between staging and production environments.
6. What is a PWA and its benefits?
7. How do you implement error tracking?

### Advanced
1. Design a CI/CD pipeline for a microservices application.
2. How would you optimize performance for a data-heavy application?
3. Explain different deployment strategies (blue-green, canary, rolling).
4. How do you implement A/B testing in production?
5. What's your strategy for zero-downtime deployments?
6. How do you monitor and debug production issues?
7. Explain service worker caching strategies.

### Practical
1. Your app is slow. How do you diagnose and fix it?
2. Design a deployment strategy for a global application.
3. How would you implement feature flags?
4. Debug a failed production deployment.

---

## ✅ Success Criteria

- [ ] Set up automated CI/CD pipeline
- [ ] Deploy to multiple platforms
- [ ] Achieve Lighthouse score > 90
- [ ] Implement proper caching strategies
- [ ] Set up error tracking and monitoring
- [ ] Configure environment variables properly
- [ ] Implement PWA features
- [ ] Optimize bundle size and load time
- [ ] Set up analytics tracking
- [ ] Create deployment documentation

---

## 🚀 Next Steps

Tomorrow (Day 28), you'll learn:
- **Web Security**: OWASP Top 10, authentication, authorization
- **Best Practices**: Code quality, accessibility, SEO
- **Security headers and CSP**
- **API security and data protection**

**Preparation:**
- Review authentication concepts
- Understand common vulnerabilities
- Explore OWASP guidelines
- Learn about HTTPS and encryption

---

**Remember**: Fast, reliable deployments with proper monitoring are key to successful applications! 🚀
