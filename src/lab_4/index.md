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
display(html`
  <div style="max-width: 100%; font-family: system-ui, sans-serif;">
    <h2 style="
      font-size: 1.6rem;
      margin-bottom: 8px;
      white-space: nowrap;
    ">
      Heavy Metals (ppb) Over Time by Monitoring Site
    </h2>

    <div style="
      border: 1px solid #9ec5fe;
      background: #f8fbff;
      padding: 10px 14px;
      border-radius: 6px;
      margin-bottom: 18px;
      max-width: 650px;
    ">
      Higher values indicate more heavy metal contamination in the water.
      <br>
      <b>ppb = parts per billion | EPA = Environmental Protection Agency</b>
    </div>

    <div style="display: flex; gap: 16px; align-items: flex-start;">
      <div>
        ${Plot.plot({
          width: Math.min(width * 1.2, 1700),
          height: 520,
          style: {
            fontSize: "15px"
          },
          marginLeft: 75,
          marginRight: 150,
          marginBottom: 50,
          x: {
            label: "Date",
            grid: true,
            tickSize: 6
          },
          y: {
            label: "↑ Heavy Metals (ppb)",
            grid: true,
            tickSize: 6
          },
          color: {
            legend: false,
            domain: ["East", "North", "South", "West"],
            range: ["#3366cc", "#f39c12", "#e74c3c", "#16a085"]
          },
          marks: [
            Plot.ruleY([20], {
              stroke: "#2112f3",
              strokeDasharray: "5,5",
              strokeWidth: 1
            }),

            Plot.ruleY([30], {
              stroke: "red",
              strokeDasharray: "5,5",
              strokeWidth: 1
            }),

            Plot.text([{date: d3.max(water, d => d.date), y: 20, label: "Warning Level\n(20 ppb)"}], {
              x: "date",
              y: "y",
              text: "label",
              dx: 14,
              dy: -4,
              fill: "#003366",
              textAnchor: "start",
              lineAnchor: "top",
              fontSize: 14
            }),

            Plot.text([{date: d3.max(water, d => d.date), y: 30, label: "Danger Zone\n(30 ppb)"}], {
              x: "date",
              y: "y",
              text: "label",
              dx: 14,
              dy: -4,
              fill: "red",
              textAnchor: "start",
              lineAnchor: "top",
              fontSize: 14
            }),

            Plot.lineY(water, {
              x: "date",
              y: "heavy_metals_ppb",
              stroke: "station_id",
              strokeWidth: 1.2,
              tip: true
            }),

            Plot.dot(water, {
              x: "date",
              y: "heavy_metals_ppb",
              fill: "station_id",
              r: 2,
              tip: true
            })
          ]
        })}

        <div style="
          margin-top: 14px;
          padding: 10px 14px;
          border: 1px solid #ddd;
          border-radius: 6px;
          max-width: 900px;
          background: #fafafa;
          text-align: center;
        ">
          <b>Note:</b> The warning level (20 ppb) and danger zone (30 ppb) are reference thresholds used to identify potentially harmful concentrations of heavy metals in the lake.
        </div>
      </div>

      <div style="
        border: 1px solid #ddd;
        border-radius: 8px;
        padding: 12px 14px;
        width: 150px;
        background: white;
        margin-top: 55px;
      ">
        <h3 style="
          margin-top: 0;
          font-size: 0.95rem;
          line-height: 1.2;
        ">
          Lake Clearwater<br>Monitoring Sites
        </h3>

        ${[
          ["#3366cc", "East Shore"],
          ["#f39c12", "North Shore"],
          ["#e74c3c", "South Shore"],
          ["#16a085", "West Shore"]
        ].map(([color, title]) => html`
          <div style="display: flex; gap: 8px; margin-bottom: 14px; align-items: center;">
            <div style="
              width: 50px;
              height: 3px;
              background: ${color};
              position: relative;
              flex-shrink: 0;
            ">
              <span style="
                position: absolute;
                left: 19px;
                top: -5px;
                width: 12px;
                height: 12px;
                background: ${color};
                border-radius: 50%;
              "></span>
            </div>

            <div style="
              font-weight: 600;
              font-size: 0.85rem;
              white-space: nowrap;
            ">
              ${title}
            </div>
          </div>
        `)}
      </div>
    </div>
  </div>
`)
```

The West station shows the most severe heavy metal spikes. Several readings exceed the EPA concern threshold, and some exceed the regulatory limit. This suggests the crisis is not evenly spread across the lake. The pollution pattern is concentrated near the West station.

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
    label: "↑ Fish Count Decline",
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
        strokeWidth: 2.5,
        tip: true
      }
    ),

    Plot.dot(
      fish.filter(d => d.species === "Trout"),
      {
        x: "date",
        y: "count",
        fill: "station_id",
        r: 3,
        tip: true
      }
    )
  ]
}))
```

Trout populations decline most sharply at the West station, dropping from more than 40 fish per survey to approximately 13 by the end of the study period. This matches the scientific reference, which shows that trout are especially vulnerable to heavy metal contamination.

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
    label: "Species",
    range: ["#7b2cbf", "#c77dff", "#ff66c4"]
  },

  marks: [
    Plot.dot(
      fish.filter(d => d.station_id === "West"),
      {
        x: "date",
        y: "count",
        fill: "species",
        r: 7,
        tip: true
      }
    )
  ]
}))
```

At the West station, trout show the most dramatic decline compared with bass and carp. This matters because the scientific reference identifies trout as the most sensitive species, bass as moderately sensitive, and carp as the most tolerant. The biological pattern therefore matches heavy metal contamination more than general overfishing or ordinary seasonal change.

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