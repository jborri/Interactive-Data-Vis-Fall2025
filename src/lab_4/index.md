---
title: "Lab 4: Clearwater Crisis"
toc: false
---

<!-- Import Data -->
```js
const fishes = await FileAttachment("data/fish_surveys.csv").csv({ typed: true });
const water = await FileAttachment("data/water_quality.csv").csv({ typed: true });
```

```js
Inputs.table(fishes)
```
```js
display(fishes)
```

```js
//fixing date
const fishesByDate = fishes
  .sort((a, b) => new Date(a.date) - new Date(b.date))
  .map(d => ({
    date: new Date(d.date),
    length: d.avg_length_cm,
    species: d.species,
    station: d.station_id,
    weight: d.avg_weight_g,
    count: d.count,
  }));
```

```js
  //finding deviation from average
//weight
const troutAvgWeight = d3.mean(
    fishesByDate.filter(d => d.species === "Trout"),
    d => d.weight
);

const troutWithDeviation = fishesByDate
    .filter(d => d.species === "Trout")
    .map(d => ({
        ...d,
        deviation_weight: d.weight - troutAvgWeight
    }));
//length
    const troutAvgLength = d3.mean(
    fishesByDate.filter(d => d.species === "Trout"),
    d => d.length
);

const troutWithDeviationLength = fishesByDate
    .filter(d => d.species === "Trout")
    .map(d => ({
        ...d,
        deviation_length: d.length - troutAvgLength
    }));

const troutCount = fishesByDate
    .filter(d => d.species === "Trout")
    .map(d => ({
        ...d,
        count: d.count
    }));
```

<div style="text-align: center; padding: 0 200px; font-size: 36px; font-weight: 700;">Establishing a Starting Point</div>

<div style="text-align: center; padding: 20px 200px; font-size: 20px; font-weight: 400;font-style:italic">Let's begin by analyzing the health of the fish populations in Clearwater Lake. Since trout are the most sensitive to pollutants, I believe their health metrics will provide the most obvious clues.
</div>

<div style="text-align: center; padding: 0 200px; font-size: 36px; font-weight: 700;"> Quality of Trout Populations by Location in Clearwater Lake </div>

<!-- ```js
//weight plot
Plot.plot({
    marks: [
        Plot.ruleY([0]),
        Plot.ruleX([new Date('2023-04-01')], {
      stroke: "orange", 
      tip: true,
      }),
              Plot.ruleX([new Date('2023-05-30')], {
      stroke: "orange", 
      tip: true,
      }),
        Plot.line(troutWithDeviation, {
            x: d => d.date,
            y: d => d.deviation_weight,
            fy: d => d.station,
            stroke: "black"
        }),
        Plot.dot(troutWithDeviation, {
            x: d => d.date,
            y: d => d.deviation_weight,
            fy: d => d.station,
            fill: d => d.deviation_weight > 0 ? "green" : "red",
            tip: true,
            title: (d) =>
        `Weight: ${d.deviation_weight.toFixed(1)} grams`
        })
    ]
})
```

```js
//length plot
Plot.plot({
    marks: [
        Plot.ruleY([0]),
        Plot.line(troutWithDeviationLength, {
            x: d => d.date,
            y: d => d.deviation_length,
            fy: d => d.station,
            stroke: "black"
        }),
        Plot.dot(troutWithDeviationLength, {
            x: d => d.date,
            y: d => d.deviation_length,
            fy: d => d.station,
            fill: d => d.deviation_length > 0 ? "green" : "red",
            tip: true,
            title: (d) =>
        `Weight: ${d.deviation_length.toFixed(1)} cm`
        })
    ]
})
``` -->

<div class="grid grid-cols-2">
<div class="card grid-rowspan-1"><h2>Change in Trout Length Throughout the Year</h2><h3>Plotted against the calculated average length.</h3>${Plot.plot({
    marks: [
        Plot.ruleY([0]),
        Plot.line(troutWithDeviationLength, {
            x: d => d.date,
            y: d => d.deviation_length,
            fy: d => d.station,
            stroke: "black"
        }),
        Plot.dot(troutWithDeviationLength, {
            x: d => d.date,
            y: d => d.deviation_length,
            fy: d => d.station,
            fill: d => d.deviation_length > 0 ? "green" : "red",
            tip: true,
            title: (d) =>
        `Length Deviation: ${d.deviation_length.toFixed(1)} cm`
        })
    ]
})}</div>
<div class="card"><h2>Change in Trout Weight Throughout the Year</h2><h3>Plotted against the calculated average weight.</h3>${Plot.plot({
    marks: [
        Plot.ruleY([0]),
        Plot.line(troutWithDeviation, {
            x: d => d.date,
            y: d => d.deviation_weight,
            fy: d => d.station,
            stroke: "black"
        }),
        Plot.dot(troutWithDeviation, {
            x: d => d.date,
            y: d => d.deviation_weight,
            fy: d => d.station,
            fill: d => d.deviation_weight > 0 ? "green" : "red",
            tip: true,
            title: (d) =>
        `Weight Deviation: ${d.deviation_weight.toFixed(1)} grams`
        })
    ]
})}</div>
</div>

<div style="text-align: center; padding: 20px 200px; font-size: 20px; font-weight: 400;font-style:italic">Length and weight (both indicators of health) seem fairly consistent. <br>A few blips(possibly natural variation) but nothing difinitive aside from what seems to be a decrease in both weight and length for the western population. <br><br>Let's put a pin in this for now and take a look at count next:</div>

<!-- ```js
Plot.plot({
    color: {legend: true},
    marks: [
        Plot.frame(),
        Plot.line(troutCount, {
            x: d => d.date,
            y: d => d.count,
            fy: d => d.station,
            stroke: d => d.species,
            
            
        })
    ]
})
``` -->
<div class="grid grid-cols-2">
<div class="card">${Plot.plot({
    marks: [
        Plot.frame(),
        Plot.line(troutCount, {
            x: d => d.date,
            y: d => d.count,
            fy: d => d.station,
            stroke: d => d.species,            
        })
    ]
})}</div>
<div style="text-align: center;padding: 200px 0 0 0; font-size: 20px; font-weight: 400;font-style:italic">Well it seems trout populations remain fairly consistent in every region of the lake aside from the west and south sides.... But maybe it has something to do with water quality... I mean surely no one is dumping excess amounts of pollutants into the lake.</div>
</div>
