---
title: "Lab 2: Subway Staffing"
toc: true
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
```
```js
const ridershipWithTraffic = ridership.map(d => ({
  ...d,
  traffic: d.entrances + d.exits
}))
const ridershipWithFare = ridershipWithTraffic.map(d => ({
  ...d,
  fare_period: d.date < new Date("2025-07-15") ? "Before Fare Increase" : "After Fare Increase"
}))
```

## 1. How did local events impact ridership in summer 2025? What effect did the July 15 fare increase have?

```js
```js
Plot.plot({
  title: "Average Traffic Before vs After Fare Increase",
  y: { label: "Average Traffic" },
  x: { label: "Fare Period" },
  marks: [
    Plot.barY(
      ridershipWithFare,
      Plot.groupX(
        { y: "mean" },
        { x: "fare_period", y: "traffic", fill: "green" }
      )
    )
  ]
})
```

This bar chart compares average subway traffic before and after the July 15 fare increase. The data shows the average ridership slightly decreased after July 15, suggesting that the fare increase may have had a small negative impact on subway usage among locals, although overall demand remained relatively high. The subway system still remains essential despite the increase.

```js
Plot.plot({
  title: "Daily Subway Traffic with Fare Increase Marker",
  y: { label: "Traffic" },
  x: { label: "Date" },
  marks: [
    Plot.lineY(ridershipWithTraffic, {
      x: "date",
      y: "traffic",
      stroke: "steelblue"
    }),
    Plot.ruleX([new Date("2025-07-15")], { stroke: "red" }),
    Plot.text([new Date("2025-07-15")], {
      x: d => d,
      y: 25000,
      text: "Fare Increase",
      fill: "red",
      dy: -10
    })
  ]
})
```

 Local events appear to contribute to noticeable spikes in ridership at nearby stations during summer 2025. Periods of increased traffic often align with likely event activity, suggesting that scheduled events play a meaningful role in driving short-term demand, even alongside broader policy changes like the fare increase.

## 2. How do the stations compare when it comes to response time? Which are the best, which are the worst?

```js
Plot.plot({
  title: "Average Response Time by Station",
  marginLeft: 140,
  x: { label: "Average Response Time (minutes)" },
  y: { label: null },
  marks: [
    Plot.barX(
      incidents,
      Plot.groupY(
        { x: "mean" },
        { y: "station", x: "response_time_minutes", sort: { y: "x" }, fill: "steelblue" }
      )
    )
  ]
})
```

This chart ranks stations by average incident response time, showing clear differences in performance across locations. Stations with lower response times are more efficient, while those with higher response times may face operational challenges such as higher traffic, staffing limitations, or more complex incidents.

```js
const staffingPressure = Object.entries(
  upcoming_events.reduce((acc, d) => {
    acc[d.nearby_station] = (acc[d.nearby_station] || 0) + d.expected_attendance
    return acc
  }, {})
).map(([station, total_attendance]) => ({
  station,
  total_attendance,
  staffing: currentStaffing[station],
  pressure: total_attendance / currentStaffing[station]
}))
```

## 3. Which three stations need the most staffing help for next summer based on the 2026 event calendar?

```js
Plot.plot({
  title: "Stations with the Highest Staffing Pressure for Summer 2026",
  marginLeft: 140,
  x: { label: "Event Attendance per Current Staff Member" },
  y: { label: null },
  marks: [
    Plot.barX(staffingPressure, {
      x: "pressure",
      y: "station",
      sort: { y: "-x" },
      fill: "purple"
    })
  ]
})
```

This chart estimates staffing pressure by comparing projected event attendance in summer 2026 to current station staffing levels. Stations with the highest pressure values are likely to experience the greatest strain, indicating a clear need for additional staffing at those locations to manage anticipated demand.

## Conclusion

Overall, the dashboard shows that ridership demand, response performance, and projected event pressure vary substantially across stations, which supports targeted staffing decisions rather than uniform staffing increases.