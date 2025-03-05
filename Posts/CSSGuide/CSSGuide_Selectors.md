---
title: "Základní CSS selektory"
description: "Základní CSS selektory"
author: "Ondrej Sevcak"
date: "2024-02-02"
category: "CSS"
hashtags: "#CSS #FrontEnd #Basics"
publish: false
---

## Nejužitečnější CSS Selektory

###  Univerzální selektor (**\***)
- Vybere **všechny prvky**.

```css
* {
  margin: 0;
  padding: 0;
}
```

### Typový selektor (**element**)
- Vybere **všechny instance** konkrétního html tagu.

```css
p {
  color: blue;
}
```

###  Třídový selektor (**.classname**)
- Vybere **všechny prvky** s danou třídou.

```css
.button {
  background: red;
}
```

### ID selektor (**#idname**)
- Vybere **jeden prvek** s daným ID.

```css
#header {
  background: black;
}
```

### Selektor potomků (**parent descendant**)
- Vybere **vnořené prvky** uvnitř jiného prvku.

```css
nav ul {
  list-style: none;
}
```

### Selektor přímých potomků (**parent > child**)
- Vybere **pouze přímé potomky**.

```css
div > p {
  color: green;
}
```

### Selektor sousedních sourozenců (**element + sibling**)
- Vybere **bezprostředně následujícího sourozence**.

```css
h1 + p {
  color: red;
}
```

###  Selektor obecných sourozenců (**element ~ sibling**)
- Vybere **všechny sourozence po daném prvku**.

```css
h1 ~ p {
  color: blue;
}
```

### Selektor atributů (**[attribute]**)
- Vybere prvky s konkrétním atributem.

```css
input[type="text"] {
  border: 2px solid blue;
}
```

### Pseudo-třídy (**:hover**, **:first-child**, **:nth-child(n)**, **:not()**, atd.)

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

###  Pseudo-prvky (**::before**, **::after**)

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


