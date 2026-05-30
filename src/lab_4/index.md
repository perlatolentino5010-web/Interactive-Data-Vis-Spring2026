---
title: "Lab 4: Clearwater Crisis"
toc: false
author: "Perla Holmes 🕵🏻‍♀️"
---

# Lab 4: Clearwater Crisis

<p>
  <strong>Investigator:</strong> Perla Holmes 🕵🏻‍♀️
</p>

<p>
  <strong>Case File:</strong> The Crisis on Lake Clearwater
</p>

<div style="white-space: nowrap; width: 1200px; margin-bottom: 24px;">
  <strong>Main Question:</strong> Who is responsible for the ecological collapse of Lake Clearwater?
</div>

```js
const water = await FileAttachment("data/water_quality.csv").csv({ typed: true });
const fish = await FileAttachment("data/fish_surveys.csv").csv({ typed: true });
const stations = await FileAttachment("data/monitoring_stations.csv").csv({ typed: true });
const activities = await FileAttachment("data/suspect_activities.csv").csv({ typed: true });
```

## Background

Lake Clearwater was once a healthy recreational lake known for clear water and thriving fish populations. Over the last two years, however, the ecosystem has experienced a dramatic decline. Sensitive fish species, particularly trout, have suffered major population losses, while water quality measurements suggest that pollution may be affecting parts of the lake.

Four potential suspects operate around Lake Clearwater: Riverside Farm, Clearwater Fishing Lodge, Lakeview Resort, and ChemTech Manufacturing. Each has activities that could potentially impact the environment. The challenge of this investigation is to determine whether the ecological collapse can be linked to a specific suspect using evidence from water quality measurements, fish population surveys, station locations, and documented activity records.

The goal of this investigation is to determine which suspect best matches the available evidence.

## Case Strategy

To identify the most likely culprit, I looked for a suspect that matched three types of evidence:

1. **Temporal evidence** — when pollution spikes happened.
2. **Biological evidence** — which fish species declined most severely.
3. **Spatial evidence** — which suspect was closest to the worst station.

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

Heavy metals are a leading suspect because they directly affect sensitive fish species such as trout. The blue line marks the warning threshold of 20 ppb, and the red line marks the danger zone of 30 ppb.

