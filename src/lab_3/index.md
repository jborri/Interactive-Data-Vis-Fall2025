---
title: "Lab 3: Mayoral Mystery"
toc: false
theme: [coffee, alt, wide]
style: custom-style.css

---
#  (´ε｀ )⊹♡Mayoral Mystery♡⊹ ( ˘ ³˘)
## Joseph Borri, Lab 3: Interactive Data Visualization, Fall 2025


# Datasets used:
## for full transparancy
## Results
```js
Inputs.table(results)
```
## Survey
```js
Inputs.table(survey)
```
## Events
```js
Inputs.table(survey)
```
<!-- Import Data -->
```js
const nyc = await FileAttachment("data/nyc.json").json();
const results = await FileAttachment("data/election_results.csv").csv({ typed: true });
const survey = await FileAttachment("data/survey_responses.csv").csv({ typed: true });
const events = await FileAttachment("data/campaign_events.csv").csv({ typed: true });

// NYC geoJSON data
// display(nyc)
// Campaign data (first 10 objects)
// display(results.slice(0,10))
// display(survey.slice(0,10))
// display(events.slice(0,10))
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
const cateMap = new Map(results.map(r => [r.boro_cd, r.income_category]))


// Join votes_candidate and median_household_income to each district feature
const districtsWithData = districts.features.map(feature => {
  const boroCd = feature.properties.BoroCD;
  const votes = votingMap.get(boroCd);
  const income = incomeMap.get(boroCd);
  const category = cateMap.get(boroCd);
  return {
    ...feature,
    properties: {
      ...feature.properties,
      votes_candidate: votes,
      median_household_income: income,
      income_category: category
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
const incomeColors = {
  Low: "#B2D732",
  Middle: "#4424D6",
  High: "#e285fb" 
};
```

<!-- ```js
Plot.plot({
  r: {range: [0, 7]},
  projection: {
    domain: districtsJoined,
    type: "mercator",
  },
  color: {
    type: "sequential",
    n: 5,
    scheme: "BuPu",
    legend: true,
    label: "Votes for Candidate (in thousands)"
  },
  marks: [
    Plot.geo(districtsJoined, {
      fill: d => d.properties.votes_candidate/1000,
      stroke: "black",
      strokeWidth: 0.5
    }),
    Plot.dot(districtsJoined, Plot.geoCentroid({
      r: d => d.properties.median_household_income/10000,
      fill: d => incomeColors[d.properties.income_category] ?? "#666",
      tip: {
      channels: { Income: "median_household_income", votes: "votes_candidate", category:"income_category" },
          format: {
 
    }},
    })), 
  ]
})
```
```js
    Plot.legend({
  color: {
    type: "ordinal",
    domain: Object.keys(incomeColors),
    range: Object.values(incomeColors),
    label: "Income category"
  }
})
``` -->

