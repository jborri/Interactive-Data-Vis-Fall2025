---
title: "Lab 1: Passing Pollinators"
toc: true
theme: [deep-space]

---
# Lab 1: Passing Pollinators
## Joseph Borri


# Dataset


```js
// view(aapl)
Inputs.table(pollinators)
```

# 1. What is the body mass and wing span distribution of each pollinator species observed?

```js
const pollinators = FileAttachment("./data/pollinator_activity_data.csv").csv()
const body_mass = "avg_body_mass_g"
const wingspan = "avg_wing_span_mm"
const time_of_day = "observation_hour"
const visits = "visit_count"
// (x-axis variable will be chosen later in the weather section)

```

```js
Plot.plot({
    // width: 700,
    // height: 400,
    x: {ticks: d3.range(0, 0.6, 0.1),
        domain: [0, 0.55]
        },
    y: {ticks: d3.range(10, 60, 5),
        domain: [10, 60],
    },   
    title: "Mass/Wingspan Ratio Among Pollinating Species",
    // subtitle: "See which species has the largest ratio",
    color: {legend: true},
    marks: [
        Plot.frame(),
        Plot.density(pollinators, { 
            x: body_mass, fill:"pollinator_species", fillOpacity: 0.4,
            y: wingspan,
            tip: true,
            stroke: "pollinator_species", strokeOpacity: 0.6,
            sort: {y: "-x", y: "-x", },  
            }),
            ]
})
```

As you can see, not only are carpenter bees the largest among the observed species, they also have the highest variation.


# 2. What is the ideal weather (conditions, temperature, etc) for pollinating?

```js

Plot.plot({
    width: 700,
    height: 400, 
    title: "Ideal Temperature for Pollinator Visits",
    // subtitle: "See which species has the largest ratio",
    color: {legend: true},
    marks: [
        Plot.frame(),
        Plot.barX(pollinators, { 
            x: "temperature", fill:"flower_species", fillOpacity: 0.4,
            y: "visit_count",
            // fy: "pollinator_species",
            tip: true,
            // stroke: "pollinator_species", strokeOpacity: 0.6,
            sort: { },  
            }),
            ]
})
```
```js
Plot.plot({
  marginLeft: 60,
  x: {label: "Frequency →"},
  y: {label: null},
  color: {legend: true},
  marks: [
    Plot.barX(pollinators, {y: "observation_hour", x: 1, inset: 0.5, fill: "flower_species", sort: "visit_count",tip: true,}),
    Plot.ruleX([0]),
    
  ]
})
```