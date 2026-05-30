---
title: "Lab 3: Mayoral Mystery"
toc: true
---

# Lab 3: Mayoral Mystery

By Perla Tolentino

The following is an analysis and review of the NYC mayoral election campaign's performance, results, and post-election survey responses across districts.

<!-- Import Data -->
```js
const nyc = await FileAttachment("data/nyc.json").json();
const results = await FileAttachment("data/election_results.csv").csv({ typed: true });
const survey = await FileAttachment("data/survey_responses.csv").csv({ typed: true });
const events = await FileAttachment("data/campaign_events.csv").csv({ typed: true });

const districts = topojson.feature(nyc, nyc.objects.districts)

const totalCandidateVotes = d3.sum(results, d => d.votes_candidate);
const totalOpponentVotes = d3.sum(results, d => d.votes_opponent);
const totalVotes = totalCandidateVotes + totalOpponentVotes;
const overallShare = totalCandidateVotes / totalVotes;

display({
  "Candidate Votes": totalCandidateVotes,
  "Opponent Votes": totalOpponentVotes,
  "Candidate Vote Share": d3.format(".1%")(overallShare)
});

// display(nyc)
// display(results.slice(0,10))
// display(survey.slice(0,10))
// display(events.slice(0,10))
```

Although the candidate lost overall, district-level support was not evenly distributed. Some districts were much more favorable than others, indicating that geography and district characteristics were important factors.


```js
// The nyc file is saved in data as a topoJSON instead of a geoJSON. Thats primarily for size reasons -- it saves us 3MB of data. For Plot to render it, we have to convert it back to its geoJSON feature collection.
const resultsByDistrict = new Map(results.map(d => [String(d.boro_cd), d]));

const getBoroCd = d => String(d.properties.boro_cd);

const districtResults = districts.features.map(d => {
  const result = resultsByDistrict.get(getBoroCd(d));
  return {
    ...d,
    properties: {
      ...d.properties,
      ...result,
      candidate_vote_share: result
        ? result.votes_candidate / (result.votes_candidate + result.votes_opponent)
        : null
    }
  };
});

const resultsWithShare = results.map(d => ({
  ...d,
  candidate_vote_share: d.votes_candidate / (d.votes_candidate + d.votes_opponent),
  vote_margin: d.votes_candidate - d.votes_opponent
}));
```

```js
// Campaign events by location and attendance
Plot.plot({
  projection: {
    domain: districts,
    type: "mercator",
  },
  color: {
    legend: true
  },
  marks: [
    Plot.geo(districts, {
      fill: "#ebeaf9",
      stroke: "white"
    }),
    Plot.dot(events, {
      x: "longitude",
      y: "latitude",
      r: d => Math.sqrt(d.estimated_attendance),
      fill: "event_type",
      title: d => `${d.event_type}
Attendance: ${d.estimated_attendance}
District: ${d.boro_cd}`
    })
  ]
})
```

This map shows where campaign events were held across NYC. Larger dots represent higher estimated attendance, and colors show different event types. Some areas appear to have higher comunity engagement such as Manhattan, Bronx and part of Brooklyn (Faro Rockaway being a major exception), where as Queens seems somewhat active. Staten Island on the other hand shows the lowest engagement, hinting to a disconnect in this city's role in the broader NYC community.

```js
const voteResultsByDistrict = new Map(
  results.map(d => [String(d.boro_cd), d])
);

const voteShareDistricts = {
  type: "FeatureCollection",
  features: districts.features.map(d => {
    const districtID = String(d.properties.boro_cd ?? d.properties.BoroCD ?? d.id);
    const result = voteResultsByDistrict.get(districtID);

    return {
      ...d,
      properties: {
        ...d.properties,
        boro_cd: districtID,
        votes_candidate: result?.votes_candidate,
        votes_opponent: result?.votes_opponent,
        income_category: result?.income_category,
        candidate_vote_share: result
          ? result.votes_candidate / (result.votes_candidate + result.votes_opponent)
          : null,
        vote_margin: result
          ? result.votes_candidate - result.votes_opponent
          : null
      }
    };
  })
};
```

```js
// Candidate vote share choropleth map
Plot.plot({
  width: 850,
  height: 700,

  projection: {
    type: "mercator",
    domain: voteShareDistricts
  },

  color: {
    type: "quantile",
    scheme: "blues",
    label: "Candidate Vote Share",
    legend: true
  },

  marks: [
    Plot.geo(voteShareDistricts, {
      fill: d => d.properties.candidate_vote_share,
      stroke: "#404040",
      strokeWidth: 0.7,
      title: d => `District: ${d.properties.boro_cd}
