---
layout: ../layouts/Report.astro
title: Can You Measure Parks?
description: What foot traffic data can and cannot tell you about public parks, using LA County as the test case.
---

Several people asked whether the Cost of Fear method extends from storefronts to **public parks**.

We tested it in LA County. The short answer: **not directly, and the reason is worth knowing before you
buy any data.** This page shows what we found, so you do not repeat the work.

**Three explorers, all self-contained:**

- [**Park inventory →**](../park-inventory-explorer.html) — all 3,543 SafeGraph park records, with
  record quality, visit-data coverage and the parking-lot flag that decides whether agency vehicle
  counts can be joined
- [**In-park venue traffic →**](../parks-explorer.html) — the venue proxy, park boundaries and the
  nine enforcement sites
- [**Neighborhood data →**](../neighborhood-explorer.html) — every tract by composition, with raw
  counts by year from each dataset we tested

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

The comparison is seasonal, not raw. Every one of these parks gets busier from spring into summer, so
the question is not whether traffic rose but whether it rose as much as it had the year before. A
smaller summer step than 2024's *is* a decline against the counterfactual, even when the level is up.

Change in visits to in-park venues over the four weeks after 6 June 2025, against the four weeks
before, compared with the same two windows of 2024:

| Park | 2025 | 2024 | Difference |
|---|---|---|---|
| Exposition Park | −8.5% | +7.9% | −16.4 points |
| Hancock Park | −10.0% | +5.9% | −15.9 points |
| Will Rogers State Historic Park | −2.2% | +10.2% | −12.5 points |
| Griffith Park | +14.7% | +24.9% | −10.2 points |
| Sepulveda Basin Recreation Area | −3.3% | +5.5% | −8.8 points |

### The screen matters more than the result

An earlier version of this page reported 82 parks and a −3.4 point shortfall. **That screen was too
loose, and we have replaced it.** Two passes of cleaning, both of which you can reproduce:

| Screen | Parks | Summer step 2024 | Summer step 2025 | Shortfall |
|---|---|---|---|---|
| Any polygon that looks like a park (first pass) | 82 | +13.8% | +10.3% | −3.4 points |
| Minus malls, hotels, parking, convention centres | 61 | +9.6% | +4.1% | −5.5 points |
| **Park amenities only** | **28** | **+11.2%** | **+1.1%** | **−10.1 points** |

The third row is the one to use. It keeps museums, concessions, rec centers, libraries and nature
sites, and drops anything you book or pay to enter — golf courses, RV parks, theme parks, marinas,
racetracks — because their traffic follows a business cycle rather than park use. Griffith Park and
Sepulveda Basin are kept by name despite their municipal golf, on the grounds that golf inside a large
public park is park land.

Splitting the two windows shows where the shortfall lives. Across the 28 parks the **spring baseline
was flat year over year (+1.2%) and summer traffic fell 8.0%.** By distance to the nearest enforcement
site:

| Distance to nearest site | Parks | Spring 2025 vs 2024 | Summer 2025 vs 2024 | Shortfall |
|---|---|---|---|---|
| Within 5 miles | 6 | +0.4% | −11.3% | −12.1 points |
| 5–15 miles | 18 | +1.7% | −6.9% | −9.8 points |
| Beyond 15 miles | 4 | −2.7% | +1.1% | +4.0 points |

Do this decomposition on your own data before you believe a ratio. A park can post a large apparent
"gain" purely because its spring baseline was weak: Frank G. Bonelli Regional Park showed +33 points
until we looked, and its summer 2025 was in fact 3% *below* summer 2024 — the swing came from its RV
campground having a bad spring. Ratios move for two reasons and only one of them is the one you want.

**Do not read this as an effect of enforcement.** There is still no control group, no correction for
panel attrition, and the mix of venues differs from park to park. It is a signal worth investigating,
not a finding.

> **The bug that produced the first number.** `SUB_CATEGORY` values in SafeGraph Places carry a
> **trailing space** — `"Parking Lots and Garages "`, not `"Parking Lots and Garages"`. Every
> exact-match filter we wrote silently matched nothing. That is how "Pike Park" in Long Beach reached
> our first table at +50 points: eight venues, six of them a parking structure and a convention
> centre, none of them caught by a screen that was supposed to catch exactly that. Call `.str.strip()`
> before you match, and then look at the actual venue names in your top and bottom ten.

And when we did investigate it with a source that measures parks directly, it did not hold up — see
below. The two are measuring different things: this table is destination parks seen through their
concessions and museums, and the section below is 49 parks measured as whole block groups. They also
disagree on distance — the venue proxy has parks nearest the enforcement sites falling furthest, and
the block-group measure has them doing best. Where they disagree, trust the direct measure.

## We then tried both remaining options. Here is what they cost and what they gave.

### Veraset Visits — covers parks, but the panel moved under us

Veraset uses SafeGraph's places, so it inherits the park locations. It is 17.75 TB and cannot be
filtered server-side, but the files are Parquet served over URLs that honour HTTP range requests —
so you can fetch just the columns you need and never store a file. We pulled 183 days for 2,513
LA parks that way, at about 9% of the source bytes.

**The data was not usable for an impact estimate.** The feed grew roughly **14× inside 2025** —
584 visits per park in the week of 1 March, 8,037 by 27 June, across a stable set of 431 parks. Any
before-and-after comparison measures the feed being built out. (An earlier version of this page said
30×, from a partial run; 14× is the figure our published weekly series reproduces.) Worse, when we ran the weekly event study that tests the
assumption, **the pre-period coefficients averaged +20% with 12 of 13 weeks significant**: treated
and control parks were already diverging months before June. The post-period coefficients were
near zero.

