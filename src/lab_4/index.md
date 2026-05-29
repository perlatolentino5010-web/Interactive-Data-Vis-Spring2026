---
title: "Lab 4: Clearwater Crisis"
toc: false
---

# Lab 4: Clearwater Crisis

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
```

## Evidence 1: Heavy Metals by Station Over Time

Heavy metals are a leading suspect because they directly affect sensitive fish species such as trout.

```js
display(Plot.plot({
  width,
  height: 450,
  marginLeft: 60,
  x: {
    label: "Date",
    grid: true
  },
  y: {
    label: "Heavy Metals (ppb)",
    grid: true
  },
  color: {
    legend: true,
    label: "Station"
  },
  marks: [
    Plot.ruleY([20], { stroke: "orange" }),
    Plot.ruleY([30], { stroke: "red" }),

    Plot.lineY(water, {
      x: "date",
      y: "heavy_metals_ppb",
      stroke: "station_id",
      tip: true
    })
  ]
}))
```

## Initial Findings

Based on EPA guidance:

- Heavy metals above 20 ppb harm sensitive fish.
- Heavy metals above 30 ppb violate regulatory standards.
- Trout are highly sensitive to pollution.
- The suspect whose location and activities align with heavy-metal contamination will be the strongest candidate.


## Evidence 2: Trout Population Trends

Trout are highly sensitive to pollution and serve as an early warning indicator of ecosystem stress.

```js
display(Plot.plot({
  width,
  height: 450,
  marginLeft: 60,
  x: {
    label: "Date",
    grid: true
  },
  y: {
    label: "Fish Count",
    grid: true
  },
  color: {
    legend: true
  },
  marks: [
    Plot.lineY(
      fish.filter(d => d.species === "Trout"),
      {
        x: "date",
        y: "count",
        stroke: "station_id",
        tip: true
      }
    )
  ]
}))
```