---
title: "Position Property"
description: "Position Property"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
---

## Position Property in CSS

The `position` property controls how an element is placed relative to other elements or the viewport.

### `static` (Default)
- **Normal document flow.**
- `top`, `left`, `right`, `bottom` **do not work**.

```css
.static-example {
  position: static;
}
```

###  `relative`
- Positioned **relative to its normal position**.

```css
.relative-example {
  position: relative;
  top: 20px;
  left: 10px;
}
```

###  `absolute`
- Positioned **relative to the nearest positioned ancestor**.
- If no ancestor is positioned, it is **relative to the `<body>`**.

```css
.absolute-example {
  position: absolute;
  top: 50px;
  right: 20px;
}
```

### `fixed`
- Positioned **relative to the viewport**.
- Does **not move** when scrolling.

```css
.fixed-example {
  position: fixed;
  top: 0;
  width: 100%;
}
```

###  `sticky`
- Acts like `relative`, but becomes `fixed` when scrolled to a set position.

```css
.sticky-example {
  position: sticky;
  top: 0;
}
```

## Summary
- Use **`display`** for layout and structure.
- Use **`position`** for precise element placement.


