---
layout: ../layouts/Guide.astro
title: Cost of Fear Replication Guide
description: How to measure foot traffic changes and lost sales around immigration enforcement actions in your own area.
---

This guide shows you how to run our analysis in your own area.

We measured how much foot traffic dropped at businesses near immigration enforcement actions, and
what that meant in lost sales. You can do the same thing where you are.

We share our code, so you do not have to write it. Most of the steps run on their own. The parts you
do by hand are checking the map points, confirming what kind of business each storefront is, and
picking your spend-per-visit rates.

**What you will end up with:** for each enforcement site, the change in average daily visits at
nearby businesses in the 14 days before and the 14 days after. Then those lost visits turned into
dollars.

## Start with your study area

Pick your study area first. This one choice affects everything else: how much data you download, how
long each step takes, and whether a laptop can handle it.

| Study area | Businesses, roughly | Data size | What you need |
|---|---|---|---|
| One neighborhood or PUMA | A few hundred to 2,000 | Under 500 MB | Any laptop |
| One city | 10,000 to 40,000 | About 1 GB | Any laptop |
| One county (we used LA) | About 113,000 | About 3 GB | 16 GB of RAM |
| A whole state | Over 1,000,000 | 84 GB | A desktop with lots of space |

> **Note:** Dewey lets you filter the data before you download it. You can set the dates and the area
you want. Set both to match your study area, and you only download what you need. Our script already
sets the date range. Add your area to it.

If you do not filter, you get everything. All of California is 84 GB. That is 362 weekly files at
about 281 MB each. We downloaded all of it, even though our analysis only used 989 businesses near
nine sites. Filter down to just what you need.

## What you need before you start

- [ ] **Foot traffic data.** We used Advan Research Weekly Patterns Plus, bought through Dewey Data.
      Get at least 12 months before your earliest event and one month after your latest one.
- [ ] **A list of enforcement events.** You need a street address and a date for each one. Local news,
      legal aid groups, and public records are good sources. Without dates you cannot do a
      before-and-after comparison.
- [ ] **A Geocodio account.** This turns addresses into map coordinates. See Step 2.
- [ ] **Spend-per-visit numbers for your area.** These are industry averages for each type of
      business, adjusted for local prices. Pick your source before you look at any results.
- [ ] **Disk space.** Plan for your download size plus about 20 percent.

## What is already automated

| Task | Tool | Notes |
|---|---|---|
| Download the weekly data | Our script | Runs on its own. |
| Filter it to your area | Our script | One pass through the weekly files. |
| Draw the circles around each site | Python, QGIS, or ArcGIS | Any of the three works. Use what your team already knows. Our script uses Python. |
| Find the businesses inside the circles | Our script | |
| Compare before and after | Our script | Averages and percent change. |
| Turn visits into dollars | Our script | Once you pick your spend rates. |

To run all of this you need someone who can open and run a Python notebook. They do not need to know
how to write one. If nobody on your team has done this, it takes about half a day to get set up.

Three things are still done by hand: checking each map point, confirming what kind of business each
storefront is, and picking your spend-per-visit rates. Those are Steps 2, 4, and 6.

## Step 1: Get the data

- [ ] Set your filters first. Pick your dates and your area.
- [ ] Run the download script.
- [ ] Check that every file opens. Some files come down broken. Our script downloads those again on its own.

**Decide:** how far back your baseline goes. We used a full year so we could see seasonal patterns.

**You are done when:** you have all the weeks you asked for and every file opens.

## Step 2: Find and map the enforcement sites

- [ ] Write down your rules for what counts before you start collecting. Ours were retail sites, not
      homes, where arrests, workplace raids, or other visible enforcement happened.
- [ ] For each site, record the address, the date, and where you got the information.
- [ ] Put your addresses into [Geocodio](https://www.geocod.io/). Upload a spreadsheet, get back
      coordinates. It also adds census tract and FIPS codes, which you will want later. This is what
      we used.
- [ ] Open every point on a map and look at it. Geocoders sometimes place an address a block off, or
      in the wrong city. Fix those by hand in Google Maps.

> **Note:** Every before-and-after window is built from these dates. If a date changes later, you have
to redo Step 3.

**You are done when:** every site has coordinates you have looked at, a date, and a source.

## Step 3: Draw the circles and pull nearby businesses

- [ ] Draw two zones around each site. An inner circle of **0.25 miles**, and an outer ring from
      **0.26 to 0.50 miles**.
- [ ] Pull every business in the data that falls inside those zones.
- [ ] Keep the ones customers visit: retail, grocery, restaurants and cafés, and personal services.

**Decide:** how big your circles should be. A quarter mile is about a five minute walk. If your area
is more spread out than Los Angeles, you may want bigger ones. Decide before you see results, and say
what you picked.

**You are done when:** every site has a business count, and no site came back empty.

## Step 4: Clean up and sort the businesses

This step takes the longest.

- [ ] Remove duplicate listings for the same storefront.
- [ ] Remove businesses that were closed during your time window.
- [ ] Remove numbers that cannot be right. A small corner store will not have 50,000 visits in a week.
- [ ] Check what each business actually is. AI can suggest a category. A person confirms it using
      Street View, Yelp, and a web search.
- [ ] Sort each business into one category. We used seven: Grocery, Retail, Retail Complex, Big-Box
      Retail, Services, Restaurants, and Specialty Food & Drink.
- [ ] Keep a written list of the rules you used, so you can explain them later.

We made a guide for our own reviewers: `data_dictionary_poi_cleaning.csv`. It explains every column
and what to check. You can hand it to your team as is.

> **Note:** Take your time here. If a Home Depot gets sorted in as a small shop, your dollar estimate
will be off by a lot.

**You are done when:** every business has a confirmed category, and you wrote down why you removed
anything you removed.

## Step 5: Measure the change in visits

- [ ] For each business, take the average daily visits for the **14 days before** its date.
- [ ] Do the same for the **14 days after**.
- [ ] Find the change and the percent change.
- [ ] Group the results by site, by zone, and by category.
- [ ] Run the same dates one year earlier as a check. If visits dropped then too, you are looking at
      a normal seasonal dip, not the event.

**Decide:** what to do with the day of the event itself. Say what you chose in your writeup.

**You are done when:** every site and category has a before number, an after number, and a percent change.

## Step 6: Turn visits into dollars

- [ ] Give each category one spend-per-visit rate, based on industry averages adjusted for local prices.
- [ ] Multiply the lost visits by that rate.
- [ ] Add it up by site, by category, and overall.
- [ ] Share a range instead of one number, and say which rate you used for each category.
- [ ] Put your rate table in an appendix so others can redo the math with their own numbers.

> **Note:** These rates are estimates, not measurements. Someone should be able to swap in their own
rates and see how the total changes. Say so in your report.

**You are done when:** someone can get your totals using only your rate table and your visit numbers.

## What to tell readers about the limits

Please share these along with your numbers. They change what the findings mean.

- **The data does not see informal businesses.** Street vendors, food trucks, home businesses, pop-up
  markets, and swap meets do not show up. These are often the businesses most affected by enforcement.
- **Phone location data misses people.** It undercounts lower income households, people with less
  formal education, and Latino communities.
- **So your number is a floor.** The real drop is probably larger than what you measured.
- **This shows a change, not a cause.** Comparing before and after does not prove enforcement caused
  the drop. Do not write that it did.
- **The visit counts are already adjusted** by the data provider. Do not adjust them again.
