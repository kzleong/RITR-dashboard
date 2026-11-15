# Penang Round Island Relay: Baton Route Guide

A single-page guide to the 12 baton legs of the Penang Round the Island Relay. It lists each leg in running order with its start point, distance, climb and descent, an estimated difficulty rating, and a link to the original course in Garmin Connect. The list can be re-sorted by difficulty or distance.

There is no build step, framework or server. The whole site is one static HTML file.

## What's in the repo

| File | Purpose |
| --- | --- |
| `index.html` | The site. HTML, CSS and JavaScript in one file, with the route data embedded. |
| `route-map.jpg` | Official route map from the Penang Amateur Athletic Association, shown at the bottom of the page. |
| `route-metrics.csv` | Summary metrics for each baton (distance, gain, loss, elevation range, difficulty, effort score, rank, Garmin link). |
| `README.md` | This file. |

## Viewing it locally

Open `index.html` in a browser. Keep `route-map.jpg` in the same folder so the map loads.

## Publishing with GitHub Pages

1. Push the files to the repository's default branch.
2. In the repository, go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select the branch, and set the folder to `/ (root)`.
4. Save. The site will be served at the URL GitHub shows on that page.

Any other static host (Netlify, Cloudflare Pages, a plain web server) works the same way: serve the folder as-is.

## How the page works

- The `routes` array near the bottom of `index.html` holds one object per baton: distance, gain, loss, min and max elevation, effort score, difficulty, rank, Garmin course URL, and a down-sampled elevation profile.
- The page renders the list from that array. The sort buttons reorder it by baton number, effort rank (easiest or hardest first), or distance.
- The small line beside each baton is a sparkline drawn from the elevation profile. To remove it, delete the `spark(...)` call in the `render` function.
- Start locations for each baton live in the `starts` object in the same script. They are transcribed from the official route map.

## How difficulty is estimated

The rating is a comparison aid, not an official grading.

```
effort index = distance (km) + ascent (m) / 100 + descent (m) / 200
```

| Rating | Effort index |
| --- | --- |
| Easy | up to 5.50 |
| Moderate | 5.51 to 7.50 |
| Hard | above 7.50 |

Ascent and descent are estimated from the GPX elevation samples. Samples are smoothed with a 5-point rolling median, and only changes greater than 1 m between adjacent smoothed samples are accumulated, which reduces GPS elevation noise. Distance is calculated from consecutive track coordinates using the haversine formula. Minimum and maximum elevation are the raw GPX sample extremes. Figures can differ from Garmin's own summaries.

Heat, surface, road crossings and how sustained a climb is will all affect how a leg feels, so check the Garmin course before running.

## Updating the data

The route data comes from 12 Garmin Connect GPX exports, which are not stored in this repo. To change a route:

1. Export the new GPX from Garmin Connect and recompute the metrics using the method above.
2. Update the matching row in `route-metrics.csv`.
3. Update the matching object in the `routes` array in `index.html`, including its `profile` points if the elevation changed.
4. If rankings change, recompute `rank` across all 12 batons so the difficulty sort stays correct.

`route-metrics.csv` and the `routes` array are maintained separately, so keep them in step.

## Known differences from the official map

The distances printed on the official map are rounded figures for each leg and do not always match the Garmin courses. Most legs agree within about 0.15 km, but batons 8 and 9 differ by more (the map shows 7.16 km and 5.69 km, the Garmin courses 7.86 km and 5.11 km). Worth confirming against the course you actually plan to run.

## Credits

- Route map: Penang Amateur Athletic Association, "Round the Island Relay Route".
- Course data: Garmin Connect.