Vote share: ${d3.format(".1%")(d.properties.candidate_vote_share)}
Vote margin: ${d.properties.vote_margin}
Income category: ${d.properties.income_category}`,
      tip: true
    })
  ]
})
```


This choropleth map shows the candidate's vote share across NYC districts. Darker districts indicate stronger electoral performance, while lighter districts indicate weaker support. Viewing vote share geographically helps identify clusters of support and reveals whether the campaign performed consistently across boroughs or relied heavily on a few strong districts.


```js
// Vote margin by district, sorted vertically
Plot.plot({
  width: 1100,
  height: 850,
  marginLeft: 70,
  marginRight: 40,
  marginBottom: 50,

  x: {
    label: "Vote Margin →",
    grid: true
  },

  y: {
    label: "District",
    domain: resultsWithShare
      .slice()
      .sort((a, b) => a.vote_margin - b.vote_margin)
      .map(d => d.boro_cd)
  },

  color: {
    legend: true,
    domain: ["Opponent ahead", "Candidate ahead"],
    range: ["#e8b23a", "#4f6fd5"]
  },

  marks: [
    Plot.barX(resultsWithShare, {
      y: "boro_cd",
      x: "vote_margin",
      fill: d => d.vote_margin > 0 ? "Candidate ahead" : "Opponent ahead",
      title: d => `District: ${d.boro_cd}
Vote margin: ${d.vote_margin}
Income category: ${d.income_category}`
    }),

    Plot.ruleX([0], {
      stroke: "black",
      strokeWidth: 1
    })
  ]
})
```


This chart shows vote margin by district. Positive margins indicate districts where the candidate received more votes than the opponent, while negative margins show districts where the opponent performed better. While the candidate dominated the district count (36), the oponent had an overwhelming majority of voter suport int he fewer districts that supported him (23), showing the importance of voter's power.


```js
const incomeVoteShareChartData = resultsWithShare.map(d => ({
  ...d,
  vote_share_percent:
    d.candidate_vote_share > 1
      ? d.candidate_vote_share
      : d.candidate_vote_share * 100
}));
```

```js
Plot.plot({
  width: 950,
  height: 500,
  marginLeft: 80,
  marginBottom: 60,

  x: {
    label: "Income Category",
    domain: ["High", "Middle", "Low"]
  },

  y: {
    label: "↑ Candidate Vote Share (%)",
    domain: [24, 61],
    grid: true
  },

  color: {
    legend: true,
    domain: ["High", "Middle", "Low"],
    range: ["#4b67a1", "#88c080", "#e8b67a"]
  },

  marks: [
    Plot.ruleY([overallShare * 100], {
      stroke: "black",
      strokeDasharray: "4,4"
    }),

    Plot.boxY(resultsWithShare, {
      x: "income_category",
      y: d => d.candidate_vote_share * 100,
      fill: "income_category",
      fillOpacity: 0.35,
      stroke: "#444"
    }),

    Plot.dot(resultsWithShare, {
      x: "income_category",
      y: d => d.candidate_vote_share * 100,
      fill: "income_category",
      r: 5,
      stroke: "white",
      strokeWidth: 1.2,
      tip: true,
      title: d => `District: ${d.boro_cd}
Income: ${d.income_category}
Vote Share: ${d3.format(".1%")(d.candidate_vote_share)}`
    })
  ]
})
```


This chart compares candidate vote share across low-, middle-, and high-income districts. The candidate performed strongest in low-income districts, where vote share was consistently above the citywide average. Middle-income districts showed more mixed results, with some districts strongly supporting the candidate and others providing weaker support. High-income districts had the lowest levels of support overall, with most districts falling below the citywide average. These patterns suggest that income may have influenced voting behavior and that the campaign was more successful in lower-income communities than in wealthier areas.


```js
// Survey awareness and voting behavior
Plot.plot({
  width: 700,
  height: 400,
  x: {
    label: "Heard of candidate"
  },
  y: {
    label: "Number of respondents"
  },
  color: {
    legend: true,
    domain: ["No", "Yes"],
    range: ["#fd9343", "#35ae63"] // muted orange, muted green
  },
  marks: [
    Plot.barY(
      survey,
      Plot.groupX(
        { y: "count" },
        { x: "heard_of_candidate", fill: "voted", title: "heard_of_candidate" }
      )
    ),
    Plot.ruleY([0])
  ]
})
```


Finally, this chart compares whether survey respondents had heard of the candidate and whether they voted. This helps connect campaign awareness with voter participation. Overall, this chart is a reflection of conscious voting having greater impact in this NYC majoral election.

