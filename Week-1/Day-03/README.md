# Day 03: Flexbox & Grid Layouts

## 🎯 Learning Objectives

By the end of today, you will understand:
- CSS Flexbox for one-dimensional layouts
- CSS Grid for two-dimensional layouts
- When to use Flexbox vs Grid
- Modern responsive layout patterns
- Alignment and distribution techniques

## 📚 Topics to Study

### 1. Flexbox Fundamentals

```css
/* Container properties */
.flex-container {
  display: flex; /* or inline-flex */
  
  /* Main axis direction */
  flex-direction: row | row-reverse | column | column-reverse;
  
  /* Wrapping */
  flex-wrap: nowrap | wrap | wrap-reverse;
  
  /* Shorthand for direction and wrap */
  flex-flow: row wrap;
  
  /* Main axis alignment */
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
  
  /* Cross axis alignment */
  align-items: stretch | flex-start | flex-end | center | baseline;
  
  /* Multi-line cross axis alignment */
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
  
  /* Gap between items */
  gap: 20px;
  row-gap: 20px;
  column-gap: 10px;
}

/* Item properties */
.flex-item {
  /* Growth factor */
  flex-grow: 0; /* default */
  
  /* Shrink factor */
  flex-shrink: 1; /* default */
  
  /* Base size */
  flex-basis: auto; /* or specific size */
  
  /* Shorthand: grow shrink basis */
  flex: 1 1 auto;
  flex: 1; /* Common: grow */
  
  /* Individual alignment */
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
  
  /* Order */
  order: 0; /* default */
}
```

### 2. CSS Grid Fundamentals

```css
/* Container properties */
.grid-container {
  display: grid; /* or inline-grid */
  
  /* Define columns */
  grid-template-columns: 200px 1fr 2fr;
  grid-template-columns: repeat(3, 1fr);
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  
  /* Define rows */
  grid-template-rows: 100px auto 100px;
  grid-template-rows: repeat(3, 100px);
  
  /* Named grid areas */
  grid-template-areas:
    "header header header"
    "sidebar main main"
    "footer footer footer";
  
  /* Gaps */
  gap: 20px;
  row-gap: 20px;
  column-gap: 10px;
  
  /* Alignment */
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
  justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
  align-content: start | end | center | stretch | space-around | space-between | space-evenly;
  
  /* Auto-sizing */
  grid-auto-rows: 100px;
  grid-auto-columns: 1fr;
  grid-auto-flow: row | column | dense;
}

/* Item properties */
.grid-item {
  /* Column placement */
  grid-column-start: 1;
  grid-column-end: 3;
  grid-column: 1 / 3; /* shorthand */
  grid-column: 1 / span 2; /* span columns */
  
  /* Row placement */
  grid-row-start: 1;
  grid-row-end: 3;
  grid-row: 1 / 3; /* shorthand */
  
  /* Named area */
  grid-area: header;
  
  /* Individual alignment */
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
}
```

### 3. Flexbox vs Grid Decision Matrix

**Use Flexbox when:**
- Creating one-dimensional layouts (row or column)
- Content size should control layout
- Need simple alignment and distribution
- Building navigation bars, toolbars
- Creating flexible, content-driven layouts

**Use Grid when:**
- Creating two-dimensional layouts (rows and columns)
- Layout should control content size
- Need precise placement of items
- Building complex page layouts
- Creating responsive card grids

**Use Both:**
- Grid for overall page structure
- Flexbox for component internals

### 4. Common Layout Patterns

```css
/* Holy Grail Layout - Grid */
.holy-grail {
  display: grid;
  grid-template-areas:
    "header header header"
    "nav main aside"
    "footer footer footer";
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}

/* Card Grid - Responsive */
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}

/* Centered Layout - Flexbox */
.center-flex {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

/* Navbar - Flexbox */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
}
```

### 5. Advanced Grid Techniques

```css
/* Implicit Grid */
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  grid-auto-rows: 200px;
}

/* Named Lines */
.named-lines {
  display: grid;
  grid-template-columns: [sidebar-start] 200px [sidebar-end main-start] 1fr [main-end];
}

/* Dense Packing */
.masonry-like {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  grid-auto-flow: dense;
}
```

