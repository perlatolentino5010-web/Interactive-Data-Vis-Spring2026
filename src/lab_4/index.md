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
{
  const stationDistances = [
    { suspect: "ChemTech Manufacturing", distance_m: 410 },
    { suspect: "Riverside Farm",         distance_m: 890 },
    { suspect: "Lakeview Resort",        distance_m: 1340 },
    { suspect: "Clearwater Fishing Lodge", distance_m: 1750 }
  ];

  const suspectIcons = {
    "ChemTech Manufacturing":     "🏭",
    "Riverside Farm":             "🌽",
    "Lakeview Resort":            "🏨",
    "Clearwater Fishing Lodge":   "🎣"
  };

  const westDistances = [...stationDistances].sort((a, b) => a.distance_m - b.distance_m);
  const maxDist = Math.max(...westDistances.map(d => d.distance_m));

  const W = 960, H = 400;
  const originX = 95, originY = H / 2;
  const trackLeft = 160, trackRight = W - 60;
  const trackLen = trackRight - trackLeft;

  const svg = d3.create("svg")
    .attr("width", W)
    .attr("height", H)
    .style("background", "#f4eadf")
    .style("border-radius", "10px")
    .style("overflow", "visible")
    .style("font-family", "system-ui, sans-serif");

  // ── axis line ──────────────────────────────────────────────
  svg.append("line")
    .attr("x1", originX + 30).attr("y1", originY)
    .attr("x2", trackRight).attr("y2", originY)
    .attr("stroke", "#bbb").attr("stroke-width", 1.5)
    .attr("stroke-dasharray", "6,4");

  // ── West Station ───────────────────────────────────────────
  svg.append("circle")
    .attr("cx", originX).attr("cy", originY)
    .attr("r", 28)
    .attr("fill", "#084081").attr("stroke", "#08304f").attr("stroke-width", 3);

  svg.append("text")
    .attr("x", originX).attr("y", originY - 40)
    .attr("text-anchor", "middle")
    .attr("font-size", 14).attr("font-weight", 700).attr("fill", "#222")
    .text("West Station");

  svg.append("text")
    .attr("x", originX).attr("y", originY - 24)
    .attr("text-anchor", "middle")
    .attr("font-size", 11).attr("fill", "#666")
    .text("damaged site");

  // ── rows: line + node + labels ─────────────────────────────
  const rowH = 72;
  const rowStart = originY - ((westDistances.length - 1) * rowH) / 2;

  westDistances.forEach((d, i) => {
    const isChemTech = d.suspect === "ChemTech Manufacturing";
    const nx = trackLeft + (d.distance_m / maxDist) * trackLen;
    const ny = rowStart + i * rowH;

    // connecting line from West station edge to node
    svg.append("line")
      .attr("x1", originX + 28).attr("y1", originY)
      .attr("x2", nx - (isChemTech ? 24 : 21)).attr("y2", ny)
      .attr("stroke", isChemTech ? "#084081" : "#aaa")
      .attr("stroke-width", isChemTech ? 3.5 : 2)
      .attr("stroke-opacity", isChemTech ? 0.85 : 0.5);

    // distance label along the line
    svg.append("text")
      .attr("x", (originX + 28 + nx) / 2)
      .attr("y", (originY + ny) / 2 - 9)
      .attr("text-anchor", "middle")
      .attr("font-size", 11).attr("font-weight", 700).attr("fill", "#444")
      .text(`${d.distance_m} m`);

    // suspect circle
    svg.append("circle")
      .attr("cx", nx).attr("cy", ny)
      .attr("r", isChemTech ? 24 : 21)
      .attr("fill", "#084081")
      .attr("fill-opacity", isChemTech ? 0.95 : 0.62)
      .attr("stroke", "#08304f").attr("stroke-width", 2.5);

    // emoji icon
    svg.append("text")
      .attr("x", nx).attr("y", ny + 8)
      .attr("text-anchor", "middle")
      .attr("font-size", isChemTech ? 20 : 17)
      .text(suspectIcons[d.suspect]);

    // suspect name
    svg.append("text")
      .attr("x", nx + 32).attr("y", ny - 4)
      .attr("font-size", 13)
      .attr("font-weight", isChemTech ? 700 : 500)
      .attr("fill", "#222")
      .text(d.suspect);

    // distance sub-label
    svg.append("text")
      .attr("x", nx + 32).attr("y", ny + 13)
      .attr("font-size", 11).attr("fill", "#555")
      .text(`${d.distance_m} m from West`);
  });

  // ── wrapper div ────────────────────────────────────────────
  const wrap = html`<div style="
    background:#f4eadf; padding:18px 20px; border-radius:12px;
    max-width:980px; font-family:system-ui,sans-serif;
  ">
    <h2 style="margin:0 0 6px;font-size:1rem;">
      Distance from the Damaged West Station to Each Suspect
    </h2>
    <p style="font-size:0.84rem;margin:0 0 14px;line-height:1.4;max-width:860px;">
      The West station is the monitoring location with the strongest heavy metal spikes and trout decline.
      This diagram connects the damaged West station to each suspect and places them according to distance.
    </p>
    ${svg.node()}
    <div style="font-size:12px;line-height:1.4;margin-top:10px;color:#444;max-width:860px;">
      <b>Key Finding:</b>
      ChemTech Manufacturing is the closest suspect to the damaged West station. Since the strongest
      contamination and trout decline appear at West, this gives ChemTech the strongest geographic
      opportunity in the case.
    </div>
  </div>`;

  return wrap;
}
```

ChemTech Manufacturing is closest to the West station, the same station where the strongest heavy metal spikes and trout decline appear. This spatial pattern strengthens the case that ChemTech had the clearest geographic opportunity to affect the most damaged part of the lake.

## Evidence 5: Suspect Activity Timeline

This timeline helps compare documented suspect activities against the pollution story.

```js
{
  const W = 900, H = 420;
  const N_WHITE = 36, N_BLACK = 36; // 72 total dots

  // Outcome bands: label, yCenter, white%, black%
  const outcomes = [
    { label: "Rich adult",              pct_white: 0.25, pct_black: 0.11, y: 60  },
    { label: "Upper-middle-class adult",pct_white: 0.20, pct_black: 0.17, y: 160 },
    { label: "Middle-class adult",      pct_white: 0.23, pct_black: 0.25, y: 260 },
    { label: "Lower-middle-class adult",pct_white: 0.08, pct_black: 0.25, y: 360 },
    { label: "Poor adult",              pct_white: 0.24, pct_black: 0.22, y: 440 },
  ];

  // Build dots: assign each to an outcome by probability
  function assignOutcomes(n, pcts) {
    const dots = [];
    let remaining = n;
    for (let i = 0; i < pcts.length; i++) {
      const count = i === pcts.length - 1
        ? remaining
        : Math.round(pcts[i] * n);
      for (let j = 0; j < count; j++) dots.push(i);
      remaining -= count;
    }
    return dots;
  }

  const whitePcts = outcomes.map(o => o.pct_white);
  const blackPcts = outcomes.map(o => o.pct_black);
  const whiteAssign = assignOutcomes(N_WHITE, whitePcts);
  const blackAssign = assignOutcomes(N_BLACK, blackPcts);

  // Initial positions: all clustered in "grew up rich" band at top-left
  const dots = [];
  [...whiteAssign.map((o,i) => ({ race: "white", outcome: o, id: i })),
   ...blackAssign.map((o,i) => ({ race: "black", outcome: o, id: N_WHITE + i }))
  ].forEach(d => {
    dots.push({
      ...d,
      x0: 80 + Math.random() * 260,
      y0: 55 + Math.random() * 40,
    });
  });

  // Final positions: spread into outcome rows
  const outcomeCounts = outcomes.map(() => ({ white: 0, black: 0 }));
  dots.forEach(d => {
    const slot = d.race === "white"
      ? outcomeCounts[d.outcome].white++
      : outcomeCounts[d.outcome].black++;
    const row = outcomes[d.outcome];
    const offset = d.race === "white" ? 0 : 14;
    d.x1 = 420 + (slot % 18) * 12 + offset + Math.random() * 4;
    d.y1 = row.y + Math.floor(slot / 18) * 12 + Math.random() * 4;
  });

  // SVG
  const svg = d3.create("svg")
    .attr("viewBox", `0 0 ${W} 480`)
    .attr("width", W)
    .style("background", "#fff")
    .style("font-family", "Georgia, serif");

  // Title
  svg.append("text")
    .attr("x", 20).attr("y", 28)
    .attr("font-size", 15).attr("font-weight", 700).attr("fill", "#111")
    .text("Follow the lives of boys who grew up in rich families…");

  svg.append("text")
    .attr("x", W - 20).attr("y", 28)
    .attr("text-anchor", "end")
    .attr("font-size", 13).attr("fill", "#555")
    .text("…and see where they end up as adults:");

  // Outcome labels (right side)
  outcomes.forEach(o => {
    svg.append("text")
      .attr("x", W - 20).attr("y", o.y + 5)
      .attr("text-anchor", "end")
      .attr("font-size", 12).attr("fill", "#333")
      .text(o.label);

    // white % stat
    svg.append("text")
      .attr("x", W - 130).attr("y", o.y - 4)
      .attr("text-anchor", "middle")
      .attr("font-size", 14).attr("font-weight", 700).attr("fill", "#e8a820")
      .text(Math.round(o.pct_white * 100));

    svg.append("text")
      .attr("x", W - 130).attr("y", o.y + 12)
      .attr("text-anchor", "middle")
      .attr("font-size", 11).attr("fill", "#888")
      .text(Math.round(o.pct_white * 100) + "%");

    // black % stat
    svg.append("text")
      .attr("x", W - 100).attr("y", o.y - 4)
      .attr("text-anchor", "middle")
      .attr("font-size", 14).attr("font-weight", 700).attr("fill", "#5b9bd5")
      .text(Math.round(o.pct_black * 100));

    svg.append("text")
      .attr("x", W - 100).attr("y", o.y + 12)
      .attr("text-anchor", "middle")
      .attr("font-size", 11).attr("fill", "#888")
      .text(Math.round(o.pct_black * 100) + "%");
  });

  // Column headers for stats
  svg.append("text").attr("x", W - 130).attr("y", 14)
    .attr("text-anchor", "middle").attr("font-size", 11).attr("fill", "#e8a820").attr("font-weight", 700)
    .text("WHITE MEN");
  svg.append("text").attr("x", W - 100).attr("y", 14)
    .attr("text-anchor", "middle").attr("font-size", 11).attr("fill", "#5b9bd5").attr("font-weight", 700)
    .text("BLACK MEN");

  // "Grew up rich" label
  svg.append("text")
    .attr("x", 24).attr("y", 75)
    .attr("font-size", 13).attr("fill", "#555").attr("font-style", "italic")
    .text("Grew up rich");

  // Narrative text (left side, mid chart)
  const narrative = svg.append("text")
    .attr("x", 24).attr("y", 200)
    .attr("font-size", 13).attr("fill", "#222");
  [
    "Most white boys \u25a0",
    "raised in wealthy",
    "families will stay rich",
    "or upper middle class",
    "as adults, but black",
    "boys \u25a0 raised in",
    "similarly rich",
    "households will not."
  ].forEach((line, i) => {
    narrative.append("tspan")
      .attr("x", 24).attr("dy", i === 0 ? 0 : 18)
      .text(line);
  });

  // Dots
  const circles = svg.selectAll("circle.dot")
    .data(dots)
    .join("circle")
    .attr("class", "dot")
    .attr("r", 5)
    .attr("cx", d => d.x0)
    .attr("cy", d => d.y0)
    .attr("fill", d => d.race === "white" ? "#e8a820" : "#5b9bd5")
    .attr("fill-opacity", 0.85);

  // Animation: transition to final positions
  let animated = false;
  function animate() {
    animated = true;
    playBtn.attr("opacity", 0.5);
    circles.transition()
      .duration(1800)
      .delay((d, i) => i * 18)
      .ease(d3.easeCubicInOut)
      .attr("cx", d => d.x1)
      .attr("cy", d => d.y1)
      .on("end", (d, i) => {
        if (i === dots.length - 1) playBtn.attr("opacity", 1);
      });
  }

  function reset() {
    animated = false;
    circles.interrupt()
      .attr("cx", d => d.x0)
      .attr("cy", d => d.y0);
    playBtn.attr("opacity", 1);
  }

  // Play/pause button
  const playBtn = svg.append("g")
    .attr("transform", `translate(${W / 2 - 30}, 200)`)
    .style("cursor", "pointer")
    .on("click", () => animated ? reset() : animate());

  playBtn.append("circle")
    .attr("r", 26).attr("fill", "#444").attr("fill-opacity", 0.75);

  playBtn.append("text")
    .attr("text-anchor", "middle").attr("dominant-baseline", "central")
    .attr("font-size", 22).attr("fill", "white")
    .text("▶");

  // Wrapper
  const container = html`<div style="
    background:#fff; padding:16px 20px; border-radius:10px;
    max-width:960px; font-family:Georgia,serif;
    border: 1px solid #e5e7eb;
  ">
    <h2 style="margin:0 0 4px; font-size:1rem; font-family:system-ui,sans-serif;">
      Applications of Interactive Data Visualization
    </h2>
    <p style="margin:0 0 12px; font-size:0.8rem; color:#666; font-family:system-ui,sans-serif;">
      Inspired by NYT "Extensive Data Shows Punishing Reach of Racism for Black Boys" — click ▶ to animate
    </p>
    ${svg.node()}
  </div>`;

  return container;
}
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