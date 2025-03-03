---
title: "Position Property"
description: "Position Property"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
---

## Vlastnost Position v CSS

Vlastnost **position** určuje, jak je prvek umístěn vzhledem k ostatním prvkům nebo viewportu.

### **static** (Výchozí)
- Normální tok dokumentu.
- **top**, **left**, **right**, **bottom** nefungují.

```css
.static-example {
  position: static;
}
```

### **relative**
- Umístěn **vzhledem ke své normální pozici**.

```css
.relative-example {
  position: relative;
  top: 20px;
  left: 10px;
}
```

### **absolute**
- Umístěn **vzhledem k nejbližšímu umístěnému parent elementu**.
- Pokud žádný parent není , je umístěn **vzhledem k `<body>`**.

```css
.absolute-example {
  position: absolute;
  top: 50px;
  right: 20px;
}
```

### **fixed**
- Umístěn **vzhledem k viewportu**.
- **Nepohybuje se** při rolování.

```css
.fixed-example {
  position: fixed;
  top: 0;
  width: 100%;
}
```

### **sticky**
- Chová se jako **relative**, ale stává se **fixed**, když se posune na nastavenou pozici.

```css
.sticky-example {
  position: sticky;
  top: 0;
}
```

## Shrnutí
- Používejte **display** pro rozvržení a strukturu.
- Používejte **position** pro přesné umístění prvků.


