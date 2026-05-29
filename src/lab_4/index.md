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

## Evidence 3: Suspect Distance to Each Monitoring Station

The worst contamination and trout decline appear at the West station. This graph compares how close each suspect is to each monitoring station.

```js
const stationDistances = stations.flatMap(d => [
  { station_id: d.station_id, suspect: "ChemTech Manufacturing", distance_m: d.distance_to_chemtech_m },
  { station_id: d.station_id, suspect: "Riverside Farm", distance_m: d.distance_to_farm_m },
  { station_id: d.station_id, suspect: "Lakeview Resort", distance_m: d.distance_to_resort_m },
  { station_id: d.station_id, suspect: "Clearwater Fishing Lodge", distance_m: d.distance_to_lodge_m }
]);
```

```js
display(Plot.plot({
  width,
  height: 450,
  marginLeft: 160,
  x: {
    label: "Distance to Station (meters)",
    grid: true
  },
  y: {
    label: "Suspect"
  },
  color: {
    legend: true,
    label: "Station"
  },
  marks: [
    Plot.dot(stationDistances, {
      x: "distance_m",
      y: "suspect",
      fill: "station_id",
      r: 7,
      tip: true
    })
  ]
}))
```

## Conclusion

The evidence points most strongly to **ChemTech Manufacturing**. The heavy metals graph shows repeated spikes above the EPA concern threshold and regulatory limit, especially at the West station. The trout population graph shows the steepest trout decline at the West station, which matches the scientific hint that trout are highly sensitive to heavy metal exposure. Finally, the distance comparison shows that the West station is closest to ChemTech Manufacturing. Together, the temporal, biological, and spatial evidence support ChemTech as the strongest suspect in the Lake Clearwater ecosystem crisis.