## 💡 Key Concepts

### Flexbox Mental Model
- Main axis vs cross axis
- Container vs items
- Space distribution
- Item sizing with flex properties

### Grid Mental Model
- Grid lines (not cells)
- Tracks (rows and columns)
- Grid areas (named regions)
- Implicit vs explicit grids

### fr Unit
- Fractional unit for flexible sizing
- Distributes available space
- Works after fixed sizes are calculated

### auto-fit vs auto-fill
```css
/* auto-fill: Creates empty tracks */
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

/* auto-fit: Collapses empty tracks */
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

## 🛠️ Practical Exercises

### Exercise 1: Navigation Layouts
Create three different navigation bars using Flexbox:
- Logo left, menu right
- Centered logo with split menu
- Logo, menu, action buttons all aligned

### Exercise 2: Card Grids
Build responsive card grids:
- Equal height cards
- 3-column desktop, 2-column tablet, 1-column mobile
- Cards with different sizes using grid-column/row span

### Exercise 3: Dashboard Layout
Create a dashboard with:
- Fixed header
- Sidebar navigation
- Main content area
- Widget cards in content area
- Footer

## 🎨 Project: Responsive Landing Page Layout

Build a complete landing page layout including:

1. **Header Section** (Flexbox)
   - Logo on the left
   - Navigation menu on the right
   - Mobile hamburger menu consideration

2. **Hero Section** (Flexbox)
   - Centered content
   - Image and text side by side
   - Responsive stack on mobile

3. **Features Section** (Grid)
   - 3-column grid on desktop
   - 2-column on tablet
   - 1-column on mobile
   - Equal-height cards

4. **Gallery/Portfolio Section** (Grid)
   - Masonry-like layout
   - Items of varying sizes
   - Responsive columns

5. **Testimonials Section** (Flexbox)
   - Centered content
   - Horizontal scrolling on mobile

6. **Footer** (Grid)
   - Multi-column layout
   - Links, newsletter signup, social icons
   - Responsive stacking

**Requirements:**
- Use semantic HTML from Day 01
- Apply box model concepts from Day 02
- Implement both Flexbox and Grid appropriately
- Fully responsive (mobile-first approach)
- Clean, maintainable code with comments
- No media queries yet (use auto-fit/auto-fill)

## 📖 Resources to Explore

### Documentation
- MDN: Complete Guide to Flexbox
- MDN: Complete Guide to Grid
- CSS-Tricks: A Complete Guide to Flexbox
- CSS-Tricks: A Complete Guide to Grid

### Interactive Learning
- Flexbox Froggy (game)
- Grid Garden (game)
- Flexbox Defense (game)

### Tools
- Firefox Grid Inspector
- Chrome DevTools Layout Panel
- Grid Calculator

### Reading
- "Layout Land" by Jen Simmons (YouTube)
- "Every Layout" by Heydon Pickering
- Rachel Andrew's Grid Examples

## 🔍 Interview Questions to Prepare

1. Explain when you would use Flexbox vs Grid.
2. What is the difference between `justify-content` and `align-items`?
3. How does the `fr` unit work in CSS Grid?
4. What's the difference between `auto-fit` and `auto-fill`?
5. Explain the flex shorthand property (flex: 1).
6. How do you center an element with Flexbox?
7. What are grid areas and how do you use them?
8. How does `flex-basis` differ from `width`?
9. Explain implicit vs explicit grid.
10. How can you create a responsive layout without media queries?
11. What is the difference between `flex-grow` and `flex-shrink`?
12. How does `gap` property work in both Flexbox and Grid?

## ✅ Success Criteria

- [ ] Can build layouts using Flexbox confidently
- [ ] Can create complex layouts with CSS Grid
- [ ] Know when to use Flexbox vs Grid
- [ ] Understand alignment properties thoroughly
- [ ] Can create responsive layouts without media queries
- [ ] Built multiple layout patterns from scratch
- [ ] Can explain the mental model of both layout systems

## 🚀 Next Steps

Tomorrow, you'll learn responsive design principles and media queries to make your layouts work perfectly on all devices!

---

**Time Investment**: 5-6 hours  
**Difficulty**: Intermediate  
**Prerequisites**: Days 01-02
