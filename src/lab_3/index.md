---
title: "Lab 3: Mayoral Mystery"
toc: false
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).

<!-- Import Data -->
```js
const nyc = await FileAttachment("data/nyc.json").json();
const results = await FileAttachment("data/election_results.csv").csv({ typed: true });
const survey = await FileAttachment("data/survey_responses.csv").csv({ typed: true });
const events = await FileAttachment("data/campaign_events.csv").csv({ typed: true });

// NYC geoJSON data
display(nyc)
// Campaign data (first 10 objects)
display(results.slice(0,10))
display(survey.slice(0,10))
display(events.slice(0,10))
```


```js
// The nyc file is saved in data as a topoJSON instead of a geoJSON. Thats primarily for size reasons -- it saves us 3MB of data. For Plot to render it, we have to convert it back to its geoJSON feature collection. 
const districts = topojson.feature(nyc, nyc.objects.districts)
display(districts)
```

```js
//joining data on BoroCD
const votingMap = new Map(results.map(r => [r.boro_cd, r.votes_candidate]))
const incomeMap = new Map(results.map(r => [r.boro_cd, r.median_household_income]))

// Join votes_candidate and median_household_income to each district feature
const districtsWithData = districts.features.map(feature => {
  const boroCd = feature.properties.BoroCD;
  const votes = votingMap.get(boroCd);
  const income = incomeMap.get(boroCd);
  return {
    ...feature,
    properties: {
      ...feature.properties,
      votes_candidate: votes,
      median_household_income: income
    }
  };
});

// Create a new feature collection with joined data
const districtsJoined = {
  ...districts,
  features: districtsWithData
};
```

```js
// Simple rendering of the NYC districts topoJSON
Plot.plot({
  // this projection is already zoomed into NYC
  projection: {
    domain: districtsJoined,
    type: "mercator",
  },
  color: {
    type: "quantile",
    n: 9,
    scheme: "purples",
    legend: true,
    label: "Votes for Candidate"
  },
  marks: [
    Plot.geo(districtsJoined, {
      fill: d => d.properties.votes_candidate,
      stroke: "black",
      strokeWidth: 0.5
    }),
    Plot.dot(districtsJoined, Plot.geoCentroid({
      r: d => d.properties.median_household_income,
      fill: "limegreen",
      tip: true,
      channels: { "name": "name", id: "id" },
    }))
    
  ]
})
```

```js
// Choropleth map showing median_household_income by boro_cd
Plot.plot({
  // this projection is already zoomed into NYC
  projection: {
    domain: districtsJoined,
    type: "mercator",
  },
  color: {
    type: "quantile",
    n: 4,
    scheme: "blues",
    legend: true,
    label: "Median Household Income ($)"
  },
  marks: [
    Plot.geo(districtsJoined, {
      fill: d => d.properties.median_household_income,
      stroke: "black",
      strokeWidth: 0.5
    })
  ]
})
```

```js
// Calculate centroids from the district geometries
const districtCentroids = districtsJoined.features.map(feature => {
  const bounds = d3.geoBounds(feature);
  const centroid = [
    (bounds[0][0] + bounds[1][0]) / 2,
    (bounds[0][1] + bounds[1][1]) / 2
  ];
  return {
    centroid: centroid,
    median_household_income: feature.properties.median_household_income,
    votes_candidate: feature.properties.votes_candidate,
    boro_cd: feature.properties.BoroCD
  };
});

// Plot with dots
Plot.plot({
  projection: {
    type: "mercator",
    domain: districtsJoined
  },
  marks: [
    Plot.geo(districtsJoined, {
      fill: d => d.properties.median_household_income,
      stroke: "black",
      strokeWidth: 0.5,
      fillOpacity: 0.3
    }),
    Plot.dot(districtCentroids,
    Plot.geoCentriod({
      r: d => d.properties.median_household_income // Scale radius
    })),
      
    })
  ]
})
```