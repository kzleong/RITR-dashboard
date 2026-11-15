# Penang Round the Island Relay: Baton Route Guide

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
- The "from → to" line and the start-point note under each baton come from the `legs` object in the same script. They are transcribed from the official route map and the official exchange-point table.

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

Ascent and descent are estimated from the GPX elevation samples. A rise or fall is only counted once it adds up to more than 2 m since the last turning point, so GPS noise is ignored but gentle, sustained slopes are kept. (An earlier version ignored any change under 1 m between neighbouring points, which discarded most gradual climbing and undercounted both figures.) Distance is calculated from consecutive track coordinates using the haversine formula. Minimum and maximum elevation are the raw GPX sample extremes.

As a check, Garmin Connect shows 63 m up and 41 m down for baton 1; this method gives 66 m and 43 m. Figures for other batons have not been checked against Garmin, so they may differ by a few metres.

Heat, surface, road crossings and how sustained a climb is will all affect how a leg feels, so check the Garmin course before running.

## Updating the data

The route data comes from 12 Garmin Connect GPX exports, which are not stored in this repo. To change a route:

1. Export the new GPX from Garmin Connect and recompute the metrics using the method above (ascent and descent use the 2 m rule).
2. Update the matching row in `route-metrics.csv`.
3. Update the matching object in the `routes` array in `index.html`, including its `profile` points if the elevation changed.
4. If rankings change, recompute `rank` across all 12 batons so the difficulty sort stays correct.

`route-metrics.csv` and the `routes` array are maintained separately, so keep them in step.

## Known differences from the official map

The distances printed on the official map are rounded figures for each leg and do not always match the Garmin courses. Most legs agree within about 0.15 km, but batons 8 and 9 differ by more (the map shows 7.16 km and 5.69 km, the Garmin courses 7.86 km and 5.11 km). Worth confirming against the course you actually plan to run.

## Visit counter

GitHub Pages has no server of its own, so the counter uses [GoatCounter](https://www.goatcounter.com), a free, cookie-free analytics service for non-commercial sites. The site code is set in `index.html` as `GOATCOUNTER_CODE` (currently `kzleong`, giving `kzleong.goatcounter.com`). Set it to an empty string to switch the counter off.

For the number to show in the footer, the public counter must be switched on in GoatCounter:

1. Sign in at `kzleong.goatcounter.com` and open **Settings** from the top menu.
2. Scroll down and tick **Allow adding visitor counts on your website**, then save. It is off by default.

The footer then shows the total page views. Visits from `localhost` are not counted, and some ad blockers block GoatCounter, so the figure can read slightly low. The GoatCounter dashboard gives a fuller breakdown by day and page.

## Credits

- Built by [Kai Zhe Leong](https://www.linkedin.com/in/kzleong/), with Claude (Anthropic).
- Route map: Penang Amateur Athletic Association, "Round the Island Relay Route".
- Course data: Garmin Connect.
