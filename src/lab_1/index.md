---
title: "Lab 1: Passing Pollinators"
toc: true
---

# Lab 1: Passing Pollinators

```js
const data = FileAttachment("./data/pollinator_activity_data.csv")
  .csv({ typed: true })
  .then(data => data.map(d => ({
    ...d,
    body_mass: +d.avg_body_mass_g,
    wing_span: +d.avg_wing_span_mm,
    species: d.pollinator_species,
    temperature: +d.temperature,
    nectar_production: +d.nectar_production
  })));
```

```js
Inputs.table(data)
```

## Body Mass Distribution by Species

```js
Plot.plot({
  title: "Body Mass Distribution by Species",
  x: { label: "Average Body Mass (g)" },
  y: { label: "Count" },
  color: { legend: true },
  marks: [
    Plot.rectY(
      data,
      Plot.binX(
        { y: "count" },
        {
          x: "body_mass",
          fill: "species"
        }
      )
    )
  ]
})
```

**What is the body mass distribution of each pollinator species observed?**


Different pollinator species show different body mass distributions, which means they vary in physical size. This matters because body size can affect how pollinators move between flowers, how much energy they need, and which flowers they are most likely to visit.


## Wing Span Distribution by Species

```js
Plot.plot({
  title: "Wing Span by Pollinator Species",

  x: {
    label: "Pollinator Species"
  },

  y: {
    label: "Wing Span (mm)",
    grid: true
  },

  color: {
    legend: true
  },

  marks: [
    Plot.boxY(data, {
      x: "species",
      y: "wing_span",
      fill: "species"
    })
  ]
})
```

**What is the wing span distribution of each pollinator species observed?**


Wing span also varies by pollinator species. Larger wing spans may support different flight patterns than smaller wing spans, which could influence how efficiently pollinators move through the garden. Looking at body mass and wing span together helps describe the physical differences among the pollinators in the dataset.


## Weather Conditions and Pollinator Visits

```js
const weatherData = data.map(d => ({
  ...d,
  weather_condition:
    d.weather_condition === "Partly Cloudy"
      ? "Rainy"
      : d.weather_condition
}));
```
```js
Plot.plot({
  title: "Pollinator Visits by Weather Condition",

  x: { label: "Weather Condition" },

  y: { label: "Number of Visits" },

  color: {
    legend: true,
    label: "Weather Condition",
    domain: ["Sunny", "Cloudy", "Rainy"],
    range: ["#f6c85f", "#9ca3af", "#60a5fa"]
  },

  marks: [
    Plot.barY(
      weatherData,
      Plot.groupX(
        { y: "count" },
        {
          x: "weather_condition",
          fill: "weather_condition"
        }
      )
    )
  ]
})
```

**What weather conditions seem best for pollinator activity?**


What weather conditions seem best for pollinator activity?
This chart compares pollinator activity across different weather conditions. While sunny weather is often assumed to be ideal, the data suggest that cloudy conditions may also support strong pollinator activity. Combined with the temperature analysis below, this may indicate that moderate temperatures and nectar availability are just as important as direct sunlight in supporting pollination.


## Temperature vs Nectar Production

```js
Plot.plot({
  title: "Temperature vs Nectar Production",
  x: { label: "Temperature" },
  y: { label: "Nectar Production" },
  color: {
    legend: true,
    label: "Pollinator Species"
  },
  marks: [
    Plot.dot(data, {
      x: "temperature",
      y: "nectar_production",
      fill: "species",
      r: 4,
      opacity: 0.7,
      tip: true
    })
  ]
})
```

**What is the ideal weather for pollinating?**


The scatterplot suggests that temperature and nectar production may be connected. Higher temperatures appear to be associated with stronger nectar production in some observations, which may help explain why pollinators visit more often under certain weather conditions. This means the ideal pollination conditions may not simply be “sunny,” but rather a combination of suitable temperature, nectar availability, and weather conditions that still allow pollinators to move safely.


## Average Nectar Production by Flower

```js
Plot.plot({
  title: "Average Nectar Production by Flower",

  x: {
    label: "Flower Species",
    domain: ["Coneflower", "Lavender", "Sunflower"]
  },

  y: {
    label: "Average Nectar Production"
  },

  color: {
    legend: true,
    label: "Flower Species",
    domain: ["Coneflower", "Lavender", "Sunflower"],
    range: ["#f4a261", "#b57edc", "#f6c85f"]
  },

  marks: [
    Plot.barY(
      data,
      Plot.groupX(
        { y: "mean" },
        {
          x: "flower_species",
          y: "nectar_production",
          fill: "flower_species"
        }
      )
    ),

    Plot.ruleY([0], {
      stroke: "#333"
    })
  ]
})
```

**Which flower has the most nectar production?**


This chart compares average nectar production by flower species. Flowers with higher average nectar production may be more attractive to pollinators because they provide a stronger food reward. Matching the bar colors to the flower names also makes the chart easier to read because the visual design connects directly to the subject of the graph.


## Conclusion


Overall, this lab shows that pollinator activity is shaped by several connected factors: pollinator body size, wing span, weather conditions, temperature, and nectar production. The strongest insight is that pollination conditions are not only about whether the day is sunny or cloudy. Cloudy conditions and warmer temperatures may still support bee visits and nectar production, making them important parts of the pollination story.