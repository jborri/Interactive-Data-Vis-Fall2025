---
title: "Lab 1: Passing Pollinators"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).

```js
Plot.plot({
    width: 300,
    height: 200,
    marks: [
        Plot.frame(),
        Plot.text(["text"], {frameAnchor: "middle", rotate: 90 })

    ]
})
```

```js
// view(aapl)
Inputs.table(aapl)
```

```js
Plot.plot({
    // width: 300,
    y: {
        grid: true
    },
    height: 200,
    marks: [
        Plot.frame(),
        Plot.line(aapl, { x: "Date", y: "Close", stroke: "green", tip: true}),
        Plot.ruleY([0])

    ]
})
```