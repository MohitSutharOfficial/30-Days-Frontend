# Day 25: Build Tools (Webpack, Vite) & Module Bundlers 📦

## 🎯 Learning Objectives

By the end of Day 25, you will:
- Understand module bundlers and their purpose in modern web development
- Configure Webpack from scratch with loaders and plugins
- Master Vite for lightning-fast development experience
- Implement code splitting and lazy loading strategies
- Optimize bundle size with tree shaking and minification
- Configure development and production builds
- Set up Hot Module Replacement (HMR) for better DX
- Analyze and optimize bundle performance
- Understand asset management and optimization

---

## 📚 Topics to Study

### 1. **Module Bundlers Fundamentals**

```javascript
// What are Module Bundlers?
/*
Module bundlers take JavaScript modules with dependencies and
bundle them into optimized files for the browser.

Problems they solve:
1. Browser compatibility with modules
2. Dependency management
3. Code optimization (minification, tree shaking)
4. Asset management (CSS, images, fonts)
5. Development experience (HMR, source maps)

Popular Bundlers:
- Webpack: Highly configurable, mature ecosystem
- Vite: Lightning fast, modern defaults
- Rollup: Library bundling, ES modules
- Parcel: Zero-config bundler
- esbuild: Extremely fast, written in Go
*/

// ES6 Modules (Input for bundlers)
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// app.js
import { add, subtract } from './math.js';
console.log(add(5, 3)); // 8

// The bundler combines these into a single optimized file
```

### 2. **Webpack Configuration**

```javascript
// Installation
// npm install --save-dev webpack webpack-cli webpack-dev-server

// webpack.config.js - Basic Configuration
const path = require('path');

module.exports = {
  // Entry point of your application
  entry: './src/index.js',
  
  // Output configuration
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
    clean: true // Clean dist folder before each build
  },
  
  // Development mode (development, production, none)
  mode: 'development',
  
  // Source maps for debugging
  devtool: 'source-map',
  
  // Development server
  devServer: {
    static: './dist',
    port: 3000,
    hot: true, // Hot Module Replacement
    open: true
  }
};

// package.json scripts
/*
{
  "scripts": {
    "start": "webpack serve --mode development",
    "build": "webpack --mode production",
    "watch": "webpack --watch"
  }
}
*/
```

### 3. **Webpack Loaders**

```javascript
// Loaders transform files into modules

// CSS Loader Configuration
// npm install --save-dev style-loader css-loader

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader']
        // Loaders are executed right-to-left
        // css-loader: interprets @import and url()
        // style-loader: injects CSS into the DOM
      }
    ]
  }
};

// Sass/SCSS Loader
// npm install --save-dev sass-loader sass
{
  test: /\.s[ac]ss$/i,
  use: ['style-loader', 'css-loader', 'sass-loader']
}

// Babel Loader for JavaScript/TypeScript
// npm install --save-dev babel-loader @babel/core @babel/preset-env @babel/preset-react
{
  test: /\.(js|jsx)$/,
  exclude: /node_modules/,
  use: {
    loader: 'babel-loader',
    options: {
      presets: [
        '@babel/preset-env',
        '@babel/preset-react'
      ]
    }
  }
}

// TypeScript Loader
// npm install --save-dev ts-loader
{
  test: /\.tsx?$/,
  use: 'ts-loader',
  exclude: /node_modules/
}

// Asset Loaders (Webpack 5)
{
  test: /\.(png|svg|jpg|jpeg|gif)$/i,
  type: 'asset/resource' // Emits files to output directory
}

{
  test: /\.(woff|woff2|eot|ttf|otf)$/i,
  type: 'asset/resource'
}

{
  test: /\.svg$/i,
  type: 'asset/inline' // Inlines as data URI
}

// File Loader (Webpack 4)
// npm install --save-dev file-loader
{
  test: /\.(png|svg|jpg|jpeg|gif)$/i,
  use: ['file-loader']
}
```

### 4. **Webpack Plugins**

```javascript
// Plugins extend Webpack's capabilities

const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const CopyWebpackPlugin = require('copy-webpack-plugin');
const webpack = require('webpack');

module.exports = {
  plugins: [
    // HTML Plugin - generates HTML file
    new HtmlWebpackPlugin({
      template: './src/index.html',
      title: 'My App',
      minify: {
        removeComments: true,
        collapseWhitespace: true
      }
    }),
    
    // Extract CSS into separate files
    new MiniCssExtractPlugin({
      filename: 'styles/[name].[contenthash].css'
    }),
    
    // Clean dist folder
    new CleanWebpackPlugin(),
    
    // Copy static files
    new CopyWebpackPlugin({
      patterns: [
        { from: 'public', to: 'public' }
      ]
    }),
    
    // Define environment variables
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
      'process.env.API_URL': JSON.stringify('https://api.example.com')
    }),
    
    // Bundle analyzer (optional)
    // npm install --save-dev webpack-bundle-analyzer
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false
    })
  ]
};
```

### 5. **Code Splitting & Lazy Loading**

