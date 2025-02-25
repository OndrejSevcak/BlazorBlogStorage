---
title: "Most useful Selectors"
description: "Most useful Selectors"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
---


## Most Useful CSS Selectors

###  Universal Selector (`*`)
- Selects **all elements**.

```css
* {
  margin: 0;
  padding: 0;
}
```

### Type Selector (`element`)
- Selects **all instances** of a specific tag.

```css
p {
  color: blue;
}
```

###  Class Selector (`.classname`)
- Selects **all elements** with a given class.

```css
.button {
  background: red;
}
```

### ID Selector (`#idname`)
- Selects a **single element** with a given ID.

```css
#header {
  background: black;
}
```

### Descendant Selector (`parent descendant`)
- Selects **nested elements** inside another element.

```css
nav ul {
  list-style: none;
}
```

### Child Selector (`parent > child`)
- Selects **only direct children**.

```css
div > p {
  color: green;
}
```

### Adjacent Sibling Selector (`element + sibling`)
- Selects **the immediate next sibling**.

```css
h1 + p {
  color: red;
}
```

###  General Sibling Selector (`element ~ sibling`)
- Selects **all siblings after a given element**.

```css
h1 ~ p {
  color: blue;
}
```

### Attribute Selector (`[attribute]`)
- Selects elements with a specific attribute.


```css
input[type="text"] {
  border: 2px solid blue;
}
```

### Pseudo-Classes (`:hover`, `:first-child`, `:nth-child(n)`, `:not()`, etc.)

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

###  Pseudo-Elements (`::before`, `::after`)

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