<!-- ```js

Plot.plot({
 margin: 50,

  marks: [
Plot.frame(),
Plot.dot(districtsJoined, {
  x: d => d.properties.votes_candidate,
  y: d => d.properties.median_household_income,
  fill: d => d.properties.income_category,
  tip: true,
})

  ]
})
``` -->
# Background
Before we begin to discuss areas in which the candidate and their team succeeded and areas where they could make changes for next time, we first need to examine the results of their recent election cycle.

 Before we look, we must ask the following questions: *who* is voting? *how* can we engage this audience? *what* entices them to vote? *when*/*where* can we find them?

# Demographics
## Let's begin by taking a look at how demographics played a roll in the mayoral race

<div class="grid grid-cols-2">
  <div class="card grid-rowspan-2"><h2>Votes For Candidate by District</h2><h3>Circle size indicates the average income of that district</h3>${Plot.plot({
  r: {range: [0, 7]},
  projection: {
    domain: districtsJoined,
    type: "mercator",
  },
  color: {
    type: "sequential",
    n: 5,
    scheme: "BuPu",
    legend: true,
    label: "Votes for Candidate (in thousands)"
  },
  marks: [
    Plot.geo(districtsJoined, {
      fill: d => d.properties.votes_candidate/1000,
      stroke: "black",
      strokeWidth: 0.5
    }),
    Plot.dot(districtsJoined, Plot.geoCentroid({
      r: d => d.properties.median_household_income/10000,
      fill: d => incomeColors[d.properties.income_category] ?? "#666",
      tip: true,
      channels: { Income: "median_household_income", votes: "votes_candidate", category:"income_category" },
    })), 
  ]
})} ${    Plot.legend({
  color: {
    type: "ordinal",
    domain: Object.keys(incomeColors),
    range: Object.values(incomeColors),
    label: "Income category"
  }
})}</div>
  <div class="card"><h2>Votes For Candidate vs. Opponent</h2>
  ${Plot.plot({
  y: {label: "Total votes", grid: true},
  x: {label: " "},
  marks: [
    Plot.barY(voteTotals, {x: "name", y: "votes", fill: "name", tip: true}),
    Plot.ruleY([0])
  ],
  color: {type: "ordinal",
    range: colors, legend: true}
})} 
</div>

   <div class="card"><h2>Votes For Candidate by Age</h2>${Plot.plot({
  marginBottom: 60,
  x: {
    tickRotate: -30,
  },
  y: {
    transform: (d) => d,
    // label: "↑ Market value (US dollars, billions)",
    grid: 5
  },
    color: {
    type: "ordinal",
    range: colors,
    legend: true,
    label: "Votes for Candidate (in thousands)"
  },
  tip: true,
  marks: [
    Plot.rectY(survey, Plot.binX({y: "count"}, {x: "age", fill: "voted", tip: true,
      channels: { Income: "median_household_income", votes: "votes_candidate" }, })),]})}
  </div>


</div>
 As you can see, there is a clear distinction between income and votes received. The candidate was highly favored by low-income residents. It also appears that the candidate lost the middle aged voters.

# Policy
## Let's begin by taking a look at how policy played a roll in the mayoral race

```js
//removing nulls
const cleanSurvey = survey.filter(d => d.age != null && d.voted_for != null)
```
```js
const colors = ["#e285fb",  "#B2D732", "#8457cc", "#c13a6b"];
```


```js
const policies = [
  { key: 'affordable_housing_alignment', label: 'Affordable Housing' },
  { key: 'public_transit_alignment', label: 'Public Transit' },
  { key: 'childcare_support_alignment', label: 'Childcare Support' },
  { key: 'small_business_tax_alignment', label: 'Small Business Tax' },
  { key: 'police_reform_alignment', label: 'Police Reform' }
];
```


```js
const incomeByBoro = new Map(results.map(d => [d.boro_cd, d.income_category]));
  // 1) derive policy keys from the survey columns
const policyKeys = Object.keys(survey[0]).filter(k => k.endsWith("_alignment"));

const policyAvg = policyKeys.map(k => ({
  policy: k,
  avg: d3.mean(survey, d => d[k])
}));
```
```js
// 2) reshape to long
const policyKeys = Object.keys(survey[0]).filter(k => k.endsWith("_alignment"));
const policyLong = survey.flatMap(d =>
  policyKeys.map(k => ({
    policy: k,
    alignment: d[k],
    voted_for: d.voted_for,
    income_category: incomeByBoro.get(d.boro_cd)
  }))
).filter(d => d.alignment != null && d.voted_for != null);

```

```js
Plot.plot({
  fx: {label: "Policy", tickRotate: -4},        // one column per policy
  y: {label: "Average alignment", grid: true},
  color: {type: "ordinal",
    range: colors,
    legend: true,},
  marks: [
    Plot.barY(
      policyLong,
      Plot.groupX(
        {y: "mean"},
        {
          fx: "policy",          // facet
          x: "voted_for",        // group within facet
          y: "alignment",
          fy: "income_category",
          fill: "income_category",
          tip: true
        }
      )
    ),
    Plot.ruleY([0])
  ]
})
```

```js
// Simple bar chart: total votes for candidate vs opponent
const voteTotals = [
  {name: "Candidate", votes: d3.sum(results, d => d.votes_candidate)},
  {name: "Opponent", votes: d3.sum(results, d => d.votes_opponent)}
];
```