```javascript
// 1. Entry Points Splitting
module.exports = {
  entry: {
    app: './src/app.js',
    admin: './src/admin.js'
  },
  output: {
    filename: '[name].bundle.js'
  }
};

// 2. Prevent Duplication with SplitChunksPlugin
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        // Extract vendor libraries
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10
        },
        // Extract common code
        common: {
          minChunks: 2,
          priority: 5,
          reuseExistingChunk: true
        }
      }
    },
    // Runtime chunk for better long-term caching
    runtimeChunk: 'single'
  }
};

// 3. Dynamic Imports (Code Splitting)
// Before
import { heavyLibrary } from './heavy-library';

// After - Lazy load when needed
button.addEventListener('click', async () => {
  const { heavyLibrary } = await import(
    /* webpackChunkName: "heavy-library" */
    './heavy-library'
  );
  heavyLibrary.doSomething();
});

// React Lazy Loading
import React, { lazy, Suspense } from 'react';

// Lazy load component
const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}

// Route-based code splitting
import { Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Dashboard = lazy(() => import('./pages/Dashboard'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/dashboard" element={<Dashboard />} />
      </Routes>
    </Suspense>
  );
}
```

### 6. **Production Optimization**

```javascript
// webpack.config.prod.js
const TerserPlugin = require('terser-webpack-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
const CompressionPlugin = require('compression-webpack-plugin');

module.exports = {
  mode: 'production',
  
  // Optimizations
  optimization: {
    minimize: true,
    minimizer: [
      // Minify JavaScript
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true // Remove console.logs
          }
        }
      }),
      // Minify CSS
      new CssMinimizerPlugin()
    ],
    
    // Code splitting configuration
    splitChunks: {
      chunks: 'all',
      maxSize: 244000, // 244kb chunks
    },
    
    // Module IDs for better caching
    moduleIds: 'deterministic',
    
    // Runtime chunk
    runtimeChunk: 'single'
  },
  
  // Output with content hash for caching
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
    clean: true
  },
  
  // Performance hints
  performance: {
    maxEntrypointSize: 512000,
    maxAssetSize: 512000,
    hints: 'warning'
  },
  
  plugins: [
    // Gzip compression
    new CompressionPlugin({
      algorithm: 'gzip',
      test: /\.(js|css|html|svg)$/,
      threshold: 10240, // Only compress files > 10KB
      minRatio: 0.8
    })
  ]
};

// Tree Shaking (automatically enabled in production)
/*
Tree shaking removes unused code from final bundle
Requirements:
1. Use ES6 module syntax (import/export)
2. Ensure sideEffects field in package.json
3. Use production mode
*/

// package.json
{
  "sideEffects": false
  // or specify files with side effects
  // "sideEffects": ["*.css", "*.scss"]
}
```

### 7. **Vite Configuration**

```javascript
// Why Vite?
/*
- Lightning-fast cold server start
- Instant HMR (Hot Module Replacement)
- Native ES modules in development
- Optimized production builds (Rollup)
- TypeScript, JSX, CSS out of the box
- Better developer experience
*/

// Installation
// npm create vite@latest my-app -- --template react-ts
// npm install

// vite.config.js/ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  // Plugins
  plugins: [react()],
  
  // Path resolution
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils')
    }
  },
  
  // Development server
  server: {
    port: 3000,
    open: true,
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  },
  
  // Build options
  build: {
    outDir: 'dist',
    sourcemap: true,
    
    // Rollup options
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'router': ['react-router-dom']
        }
      }
    },
    
    // Chunk size warnings
    chunkSizeWarningLimit: 1000,
    
    // Minification
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true
      }
    }
  },
  
  // Environment variables
  define: {
    __APP_VERSION__: JSON.stringify('1.0.0')
  },
  
  // CSS options
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "@/styles/variables.scss";`
      }
    },
    modules: {
      localsConvention: 'camelCase'
    }
  }
});

// Environment Variables in Vite
/*
.env files:
.env                # loaded in all cases
.env.local          # local overrides (gitignored)
.env.[mode]         # mode-specific
.env.[mode].local   # mode-specific local

Only variables prefixed with VITE_ are exposed:
VITE_API_URL=https://api.example.com

Access in code:
import.meta.env.VITE_API_URL
*/

// package.json scripts
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "build:analyze": "vite-bundle-visualizer"
  }
}
```

### 8. **Bundle Analysis & Optimization**

```javascript
// Webpack Bundle Analyzer
// npm install --save-dev webpack-bundle-analyzer

const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      reportFilename: 'bundle-report.html',
      openAnalyzer: false
    })
  ]
};

// Run: npm run build
// Opens visual report showing bundle composition

// Vite Bundle Visualizer
// npm install --save-dev rollup-plugin-visualizer

import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true
    })
  ]
});

