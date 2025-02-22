---
title: "Most useful Selectors"
description: "Most useful Selectors"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
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


