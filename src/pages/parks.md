---
layout: ../layouts/Report.astro
title: Can You Measure Parks?
description: What foot traffic data can and cannot tell you about public parks, using LA County as the test case.
---

Several people asked whether the Cost of Fear method extends from storefronts to **public parks**.

We tested it in LA County. The short answer: **not directly, and the reason is worth knowing before you
buy any data.** This page shows what we found, so you do not repeat the work.

[**Open the LA County park explorer →**](../parks-explorer.html)

## The problem: foot traffic data has almost no parks in it

Advan Weekly Patterns+ — the foot traffic product the guide uses — builds its list of places from
**business polygons**. Free public parks are not businesses, so they are not in it.

In LA County:

| Data source | Park locations (NAICS 712190) |
|---|---|
| SafeGraph Places | 3,543 |
| Advan Weekly Patterns+ | **14** |

All 14 of the Advan ones are national forest. Griffith Park, MacArthur Park, Elysian Park, Echo Park —
none of them are in the foot traffic data at all. We checked one week in each of 2019, 2021, 2023, 2024
and 2025, and the answer was the same every time. This is not a gap you can fix by downloading more.

> **Check this before you buy.** Ask your vendor how many POIs they have for NAICS 712190 in your county,
and ask for the list. If the names are all national or state forest, parks are not covered.

## What we could measure instead

Parks have things inside them that *are* businesses: museums, concession stands, parking structures,
golf courses, rec centers. Those are in the foot traffic data.

So we used park boundaries from SafeGraph to define "inside a park", and measured visits to the venues
that fall within each boundary. In LA County that is **895 venues inside 223 parks** — Griffith Park has
42, Whittier Narrows 27, Exposition Park 26.

**This is a proxy, and a partial one.** Only 223 of 1,005 sizable parks contain any venue, and they skew
heavily toward big destination parks. Neighborhood parks — the ones closest to where enforcement
happened — have nothing inside them to measure.

## What the proxy showed

Change in visits to in-park venues over the four weeks after 6 June 2025, compared with the same weeks
of 2024:

| Park | 2025 | 2024 | Difference |
|---|---|---|---|
| Exposition Park | −8.5% | +7.9% | −16.4 points |
| Hancock Park | −10.0% | +5.9% | −15.9 points |
| Will Rogers State Historic Park | −2.2% | +10.2% | −12.5 points |
| Griffith Park | +14.7% | +24.9% | −10.2 points |
| Sepulveda Basin Recreation Area | −3.3% | +5.5% | −8.8 points |

Most parks rose into summer 2025 more weakly than they had in 2024. Eighty-two parks had a series clean
enough to report.

**Do not read this as an effect of enforcement.** There is no control group, no correction for panel
attrition, and the mix of venues differs from park to park. It is a signal worth investigating, not a
finding.

## If you need actual park visits

One product does cover parks: **Veraset Visits**, which uses SafeGraph's places and so inherits the park
locations. It is device-level, and about **44 GB per day nationally** — a two-week window is roughly
600 GB before you filter it. Budget for the storage and the time, and narrow the window hard.

A cheaper middle path is **Advan Neighborhood Patterns+**, which counts stops per census block group
rather than per place. Parks that take up most of their own block group — Griffith, Sepulveda Basin,
Exposition — show up that way. It is about 5 GB per month nationally, and you can stream it and throw
away what you do not need.

## Watch out for bad park polygons

Some park records in SafeGraph are not parks:

- **Mislabelled shopping centers.** One "park" in our data contained 116 businesses.
- **Default shapes.** Landmark locations often carry a placeholder polygon of exactly 30,902 m² — 94 of
  them share that same area. The Hollywood Walk of Fame and the Beverly Hills sign are both filed as parks.
- **Geocoding errors.** One polygon labelled as a beach in Maui sits inside LA County.
- **Nesting.** A rose garden inside a larger park is its own record, so a venue can land in two parks at
  once. Assign each venue to the **smallest** polygon that contains it.

Filter on polygon area, drop synthetic shapes, and look at the names before you trust any of it.

## What to tell readers

- Parks are **not covered** by standard foot traffic data, and no amount of cleaning changes that.
- A venue-based proxy measures **activity at businesses inside a park**, not visits to the park.
- It misses neighborhood parks almost entirely, which is where a chilling effect would matter most.
- The same caveats from the main guide still apply: the data undercounts Latino and lower income
  communities, so any measured drop is a floor.
