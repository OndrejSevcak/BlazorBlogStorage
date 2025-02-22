---
title: "CSS Guide"
description: "Display Property, Position Property, and Selectors"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
---

# CSS Guide: Display Property, Position Property, and Selectors

## 📌 Display Property in CSS

The `display` property controls how elements are displayed in the document flow. Below are the most commonly used values:

### 🔹 `block`
- Takes the **full width** of its parent.
- Starts on a **new line**.
- Common for `<div>`, `<p>`, `<h1>`-`<h6>`, etc.

✅ **Example:**
```css
.block-example {
  display: block;
  width: 300px;
  background: lightblue;
}
```

### 🔹 `inline`
- Takes up **only as much width as necessary**.
- Does **not** start on a new line.
- Common for `<span>`, `<a>`, `<strong>`, etc.

✅ **Example:**
```css
.inline-example {
  display: inline;
  background: yellow;
}
```

### 🔹 `inline-block`
- Behaves like `inline`, but allows **setting width and height**.

✅ **Example:**
```css
.inline-block-example {
  display: inline-block;
  width: 100px;
  height: 50px;
  background: green;
}
```

### 🔹 `flex`
- Turns an element into a **flex container**.
- Makes its children follow **flexbox rules**.

✅ **Example:**
```css
.flex-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

### 🔹 `grid`
- Turns an element into a **grid container**.
- Uses **CSS Grid** for layout.

✅ **Example:**
```css
.grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
```

### 🔹 `none`
- Hides the element completely (removes it from layout).

✅ **Example:**
```css
.hidden {
  display: none;
}
```

---

## 📌 Position Property in CSS

The `position` property controls how an element is placed relative to other elements or the viewport.

### 🔹 `static` (Default)
- **Normal document flow.**
- `top`, `left`, `right`, `bottom` **do not work**.

✅ **Example:**
```css
.static-example {
  position: static;
}
```

### 🔹 `relative`
- Positioned **relative to its normal position**.

✅ **Example:**
```css
.relative-example {
  position: relative;
  top: 20px;
  left: 10px;
}
```

### 🔹 `absolute`
- Positioned **relative to the nearest positioned ancestor**.
- If no ancestor is positioned, it is **relative to the `<body>`**.

✅ **Example:**
```css
.absolute-example {
  position: absolute;
  top: 50px;
  right: 20px;
}
```

### 🔹 `fixed`
- Positioned **relative to the viewport**.
- Does **not move** when scrolling.

✅ **Example:**
```css
.fixed-example {
  position: fixed;
  top: 0;
  width: 100%;
}
```

### 🔹 `sticky`
- Acts like `relative`, but becomes `fixed` when scrolled to a set position.

✅ **Example:**
```css
.sticky-example {
  position: sticky;
  top: 0;
}
```

---

## 📌 Most Useful CSS Selectors

### 🔹 Universal Selector (`*`)
- Selects **all elements**.

✅ **Example:**
```css
* {
  margin: 0;
  padding: 0;
}
```

### 🔹 Type Selector (`element`)
- Selects **all instances** of a specific tag.

✅ **Example:**
```css
p {
  color: blue;
}
```

### 🔹 Class Selector (`.classname`)
- Selects **all elements** with a given class.

✅ **Example:**
```css
.button {
  background: red;
}
```

### 🔹 ID Selector (`#idname`)
- Selects a **single element** with a given ID.

✅ **Example:**
```css
#header {
  background: black;
}
```

### 🔹 Descendant Selector (`parent descendant`)
- Selects **nested elements** inside another element.

✅ **Example:**
```css
nav ul {
  list-style: none;
}
```

### 🔹 Child Selector (`parent > child`)
- Selects **only direct children**.

✅ **Example:**
```css
div > p {
  color: green;
}
```

### 🔹 Adjacent Sibling Selector (`element + sibling`)
- Selects **the immediate next sibling**.

✅ **Example:**
```css
h1 + p {
  color: red;
}
```

### 🔹 General Sibling Selector (`element ~ sibling`)
- Selects **all siblings after a given element**.

✅ **Example:**
```css
h1 ~ p {
  color: blue;
}
```

### 🔹 Attribute Selector (`[attribute]`)
- Selects elements with a specific attribute.

✅ **Example:**
```css
input[type="text"] {
  border: 2px solid blue;
}
```

### 🔹 Pseudo-Classes (`:hover`, `:first-child`, `:nth-child(n)`, `:not()`, etc.)
✅ **Examples:**
```css
a:hover {
  color: red;
}
```
```css
p:first-child {
  font-weight: bold;
}
```
```css
button:not(.disabled) {
  background: green;
}
```

### 🔹 Pseudo-Elements (`::before`, `::after`)
✅ **Examples:**
```css
h1::before {
  content: "🔥 ";
}
```
```css
p::first-letter {
  font-size: 30px;
}
```

---

## 🎯 Conclusion
- Use **`display`** for layout and structure.
- Use **`position`** for precise element placement.
- Use **CSS selectors** to style elements effectively.

