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
Inputs.table(water)
```
```js
display(water)
```


```js
//fixing date for fish
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

<div style="text-align: center; padding: 20px 200px; font-size: 20px; font-weight: 400;font-style:italic">Length and weight (both indicators of health) seem fairly consistent. <br>A few blips(possibly natural variation) but nothing difinitive aside from what seems to be a decrease in both weight and length for the western population. <br><br>Let's put a pin in this for now and take a look at the water quality!</div>

```js
//fixing date for water
const waterByDate = water
  .sort((a, b) => new Date(a.date) - new Date(b.date))
  .map(d => ({
    date: new Date(d.date),
    nitrogen: d.nitrogen_mg_per_L,
    phosphorus: d.phosphorus_mg_per_L,
    heavymetal: d.heavy_metals_ppb,
    turbidity: d.turbidity_ntu,
    ph: d.ph,
    temp: d.temperature_celsius,
    station: d.station_id,
    oxygen: d.dissolved_oxygen_mg_per_L,
  }));
```
<!-- ```js
//nitrogen plot
Plot.plot({
    title: "Nitrogen",
    marks: [
        Plot.ruleY([2.0],{
            stroke:"red"
        }),
                Plot.ruleY([1.5],{
            stroke:"orange"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.nitrogen,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})
```
```js
//phos plot
Plot.plot({
    title: "Phosphorus",
    marks: [
        Plot.ruleY([0.1],{
            stroke:"red"
        }),
                Plot.ruleY([0.05],{
            stroke:"orange"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.phosphorus,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})
```
```js
//heavy metal plot
Plot.plot({
    title: "Heavy Metals",
    marks: [
        Plot.ruleY([30],{
            stroke:"red"
        }),
                Plot.ruleY([20],{
            stroke:"orange"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.heavymetal,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})
```
```js
//o2 plot
Plot.plot({
    title: "Oxygen",
    marks: [
        Plot.rectY([1], {
            y1: 4.5,
            y2: 9.5,
            fill: "green",
            fillOpacity: 0.2
        }),
        Plot.ruleY([9.5], {
            stroke: "red"
        }),
        Plot.ruleY([4.5], {
            stroke: "red"
        }),
        Plot.ruleY([0]),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.oxygen,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})
``` -->
<div class="grid grid-cols-2">
<div class="card grid-rowspan-1"><h2>Change in Nitrogen Throughout the Year</h2>${Plot.plot({
    marks: [
        Plot.ruleY([2.0],{
            stroke:"red"
        }),
                Plot.ruleY([1.5],{
            stroke:"orange"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.nitrogen,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})}</div>
<div class="card">${Plot.plot({
    title: "Phosphorus",
    marks: [
        Plot.ruleY([0.1],{
            stroke:"red"
        }),
                Plot.ruleY([0.05],{
            stroke:"orange"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.phosphorus,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})}</div>
<div class="card">${Plot.plot({
    title: "Heavy Metals",
    marks: [
        Plot.ruleY([30],{
            stroke:"red"
        }),
                Plot.ruleY([20],{
            stroke:"orange"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.heavymetal,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})}</div>
<div class="card">${Plot.plot({
    title: "pH",
    marks: [
        Plot.rectY([1], {
            y1: 6.5,
            y2: 7.6,
            fill: "green",
            fillOpacity: 0.2
        }),
        Plot.ruleY([7.6], {
            stroke: "red"
        }),
        Plot.ruleY([6.5], {
            stroke: "red"
        }),
        Plot.line(waterByDate, {
            x: d => d.date,
            y: d => d.ph,
            fy: d => d.station,
            stroke: "black"
        }),
    ]
})}</div>
</div>

<div style="text-align: center; padding: 20px 200px; font-size: 20px; font-weight: 400;font-style:italic">Nitrogen and Phosphorus spike a few times during the year, but when you look at the weight and length graphs trout, the most sensitive of the species found in Clearwater Lake, are doing fine. Although it is interesting to see that the pH does not reflect this increased nitrogen. I would have thought that there would be an increase in acidity (or a lower pH) due to nitrification. But this phenomenon pales in comparision to rhe real concern: the spikes in heavy metals in the West, which go way beyond the what is permitted. The wild west is 2/2 right now... Let's see how the trout population is doing in terms of count:</div>



<!-- ```js
// COUNT
Plot.plot({
    // color: {legend: true},
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
<div style="text-align: center;padding: 200px 0 0 0; font-size: 20px; font-weight: 400;font-style:italic">Well it seems trout populations remain fairly consistent in every region of the lake aside from the west and south sides.... Something is going on over there... Next we take a look at the suspects!</div>
</div>
