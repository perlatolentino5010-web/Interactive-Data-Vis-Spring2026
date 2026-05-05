---
title: "Lab 3: Mayoral Mystery"
toc: true
---

# Lab 3: Mayoral Mystery

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
      fill: "#eeeeee",
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

This map shows where campaign events were held across NYC. Larger dots represent higher estimated attendance, and colors show different event types.

```js
// Vote margin by district
Plot.plot({
  width: 900,
  height: 450,
  x: {
    label: "District"
  },
  y: {
    label: "Vote margin"
  },
  marks: [
    Plot.barY(resultsWithShare, {
      x: "boro_cd",
      y: "vote_margin",
      fill: d => d.vote_margin > 0 ? "Candidate ahead" : "Opponent ahead",
      title: d => `District: ${d.boro_cd}
Vote margin: ${d.vote_margin}
Income category: ${d.income_category}`
    }),
    Plot.ruleY([0])
  ]
})
```

This chart shows vote margin by district. Positive margins indicate districts where the candidate received more votes than the opponent, while negative margins show districts where the opponent performed better.

```js
// Average issue alignment from survey responses
const issueSummary = [
  { issue: "Affordable housing", average: d3.mean(survey, d => +d.affordable_housing_alignment) },
  { issue: "Public transit", average: d3.mean(survey, d => +d.public_transit_alignment) },
  { issue: "Childcare", average: d3.mean(survey, d => +d.childcare_support_alignment) },
  { issue: "Small business tax", average: d3.mean(survey, d => +d.small_business_tax_alignment) },
  { issue: "Police reform", average: d3.mean(survey, d => +d.police_reform_alignment) }
];

Plot.plot({
  width: 750,
  height: 400,
  x: {
    label: "Average alignment score",
    domain: [1, 5]
  },
  y: {
    label: "Issue"
  },
  marks: [
    Plot.barX(issueSummary, {
      y: "issue",
      x: "average",
      title: d => `${d.issue}: ${d3.format(".2f")(d.average)}`
    }),
    Plot.ruleX([3])
  ]
})
```

Finally, this chart shows a summary of how survey respondents aligned with the candidate's issue positions. Scores above 3 suggest stronger agreement, while scores closer to 3 suggest weaker or more neutral alignment.