> **The trap to avoid.** A two-way fixed-effects model with a single post-period dummy gave
> −16% and a t-statistic of −4.15. It looks like a strong result. It is the pre-existing trend
> being absorbed into one coefficient. Always run the weekly version before you believe the
> pooled one.

We also checked whether outliers explained it. Dropping the top 10% of records **made the
pre-trends worse**, not better. And note that trimming *by year* is wrong here: because the feed
grows, a per-year top decile deletes 5% of March records but 19% of July, which manufactures an
effect. Trim per day.

### Advan Neighborhood Patterns+ — this one worked

NP+ counts stops per census block group rather than per place, so parks that take up most of
their own block group show up. It is 472 GB nationally, but the same range-request trick brings
LA County down to **0.7 GB fetched per month, about 60 seconds each** — 92 months in an
afternoon, 58 MB kept.

In LA County, **49 of 6,589 block groups are at least half parkland and 11 are at least
three-quarters**: Griffith, Elysian, Sepulveda Basin, Hansen Dam, Whittier Narrows, Debs,
Bonelli, Magic Johnson, El Dorado, Topanga, and MacArthur Park, which sits beside one of the
enforcement sites.

Crucially, **NP+ behaves.** Indexed to 2019, county-wide stops run 64.8 (2020), 84.4 (2021),
99.7 (2022), 95.7 (2023) — COVID in the right year, then recovery. That is what a normalised
panel looks like, and it is why this source could answer the question when Veraset could not.

## The answer for large parks: no detectable decline

Three measures agree.

**Levels.** Year-over-year by calendar month, which removes seasonality outright:

| Group | Jan–May 2025 | Jun–Dec 2025 | Step at June |
|---|---|---|---|
| Parks, 75%+ of block group | −12.6% | +10.0% | +22.6 |
| Parks, 50–75% | −7.2% | +9.5% | +16.7 |
| Rest of LA County | −11.4% | +7.3% | +18.7 |

Everything steps up in June 2025, the control included. Parks and non-parks move together.

**Distance.** Parks within three miles of an enforcement site did *best*, not worst — +22.8 index
points for summer 2025 against 2024, versus +15.4 beyond six miles.

**Who visited.** NP+ reports, for each block group and month, how many visiting devices live in
each home block group. Mapped to neighborhood composition, the share of large-park visitors from
70%+ Latino tracts moved **−0.07 points** between summer 2024 and summer 2025, inside a
15.6–16.5% band stretching back to 2019.

And the correct comparison makes that a small increase. Advan's own panel thinned unevenly — the
Latino-tract share of its LA devices fell from 24.9% to 22.9%, down 8% relative. Divide visitor
share by panel share and Latino residents visited these parks **6% more** relative to their
presence in the data, not less.

> **Do this check on your own data.** A vendor panel that shrinks unevenly will manufacture a
> composition story. Get the panel roster — Advan ships one — and divide by it before you report
> any change in who shows up.

**One finding that has nothing to do with enforcement:** Latino-tract residents are 22.9% of
Advan's LA panel but only 16.0% of visitors to these large parks. Park access in LA is unequal,
and that gap long predates 2025.

### What this does not cover

Forty-nine large parks, not the 1,800 neighborhood parks — those are too small to dominate a
block group, so the question of whether a local park emptied out is still open. Per-park series
are **not usable**: a single park block group is mostly differential-privacy noise, swinging
±375% year over year. Report tiers, not parks.

## Watch out for bad park polygons

Some park records in SafeGraph are not parks:

- **Mislabelled shopping centers.** One "park" in our data contained 116 businesses.
- **Default shapes.** Landmark locations often carry a placeholder polygon of exactly 30,902 m² — 94 of
  them share that same area. The Hollywood Walk of Fame and the Beverly Hills sign are both filed as parks.
- **Geocoding errors.** One polygon labelled as a beach in Maui sits inside LA County.
- **Nesting.** A rose garden inside a larger park is its own record, so a venue can land in two parks at
  once. Assign each venue to the **smallest** polygon that contains it.

Filter on polygon area, drop synthetic shapes, and look at the names before you trust any of it.

## The checks that changed our answers

Every number above survived these, and two did not survive them unchanged. Run them on your own data.

1. **Plot the vendor's median dwell by year.** It is the cheapest tell for a methodology change. In
   Weekly Patterns+ it **halves between 2022 and 2023** in every neighborhood type. Visit counts were
   unaffected county-wide, but a gap measured across that break is not comparable to one measured
   inside it.
2. **Check whether the season's shape is stable.** In NP+, summer's share of the year fell from about
   102 in 2019 to 77 in 2023 before recovering — so 2019 is not a safe seasonal baseline there. In
   Weekly Patterns+ it spans only 98–108, which is fine.
3. **Re-run everything on the bottom 90% of places by size.** The largest 10% of consumer POIs carry
   57% of LA County visits. Rank once across the whole period if you want comparable levels; drop each
   year's own top decile if you want a trimmed mean. Report both if they disagree.
4. **Get the panel roster and divide by it** before reporting who shows up.

In our case the trims *strengthened* the business finding — the Latino advantage runs 9 to 17 index
points depending on the treatment, positive in all four — and they weakened one claim we had made
about how precisely we could date the park result. Both are worth knowing.

## What to tell readers

- Parks are **not covered** by standard foot traffic data, and no amount of cleaning changes that.
- A venue-based proxy measures **activity at businesses inside a park**, not visits to the park.
- It misses neighborhood parks almost entirely, which is where a chilling effect would matter most.
- The same caveats from the main guide still apply: the data undercounts Latino and lower income
  communities, so any measured drop is a floor.
