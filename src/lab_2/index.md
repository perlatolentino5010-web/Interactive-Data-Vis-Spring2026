---
title: "Lab 2: Subway Staffing"
toc: true
---

# Lab 2: Subway Staffing

By Perla Tolentino

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
  x: {
    label: "Fare Period",
    domain: ["Before Fare Increase", "After Fare Increase"]
  },
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

This bar chart compares average subway traffic before and after the July 15 fare increase. The data shows the average ridership slightly decreased after July 15, suggesting that the fare increase may have had a small negative impact on subway usage among locals, although overall demand remained relatively high. The subway system still remains essential despite the increase. This suggests that subway demand is relatively stable and not highly sensitive to fare changes, indicating that many riders rely on it regardless of price.

```js
Plot.plot({
  title: "Daily Subway Traffic by Station with Fare Increase Marker",
  width: 900,
  height: 500,

  y: {
    label: "Daily Traffic"
  },

  x: {
    label: "Date"
  },

  color: {
    legend: true,
    label: "Station"
  },

  marks: [
    Plot.lineY(ridershipWithTraffic, {
      x: "date",
      y: "traffic",
      z: "station",
      stroke: "station",
      strokeOpacity: 0.55,
      strokeWidth: 1.4,
      tip: true
    }),

    Plot.ruleX([new Date("2025-07-15")], {
      stroke: "red",
      strokeWidth: 2
    }),

    Plot.text([new Date("2025-07-15")], {
      x: d => d,
      y: 25000,
      text: "Fare Increase",
      fill: "red",
      dy: -10,
      fontWeight: "bold"
    })
  ]
})
```

This revised line chart separates ridership trends by station instead of combining all station traffic into one line. Each station now has its own line, making it easier to see where traffic spikes occur and whether certain stations respond differently around local events or the July 15 fare increase. The fare increase marker is also treated as a real date, so the x-axis stays continuous over time instead of turning into date strings.

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

This chart ranks stations by average incident response time, showing clear differences in performance across locations. Stations with lower response times are more efficient, while those with higher response times may face operational challenges such as higher traffic, staffing limitations, or more complex incidents. For example, stations toward the top of the chart demonstrate faster response times, while those toward the bottom may require operational improvements or additional resources.

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

This chart estimates staffing pressure by comparing projected event attendance in summer 2026 to current station staffing levels. Stations with the highest pressure values are likely to experience the greatest strain, indicating a clear need for additional staffing at those locations to manage anticipated demand. Canal Street stands out as having the highest staffing pressure, which can be explained by its low staffing level combined with projected event attendance. This results in a significantly higher number of attendees per staff member compared to other stations.

## Conclusion

Overall, the dashboard shows that ridership demand, response performance, and projected event pressure vary substantially across stations, which supports targeted staffing decisions rather than uniform staffing increases. The analysis suggests that factors such as event activity and staffing distribution have a greater impact on system strain than fare changes alone.