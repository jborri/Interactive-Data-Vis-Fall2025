---
title: "Lab 2: Subway Staffing"
toc: true
theme: coffee
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).


<!-- Import Data -->
```js
const incidents = FileAttachment("./data/incidents.csv").csv({ typed: true })
const local_events = FileAttachment("./data/local_events.csv").csv({ typed: true })
const upcoming_events = FileAttachment("./data/upcoming_events.csv").csv({ typed: true })
const ridership = FileAttachment("./data/ridership.csv").csv({ typed: true })

```

<!-- Include current staffing counts from the prompt -->

```js
const currentStaffing = {
  "Times Sq-42 St": 19,
  "Grand Central-42 St": 18,
  "34 St-Penn Station": 15,
  "14 St-Union Sq": 4,
  "Fulton St": 17,
  "42 St-Port Authority": 14,
  "Herald Sq-34 St": 15,
  "Canal St": 4,
  "59 St-Columbus Circle": 6,
  "125 St": 7,
  "96 St": 19,
  "86 St": 19,
  "72 St": 10,
  "66 St-Lincoln Center": 15,
  "50 St": 20,
  "28 St": 13,
  "23 St": 8,
  "Christopher St": 15,
  "Houston St": 18,
  "Spring St": 12,
  "Chambers St": 18,
  "Wall St": 9,
  "Bowling Green": 6,
  "West 4 St-Wash Sq": 4,
  "Astor Pl": 7
}

//converting currentStaffing object to an array?
const currentStaffingArray = Object.keys(currentStaffing).map(function(stationName) {
  return {
    stationName: stationName,
    currentStaffingCount: currentStaffing[stationName]
  };
});
```

```js
//basically making stations an array?
const allStations = local_events.map(d => d.nearby_station)
const setStations = new Set(allStations)
const uniqueStations = Array.from(setStations)
//basically making events an array?
const allEvents = local_events.map(d => d.event_name)
const setEvents = new Set(allEvents)
const uniqueEvents = Array.from(setEvents)
//making new date
const data = local_events.map(d => ({
  ...d,
  date: new Date(d.date)
}));
//used to make line for average attendance
const avgAttendance = d3.mean(data, d => d.estimated_attendance);
const avgRidership = d3.mean(ridership, d => d.entrances + d.exits);


//summing ridership by date
const ridershipByDate1 = ridership.map(d => ({
  date: new Date(d.date),
  total: d.entrances + d.exits,
  station: d.station,
  avg_daily: (d.entrances + d.exits) / 2,
}))

//joining ridership with local_events
const ridershipWithEvents = ridershipByDate1.map(ridershipRow => {
  const matchingEvent = data.find(eventRow => 
    eventRow.date.getTime() === ridershipRow.date.getTime() &&
    eventRow.nearby_station === ridershipRow.station
  );
  
  if (matchingEvent) {
    return {
      ...ridershipRow,
      event_name: matchingEvent.event_name,
      hasEvent: true
    };
  } else {
    return {
      ...ridershipRow,
      hasEvent: false
    };
  }
});

// Calculate total ridership per station
// Step 1: Create an empty object to store totals
const totalRidershipByStation = {};

// Step 2: Loop through each ridership row and add up totals by station
ridershipByDate1.forEach(function(row) {
  const stationName = row.station;
  if (totalRidershipByStation[stationName]) {
    // If station already exists, add to the total
    totalRidershipByStation[stationName] = totalRidershipByStation[stationName] + row.total;
  } else {
    // If station doesn't exist yet, start with this row's total
    totalRidershipByStation[stationName] = row.total;
  }
});

// Step 3: Convert the object into an array for plotting
const ridershipTotals = Object.keys(totalRidershipByStation).map(function(stationName) {
  return {
    station: stationName,
    totalRidership: totalRidershipByStation[stationName]
  };
});

// Calculate average response time per station for normalization
const responseTimeByStation = {};
incidents.forEach(function(incident) {
  const stationName = incident.station;
  if (!responseTimeByStation[stationName]) {
    responseTimeByStation[stationName] = [];
  }
  responseTimeByStation[stationName].push(incident.response_time_minutes);
});

const avgResponseTimeByStation = {};
Object.keys(responseTimeByStation).forEach(function(stationName) {
  const times = responseTimeByStation[stationName];
  const sum = times.reduce(function(acc, time) {
    return acc + time;
  }, 0);
  avgResponseTimeByStation[stationName] = sum / times.length;
});

// Normalize both metrics to 0-1 scale so they can be compared on same axis
// Step 1: Find min and max for ridership
let minRidership = Infinity;
let maxRidership = -Infinity;
ridershipTotals.forEach(function(row) {
  if (row.totalRidership < minRidership) minRidership = row.totalRidership;
  if (row.totalRidership > maxRidership) maxRidership = row.totalRidership;
});

// Step 2: Find min and max for response time
let minResponseTime = Infinity;
let maxResponseTime = -Infinity;
Object.keys(avgResponseTimeByStation).forEach(function(stationName) {
  const time = avgResponseTimeByStation[stationName];
  if (time < minResponseTime) minResponseTime = time;
  if (time > maxResponseTime) maxResponseTime = time;
});

// Step 3: Create normalized data for plotting
const normalizedComparisonData = ridershipTotals.map(function(row) {
  // Normalize ridership to 0-1
  const normalizedRidership = (row.totalRidership - minRidership) / (maxRidership - minRidership);
  
  // Get response time for this station and normalize to 0-1
  const responseTime = avgResponseTimeByStation[row.station];
  const normalizedResponseTime = responseTime 
    ? (responseTime - minResponseTime) / (maxResponseTime - minResponseTime)
    : null;
  
  return {
    station: row.station,
    totalRidership: row.totalRidership,
    normalizedRidership: normalizedRidership,
    avgResponseTime: responseTime,
    normalizedResponseTime: normalizedResponseTime
  };
});

```



