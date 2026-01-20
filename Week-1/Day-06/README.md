# Day 06: CSS Preprocessors (SASS/SCSS)

## 🎯 Learning Objectives

By the end of today, you will understand:
- What CSS preprocessors are and why they're used
- SASS/SCSS syntax and features
- Variables, nesting, and mixins
- Functions and operators
- Partials and imports
- Best practices for organizing SCSS

## 📚 Topics to Study

### 1. Introduction to SASS/SCSS

**What is SASS?**
- CSS preprocessor that extends CSS with programming features
- Compiles to regular CSS
- Two syntaxes: SASS (indented) and SCSS (CSS-like)

**SCSS vs SASS:**
```scss
// SCSS (more popular, CSS-compatible)
.button {
  background: blue;
  &:hover {
    background: darkblue;
  }
}

// SASS (indented syntax)
.button
  background: blue
  &:hover
    background: darkblue
```

**Why Use SCSS?**
- Variables for reusable values
- Nesting for cleaner code
- Mixins for reusable styles
- Functions for calculations
- Partials for modular code
- Easier maintenance at scale

### 2. Variables

```scss
/* Define variables */
$primary-color: #007bff;
$secondary-color: #6c757d;
$font-stack: 'Helvetica Neue', Arial, sans-serif;
$spacing-unit: 8px;

/* Use variables */
.button {
  background-color: $primary-color;
  font-family: $font-stack;
  padding: $spacing-unit * 2;
}

/* Variable scope */
$global-var: red;

.component {
  $local-var: blue; // Only available in this block
  color: $local-var;
}

/* !default flag for library variables */
$base-font-size: 16px !default; // Can be overridden

/* Variable interpolation */
$side: top;
$radius: 10px;

.element {
  border-#{$side}-radius: $radius;
  // Outputs: border-top-radius: 10px;
}
```

### 3. Nesting

```scss
/* Basic nesting */
.navigation {
  ul {
    list-style: none;
    margin: 0;
    padding: 0;
  }
  
  li {
    display: inline-block;
  }
  
  a {
    text-decoration: none;
    
    &:hover {
      text-decoration: underline;
    }
  }
}

/* Parent selector (&) */
.button {
  background: blue;
  
  &:hover {
    background: darkblue;
  }
  
  &:disabled {
    opacity: 0.5;
  }
  
  &--large {
    padding: 20px;
  }
  
  .theme-dark & {
    background: lightblue;
  }
}

/* Property nesting */
.element {
  font: {
    family: Arial;
    size: 16px;
    weight: bold;
  }
  
  border: {
    top: 1px solid black;
    bottom: 2px solid black;
  }
}

/* Nesting best practices */
// Avoid deep nesting (max 3-4 levels)
// Bad:
.nav {
  .menu {
    .item {
      .link {
        .text { } // Too deep!
      }
    }
  }
}

// Good:
.nav-menu-item {
  &__link {
    &__text { }
  }
}
```

### 4. Mixins

```scss
/* Basic mixin */
@mixin border-radius($radius) {
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
  border-radius: $radius;
}

.box {
  @include border-radius(10px);
}

/* Mixin with multiple parameters */
@mixin box-shadow($x, $y, $blur, $color) {
  box-shadow: $x $y $blur $color;
}

.card {
  @include box-shadow(0, 2px, 5px, rgba(0,0,0,0.1));
}

/* Default parameter values */
@mixin transition($property: all, $duration: 0.3s, $easing: ease) {
  transition: $property $duration $easing;
}

.element {
  @include transition; // Uses defaults
  @include transition(transform, 0.5s); // Override some
}

/* Complex mixins */
@mixin responsive-font($min, $max, $min-vw: 320px, $max-vw: 1200px) {
  font-size: $min;
  
  @media (min-width: $min-vw) {
    font-size: calc(#{$min} + (#{$max} - #{$min}) * ((100vw - #{$min-vw}) / (#{$max-vw} - #{$min-vw})));
  }
  
  @media (min-width: $max-vw) {
    font-size: $max;
  }
}

/* Mixin with @content */
@mixin respond-to($breakpoint) {
  @if $breakpoint == "small" {
    @media (min-width: 576px) { @content; }
  }
  @else if $breakpoint == "medium" {
    @media (min-width: 768px) { @content; }
  }
  @else if $breakpoint == "large" {
    @media (min-width: 992px) { @content; }
  }
}

.sidebar {
  width: 100%;
  
  @include respond-to("medium") {
    width: 300px;
  }
}
```

