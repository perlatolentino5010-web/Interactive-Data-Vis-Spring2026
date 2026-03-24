# Interactive-Data-Vis-Spring2026

This is the home page of your new Observable Framework app.

For more, see <https://observablehq.com/framework/getting-started>.

# Lab 1: Passing Pollinators

```js
import {csv} from "d3-fetch";
data = await csv("https://raw.githubusercontent.com/InteractiveDataVis/Interactive-Data-Vis-Spring2026/refs/heads/main/src/lab_1/data/pollinator_activity_data.csv")
```

```js
Inputs.table(data)
```
