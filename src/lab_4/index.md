---
title: "Lab 4: Clearwater Crisis"
toc: false
author: "Perla Holmes 🕵🏻‍♀️"
---

# Lab 4: Clearwater Crisis

**Investigator:** Perla Holmes 🕵🏻‍♀️  
**Case File:** The Clearwater Crisis  
**Main Question:** Who is responsible for the ecological collapse of Lake Clearwater?


```js
const water = await FileAttachment("data/water_quality.csv").csv({ typed: true });
const fish = await FileAttachment("data/fish_surveys.csv").csv({ typed: true });
const stations = await FileAttachment("data/monitoring_stations.csv").csv({ typed: true });
const activities = await FileAttachment("data/suspect_activities.csv").csv({ typed: true });
```

## Background

Lake Clearwater was once a healthy recreational lake known for clear water and thriving fish populations. Over the last two years, however, the ecosystem has experienced a dramatic decline. Sensitive fish species, particularly trout, have suffered major population losses, while water quality measurements suggest that pollution may be affecting parts of the lake.

Four potential suspects operate around Lake Clearwater: Riverside Farm, Clearwater Fishing Lodge, Lakeview Resort, and ChemTech Manufacturing. Each has activities that could potentially impact the environment. The challenge of this investigation is to determine whether the ecological collapse can be linked to a specific suspect using evidence from water quality measurements, fish population surveys, station locations, and documented activity records.

Like any good detective case, the goal is not simply to identify a suspect, but to determine which suspect best matches the available temporal, biological, and spatial evidence.

## Case Strategy

To identify the most likely culprit, I looked for a suspect that matched three types of evidence:

1. **Temporal evidence** — when pollution spikes happened.
2. **Biological evidence** — which fish species declined most severely.
3. **Spatial evidence** — which suspect was closest to the worst station.

In other words: means, motive, opportunity... but make it data visualization.

## Key Data Findings

Before identifying a suspect, investigators must examine the evidence collected from Lake Clearwater. The following indicators reveal changes in water quality, fish populations, and contaminant levels that may help explain the ecological collapse.

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

Heavy metals are a leading suspect because they directly affect sensitive fish species such as trout. The orange line marks the EPA concern threshold of 20 ppb, and the red line marks the regulatory limit of 30 ppb.

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
    Plot.ruleY([20], { stroke: "orange", strokeDasharray: "4,4" }),
    Plot.ruleY([30], { stroke: "red", strokeDasharray: "4,4" }),
    Plot.lineY(water, {
      x: "date",
      y: "heavy_metals_ppb",
      stroke: "station_id",
      tip: true
    })
  ]
}))
```

**Interpretation:** The West station shows the most severe heavy metal spikes. Several readings exceed the EPA concern threshold, and some exceed the regulatory limit. This suggests the crisis is not evenly spread across the lake. The pollution pattern is concentrated near the West station.

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
    legend: true,
    label: "Station"
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

**Interpretation:** Trout populations decline most sharply at the West station, dropping from more than 40 fish per survey to approximately 13 by the end of the study period. This matches the scientific reference, which shows that trout are especially vulnerable to heavy metal contamination.

## Evidence 3: Species Sensitivity Check

If heavy metals are driving the collapse, the most sensitive species should be affected first and most severely.

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
    legend: true,
    label: "Species"
  },
  marks: [
    Plot.lineY(
      fish.filter(d => d.station_id === "West"),
      {
        x: "date",
        y: "count",
        stroke: "species",
        tip: true
      }
    )
  ]
}))
```

**Interpretation:** At the West station, trout show the most dramatic decline compared with bass and carp. This matters because the scientific reference identifies trout as the most sensitive species, bass as moderately sensitive, and carp as the most tolerant. The biological pattern therefore matches heavy metal contamination more than general overfishing or ordinary seasonal change.

## Evidence 4: Suspect Distance to Each Monitoring Station

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

**Interpretation:** ChemTech Manufacturing is only 800 meters from the West station. Other suspects are much farther from the West station. This makes ChemTech the strongest spatial match for the area with the worst contamination and the steepest trout decline.

## Evidence 5: Suspect Activity Timeline

This timeline helps compare documented suspect activities against the pollution story.

```js
display(Plot.plot({
  width,
  height: 420,
  marginLeft: 170,
  x: {
    label: "Date",
    grid: true
  },
  y: {
    label: "Suspect"
  },
  color: {
    legend: true,
    label: "Intensity"
  },
  marks: [
    Plot.dot(activities, {
      x: "date",
      y: "suspect",
      fill: "intensity",
      r: d => d.intensity === "High" ? 8 : d.intensity === "Medium" ? 6 : 4,
      tip: true
    })
  ]
}))
```

**Interpretation:** ChemTech has repeated high-intensity maintenance shutdowns across the study period. Because ChemTech is also the closest suspect to the West station and is associated with heavy metal discharge, its activity pattern strengthens the case against it.

## Alternative Suspects Considered

**Riverside Farm** is closest to the North station and would more likely explain nitrogen or phosphorus problems. However, the most severe heavy metal spikes and trout collapse occur at the West station, not the North station.

**Clearwater Fishing Lodge** is closest to the South station and could contribute to fish removal or boat activity. However, the South station does not show the strongest heavy metal contamination or the steepest trout collapse.

**Lakeview Resort** is closest to the East station and has wastewater and construction activity. However, the East station remains comparatively stable and does not show the strongest ecological decline.

## Final Conclusion

The evidence most strongly implicates **ChemTech Manufacturing** as the source of the ecological crisis in Lake Clearwater. The heavy metals analysis shows repeated spikes at the West monitoring station, with concentrations exceeding both the EPA concern threshold and, at times, the regulatory limit. The biological evidence shows that trout populations declined most dramatically at the West station, which matches the scientific reference showing that trout are highly sensitive to heavy metal contamination. The species comparison further supports this pattern because trout decline more sharply than more tolerant species. Finally, the spatial evidence shows that the West station is only 800 meters from ChemTech Manufacturing, making it the closest suspect by a substantial margin.

Taken together, the temporal, biological, and spatial evidence support ChemTech Manufacturing as the most likely contributor to the ecological collapse of Lake Clearwater. Case closed... unless ChemTech has a very good lawyer.