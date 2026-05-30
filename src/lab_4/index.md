---
title: "Lab 4: Clearwater Crisis"
toc: false
author: "Perla Holmes 🕵🏻‍♀️"
---

# Lab 4: Clearwater Crisis

**Investigator:** Perla Holmes 🕵🏻‍♀️

**Case File:** The Crisis on Lake Clearwater

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

Before identifying a suspect, the data collected from Lake Clearwater must be examined. The following indicators reveal changes in water quality, fish populations, and contaminant levels that may help explain the ecological collapse.

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
              stroke: "#3366cc",
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

## Evidence 2: Trends in Trout Population Over Time

Trout are highly sensitive to pollution and serve as an early warning indicator of ecosystem stress.

```js
display(html`
  <style>
    .neon-trout-chart,
    .neon-trout-chart * {
      color: #d7f7ff !important;
    }

    .neon-trout-chart svg text {
      fill: #d7f7ff !important;
    }

    .neon-trout-chart svg g[aria-label="tip"] path {
      fill: #243447;
      stroke: #8ecae6;
      stroke-width: 1;
    }

    .neon-trout-chart svg g[aria-label="tip"] text {
      fill: #d7f7ff !important;
      font-weight: 600;
    }
  </style>

  <div class="neon-trout-chart" style="
    background: #07111f;
    padding: 14px;
    border-radius: 14px;
    box-shadow: 0 0 18px rgba(0, 255, 255, 0.18);
    max-width: 820px;
  ">
    ${Plot.plot({
      width: 790,
      height: 315,
      marginLeft: 60,
      marginRight: 20,
      marginBottom: 45,

      style: {
        background: "#07111f",
        color: "#d7f7ff",
        fontSize: "13px"
      },

      x: {
        label: "Date",
        grid: true,
        tickSize: 6
      },

      y: {
        label: "↑ Trout Species Decline",
        grid: true,
        tickSize: 6
      },

      color: {
        legend: true,
        label: "Trout Monitoring Sites",
        domain: ["East", "North", "South", "West"],
        range: ["#38bdf8", "#a7f3d0", "#ff4fd8", "#b7ff4a"]
      },

      marks: [
        Plot.gridX({stroke: "rgba(215, 247, 255, 0.10)"}),
        Plot.gridY({stroke: "rgba(215, 247, 255, 0.10)"}),

        Plot.lineY(
          fish.filter(d => d.species === "Trout"),
          {
            x: "date",
            y: "count",
            stroke: "station_id",
            strokeWidth: 2.6,
            tip: true
          }
        ),

        Plot.dot(
          fish.filter(d => d.species === "Trout"),
          {
            x: "date",
            y: "count",
            fill: "station_id",
            stroke: "#07111f",
            strokeWidth: 1,
            r: 3.3,
            tip: true
          }
        )
      ]
    })}
  </div>
`)
```

Trout populations decline most sharply at the West station, dropping from more than 40 fish per survey to approximately 13 by the end of the study period. This matches the scientific reference, which shows that trout are especially vulnerable to heavy metal contamination.

## Evidence 3: Species Sensitivity Check

If heavy metals are driving the collapse, the most sensitive species should be affected first and most severely.

```js
display(html`
  <div style="
    background: white;
    padding: 18px;
    border-radius: 10px;
    max-width: 920px;
    font-family: system-ui, sans-serif;
  ">
    <div style="display: flex; gap: 18px; margin-bottom: 12px;">
      <span><span style="display:inline-block;width:14px;height:14px;background:#f6b26b;margin-right:6px;"></span>Bass</span>
      <span><span style="display:inline-block;width:14px;height:14px;background:#e76f51;margin-right:6px;"></span>Carp</span>
      <span><span style="display:inline-block;width:14px;height:14px;background:#9c2f36;margin-right:6px;"></span>Trout</span>
    </div>

    <div style="display: flex; gap: 18px;">
      ${["Bass", "Carp", "Trout"].map(species => {
        const colors = {
          Bass: "#f6b26b",
          Carp: "#e76f51",
          Trout: "#9c2f36"
        };

        const speciesData = fish
          .filter(d => d.station_id === "West" && d.species === species)
          .map(d => ({
            ...d,
            x1: new Date(+d.date - 10 * 24 * 60 * 60 * 1000),
            x2: new Date(+d.date + 10 * 24 * 60 * 60 * 1000)
          }));

        return html`
          <div>
            <div style="
              text-align:center;
              font-weight:600;
              color:#555;
              margin-bottom:4px;
            ">
              ${species}
            </div>

            ${Plot.plot({
              width: 270,
              height: 320,
              marginLeft: species === "Bass" ? 55 : 35,
              marginBottom: 95,

              style: {
                background: "white",
                color: "#555",
                fontSize: "12px"
              },

              x: {
                label: null,
                axis: null
              },

              y: {
                label: species === "Bass" ? "↑ Fish Count" : null,
                grid: true
              },

              marks: [
                Plot.rectY(speciesData, {
                  x1: "x1",
                  x2: "x2",
                  y1: 0,
                  y2: "count",
                  fill: colors[species],
                  fillOpacity: 0.9,
                  tip: true
                }),

                Plot.text(speciesData, {
                  x: "date",
                  y: 0,
                  text: d => d3.timeFormat("%b %Y")(d.date),
                  dy: 22,
                  rotate: -45,
                  fontSize: 10,
                  fill: "#555",
                  textAnchor: "end"
                })
              ]
            })}
          </div>
        `;
      })}
    </div>
  </div>