// Optimization Strategies
/*
1. Code Splitting
   - Route-based splitting
   - Component-based splitting
   - Vendor splitting

2. Tree Shaking
   - Use ES6 modules
   - Mark side effects in package.json
   - Analyze unused code

3. Compression
   - Enable Gzip/Brotli compression
   - Use compression plugins
   - Configure server compression

4. Asset Optimization
   - Image optimization (WebP, AVIF)
   - SVG optimization (SVGO)
   - Font subsetting

5. Caching Strategy
   - Content hash in filenames
   - Long-term caching headers
   - Service Worker caching

6. Lazy Loading
   - Route-based lazy loading
   - Component lazy loading
   - Third-party script lazy loading

7. Dependency Optimization
   - Use lighter alternatives
   - Remove unused dependencies
   - Use dynamic imports for heavy libraries
*/
```

---

## 💡 Key Concepts

1. **Module Bundling**: Combining multiple files into optimized bundles
2. **Code Splitting**: Breaking bundles into smaller chunks loaded on demand
3. **Tree Shaking**: Removing unused code from final bundle
4. **Hot Module Replacement**: Update modules without full page reload
5. **Source Maps**: Map bundled code back to source for debugging
6. **Asset Pipeline**: Processing and optimizing all asset types
7. **Development vs Production**: Different optimizations for each environment
8. **Bundle Analysis**: Understanding and optimizing bundle composition

---

## 🛠️ Practical Exercises

### Exercise 1: Webpack from Scratch
Configure Webpack for a React application with development and production modes.

### Exercise 2: Code Splitting Implementation
Implement route-based and component-based code splitting in a multi-page app.

### Exercise 3: Bundle Size Optimization
Analyze and optimize a bundle that's too large, achieving 50% size reduction.

### Exercise 4: Vite Migration
Migrate an existing Create React App to Vite and measure performance improvements.

### Exercise 5: Custom Webpack Plugin
Create a custom Webpack plugin that generates a build manifest.

---

## 🎨 Project: Optimized Multi-Page Application

Build a production-ready application with optimal bundle configuration:

**Requirements:**
1. Multiple entry points for different sections
2. Vendor code splitting
3. Route-based lazy loading
4. Image and asset optimization
5. CSS extraction and minification
6. Source maps for debugging
7. Environment variable configuration
8. Development and production builds
9. Bundle analysis and optimization
10. Compressed assets (Gzip/Brotli)

**Performance Goals:**
- Initial bundle < 200KB (gzipped)
- Time to Interactive < 3 seconds
- Lighthouse score > 90
- All routes lazy-loaded
- Vendor chunk cached separately

---

## 📖 Resources to Explore

### Documentation
- **Webpack Docs**: https://webpack.js.org/
- **Vite Docs**: https://vitejs.dev/
- **Rollup Docs**: https://rollupjs.org/

### Video Tutorials
- **Webpack 5 Full Tutorial** by Colt Steele
- **Vite Crash Course** by Traversy Media
- **Advanced Webpack** by freeCodeCamp

### Articles & Guides
- "The Cost of JavaScript" by Addy Osmani
- "Webpack Performance Guide"
- "Why Vite is the Future"
- "Code Splitting Strategies"

### Tools
- **Webpack Bundle Analyzer**: Visualize bundle composition
- **Lighthouse**: Performance auditing
- **WebPageTest**: Real-world performance testing
- **Source Map Explorer**: Analyze bundle contents

---

## 🔍 Interview Questions

### Basic
1. What is a module bundler and why do we need it?
2. Explain the difference between Webpack and Vite.
3. What are loaders in Webpack?
4. What is code splitting?
5. Explain tree shaking.

### Intermediate
1. How does Hot Module Replacement work?
2. What are the different code splitting strategies?
3. Explain the difference between development and production builds.
4. How do you optimize bundle size in Webpack?
5. What are Webpack plugins and how do they differ from loaders?
6. How do source maps work?
7. Explain the concept of chunking in Webpack.

### Advanced
1. How would you implement a custom Webpack loader?
2. Explain the splitChunks optimization algorithm.
3. How does Vite achieve faster builds than Webpack?
4. What is the difference between static and dynamic imports?
5. How would you set up micro-frontends with Webpack Module Federation?
6. Explain the caching strategy for long-term caching.
7. How do you debug bundle size issues?

### Practical
1. Your bundle is 5MB. How would you optimize it?
2. Design a build pipeline for a monorepo with shared dependencies.
3. How would you migrate from Webpack to Vite?
4. Implement a progressive loading strategy for a large application.

---

## ✅ Success Criteria

- [ ] Understand module bundling concepts
- [ ] Configure Webpack from scratch
- [ ] Set up and use Vite effectively
- [ ] Implement code splitting strategies
- [ ] Optimize bundle size and performance
- [ ] Configure loaders and plugins
- [ ] Set up development and production builds
- [ ] Analyze bundles and identify optimization opportunities
- [ ] Implement lazy loading for routes and components
- [ ] Understand tree shaking and minification

---

## 🚀 Next Steps

Tomorrow (Day 26), you'll learn:
- **Version Control with Git**: Advanced workflows and best practices
- **Branching strategies**: GitFlow, trunk-based development
- **Collaboration**: Pull requests, code reviews, conflict resolution
- **Git hooks and automation**

**Preparation:**
- Review basic Git commands
- Understand branching concepts
- Explore GitHub/GitLab features
- Practice merge conflict resolution

---

**Remember**: Build tools are the foundation of modern web development. Master them for optimal performance! 📦
