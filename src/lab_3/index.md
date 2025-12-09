---
title: "Lab 3: Mayoral Mystery"
toc: false
theme: [wide]
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
Inputs.table(events)
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
// display(districts)
```

```js
//joining data on BoroCD
const votingMap = new Map(results.map(r => [r.boro_cd, r.votes_candidate]))
const incomeMap = new Map(results.map(r => [r.boro_cd, r.median_household_income]))
const cateMap = new Map(results.map(r => [r.boro_cd, r.income_category]))


//joining votes_candidate and median_household_income to each district feature
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

//joining
const districtsJoined = {
  ...districts,
  features: districtsWithData
};
```

```js
//color scale
const incomeColors = {
  Low: "#B2D732",
  Middle: "#4424D6",
  High: "#e285fb" 
};
```
```js
//summing total votes for candidate and opponent
const voteTotals = [
  {name: "Candidate", votes: d3.sum(results, d => d.votes_candidate)},
  {name: "Opponent", votes: d3.sum(results, d => d.votes_opponent)}
];
//making it a percentage
const totalVotes = d3.sum(voteTotals, d => d.votes);
voteTotals.forEach(d => {
  d.percentage = +(d.votes / totalVotes * 100).toFixed(1);
});


const candidatePercentage = voteTotals.find(d => d.name === "Candidate")?.percentage ?? 0;
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
      strokeWidth: 0.5,
    }),
    Plot.dot(districtsJoined, Plot.geoCentroid({
      r: d => d.properties.median_household_income/10000,
      fill: d => incomeColors[d.properties.income_category] ?? "#666",

      title: (d) =>
        `Votes: ${d.properties.votes_candidate} \nMedian Income: ${d.properties.median_household_income} \nIncome Category: ${d.properties.income_category} `,
        tip: true,
      
    })),

  ],
  

})
``` -->
<!-- 
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
            title: (d) =>
        `Votes: ${d.properties.votes_candidate} \nMedian Income: ${d.properties.median_household_income} \nIncome Category: ${d.properties.income_category} `,
        tip: true,
    })), 
  ]
})} ${Plot.legend({
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
    Plot.barY(voteTotals, {x: "name", y: "votes", fill: "name",title: (d) =>
      `Votes: ${d.votes}\nPercentage: ${d.percentage}%`,
      tip: true,}),
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
    grid: 5
  },
    color: {
    type: "ordinal",
    range: colors,
    legend: true,
    label: "Voted for Candidate"
  },
  tip: true,
  marks: [
    Plot.rectY(survey, Plot.binX({y: "count"}, {x: "age", fill: "voted", tip: true,
      channels: { Income: "median_household_income", votes: "votes_candidate" }, })),]})}
  </div>


</div>
 As you can see, there is a clear distinction between income and votes received. The candidate was highly favored by low-income residents. It also appears that the candidate lost the middle aged voters.

# Policy
## Next, let us take a look at how policy played a roll in the mayoral race



```js
const colors = ["#e285fb",  "#B2D732", "#8457cc", "#c13a6b"];
```


```js
//mapping income category by borough code
const incomeByBorough = new Map(results.map(d => [d.boro_cd, d.income_category]));

const policyKeys = Object.keys(survey[0]).filter(k => k.endsWith("_alignment"));

const policyAvg = policyKeys.map(k => ({
  policy: k,
  avg: d3.mean(survey, d => d[k])
}));

//array and removing nulls
const policyList = survey.flatMap(d =>
  policyKeys.map(k => ({
    policy: k,
    alignment: d[k],
    voted_for: d.voted_for,
    income_category: incomeByBorough.get?.(d.boro_cd) ?? "unknown"
  }))
).filter(d => d.alignment != null && d.voted_for != null);
```


<div class="grid grid-cols-2">
<div class="card grid-rowspan-1"><h2>Average Policy Alignment by Income Level</h2>${
Plot.plot({
  fx: {label: "Policies"},   
  y: {label: "Average alignment", grid: true},
  x: {label: "Voted For"},
  fy: {label: "Income Category"},
  color: {type: "ordinal",
    range: colors,
    legend: true,},
  marks: [
    Plot.barY(
      policyList,
      Plot.groupX(
        {y: "mean"},
        {
          fx: d => policyLabels[d.policy] || d.policy,     
          x: "voted_for",        
          y: "alignment",
          fy: "income_category",
          fill: "income_category",
          tip: true,
        }
      )
    ),Plot.ruleY([0])]})}</div>
<div class="card grid-colspan-1" style="max-height:550px;overflow:auto"><h2>Open Responses from Constituents on Policy</h2>
  ${survey
    .filter(d => d.open_response)
    .slice(0,50)
    .map(d => html`<p><strong>Age ${d.age}, ${d.voted_for}:</strong> ${d.open_response}</p>`)}</div>
</div>

# For the Next Campaign
## Finally, based on the data presented above, here are some recommendations for the next campaign:
- Focus on engaging middle-aged voters, as they showed lower support for the candidate.
- Leverage the strong support from low-income communities by organizing more events in these areas.
- Analyze open-ended feedback to identify specific issues that resonated with voters and incorporate these into future campaign strategies.
- Consider targeted outreach efforts in districts with lower voter turnout to boost engagement.

```js

const policyLabels = {
  affordable_housing_alignment: 'Affordable Housing',
  public_transit_alignment: 'Public Transit',
  childcare_support_alignment: 'Childcare Support',
  small_business_tax_alignment: 'Small Business Tax',
  police_reform_alignment: 'Police Reform'
};
```