`)
```

At the West station, trout show the most dramatic decline compared with bass and carp. This matters because the scientific reference identifies trout as the most sensitive species, bass as moderately sensitive, and carp as the most tolerant. The biological pattern therefore matches heavy metal contamination more than general overfishing or ordinary seasonal change.

<h2 style="white-space: nowrap; font-size: 1.9rem;">
  Evidence 4: Suspect Distance to Each Monitoring Station
</h2>

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
display(html`
  <div style="
    background: #f4eadf;
    padding: 12px 16px;
    border-radius: 12px;
    max-width: 760px;
    font-family: system-ui, sans-serif;
  ">
    <h2 style="
      margin: 0 0 4px 0;
      font-size: 0.95rem;
      white-space: nowrap;"
    ">
      Suspect Distance to Lake Clearwater Monitoring Stations
    </h2>

    <div style="
      font-size: 0.8rem;
      margin-bottom: 8px;
    ">
      Shorter distances may indicate stronger geographic opportunity for contamination.
    </div>

    ${Plot.plot({
      width: 700,
      height: 250,

      marginLeft: 150,
      marginRight: 20,
      marginBottom: 40,

      style: {
        background: "#f4eadf",
        color: "#333",
        fontSize: "12px"
      },

      x: {
        label: "Distance to Monitoring Station (meters)",
        grid: true,
        tickSize: 6
      },

      y: {
        label: null,
        grid: true
      },

      color: {
        legend: true,
        label: "Monitoring Station",
        domain: ["East", "North", "South", "West"],
        range: ["#b9c9e6", "#74a9cf", "#2b8cbe", "#084081"]
      },

      marks: [
        Plot.gridX({
          stroke: "#888",
          strokeOpacity: 0.35,
          strokeDasharray: "3,4"
        }),

        Plot.gridY({
          stroke: "#ffffff",
          strokeOpacity: 0.35
        }),

        Plot.dot(stationDistances, {
          x: "distance_m",
          y: "suspect",
          fill: "station_id",
          stroke: "#08304f",
          strokeWidth: 1.2,
          r: 8,
          fillOpacity: 0.85,
          tip: true
        })
      ]
    })}

    <div style="
      font-size: 11px;
      text-align: center;
      margin-top: 4px;
      color: #555;
    ">
      Blue tones represent Lake Clearwater monitoring stations.
    </div>
  </div>
`)
```

ChemTech has repeated high-intensity maintenance shutdowns across the study period. Because ChemTech is also the closest suspect to the West station and is associated with heavy metal discharge, its activity pattern strengthens the case against it.

<h2 style="white-space: nowrap; font-size: 1.6rem;">
  Evidence 4: Suspect Distance to Each Monitoring Station
</h2>

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
display(html`
  <div style="
    background: #f4eadf;
    padding: 12px 16px;
    border-radius: 12px;
    max-width: 760px;
    font-family: system-ui, sans-serif;
  ">
    <h2 style="
      margin: 0 0 4px 0;
      font-size: 0.95rem;
      white-space: nowrap;
    ">
      Suspect Distance to Lake Clearwater Monitoring Stations
    </h2>

    <div style="
      font-size: 0.8rem;
      margin-bottom: 8px;
    ">
      Shorter distances may indicate stronger geographic opportunity for contamination.
    </div>

    ${Plot.plot({
      width: 700,
      height: 250,
      marginLeft: 150,
      marginRight: 20,
      marginBottom: 40,

      style: {
        background: "#f4eadf",
        color: "#333",
        fontSize: "12px"
      },

      x: {
        label: "Distance to Monitoring Station (meters)",
        grid: true,
        tickSize: 6
      },

      y: {
        label: null,
        grid: true
      },

      color: {
        legend: true,
        label: "Monitoring Station",
        domain: ["East", "North", "South", "West"],
        range: ["#b9c9e6", "#74a9cf", "#2b8cbe", "#084081"]
      },

      marks: [
        Plot.gridX({
          stroke: "#888",
          strokeOpacity: 0.35,
          strokeDasharray: "3,4"
        }),

        Plot.gridY({
          stroke: "#ffffff",
          strokeOpacity: 0.35
        }),

        Plot.dot(stationDistances, {
          x: "distance_m",
          y: "suspect",
          fill: "station_id",
          stroke: "#08304f",
          strokeWidth: 1.2,
          r: 8,
          fillOpacity: 0.85,
          tip: true
        })
      ]
    })}

    <div style="
      font-size: 11px;
      text-align: center;
      margin-top: 4px;
      color: #555;
    ">
      Blue tones represent Lake Clearwater monitoring stations.
    </div>
  </div>
