---
title: "Lab 0: Getting Started"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).


# *☆～（ゝ。∂）Welcome ♡( •ॢ◡-ॢ)✧˖° ♡*
## to Joe's Lab 0

![quick sketch for next painting](https://s3.amazonaws.com/files.commons.gc.cuny.edu/wp-content/blogs.dir/32576/files/2025/10/drawing.png "quick sketch")

```js
const subjectInput = html`<input type="text" placeholder="Choose Wisely">`;
const subject = Generators.input(subjectInput);
```

<div class="card" style="display: grid; gap: 0.5rem;">
  <div>Enter Emotion: ${subjectInput}</div>
  <div>Today will be a <b>${subject || "unknown"}</b> day!</div>
</div>
