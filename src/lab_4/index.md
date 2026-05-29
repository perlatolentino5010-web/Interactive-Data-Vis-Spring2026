---
title: "Lab 4: Clearwater Crisis"
toc: false
---

## Research Question

Who is responsible for the ecological collapse of Lake Clearwater?

```js
const water = await FileAttachment("data/water_quality.csv").csv({ typed: true });
const fish = await FileAttachment("data/fish_surveys.csv").csv({ typed: true });
const stations = await FileAttachment("data/monitoring_stations.csv").csv({ typed: true });
const activities = await FileAttachment("data/suspect_activities.csv").csv({ typed: true });
```

## Data Preview

### Water Quality Data

```js
Inputs.table(water)
```

### Fish Survey Data

```js
Inputs.table(fish)
```

### Monitoring Stations

```js
Inputs.table(stations)
```

### Suspect Activities

```js
Inputs.table(activities)

## Evidence 1: Heavy metals over time by station

Heavy metals are the key pollutant to investigate because the Clearwater Crisis prompt states that levels above 20 ppb harm sensitive fish and levels above 30 ppb violate regulatory limits.
```

```js
display(Plot.plot({
  width,
  height: 400,
  marginLeft: 60,
  x: {
    label: "Date",
    grid: true
  },
  y: {
    label: "Heavy metals (ppb)",
    grid: true
  },
  color: {
    legend: true,
    label: "Station"
  },
  marks: [
    Plot.ruleY([20], { strokeDasharray: "4,4" }),
    Plot.ruleY([30], { strokeDasharray: "4,4" }),
    Plot.lineY(water, {
      x: "date",
      y: "heavy_metals_ppb",
      stroke: "station_id",
      tip: true
    })
  ]
}))
```