### 5. Functions

```scss
/* Built-in color functions */
$base-color: #3498db;

.element {
  background: $base-color;
  color: darken($base-color, 20%);
  border: 1px solid lighten($base-color, 10%);
  box-shadow: 0 2px 5px rgba($base-color, 0.3);
}

/* More color functions */
$color: #ff6b6b;

.examples {
  // Lighten/darken
  color: lighten($color, 10%);
  background: darken($color, 15%);
  
  // Saturate/desaturate
  border: saturate($color, 20%);
  outline: desaturate($color, 50%);
  
  // Adjust hue
  box-shadow: adjust-hue($color, 90deg);
  
  // Mix colors
  background: mix($color, blue, 50%);
  
  // Transparency
  border: rgba($color, 0.5);
  background: transparentize($color, 0.3);
}

/* Custom functions */
@function calculate-rem($px) {
  @return $px / 16px * 1rem;
}

.element {
  font-size: calculate-rem(18px); // 1.125rem
}

/* Math functions */
@use "sass:math";

$width: 100px;

.element {
  width: math.div($width, 2); // 50px
  height: math.ceil(45.6px); // 46px
  margin: math.floor(23.8px); // 23px
  padding: math.max(10px, 20px); // 20px
}
```

### 6. Partials and Imports

```scss
/* File structure */
/*
styles/
  ├── abstracts/
  │   ├── _variables.scss
  │   ├── _mixins.scss
  │   └── _functions.scss
  ├── base/
  │   ├── _reset.scss
  │   ├── _typography.scss
  │   └── _base.scss
  ├── components/
  │   ├── _buttons.scss
  │   ├── _cards.scss
  │   └── _forms.scss
  ├── layout/
  │   ├── _header.scss
  │   ├── _footer.scss
  │   └── _grid.scss
  └── main.scss
*/

/* main.scss - Import order matters */
// Abstracts (no output)
@use 'abstracts/variables';
@use 'abstracts/mixins';
@use 'abstracts/functions';

// Base
@use 'base/reset';
@use 'base/typography';
@use 'base/base';

// Layout
@use 'layout/header';
@use 'layout/footer';
@use 'layout/grid';

// Components
@use 'components/buttons';
@use 'components/cards';
@use 'components/forms';

/* Using @use and namespaces (modern) */
@use 'abstracts/variables' as vars;
@use 'abstracts/mixins' as mix;

.element {
  color: vars.$primary-color;
  @include mix.border-radius(5px);
}

/* Using @forward for libraries */
// abstracts/_index.scss
@forward 'variables';
@forward 'mixins';
@forward 'functions';

// Then import all at once:
@use 'abstracts';

.element {
  color: abstracts.$primary-color;
}
```

### 7. Control Directives

```scss
/* @if / @else */
@mixin theme-colors($theme) {
  @if $theme == "dark" {
    background: #333;
    color: #fff;
  } @else if $theme == "light" {
    background: #fff;
    color: #333;
  } @else {
    background: #f0f0f0;
    color: #000;
  }
}

/* @for loop */
@for $i from 1 through 12 {
  .col-#{$i} {
    width: 100% / 12 * $i;
  }
}

/* @each loop */
$colors: (
  primary: #007bff,
  secondary: #6c757d,
  success: #28a745,
  danger: #dc3545
);

@each $name, $color in $colors {
  .btn-#{$name} {
    background-color: $color;
    
    &:hover {
      background-color: darken($color, 10%);
    }
  }
}

/* @while loop */
$heading-size: 32px;
$i: 1;

@while $i <= 6 {
  h#{$i} {
    font-size: $heading-size;
  }
  $heading-size: $heading-size * 0.8;
  $i: $i + 1;
}
```

### 8. Maps and Lists

```scss
/* Maps */
$breakpoints: (
  small: 576px,
  medium: 768px,
  large: 992px,
  xlarge: 1200px
);

@each $name, $size in $breakpoints {
  @media (min-width: $size) {
    .container-#{$name} {
      max-width: $size;
    }
  }
}

/* Map functions */
$theme: (
  primary: #007bff,
  secondary: #6c757d
);

.element {
  color: map-get($theme, primary);
}

/* Lists */
$font-stack: 'Helvetica', 'Arial', sans-serif;
$sizes: 12px 14px 16px 18px 20px;

.element {
  font-family: $font-stack;
  font-size: nth($sizes, 3); // 16px (1-indexed)
}
```