## 1. How did local events impact ridership in summer 2025? What effect did the July 15th fare increase have?
```js
display(ridership[0])
const selectedLine = view(Inputs.select(uniqueStations))
```

```js
Plot.plot({
   x: {
    type: "time",
    label: "Date",
    domain: [new Date('2025-06-01'), new Date('2025-08-14')]},
  marks: [
    Plot.frame(),
    Plot.ruleX([new Date('2025-07-15')], {
      stroke: "orange", 
      // tip: true,
      }),
    Plot.line(ridershipByDate1.filter(d => d.station === selectedLine), {
      x: (d) => d.date,
      y: (d) => d.total,
      // tip: true,
    }),
    Plot.dot(ridershipWithEvents.filter(d => 
      d.hasEvent && d.station === selectedLine
    ), {
      x: (d) => d.date,
      y: (d) => d.total,
      title: (d) => d.event_name, 
      fill: "red",
      size: 50,
      tip: true,
    }),
  ]
})
```
As you can see, ridership spikes around local events. There is a noticeable drop in ridership following the fare increase on July 15th, 2025.

## 2. How do the stations compare when it comes to response time? Which are the best, which are the worst?



```js
Plot.plot({
  width: width,
  x: {
    label: "Normalized Value (0-1)",
    domain: [0, 1]
  },
  marks: [
    Plot.frame(),
    // Bars showing normalized average response time per station
    Plot.barX(normalizedComparisonData.filter(d => d.normalizedResponseTime !== null), {
      x: (d) => d.normalizedResponseTime,
      y: (d) => d.station,
      fill: "gray",
      fillOpacity: 0.7,
      tip: true,
      title: (d) => `${d.station}: ${d.avgResponseTime.toFixed(1)} min avg response time`
    }),
    // Dots showing normalized total ridership per station
    Plot.dot(normalizedComparisonData, {
      x: (d) => d.normalizedRidership,
      y: (d) => d.station,
      fill: "red",
      size: 100,
      tip: true,
      title: (d) => `${d.station}: ${d.totalRidership.toLocaleString()} total riders`
    })
  ]
  
})
```

<!-- ```js
Plot.plot({
  width: width,
  
  marks: [
    Plot.frame(),
    Plot.barX( incidents,
      Plot.groupY(
        {x: "mean"}, 
        {y: "station", x: "response_time_minutes", fill: "station", fillOpacity: 0.7 , tip: true,},       
      )
    ),
    Plot.dot(ridershipTotals, {
      x: (d) => d.totalRidership,
      y: (d) => d.station,
      fill: "blue",
      size: 100,
      tip: true,
      title: (d) => `${d.station}: ${d.totalRidership.toLocaleString()} total riders`
    })
  ]
  // should i normalize the scales, or convert minutes to seconds? D;
})
```
```js
Plot.plot({
  width: width,
  
  marks: [
    Plot.frame(),
    Plot.barX( incidents,
      Plot.groupY(
        {x: "mean"}, 
        {y: "station", x: "response_time_minutes", fill: "station", fillOpacity: 0.7 , tip: true,},       
      )
    ),

  ]
  
})
``` -->


As you can see, stations with higher traffic have lower response times. 86th, houston and 50th stations seem to be the best.

