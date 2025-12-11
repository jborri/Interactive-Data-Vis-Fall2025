---
title: "Lab 4: Clearwater Crisis"
toc: false
---

<!-- Import Data -->
```js
const bookcase = await FileAttachment("data/book_log.csv").csv({ typed: true });
```

```js
Inputs.table(bookcase)
```
```js
display(bookcase)
```

```js
Plot.plot({
    margin: 90,
  marks: [
    Plot.frame(),
    Plot.barX(bookcase, Plot.groupY(
      {x: "count"},
      {y: "genre_1",
        tip: true,
      },
      
    ))
    
  ]
})
```

<!-- Import Data -->
```js
const cognition = await FileAttachment("data/human_cognitive_performance.csv").csv({ typed: true });
```

```js
Inputs.table(cognition)
```
```js
display(cognition)
```

