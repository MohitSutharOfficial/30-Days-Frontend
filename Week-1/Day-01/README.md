# Day 01: Semantic HTML5 & Document Structure

## 🎯 Learning Objectives

By the end of today, you will understand:
- HTML5 document structure and semantic elements
- The importance of semantic markup for accessibility and SEO
- Proper use of headings, sections, and landmark elements
- HTML5 APIs and new features
- Best practices for structuring modern web documents

## 📚 Topics to Study

### 1. HTML5 Document Structure
```
<!-- Study the proper HTML5 document structure -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document Title</title>
</head>
<body>
  <!-- Content goes here -->
</body>
</html>
```

### 2. Semantic HTML Elements
- `<header>` - Header content for document or section
- `<nav>` - Navigation links
- `<main>` - Main content (only one per page)
- `<article>` - Independent, self-contained content
- `<section>` - Thematic grouping of content
- `<aside>` - Sidebar or tangentially related content
- `<footer>` - Footer content
- `<figure>` and `<figcaption>` - Images with captions
- `<time>` - Machine-readable dates and times

### 3. Content Sectioning
- Proper heading hierarchy (h1-h6)
- Using `<div>` vs semantic elements
- ARIA landmarks and roles
- Document outline algorithm

### 4. Text-Level Semantics
- `<strong>` vs `<b>` - Semantic importance vs visual styling
- `<em>` vs `<i>` - Emphasis vs italic styling
- `<mark>` - Highlighted text
- `<abbr>` - Abbreviations
- `<code>`, `<pre>`, `<kbd>` - Code formatting

### 5. Forms and Input Elements
- HTML5 input types (email, url, date, number, range, etc.)
- Form attributes (required, pattern, placeholder)
- `<datalist>` for autocomplete
- `<output>` for calculation results

### 6. Meta Tags and SEO
- Essential meta tags for modern websites
- Open Graph tags for social media
- Twitter Card tags
- Schema.org structured data

## 💡 Key Concepts

### Why Semantic HTML Matters
1. **Accessibility**: Screen readers rely on semantic structure
2. **SEO**: Search engines better understand your content
3. **Maintainability**: Code is easier to read and update
4. **Future-proof**: Standards-compliant code ages better

### Common Mistakes to Avoid
- Using `<div>` for everything instead of semantic elements
- Skipping heading levels (e.g., h1 to h3)
- Multiple `<main>` elements on one page
- Not including `lang` attribute on `<html>`
- Improper nesting of elements

## 🛠️ Practical Exercises

### Exercise 1: Blog Post Layout
Create a semantic HTML structure for a blog post including:
- Site header with navigation
- Article with heading, metadata, and content
- Sidebar with related posts
- Footer with copyright information

### Exercise 2: Product Page
Structure a product page with:
- Product images with captions
- Product description and specifications
- Customer reviews section
- Related products sidebar

### Exercise 3: Landing Page
Build a landing page structure with:
- Hero section
- Features section with cards
- Testimonials
- Call-to-action section
- Contact form

## 🎨 Project: Personal Portfolio Structure

Create the HTML structure for a personal portfolio website including:
- Header with logo and navigation
- Hero section with introduction
- About section
- Projects/work showcase section
- Skills section
- Contact form
- Footer with social links

**Requirements:**
- Use only semantic HTML5 elements
- Proper heading hierarchy
- Valid HTML5 (validate using W3C validator)
- Include appropriate meta tags
- Add comments explaining your structural choices

## 📖 Resources to Explore

### Documentation
- MDN Web Docs: HTML5 Elements
- W3C HTML5 Specification
- HTML Living Standard (WHATWG)

### Tools
- W3C Markup Validation Service
- WAVE Web Accessibility Evaluation Tool
- Chrome DevTools Lighthouse

### Reading
- "Semantic HTML" - web.dev
- "HTML5 Doctor" - Element Index
- "Dive Into HTML5" by Mark Pilgrim

## 🔍 Interview Questions to Prepare

1. What is semantic HTML and why is it important?
2. Explain the difference between `<div>` and `<section>`.
3. What are ARIA roles and when should you use them?
4. How does semantic HTML improve SEO?
5. What's the difference between `<article>` and `<section>`?
6. Explain the HTML5 document outline algorithm.
7. What are data attributes and how are they used?
8. How do you make a website accessible using HTML?
9. What are the new HTML5 form input types?
10. Explain the purpose of the `<main>` element.

## ✅ Success Criteria

- [ ] Can explain the purpose of each semantic HTML5 element
- [ ] Understand proper document structure and nesting
- [ ] Can create accessible HTML markup
- [ ] Know when to use semantic elements vs generic divs
- [ ] Understand meta tags and their importance
- [ ] Can validate HTML and fix common errors
- [ ] Built at least one complete semantic HTML structure

## 🚀 Next Steps

Tomorrow, you'll dive into CSS3 fundamentals and learn how to style your semantic HTML structures effectively!

---

**Time Investment**: 4-6 hours  
**Difficulty**: Beginner  
**Prerequisites**: Basic understanding of HTML
