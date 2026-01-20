# Day 02: CSS3 Fundamentals & Box Model

## 🎯 Learning Objectives

By the end of today, you will understand:
- CSS3 syntax, selectors, and specificity
- The CSS Box Model in depth
- Positioning and display properties
- Modern CSS units and values
- CSS cascade, inheritance, and specificity rules

## 📚 Topics to Study

### 1. CSS Syntax and Selectors

```css
/* Basic selector types to master */

/* Element selector */
p { color: blue; }

/* Class selector */
.highlight { background: yellow; }

/* ID selector */
#header { font-size: 24px; }

/* Attribute selector */
[type="text"] { border: 1px solid gray; }

/* Pseudo-classes */
a:hover { color: red; }
li:first-child { font-weight: bold; }
input:focus { outline: 2px solid blue; }

/* Pseudo-elements */
p::first-line { text-transform: uppercase; }
h1::before { content: "→ "; }

/* Combinators */
div > p { } /* Direct child */
div p { }   /* Descendant */
div + p { } /* Adjacent sibling */
div ~ p { } /* General sibling */
```

### 2. The Box Model

```css
/* Understanding the box model components */
.box {
  /* Content */
  width: 300px;
  height: 200px;
  
  /* Padding - space inside the border */
  padding: 20px;
  padding: 10px 20px; /* vertical | horizontal */
  padding: 10px 20px 15px 25px; /* top | right | bottom | left */
  
  /* Border */
  border: 2px solid black;
  border-radius: 10px;
  
  /* Margin - space outside the border */
  margin: 20px;
  margin: 20px auto; /* Center horizontally */
  
  /* Box-sizing */
  box-sizing: border-box; /* Include padding and border in width/height */
}
```

### 3. Display Property

- `display: block` - Full width, starts on new line
- `display: inline` - Width of content, no line break
- `display: inline-block` - Inline but respects width/height
- `display: none` - Removes from layout
- `display: flex` - Flexbox container (covered Day 3)
- `display: grid` - Grid container (covered Day 3)

### 4. Positioning

```css
/* Static - default positioning */
.static { position: static; }

/* Relative - positioned relative to normal position */
.relative {
  position: relative;
  top: 10px;
  left: 20px;
}

/* Absolute - positioned relative to nearest positioned ancestor */
.absolute {
  position: absolute;
  top: 0;
  right: 0;
}

/* Fixed - positioned relative to viewport */
.fixed {
  position: fixed;
  bottom: 20px;
  right: 20px;
}

/* Sticky - toggles between relative and fixed */
.sticky {
  position: sticky;
  top: 0;
}
```

### 5. CSS Units

**Absolute Units:**
- `px` - Pixels (most common)
- `pt`, `cm`, `mm`, `in` - Print units

**Relative Units:**
- `%` - Percentage of parent
- `em` - Relative to font-size of element
- `rem` - Relative to font-size of root element
- `vh`, `vw` - Viewport height/width percentage
- `vmin`, `vmax` - Smaller/larger of vh or vw
- `ch` - Width of the "0" character
- `ex` - Height of lowercase "x"

### 6. Specificity and Cascade

**Specificity Hierarchy:**
1. Inline styles (1000 points)
2. IDs (100 points)
3. Classes, attributes, pseudo-classes (10 points)
4. Elements, pseudo-elements (1 point)

```css
/* Understanding specificity */
p { color: black; }           /* 1 point */
.text { color: blue; }         /* 10 points */
#intro { color: red; }         /* 100 points */
p.text { color: green; }       /* 11 points */
```

### 7. Colors and Backgrounds

