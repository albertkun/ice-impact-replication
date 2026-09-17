---
layout: ../layouts/Report.astro
title: Which Dataset Covers Your Neighborhoods?
description: Raw counts by year and neighborhood type for every foot-traffic product we tested in LA County, so you can see coverage before you buy.
---

Before you buy a foot-traffic dataset, you want to know one thing: **does it actually see the
neighborhoods you care about?** Vendor coverage maps will not tell you. Counts by neighborhood will.

This page and its explorer give you ours for LA County, so you have a benchmark to ask for.

[**Open the neighborhood data explorer →**](../neighborhood-explorer.html)

Every one of the county's 2,493 census tracts, classified by composition, with the raw counts each
product reports for it — filterable by dataset, year by year, no indexing.

## How the neighborhoods are defined

From ACS 2018–2022 five-year estimates on 2020 tract boundaries. A tract is **70%+ Latino** or
**70%+ non-Latino White** when that group is at least 70% of its population; everything else is
**mixed**.

| Neighborhood type | Tracts |
|---|---|
| 70%+ Latino | 685 |
| 70%+ non-Latino White | 163 |
| Other / mixed | 1,628 |
| Unclassified | 22 |

We deliberately do not use the 50-plus-band taxonomy some published datasets carry — three levels
are what the sample supports.

## What each product reports

All four are on Dewey. Counts are LA County, as the vendor reports them.

| Product | Unit | Years | 70%+ Latino | 70%+ NL White | Other |
|---|---|---|---|---|---|
| Advan Weekly Patterns+ | visits, 2025 | 2019–2025 | 1.25B | 436M | 4.05B |
| Advan Weekly Patterns+ | businesses, 2025 | 2019–2025 | 22,585 | 9,568 | 74,191 |
| Advan Neighborhood Patterns+ | stops, 2025 | 2019–2026 | 1.01B | 374M | 3.80B |
| Veraset Visits | park visits, 2025 | 2024–2025 | 13.9M | 6.7M | 43.7M |
| SafeGraph Places | park records | roster | 532 | 529 | 2,432 |

Three things to take from that table.

**Coverage is roughly proportional to tract count, which is the good news.** Latino tracts are 27% of
the county's tracts and carry 22% of Advan's business visits. There is no gaping hole.

**But businesses per tract are not equal.** 685 Latino tracts hold 22,585 businesses, or 33 each;
163 non-Latino White tracts hold 9,568, or 59 each. A per-tract average will not mean the same thing
in both, which is why every comparison in this project is indexed within neighborhood type rather
than compared across it.

**And the park roster is the exception worth noticing.** SafeGraph has 532 park records in 685 Latino
tracts and 529 in just 163 non-Latino White tracts — more than three times the parks per tract. Some
of that is real (large wildland parks sit in low-density areas) and some is the record-quality problem
described in [Can You Measure Parks?](./parks). Either way, do not read park counts as park access.

## Reading the charts honestly

The explorer plots raw counts, which means the scale differences between products are real and so are
the traps:

- **Veraset 2024 vs 2025 looks like a 13× increase in park visits.** It is not. The feed grew about
  30× during 2025 as Veraset built it out. Both years are also partial — 57 days in 2024, 126 in 2025.
  Points for partial years are drawn hollow.
- **Advan Neighborhood Patterns+ 2026 is partial** and sits on restated block-group boundaries: 1,124
  new IDs appear that year alongside the old ones. Do not compare a 2026 level to earlier years.
- **SafeGraph Places is a roster, not a time series.** One point, no trend.
- **Only the two Advan products are normalised** to an estimated population. Veraset is raw device
  counts with no scaling, which is exactly why its levels move so much.

Use the log scale toggle when you want to read the smaller groups: mixed tracts carry about four times
the volume of Latino tracts, so on a linear axis the other lines compress toward the floor.

## What to ask your vendor

1. **Counts by neighborhood, not a coverage map.** Ask for POI or device counts broken out by a
   composition split you choose. If they will not, that is an answer.
2. **The panel roster.** Advan ships devices-residing per block group per month. You need it to tell a
   change in who is *in the data* from a change in who is *out in the world*.
3. **Their methodology change log.** Median dwell in Weekly Patterns+ halves between 2022 and 2023.
   We found that by plotting it, not by being told.
4. **Whether visits are normalised, and to what.** "Panel-normalised" and "raw device counts" support
   completely different claims.

The rest of the replication guide assumes you have these. [Start there](./guide).

## Download the tables

Every number on this page and in [Can You Measure Parks?](./parks) comes from these, all CSV:

| File | What it is |
|---|---|
| [park-inventory.csv](../data/park-inventory.csv) | 3,543 LA County park records, 38 columns: join keys, geometry quality, parking-lot flag, visit coverage |
| [recreation_poi_coverage.csv](../data/recreation_poi_coverage.csv) | SafeGraph recreation POIs by NAICS, county and city |
| [class_annual_raw_summary.csv](../data/class_annual_raw_summary.csv) | Businesses, weekly visits and visits per business by neighborhood type and year |
| [gap_trim_all_specs.csv](../data/gap_trim_all_specs.csv) | The Latino−NL White gap under four outlier treatments |
| [wp_seasonality_by_class.csv](../data/wp_seasonality_by_class.csv) | Summer's share of the year, Weekly Patterns+, by class |
| [np_park_yoy_step.csv](../data/np_park_yoy_step.csv) | Park block-group stops, year-over-year step at June 2025 |
| [np_park_visitor_share_monthly.csv](../data/np_park_visitor_share_monthly.csv) | Park visitor composition by home neighborhood, monthly |
| [np_park_visitor_ratio.csv](../data/np_park_visitor_ratio.csv) | Visitor share ÷ panel share — park-visiting intensity |
| [np_park_summer_ratio.csv](../data/np_park_summer_ratio.csv) | Park vs county summer share, by year |
| [np_seasonality_trim_robustness.csv](../data/np_seasonality_trim_robustness.csv) | The same ratio under three outlier treatments |
| [veraset_trim_event_study.csv](../data/veraset_trim_event_study.csv) | Veraset pre-trend test, trimmed and untrimmed |
| [neighborhood-data.json](../neighborhood-data.json) | The explorer'"'"'s own data: 2,493 tract geometries with composition, plus every series above |

The scripts that produce them live in the analysis repo; open an issue if you want them.