```js
display(html`
  <div style="max-width: 100%; font-family: system-ui, sans-serif;">
    <h2 style="font-size: 1.6rem; margin-bottom: 8px; white-space: nowrap;">
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
          style: { fontSize: "15px" },
          marginLeft: 75,
          marginRight: 150,
          marginBottom: 50,
          x: { label: "Date", grid: true, tickSize: 6 },
          y: { label: "↑ Heavy Metals (ppb)", grid: true, tickSize: 6 },
          color: {
            legend: false,
            domain: ["East", "North", "South", "West"],
            range: ["#3366cc", "#f39c12", "#e74c3c", "#16a085"]
          },
          marks: [
            Plot.ruleY([20], { stroke: "#3366cc", strokeDasharray: "5,5", strokeWidth: 1 }),
            Plot.ruleY([30], { stroke: "red", strokeDasharray: "5,5", strokeWidth: 1 }),

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
        <h3 style="margin-top: 0; font-size: 0.95rem; line-height: 1.2;">
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

            <div style="font-weight: 600; font-size: 0.85rem; white-space: nowrap;">
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


## Evidence 2: Species Sensitivity Check

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

    <div style="
      background:#faf6ef;
      border-left:4px solid #9c2f36;
      padding:10px 14px;
      margin-bottom:14px;
      max-width:880px;
      font-size:14px;
      line-height:1.4;
    ">
      <b>Location:</b> West Monitoring Station<br>
      This chart focuses only on the West station, where the strongest heavy metal spikes were observed. It compares how Bass, Carp, and Trout populations changed at that same location.
    </div>

    <div style="display: flex; gap: 18px; margin-bottom: 12px;">
      <span><span style="display:inline-block;width:14px;height:14px;background:#f6b26b;margin-right:6px;"></span>Bass: moderate decline</span>
      <span><span style="display:inline-block;width:14px;height:14px;background:#e76f51;margin-right:6px;"></span>Carp: stable/increasing</span>
      <span><span style="display:inline-block;width:14px;height:14px;background:#9c2f36;margin-right:6px;"></span>Trout: sharp decline</span>
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
              ${species} at West Station
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

    <div style="
      margin-top:14px;
      padding:10px 14px;
      background:#f8fbff;
      border:1px solid #cfe2ff;
      border-radius:8px;
      max-width:880px;
      font-size:14px;
      line-height:1.4;
    ">
      <b>Key Finding:</b>
      At the West station, Trout show the steepest population decline, Bass decline moderately, and Carp remain comparatively stable. This pattern supports the broader case that the most damaged station also shows the strongest biological stress.
    </div>

  </div>
`)
```

At the West station, trout show the most dramatic decline compared with bass and carp. This matters because the scientific reference identifies trout as the most sensitive species, bass as moderately sensitive, and carp as the most tolerant. The biological pattern therefore matches heavy metal contamination more than general overfishing or ordinary seasonal change.

## Evidence 3: Trends in Trout Population Across Stations

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
        tickSize: 6,
        domain: [new Date("2023-01-01"), new Date("2024-10-31")]
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

Trout populations decline most sharply at the West station, dropping from more than 40 fish per survey to approximately 13 by the end of the study period. This matches the scientific reference, which shows that trout are especially vulnerable to heavy metal contamination and further exposes the West as the most uninhabitable location in the lake.

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

const westDistances = stationDistances
  .filter(d => d.station_id === "West")
  .sort((a, b) => a.distance_m - b.distance_m);
```

```js
display(html`
  <div style="
    background:#f4eadf;
    padding:18px 20px;
    border-radius:12px;
    max-width:900px;
    font-family:system-ui, sans-serif;
  ">
    <h2 style="
      margin:0 0 6px 0;
      font-size:1rem;
      white-space:nowrap;
    ">
      Distance from the Damaged West Station to Each Suspect
    </h2>

    <div style="
      font-size:0.84rem;
      margin-bottom:14px;
      line-height:1.4;
      max-width:820px;
    ">
      The West station is the monitoring location with the strongest heavy metal spikes and trout decline.
      Suspects are positioned according to their actual distance from the West station.
      Shorter distances may indicate greater geographic opportunity for contamination.
    </div>

    <svg width="850" height="260" style="background:#f4eadf; overflow:visible;">

      <!-- Baseline -->
      <line
        x1="90"
        y1="140"
        x2="760"
        y2="140"
        stroke="#888"
        stroke-width="4"
        stroke-opacity="0.4"
      />

      <!-- West Station -->
      <circle
        cx="90"
        cy="140"
        r="24"
        fill="#084081"
        stroke="#08304f"
        stroke-width="3"
      />

      <text
        x="90"
        y="95"
        text-anchor="middle"
        font-size="15"
        font-weight="700"
        fill="#222"
      >
        West Station
      </text>

      <text
        x="90"
        y="113"
        text-anchor="middle"
        font-size="11"
        fill="#555"
      >
        damaged site
      </text>

      ${westDistances.map((d, i) => {

        const x =
          90 + (d.distance_m / 5600) * 650;

        const labelAbove = i % 2 === 0;

        return html`

          <!-- connection -->
          <line
            x1="114"
            y1="140"
            x2="${x}"
            y2="140"
            stroke="#999"
            stroke-width="2"
            stroke-opacity="0.5"
          />

          <!-- suspect node -->
          <circle
            cx="${x}"
            cy="140"
            r="${d.suspect === "ChemTech Manufacturing" ? 18 : 15}"
            fill="#084081"
            fill-opacity="${d.suspect === "ChemTech Manufacturing" ? 0.95 : 0.65}"
            stroke="#08304f"
            stroke-width="2"
          />

          <!-- distance -->
          <text
            x="${x}"
            y="118"
            text-anchor="middle"
            font-size="11"
            font-weight="700"
            fill="#333"
          >
            ${d.distance_m} m
          </text>

          <!-- suspect label -->
          <text
            x="${x}"
            y="${labelAbove ? 85 : 190}"
            text-anchor="middle"
            font-size="12"
            font-weight="${d.suspect === "ChemTech Manufacturing" ? 700 : 500}"
            fill="#222"
          >
            ${d.suspect}
          </text>

        `;
      })}

    </svg>

    <div style="
      font-size:12px;
      line-height:1.4;
      margin-top:10px;
      color:#444;
      max-width:820px;
    ">
      <b>Key Finding:</b>
      ChemTech Manufacturing appears closest to the West station, while Riverside Farm is farthest away. Because the strongest contamination and trout decline occur at the West station, ChemTech has the greatest geographic opportunity to contribute to the environmental damage observed there.
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
    background:#f7fbff;
    padding:16px;
    border-radius:10px;
    max-width:850px;
    font-family:system-ui, sans-serif;
  ">
    <div style="
      background:#eef6ff;
      border-left:4px solid #1e3a8a;
      padding:10px 12px;
      margin-bottom:12px;
      color:#374151;
      font-size:13px;
      line-height:1.4;
    ">
      <b>What this shows:</b><br>
      This timeline compares documented suspect activities during the crisis period. 
      High-intensity activity does not automatically mean heavy metal waste, but ChemTech matters most because it is the suspect closest to the damaged West station and is connected to heavy metal discharge.
    </div>

    <div style="
      display:flex;
      gap:14px;
      align-items:center;
      margin-bottom:10px;
      font-size:13px;
      color:#374151;
    ">
      <b>Activity Intensity:</b>
      <span><span style="display:inline-block;width:14px;height:14px;background:#93c5fd;margin-right:5px;"></span>Low</span>
      <span><span style="display:inline-block;width:14px;height:14px;background:#3b82f6;margin-right:5px;"></span>Medium</span>
      <span><span style="display:inline-block;width:14px;height:14px;background:#1e3a8a;margin-right:5px;"></span>High</span>
    </div>

    ${Plot.plot({
      width: Math.min(width * 0.72, 840),
      height: 350,
      marginLeft: 165,
      marginBottom: 55,

      style: {
        background: "#f7fbff",
        color: "#374151",
        fontSize: "13px"
      },

      x: {
        label: "Date →",
        grid: true,
        tickSize: 6
      },

      y: {
        label: null
      },

      color: {
        legend: false,
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
          strokeWidth: d => d.suspect === "ChemTech Manufacturing" ? 4 : 2.5,
          strokeOpacity: d => d.suspect === "ChemTech Manufacturing" ? 0.65 : 0.35
        }),

        Plot.barX(activities, {
          x1: "date",
          x2: d => new Date(+d.date + 35 * 24 * 60 * 60 * 1000),
          y: "suspect",
          fill: "intensity",
          rx: 10,
          insetTop: 8,
          insetBottom: 8,
          fillOpacity: d => d.suspect === "ChemTech Manufacturing" ? 1 : 0.72,
          tip: true
        }),

        Plot.dot(activities, {
          x: "date",
          y: "suspect",
          fill: "intensity",
          stroke: "white",
          strokeWidth: 1.8,
          r: d =>
            d.intensity === "High" ? 5.5 :
            d.intensity === "Medium" ? 4.8 : 4,
          tip: true
        }),

        Plot.text(
          activities.filter(d => d.suspect === "ChemTech Manufacturing"),
          {
            x: "date",
            y: "suspect",
            text: d => d.intensity === "High" ? "High" : "",
            dy: -18,
            fill: "#1e3a8a",
            fontSize: 10,
            fontWeight: 700
          }
        )
      ]
    })}

    <div style="
      margin-top:10px;
      padding:10px 12px;
      background:white;
      border:1px solid #dbeafe;
      border-radius:8px;
      color:#374151;
      font-size:13px;
      line-height:1.4;
    ">
      <b>Key Finding:</b>
      ChemTech Manufacturing shows repeated high-intensity activity across the study period. 
      Because ChemTech is also closest to the damaged West station, this timing strengthens the case that it had both geographic opportunity and repeated activity during the crisis.
    </div>
  </div>
`)
```

ChemTech has repeated high-intensity maintenance shutdowns across the study period. Because ChemTech is also the closest suspect to the West station and is associated with heavy metal discharge, its activity pattern strengthens the case against it.

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
const lakeImage = await FileAttachment("docs/Lake-Clearwater-Image.png").url();

display(html`
  <div style="margin-top:40px; text-align:center;">
    <img
      src="${lakeImage}"
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