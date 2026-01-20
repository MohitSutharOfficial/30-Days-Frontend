# Day 04: Responsive Design & Media Queries

## 🎯 Learning Objectives

By the end of today, you will understand:
- Responsive design principles and mobile-first approach
- Media queries and breakpoints
- Responsive units and viewport units
- Responsive images and typography
- Modern responsive design techniques

## 📚 Topics to Study

### 1. Responsive Design Principles

**Core Concepts:**
- **Fluid Layouts**: Use percentages and flexible units instead of fixed widths
- **Flexible Images**: Scale images within their containers
- **Media Queries**: Apply different styles for different screen sizes
- **Mobile-First**: Start with mobile design, then enhance for larger screens
- **Progressive Enhancement**: Basic functionality for all, enhanced features for capable devices

### 2. Media Queries Syntax

```css
/* Basic media query structure */
@media media-type and (condition) {
  /* CSS rules */
}

/* Screen size queries */
@media (min-width: 768px) {
  /* Styles for screens >= 768px */
}

@media (max-width: 767px) {
  /* Styles for screens <= 767px */
}

/* Combining conditions */
@media (min-width: 768px) and (max-width: 1024px) {
  /* Tablet range */
}

/* Device orientation */
@media (orientation: landscape) {
  /* Landscape mode */
}

@media (orientation: portrait) {
  /* Portrait mode */
}

/* Resolution queries */
@media (min-resolution: 2dppx) {
  /* Retina displays */
}

/* Prefer reduced motion */
@media (prefers-reduced-motion: reduce) {
  * {
    animation: none !important;
    transition: none !important;
  }
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  /* Dark mode styles */
}

/* Print styles */
@media print {
  /* Print-specific styles */
}
```

### 3. Common Breakpoints

```css
/* Mobile-first approach */

/* Mobile (default) */
.container {
  width: 100%;
  padding: 1rem;
}

/* Small tablets (≥576px) */
@media (min-width: 576px) {
  .container {
    max-width: 540px;
    margin: 0 auto;
  }
}

/* Tablets (≥768px) */
@media (min-width: 768px) {
  .container {
    max-width: 720px;
  }
}

/* Desktop (≥992px) */
@media (min-width: 992px) {
  .container {
    max-width: 960px;
  }
}

/* Large desktop (≥1200px) */
@media (min-width: 1200px) {
  .container {
    max-width: 1140px;
  }
}

/* Extra large (≥1400px) */
@media (min-width: 1400px) {
  .container {
    max-width: 1320px;
  }
}
```

### 4. Responsive Typography

```css
/* Fluid typography using clamp() */
h1 {
  font-size: clamp(1.5rem, 5vw, 3rem);
  /* min: 1.5rem, preferred: 5vw, max: 3rem */
}

/* Responsive line height */
body {
  font-size: 16px;
  line-height: 1.5;
}

@media (min-width: 768px) {
  body {
    font-size: 18px;
    line-height: 1.6;
  }
}

/* Viewport-based units */
.hero-title {
  font-size: 8vw; /* 8% of viewport width */
}

/* CSS custom properties for scaling */
:root {
  --base-size: 16px;
}

@media (min-width: 768px) {
  :root {
    --base-size: 18px;
  }
}

body {
  font-size: var(--base-size);
}
```

### 5. Responsive Images

```html
<!-- Responsive image in HTML -->
<img 
  src="image-small.jpg"
  srcset="image-small.jpg 400w,
          image-medium.jpg 800w,
          image-large.jpg 1200w"
  sizes="(max-width: 400px) 100vw,
         (max-width: 800px) 50vw,
         33vw"
  alt="Description"
/>

<!-- Picture element for art direction -->
<picture>
  <source media="(min-width: 1200px)" srcset="desktop.jpg">
  <source media="(min-width: 768px)" srcset="tablet.jpg">
  <img src="mobile.jpg" alt="Description">
</picture>
```

```css
/* Responsive images in CSS */
img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* Background images */
.hero {
  background-image: url('mobile-hero.jpg');
  background-size: cover;
  background-position: center;
}

@media (min-width: 768px) {
  .hero {
    background-image: url('desktop-hero.jpg');
  }
}

/* Object-fit for image containers */
.thumbnail {
  width: 200px;
  height: 200px;
  object-fit: cover; /* or contain, fill, scale-down */
}
```

### 6. Responsive Layout Patterns

```css
/* Sidebar Pattern - Stack on mobile, side-by-side on desktop */
.layout {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

@media (min-width: 768px) {
  .layout {
    grid-template-columns: 250px 1fr;
  }
}

/* Column Drop Pattern */
.columns {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

@media (min-width: 768px) {
  .columns {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 1024px) {
  .columns {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* Container Query (Modern) */
.container {
  container-type: inline-size;
}

@container (min-width: 700px) {
  .card {
    display: grid;
    grid-template-columns: 200px 1fr;
  }
}
```

### 7. Mobile Navigation Patterns

