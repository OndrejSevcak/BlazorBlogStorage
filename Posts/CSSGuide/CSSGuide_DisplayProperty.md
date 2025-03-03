---
title: "Display Property"
description: "Display Property"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
---

## Vlastnost Display v CSS

Vlastnost **display** určuje, jak jsou prvky zobrazeny v dokumentu<br>
Nejpoužívanější hodnoty jsou:

### **block**
- Zabírá **celou šířku** svého parent elementu.
- Začíná na **novém řádku**.
- Běžné pro `<div>`, `<p>`, `<h1>`-`<h6>`, atd.

```css
.block-example {
  display: block;
  width: 300px;
  background: lightblue;
}
```

### **inline**
- Zabírá **pouze tolik šířky, kolik je potřeba**.
- **Nezačíná** na novém řádku.
- Běžné pro `<span>`, `<a>`, `<strong>`, atd.

```css
.inline-example {
  display: inline;
  background: yellow;
}
```

### **inline-block**
- Chová se jako `inline`, ale umožňuje **nastavit šířku a výšku**.

```css
.inline-block-example {
  display: inline-block;
  width: 100px;
  height: 50px;
  background: green;
}
```

### **flex**
- Přemění prvek na **flex container**.
- Děti tohoto prvku se řídí **pravidly flexboxu**.

```css
.flex-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

### **grid**
- Přemění prvek na **grid container**.
- Používá **CSS Grid** pro rozvržení.

```css
.grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
```

### **none**
- Úplně skryje prvek (odstraní ho z rozvržení).

```css
.hidden {
  display: none;
}
```

## Shrnutí
- Používejte **`display`** pro rozvržení a strukturu.
- Používejte **`position`** pro přesné umístění prvků.