## 3. Which three stations need the most staffing help for next summer based on the 2026 event calendar?

```js
// Analyze 2026 upcoming events to determine staffing needs
// Step 1: Count events and sum expected attendance per station
const eventsByStation2026 = {};

upcoming_events.forEach(function(event) {
  const stationName = event.nearby_station;
  if (!eventsByStation2026[stationName]) {
    eventsByStation2026[stationName] = {
      eventCount: 0,
      totalExpectedAttendance: 0
    };
  }
  eventsByStation2026[stationName].eventCount += 1;
  eventsByStation2026[stationName].totalExpectedAttendance += event.expected_attendance;
});

// Step 2: Calculate average response time per station from historical data
const responseTimeByStation = {};
incidents.forEach(function(incident) {
  const stationName = incident.station;
  if (!responseTimeByStation[stationName]) {
    responseTimeByStation[stationName] = [];
  }
  responseTimeByStation[stationName].push(incident.response_time_minutes);
});

const avgResponseTimeByStation = {};
Object.keys(responseTimeByStation).forEach(function(stationName) {
  const times = responseTimeByStation[stationName];
  const sum = times.reduce(function(acc, time) {
    return acc + time;
  }, 0);
  avgResponseTimeByStation[stationName] = sum / times.length;
});

// Step 3: Create staffing need analysis
// Stations need more help if they have:
// - Many events in 2026
// - High expected attendance
// - Low current staffing
// - Poor historical response times
const staffingNeedAnalysis = Object.keys(eventsByStation2026).map(function(stationName) {
  const events = eventsByStation2026[stationName];
  const currentStaff = currentStaffing[stationName] || 0;
  const avgResponseTime = avgResponseTimeByStation[stationName] || 0;
  
  // Calculate a "need score" - higher means more help needed
  // Normalize each factor and combine them
  const eventScore = events.eventCount / 10; // Normalize by max events
  const attendanceScore = events.totalExpectedAttendance / 100000; // Normalize by max attendance
  const lowStaffingScore = (20 - currentStaff) / 20; // Lower staffing = higher need
  const poorResponseScore = avgResponseTime / 25; // Higher response time = higher need
  
  const needScore = (eventScore * 0.3) + (attendanceScore * 0.3) + (lowStaffingScore * 0.2) + (poorResponseScore * 0.2);
  
  return {
    station: stationName,
    eventCount: events.eventCount,
    totalExpectedAttendance: events.totalExpectedAttendance,
    currentStaffing: currentStaff,
    avgResponseTime: avgResponseTime,
    needScore: needScore
  };
});

// Step 4: Sort by need score and get top 3
const top3StationsNeedingHelp = staffingNeedAnalysis
  .sort(function(a, b) {
    return b.needScore - a.needScore; // Sort descending
  })
  .slice(0, 3);

// Display the results
display(top3StationsNeedingHelp.map(function(station, index) {
  return {
    rank: index + 1,
    station: station.station,
    events: station.eventCount,
    expectedAttendance: station.totalExpectedAttendance.toLocaleString(),
    currentStaffing: station.currentStaffing,
    avgResponseTime: station.avgResponseTime.toFixed(1) + " min",
    needScore: station.needScore.toFixed(2)
  };
}));
```

```js
// Visualize staffing needs
Plot.plot({
  marks: [
    Plot.frame(),
    Plot.barX(staffingNeedAnalysis.sort((a, b) => b.needScore - a.needScore), {
      x: (d) => d.needScore,
      y: (d) => d.station,
      fill: (d) => {
        // Highlight top 3 in red
        const isTop3 = top3StationsNeedingHelp.some(s => s.station === d.station);
        return isTop3 ? "red" : "green";
      },
      tip: true,
      title: (d) => {
        const isTop3 = top3StationsNeedingHelp.some(s => s.station === d.station);
        const rank = isTop3 ? top3StationsNeedingHelp.findIndex(s => s.station === d.station) + 1 : "";
        return `${d.station}\nRank: ${rank || "N/A"}\nEvents: ${d.eventCount}\nExpected Attendance: ${d.totalExpectedAttendance.toLocaleString()}\nCurrent Staff: ${d.currentStaffing}\nAvg Response: ${d.avgResponseTime.toFixed(1)} min\nNeed Score: ${d.needScore.toFixed(2)}`;
      }
    })
  ]
})
```

As you can see with the historic data and upcoming events, Canal street, Penn Station and Chambers St, will need more staffing. These stations are the confluence for the most events (and therefore, potentially the most ridership) with the lowest current staff.