# Day 05: CSS Animations & Transitions

## 🎯 Learning Objectives

By the end of today, you will understand:
- CSS transitions for smooth property changes
- CSS animations and keyframes
- Transform properties for 2D and 3D effects
- Animation timing functions and performance
- Best practices for animations

## 📚 Topics to Study

### 1. CSS Transitions

```css
/* Basic transition syntax */
.element {
  transition: property duration timing-function delay;
}

/* Examples */
.button {
  background-color: blue;
  transition: background-color 0.3s ease-in-out;
}

.button:hover {
  background-color: darkblue;
}

/* Multiple transitions */
.card {
  transition: 
    transform 0.3s ease,
    box-shadow 0.3s ease,
    opacity 0.2s linear;
}

/* Transition all properties */
.element {
  transition: all 0.3s ease;
}

/* Individual properties */
.element {
  transition-property: transform, opacity;
  transition-duration: 0.3s, 0.2s;
  transition-timing-function: ease-out, linear;
  transition-delay: 0s, 0.1s;
}
```

### 2. Transform Properties

```css
/* 2D Transforms */
.element {
  /* Translate (move) */
  transform: translate(50px, 100px);
  transform: translateX(50px);
  transform: translateY(100px);
  
  /* Scale */
  transform: scale(1.5);
  transform: scale(1.5, 0.5); /* x, y */
  transform: scaleX(1.5);
  
  /* Rotate */
  transform: rotate(45deg);
  
  /* Skew */
  transform: skew(10deg, 20deg);
  transform: skewX(10deg);
  
  /* Combining transforms */
  transform: translate(50px, 50px) rotate(45deg) scale(1.2);
  
  /* Transform origin */
  transform-origin: center center; /* default */
  transform-origin: top left;
  transform-origin: 50% 50%;
}

/* 3D Transforms */
.element-3d {
  transform: perspective(1000px) rotateY(45deg);
  transform: translateZ(100px);
  transform: rotate3d(1, 1, 1, 45deg);
  
  /* Preserve 3D space for children */
  transform-style: preserve-3d;
  
  /* Backface visibility */
  backface-visibility: hidden;
}
```

### 3. CSS Animations with Keyframes

```css
/* Define keyframes */
@keyframes slideIn {
  from {
    transform: translateX(-100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

/* With percentages */
@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

/* Complex multi-step animation */
@keyframes complexMove {
  0% {
    transform: translateX(0) translateY(0);
    background: red;
  }
  25% {
    transform: translateX(100px) translateY(0);
    background: blue;
  }
  50% {
    transform: translateX(100px) translateY(100px);
    background: green;
  }
  75% {
    transform: translateX(0) translateY(100px);
    background: yellow;
  }
  100% {
    transform: translateX(0) translateY(0);
    background: red;
  }
}

/* Apply animation */
.animated {
  animation-name: slideIn;
  animation-duration: 1s;
  animation-timing-function: ease-out;
  animation-delay: 0.5s;
  animation-iteration-count: infinite;
  animation-direction: alternate;
  animation-fill-mode: forwards;
  animation-play-state: running;
  
  /* Shorthand */
  animation: slideIn 1s ease-out 0.5s infinite alternate forwards;
}
```

### 4. Timing Functions

```css
/* Built-in timing functions */
.element {
  /* Linear - constant speed */
  transition-timing-function: linear;
  
  /* Ease - slow start and end */
  transition-timing-function: ease;
  
  /* Ease-in - slow start */
  transition-timing-function: ease-in;
  
  /* Ease-out - slow end */
  transition-timing-function: ease-out;
  
  /* Ease-in-out - slow start and end */
  transition-timing-function: ease-in-out;
  
  /* Custom cubic-bezier */
  transition-timing-function: cubic-bezier(0.68, -0.55, 0.265, 1.55);
  
  /* Steps (for frame-by-frame) */
  animation-timing-function: steps(4, end);
}
```

### 5. Common Animation Patterns

```css
/* Fade in */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Slide from left */
@keyframes slideFromLeft {
  from { transform: translateX(-100%); }
  to { transform: translateX(0); }
}

/* Bounce */
@keyframes bounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-30px);
  }
  60% {
    transform: translateY(-15px);
  }
}

/* Shake */
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 90% { transform: translateX(-10px); }
  20%, 40%, 60%, 80% { transform: translateX(10px); }
}

/* Rotate continuously */
@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

/* Scale pulse */
@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
}

/* Flip card */
@keyframes flip {
  from { transform: rotateY(0); }
  to { transform: rotateY(180deg); }
}
```

### 6. Interactive Animations