## 💡 Key Concepts

### Best Practices

1. **Organization**: Use partial files organized by purpose
2. **Naming**: Follow BEM or similar methodology
3. **Nesting**: Limit to 3-4 levels max
4. **Variables**: Use descriptive names, group logically
5. **Mixins**: Create for repeated patterns only
6. **Comments**: Document complex logic

### Common Patterns

```scss
/* Design tokens */
$colors: (
  brand: (
    primary: #007bff,
    secondary: #6c757d
  ),
  ui: (
    success: #28a745,
    error: #dc3545
  )
);

/* Utility generator */
$spacing-values: (0, 4, 8, 12, 16, 20, 24, 32);

@each $value in $spacing-values {
  .m-#{$value} { margin: #{$value}px; }
  .mt-#{$value} { margin-top: #{$value}px; }
  .mb-#{$value} { margin-bottom: #{$value}px; }
  .ml-#{$value} { margin-left: #{$value}px; }
  .mr-#{$value} { margin-right: #{$value}px; }
}
```

## 🛠️ Practical Exercises

### Exercise 1: Setup SCSS Project
- Install SASS compiler
- Create folder structure
- Setup watch/compile script
- Create basic variables file

### Exercise 2: Theme System
Create a theming system with:
- Color variables (primary, secondary, etc.)
- Typography scale
- Spacing system
- Breakpoint map
- Dark mode variant

### Exercise 3: Utility Classes
Generate utility classes for:
- Margins and padding
- Text alignment
- Display properties
- Flex utilities

## 🎨 Project: Refactor Previous Projects

Take your work from Days 1-5 and refactor using SCSS:

**Requirements:**
- Organize into partials (_variables, _mixins, etc.)
- Create a design token system
- Use nesting appropriately (max 3 levels)
- Create reusable mixins for common patterns
- Use functions for calculations
- Generate utility classes
- Support light/dark themes
- Add responsive mixins

**Structure:**
```
styles/
  ├── abstracts/
  │   ├── _variables.scss    (colors, spacing, breakpoints)
  │   ├── _mixins.scss        (responsive, flexbox helpers)
  │   └── _functions.scss     (rem conversion, etc.)
  ├── base/
  │   ├── _reset.scss         (normalize)
  │   ├── _typography.scss    (font scales)
  │   └── _utilities.scss     (generated utilities)
  ├── components/
  │   ├── _buttons.scss
  │   ├── _cards.scss
  │   └── _forms.scss
  ├── layout/
  │   ├── _header.scss
  │   ├── _grid.scss
  │   └── _footer.scss
  └── main.scss
```

## 📖 Resources to Explore

### Documentation
- Official SASS Documentation
- SASS Guidelines by Hugo Giraudel
- SASS @use vs @import

### Tools
- Node-sass or Dart Sass
- VS Code SCSS extensions
- Live Sass Compiler

### Reading
- "Sass for Web Designers" by Dan Cederholm
- SMACSS methodology
- BEM with SASS

## 🔍 Interview Questions to Prepare

1. What is a CSS preprocessor and why use one?
2. What's the difference between SASS and SCSS?
3. Explain the & parent selector.
4. What are mixins and when should you use them?
5. What's the difference between @use and @import?
6. How do you organize SCSS files in a large project?
7. What are partials in SASS?
8. Explain variable scope in SASS.
9. What are SASS maps and how do you use them?
10. How do you compile SASS to CSS?
11. What's the difference between a mixin and a function?
12. How do you handle browser prefixes in SASS?

## ✅ Success Criteria

- [ ] Understand SCSS syntax and features
- [ ] Can use variables, nesting, and mixins
- [ ] Know how to organize SCSS into partials
- [ ] Can create functions and use control directives
- [ ] Understand @use vs @import
- [ ] Can setup and compile SCSS projects
- [ ] Refactored previous work using SCSS

## 🚀 Next Steps

Tomorrow, you'll learn CSS architecture and the BEM methodology for scalable CSS!

---

**Time Investment**: 4-5 hours  
**Difficulty**: Intermediate  
**Prerequisites**: Days 01-05, Basic CSS
