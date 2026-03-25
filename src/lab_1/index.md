---
title: "Lab 1: Passing Pollinators"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README).

# Lab 1: Passing Pollinators

```js
const data = await d3.csv(
  "https://raw.githubusercontent.com/InteractiveDataVis/Interactive-Data-Vis-Spring2026/main/src/lab_1/data/pollinator_activity_data.csv",
  d => ({
    ...d,
    body_mass: +d.avg_body_mass_g,
    wing_span: +d.avg_wing_span_mm,
    species: d.pollinator_species,
    temperature: +d.temperature,
    nectar_production: +d.nectar_production
  })
)
```

```js
Inputs.table(data)
```

```js
Plot.plot({
  title: "Body Mass Distribution by Species",
  marks: [
    Plot.rectY(
      data,
      Plot.binX(
        {y: "count"},
        {
          x: "body_mass",
          fill: "species"
        }
      )
    )
  ]
})
```

**What is the body mass and wing span distribution of each pollinator species observed?** 
Different species show different body mass distributions, indicating variation in size across pollinators.

```js
Plot.plot({
  title: "Wing Span Distribution by Species",
  marks: [
    Plot.rectY(
      data,
      Plot.binX(
        {y: "count"},
        {
          x: "wing_span",
          fill: "species"
        }
      )
    )
  ]
})
```

**What is the ideal weather (conditions, temperature, etc) for pollinating?** 
Wing span varies by species and may influence flight patterns and pollination behavior.

```js
Plot.plot({
  title: "Temperature vs Nectar Production",
  marks: [
    Plot.dot(data, {
      x: "temperature",
      y: "nectar_production",
      fill: "species",
      r: 4,
      opacity: 0.7
    })
  ]
})
```

```js
Plot.plot({
  title: "Average Nectar Production by Flower",
  marks: [
    Plot.barY(
      data,
      Plot.groupX(
        {y: "mean"},
        {
          x: "flower_species",
          y: "nectar_production"
        }
      )
    )
  ]
})
```
**Which flower has the most nectar production?** 
Some flower species produce more nectar on average, making them more attractive to pollinators. Nectar production appears to increase within certain temperature ranges, suggesting optimal environmental conditions for pollination.