```css
/* Mobile menu - hidden by default */
.nav-menu {
  display: none;
  position: fixed;
  top: 60px;
  left: 0;
  width: 100%;
  background: white;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

.nav-menu.active {
  display: flex;
  flex-direction: column;
}

/* Desktop menu - always visible */
@media (min-width: 768px) {
  .nav-menu {
    display: flex;
    flex-direction: row;
    position: static;
    box-shadow: none;
  }
  
  .hamburger {
    display: none;
  }
}
```

## 💡 Key Concepts

### Mobile-First Strategy
```css
/* Start with mobile (no media query) */
.element {
  font-size: 14px;
  padding: 10px;
}

/* Enhance for larger screens */
@media (min-width: 768px) {
  .element {
    font-size: 16px;
    padding: 20px;
  }
}
```

### Viewport Meta Tag (Essential)
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

### CSS Custom Properties for Theming
```css
:root {
  --spacing-unit: 0.5rem;
  --max-width: 1200px;
}

@media (min-width: 768px) {
  :root {
    --spacing-unit: 1rem;
  }
}
```

### Modern CSS Functions
```css
/* clamp() - Responsive sizing */
.title {
  font-size: clamp(1.5rem, 2vw + 1rem, 3rem);
}

/* min() and max() */
.container {
  width: min(90%, 1200px);
  padding: max(1rem, 5vw);
}
```

## 🛠️ Practical Exercises

### Exercise 1: Responsive Navigation
Create a navigation that:
- Shows hamburger menu on mobile
- Expands to horizontal menu on tablet+
- Includes logo and menu items
- Has smooth transitions

### Exercise 2: Card Grid
Build a responsive card grid:
- 1 column on mobile
- 2 columns on tablet
- 3-4 columns on desktop
- Maintains aspect ratio

### Exercise 3: Responsive Typography Scale
Implement a fluid typography system:
- Base size scales with viewport
- Maintains readable line lengths
- Uses clamp() for smooth scaling

## 🎨 Project: Fully Responsive Website

Create a complete responsive website with:

### 1. Header
- Logo and navigation
- Mobile hamburger menu
- Sticky on scroll
- Search bar (hide on mobile, show on desktop)

### 2. Hero Section
- Full viewport height on desktop
- Responsive background image
- Overlay text with good contrast
- CTA button that scales appropriately

### 3. Features Grid
- 1 column mobile
- 2 columns tablet
- 3 columns desktop
- Icons, titles, descriptions

### 4. Content Section
- Two-column layout (text + image)
- Stacks on mobile
- Alternating layout on desktop

### 5. Gallery
- Responsive image grid
- Masonry layout on desktop
- Single column on mobile
- Lightbox consideration (structure only)

### 6. Testimonials
- Carousel on mobile (structure)
- 3-column grid on desktop
- Equal height cards

### 7. Footer
- 4-column layout on desktop
- Stacked sections on mobile
- Social links
- Newsletter signup form

**Requirements:**
- Mobile-first approach
- All content accessible at all breakpoints
- Images scale appropriately
- Typography is readable on all devices
- Touch-friendly tap targets (min 44x44px)
- Test at multiple breakpoints
- Consider performance (loading strategies)

**Breakpoints to Test:**
- 320px (small mobile)
- 375px (mobile)
- 768px (tablet)
- 1024px (desktop)
- 1440px (large desktop)

## 📖 Resources to Explore

### Documentation
- MDN: Responsive Design
- MDN: Using Media Queries
- Web.dev: Responsive Web Design Basics

### Tools
- Chrome DevTools Device Mode
- Firefox Responsive Design Mode
- Responsively App (testing multiple devices)
- BrowserStack (real device testing)

### Testing
- Mobile-Friendly Test (Google)
- Responsive Breakpoint Generator
- Am I Responsive?

### Reading
- "Responsive Web Design" by Ethan Marcotte
- "Mobile First" by Luke Wroblewski
- Material Design Responsive Guidelines

## 🔍 Interview Questions to Prepare

1. What is responsive web design?
2. Explain the mobile-first approach and its benefits.
3. What is the viewport meta tag and why is it important?
4. How do media queries work?
5. What are common breakpoints and how do you choose them?
6. Explain the difference between mobile-first and desktop-first.
7. How do you make images responsive?
8. What is the `srcset` attribute?
9. How would you handle responsive typography?
10. What is the difference between `em`, `rem`, and `vh/vw` units?
11. How do you test responsive designs?
12. What are container queries and how are they different from media queries?
13. Explain touch-friendly design principles.
14. How do you optimize performance for mobile devices?

## ✅ Success Criteria

- [ ] Understand responsive design principles thoroughly
- [ ] Can write media queries confidently
- [ ] Know mobile-first development approach
- [ ] Can create fluid layouts that work on all devices
- [ ] Understand responsive images and typography
- [ ] Can test designs at multiple breakpoints
- [ ] Built a fully responsive multi-page layout

## 🚀 Next Steps

Tomorrow, you'll bring your designs to life with CSS animations and transitions!

---

**Time Investment**: 5-6 hours  
**Difficulty**: Intermediate  
**Prerequisites**: Days 01-03
