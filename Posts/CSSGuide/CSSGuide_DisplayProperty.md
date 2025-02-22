---
title: "Display Property"
description: "Display Property"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
---

# CSS Guide: Display Property

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


## 🎯 Conclusion
- Use **`display`** for layout and structure.
- Use **`position`** for precise element placement.