```css
/* Button hover effect */
.button {
  background: #007bff;
  transform: translateY(0);
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
  transition: all 0.3s ease;
}

.button:hover {
  background: #0056b3;
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0,0,0,0.15);
}

.button:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

/* Card hover effect */
.card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
  transform: translateY(-10px) scale(1.02);
  box-shadow: 0 20px 40px rgba(0,0,0,0.2);
}

/* Image zoom on hover */
.image-container {
  overflow: hidden;
}

.image-container img {
  transition: transform 0.5s ease;
}

.image-container:hover img {
  transform: scale(1.2);
}

/* Loading spinner */
.spinner {
  animation: spin 1s linear infinite;
}

/* Skeleton loader */
@keyframes shimmer {
  0% { background-position: -1000px 0; }
  100% { background-position: 1000px 0; }
}

.skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 1000px 100%;
  animation: shimmer 2s infinite;
}
```

## 💡 Key Concepts

### Performance Considerations

**Properties to Animate (60fps):**
- `transform` (translate, scale, rotate)
- `opacity`

**Properties to Avoid Animating:**
- `width`, `height` (use scale instead)
- `margin`, `padding` (use transform instead)
- `top`, `left` (use transform instead)

**Why?**
- Transform and opacity are GPU-accelerated
- Other properties trigger layout recalculation

### Hardware Acceleration
```css
/* Force hardware acceleration */
.accelerated {
  transform: translateZ(0);
  /* or */
  will-change: transform;
}

/* Use will-change sparingly */
.element {
  will-change: transform, opacity;
}

/* Remove will-change after animation */
.element:hover {
  will-change: auto;
}
```

### Accessibility Considerations
```css
/* Respect user preferences */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

## 🛠️ Practical Exercises

### Exercise 1: Button Collection
Create 5 different button hover animations:
- Scale and brighten
- Slide border
- 3D press effect
- Gradient shift
- Icon rotate

### Exercise 2: Loading Indicators
Build 3 loading animations:
- Spinner
- Dots bouncing
- Progress bar filling

### Exercise 3: Card Interactions
Create card hover effects:
- Lift with shadow
- Flip to show back
- Slide content reveal
- Image zoom with overlay

## 🎨 Project: Animated Landing Page

Enhance your Day 04 responsive website with animations:

### 1. Page Load Animations
- Hero section fades in
- Elements slide in from sides
- Stagger animations for list items

### 2. Navigation
- Smooth mobile menu slide
- Underline animation on hover
- Logo subtle pulse

### 3. Buttons
- Hover states with transforms
- Active press effect
- Ripple effect structure

### 4. Cards
- Hover lift animation
- Image zoom on hover
- Icon animations

### 5. Scroll Animations (CSS only structure)
- Elements animate when in viewport
- Parallax effect preparation
- Smooth scroll behavior

### 6. Micro-interactions
- Form input focus states
- Checkbox/radio animations
- Toggle switch animation

### 7. Loading States
- Skeleton screens
- Loading spinner
- Progress indicators

**Requirements:**
- Use transitions for state changes
- Use keyframe animations for continuous effects
- All animations should be purposeful, not distracting
- Respect `prefers-reduced-motion`
- Smooth 60fps animations (use transform and opacity)
- Consistent timing and easing
- Add comments explaining complex animations

**Animation Guidelines:**
- Duration: 200-500ms for UI interactions
- Easing: ease-out for entrances, ease-in for exits
- Keep it subtle and professional
- Test on slower devices

## 📖 Resources to Explore

### Documentation
- MDN: Using CSS Transitions
- MDN: Using CSS Animations
- MDN: CSS Transform
- Web.dev: Animations

### Tools
- Cubic-bezier.com (timing function generator)
- Animista (animation library)
- Chrome DevTools Animation Inspector

### Libraries (for reference, not implementation)
- Animate.css (study patterns)
- GreenSock (GSAP) documentation
- Framer Motion documentation

### Reading
- "High Performance Animations" - Paul Irish
- "The Illusion of Life" - Disney Animation Principles
- Web Animation API documentation

## 🔍 Interview Questions to Prepare

1. What's the difference between transition and animation?
2. Which CSS properties are performant to animate?
3. What is hardware acceleration and how do you enable it?
4. Explain the `will-change` property.
5. What are keyframes in CSS?
6. How do you create a smooth 60fps animation?
7. What is the difference between `ease`, `ease-in`, and `ease-out`?
8. How do you respect user motion preferences?
9. What is `transform-origin`?
10. Explain 3D transforms and perspective.
11. What is `animation-fill-mode`?
12. How do you chain multiple animations?
13. What causes layout thrashing?
14. How do you debug animation performance?

## ✅ Success Criteria

- [ ] Understand transitions vs animations
- [ ] Can create smooth, performant animations
- [ ] Know which properties to animate for performance
- [ ] Can use transforms for 2D and 3D effects
- [ ] Understand timing functions and easing
- [ ] Can create complex keyframe animations
- [ ] Respect accessibility with reduced motion
- [ ] Built multiple interactive animated components

## 🚀 Next Steps

Tomorrow, you'll learn CSS preprocessors (SASS/SCSS) to write more maintainable CSS!

---

**Time Investment**: 5-6 hours  
**Difficulty**: Intermediate  
**Prerequisites**: Days 01-04