`)
```

ChemTech Manufacturing is closest to the West station, the same station where the strongest heavy metal spikes and trout decline appear. This spatial pattern strengthens the case that ChemTech had the clearest geographic opportunity to affect the most damaged part of the lake.

## Evidence 5: Suspect Activity Timeline

This timeline helps compare documented suspect activities against the pollution story.

```js
display(html`
  <div style="
    background: #f7fbff;
    padding: 14px;
    border-radius: 10px;
    max-width: 820px;
  ">
    <div style="
      font-family: system-ui, sans-serif;
      font-size: 13px;
      color: #374151;
      margin-bottom: 8px;
      font-weight: 600;
    ">
      Activity Intensity: Low, Medium, High
    </div>

    ${Plot.plot({
      width: Math.min(width * 0.68, 820),
      height: 330,
      marginLeft: 165,
      marginBottom: 55,

      style: {
        background: "#f7fbff",
        color: "#374151",
        fontSize: "13px"
      },

      x: {
        label: "Date",
        grid: true,
        tickSize: 6
      },

      y: {
        label: "Suspect"
      },

      color: {
        legend: true,
        label: "Activity Intensity",
        domain: ["Low", "Medium", "High"],
        range: ["#93c5fd", "#3b82f6", "#1e3a8a"]
      },

      marks: [
        Plot.gridX({
          stroke: "#cbd5e1",
          strokeOpacity: 0.55
        }),

        Plot.line(activities, {
          x: "date",
          y: "suspect",
          z: "suspect",
          stroke: "#111827",
          strokeWidth: 3,
          strokeOpacity: 0.5
        }),

        Plot.barX(activities, {
          x1: "date",
          x2: d => new Date(+d.date + 35 * 24 * 60 * 60 * 1000),
          y: "suspect",
          fill: "intensity",
          rx: 10,
          insetTop: 8,
          insetBottom: 8,
          tip: true
        }),

        Plot.dot(activities, {
          x: "date",
          y: "suspect",
          fill: "intensity",
          stroke: "white",
          strokeWidth: 1.8,
          r: d =>
            d.intensity === "High" ? 5 :
            d.intensity === "Medium" ? 4.5 : 4,
          tip: true
        })
      ]
    })}
  </div>
`)
```

## Alternative Suspects Considered

**Riverside Farm** is closest to the North station and would more likely explain nitrogen or phosphorus problems. However, the most severe heavy metal spikes and trout collapse occur at the West station, not the North station.

**Clearwater Fishing Lodge** is closest to the South station and could contribute to fish removal or boat activity. However, the South station does not show the strongest heavy metal contamination or the steepest trout collapse.

**Lakeview Resort** is closest to the East station and has wastewater and construction activity. However, the East station remains comparatively stable and does not show the strongest ecological decline.

## Final Conclusion

The evidence most strongly implicates **ChemTech Manufacturing** as the source of the ecological crisis in Lake Clearwater. The heavy metals analysis shows repeated spikes at the West monitoring station, with concentrations exceeding both the EPA concern threshold and, at times, the regulatory limit. The biological evidence shows that trout populations declined most dramatically at the West station, which matches the scientific reference showing that trout are highly sensitive to heavy metal contamination. The species comparison further supports this pattern because trout decline more sharply than more tolerant species. Finally, the spatial evidence shows that the West station is only 800 meters from ChemTech Manufacturing, making it the closest suspect by a substantial margin.

Taken together, the temporal, biological, and spatial evidence support ChemTech Manufacturing as the most likely contributor to the ecological collapse of Lake Clearwater. Case closed... unless ChemTech has a very good lawyer.

**"When you have eliminated the impossible, whatever remains, however improbable, must be the truth." ~ Sherlock Holmes**

# The End

```js
display(html`
  <div style="margin-top:40px; text-align:center;">
    <img
      src="./_file/Lake Clearwater Image.png"
      alt="Lake Clearwater"
      style="
        width:100%;
        max-width:1000px;
        border-radius:12px;
        box-shadow:0 4px 12px rgba(0,0,0,0.15);
      "
    >

    <div style="
      margin-top:8px;
      font-size:12px;
      color:#666;
      font-style:italic;
    ">
      Lake Clearwater, Canterbury, New Zealand.
    </div>
  </div>
`)
```