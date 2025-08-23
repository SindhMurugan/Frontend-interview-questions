# HTML & CSS Interview Notes

### 1. Semantic Tags and Advantages
- **Semantic tags** clearly describe their meaning in a human- and machine-readable way.  
- Examples: `<header>`, `<footer>`, `<article>`, `<section>`, `<nav>`.  
- **Advantages**:
  - Improves SEO.
  - Enhances accessibility (screen readers can understand better).
  - Makes code more readable and maintainable.

---

### 2. Grid vs Flexbox
- **Flexbox**: One-dimensional (row or column). Best for aligning items in a single direction.
- **Grid**: Two-dimensional (rows and columns). Best for complex layouts.  

---

### 3. How to Center a div Element
**Horizontally & Vertically using Flexbox:**
```css
.parent {
  display: flex;
  justify-content: center; /* horizontal */
  align-items: center;     /* vertical */
  height: 100vh;
}
```

---

### 4. Media Queries
- Allow responsive design based on screen size, resolution, or device.
```css
@media (max-width: 768px) {
  body {
    background-color: lightblue;
  }
}
```

---

### 5. Block vs Inline Elements
- **Block**: Takes full width, starts on a new line. Examples: `<div>`, `<p>`, `<h1>`.  
- **Inline**: Takes only as much width as needed, does not start on a new line. Examples: `<span>`, `<a>`, `<strong>`.  

---

### 6. Display Types
- `block` → full width, new line.  
- `inline` → same line, only content width.  
- `inline-block` → inline but allows setting width/height.  
- `flex` → flexible box layout.  
- `grid` → two-dimensional layout.  
- `none` → hides the element.  

---

### 7. Semantic Tag Names
- `<header>`, `<footer>`, `<nav>`, `<article>`, `<section>`, `<aside>`, `<main>`, `<figure>`, `<figcaption>`.  

---

### 8. Pseudo-class Elements
- Define a special state of an element.  
Examples:  
- `:hover` → when mouse is over.  
- `:focus` → when input is focused.  
- `:first-child` → first element in a parent.  
- `:nth-child(n)` → specific child.  

---

### 9. Keyframes
- Used in CSS animations.
```css
@keyframes slide {
  from { transform: translateX(0); }
  to { transform: translateX(100px); }
}

.box {
  animation: slide 2s infinite;
}
```

---

### 10. Change the Order of the div
Using **Flexbox order property**:
```css
.container {
  display: flex;
}
.div1 { order: 2; }
.div2 { order: 1; }
```

---

### 11. Media Query Breakpoints
Common breakpoints:  
- `320px` – mobile portrait  
- `480px` – mobile landscape  
- `768px` – tablet  
- `1024px` – small laptop  
- `1200px+` – desktop  

---

### 12. Doctype
- `<!DOCTYPE html>`  
- Tells the browser which version of HTML is used.  
- In HTML5, it’s simplified to above single line.  

---

### 13. Relative vs Absolute Position
- **relative**: Position relative to its original place.  
- **absolute**: Position relative to the nearest positioned ancestor (not static).  
```css
.relative-box {
  position: relative;
  top: 20px;
}
.absolute-box {
  position: absolute;
  top: 20px;
  left: 30px;
}
```
---