```css
.element {
  /* Color formats */
  color: red;                           /* Named */
  color: #ff0000;                       /* Hex */
  color: rgb(255, 0, 0);               /* RGB */
  color: rgba(255, 0, 0, 0.5);         /* RGBA */
  color: hsl(0, 100%, 50%);            /* HSL */
  color: hsla(0, 100%, 50%, 0.5);      /* HSLA */
  
  /* Background properties */
  background-color: #f0f0f0;
  background-image: url('image.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  
  /* Shorthand */
  background: #f0f0f0 url('image.jpg') no-repeat center/cover;
  
  /* Gradients */
  background: linear-gradient(to right, red, blue);
  background: radial-gradient(circle, red, blue);
}
```

## 💡 Key Concepts

### Box Model Calculation
```
Total Width = width + padding-left + padding-right + border-left + border-right + margin-left + margin-right
Total Height = height + padding-top + padding-bottom + border-top + border-bottom + margin-top + margin-bottom

With box-sizing: border-box:
Total Width = width + margin-left + margin-right
```

### Collapsing Margins
- Vertical margins between adjacent block elements collapse
- Larger margin wins
- Doesn't happen with padding or inline elements

### Centering Techniques
```css
/* Horizontal center */
.center { margin: 0 auto; width: 80%; }

/* Vertical and horizontal center with positioning */
.center-abs {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

## 🛠️ Practical Exercises

### Exercise 1: Card Component
Style a card component with:
- Box shadow for depth
- Rounded corners
- Proper padding and margins
- Hover effects
- Border styling

### Exercise 2: Navigation Bar
Create a horizontal navigation bar with:
- Centered logo
- Right-aligned menu items
- Active state styling
- Hover effects
- Fixed positioning

### Exercise 3: Centering Challenge
Practice different centering techniques:
- Center a div horizontally
- Center a div vertically
- Center content both ways
- Center text within containers

## 🎨 Project: Product Card Layout

Create a styled product card that includes:
- Product image section
- Product title and description
- Price display
- "Add to Cart" button
- Rating stars
- Discount badge (positioned absolutely)

**Requirements:**
- Use proper box model properties
- Apply appropriate positioning
- Include hover states
- Use modern color values (HSL or RGBA)
- Ensure proper spacing with margins and padding
- Add box shadows for depth
- Make it visually appealing without images (use gradients/colors)

**Bonus Challenges:**
- Create variations (different sizes, themes)
- Add a "Sale" ribbon in the corner
- Implement a flip animation on hover (preparation for Day 5)

## 📖 Resources to Explore

### Documentation
- MDN Web Docs: CSS Box Model
- CSS Tricks: Complete Guide to Box Sizing
- W3C CSS Specification

### Tools
- Chrome DevTools (inspect element, box model viewer)
- CSS Specificity Calculator
- Browser DevTools Color Picker

### Reading
- "CSS: The Definitive Guide" by Eric Meyer
- "Every Layout" - Layout Patterns
- "CSS Secrets" by Lea Verou

## 🔍 Interview Questions to Prepare

1. Explain the CSS box model.
2. What is the difference between `margin` and `padding`?
3. What does `box-sizing: border-box` do?
4. Explain CSS specificity and how it's calculated.
5. What's the difference between `display: none` and `visibility: hidden`?
6. How do you center a div horizontally and vertically?
7. What are the different position values and when would you use each?
8. Explain the CSS cascade and inheritance.
9. What's the difference between `em` and `rem` units?
10. How do collapsing margins work?
11. What's the difference between absolute and relative positioning?
12. How would you debug CSS specificity issues?

## ✅ Success Criteria

- [ ] Understand the complete box model and can calculate element sizes
- [ ] Know all major CSS selectors and their specificity
- [ ] Can position elements using different position values
- [ ] Understand when to use different CSS units
- [ ] Can center elements using multiple techniques
- [ ] Know how the cascade and inheritance work
- [ ] Built styled components using box model properties

## 🚀 Next Steps

Tomorrow, you'll master modern layout techniques with Flexbox and CSS Grid!

---

**Time Investment**: 4-6 hours  
**Difficulty**: Beginner to Intermediate  
**Prerequisites**: Day 01, Basic CSS knowledge
