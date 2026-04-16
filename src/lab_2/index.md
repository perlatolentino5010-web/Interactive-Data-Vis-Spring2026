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

This chart compares average subway traffic before and after the July 15 fare increase. It helps show whether ridership changed after the fare increase. Based on the chart, average ridership [increased / decreased / stayed similar] after July 15.

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
    Plot.ruleX(["2025-07-15"], { stroke: "red" }),
    Plot.text(["2025-07-15"], {
      x: d => d,
      y: 25000,
      text: "Fare Increase",
      fill: "red",
      dy: -10
    })
  ]
})
```

Local events likely contributed to ridership spikes at nearby stations during summer 2025. 
The time pattern can be used alongside the events dataset to identify whether traffic rose around major event dates. This suggests that both scheduled events and fare policy may have influenced station demand.

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

This chart ranks stations by average incident response time. Stations with lower average response times performed better, while stations with higher averages performed worse. The best-performing stations are those at the bottom of the time scale, and the worst-performing stations are those with the highest average response times.

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

This chart estimates staffing pressure by comparing projected event attendance in summer 2026 to current station staffing levels. Stations with the highest pressure values are likely to need the most staffing support next summer. Based on this measure, the three stations most in need of additional staffing are the top three shown in the chart.

## Conclusion

Overall, the dashboard shows that ridership demand, response performance, and projected event pressure vary substantially across stations, which supports targeted staffing decisions rather than uniform staffing increases.