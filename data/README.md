# Workshop data: the three upload tables

Upload these to your own CARTO connection before the labs: **Data Explorer → Import data**, choose your dataset, and keep the table name shown. The importer detects the column types for you.

| File | Rows | Size | What it's for | How it connects |
|---|---|---|---|---|
| `film_incentives.csv` | 414 (one per county in the 11 western states) | 183 KB | Film incentive rate for each county, used by **2 · Logistics** | Joined on `county_state`, e.g. `Blaine County, ID` |
| `classic_film_locations.csv` | 38 | 49 KB | Classic film locations for the map, with pop-up images | Points are built from `latitude` and `longitude` |
| `film_landcover_areas.csv` | 1,777 (one per scouting area) | 85 KB | Land cover percentages, used by **1 · Look (fast)** | Joined on `area_id` (an H3 resolution-4 cell) |

Only `film_landcover_areas` is optional: skip it if you run the full **1 · Look**, which computes land cover from the raster itself.

## Where the data comes from

- **Film incentives and film locations were researched with Claude.** Each row cites the sources it was built from (`source_urls`, `source_notes`). Incentive rates come from each state's official film office or tax agency; filming locations were checked for outdoor, on-location filming and placed on the specific spot (a landmark, viewpoint or building). Images are from Wikimedia Commons, with the license in `image_credit`.
- **Land cover was derived from NLCD land cover classes**, read from the USDA Cropland Data Layer raster (`carto-demo-data.demo_rasters.usda_land_classification`), which uses NLCD classes for non-crop land. Each scouting area's percentages are the share of its cells in each class.
- Everything else the workflows use is public: CARTO Spatial Features (Data Observatory), US Census TIGER boundaries and Overture places.

These tables support a teaching exercise. Incentive rates are headline figures: caps, minimum spend and eligibility apply, so confirm with the state film office before budgeting. There is no single master source for film incentives; NCSL and Entertainment Partners publish multi-state summaries that are useful for cross-checking.

## film_incentives.csv

| Column | Meaning |
|---|---|
| `county_state` | Join key: county name and state code, e.g. `Coconino County, AZ` |
| `county`, `state`, `state_name`, `county_fips` | County and state identifiers (`county_fips` is the TIGER `geoid`, kept as text) |
| `credit_base_pct` | The state's base credit or rebate, % of qualified spend |
| `credit_uplift_pct` | Extra % for filming in this county (rural or out-of-zone uplifts) |
| `incentive_pct` | `credit_base_pct + credit_uplift_pct`, the value the scoring uses |
| `program`, `uplift_rule`, `incentive_note` | Program name, the uplift rule, and a one-line summary |
| `source_urls`, `source_notes` | Where the rate came from (links separated by `|`) and what each source says |
| `as_of` | When the rates were researched (2026-10) |

**How uplifts are assigned.** County-list rules are applied exactly (Washington's rural counties, Nevada outside Clark County, Utah outside the Wasatch Front counties; Washington's rural list is approximate). Distance rules (California's 30-mile LA zone, New Mexico's 60 miles from Albuquerque and Santa Fe, 30 miles from Portland) are checked at each county's center point, so a large county is all in or all out. Colorado's discretionary +2% and Arizona's spend-based steps above 15% are not modeled.

### Rates by state

| State | Base | Location uplift | Counties at each rate | Official sources |
|---|---|---|---|---|
| AZ | 15% | none by location (rate rises with spend) | 15 at 15% | [1](https://www.azleg.gov/ars/43/01165.htm) |
| CA | 35% | +5% outside the Los Angeles 30-mile studio zone | 1 at 35%, 57 at 40% | [1](https://film.ca.gov/tax-credit/the-basics-4-0) · [2](https://film.ca.gov/locations/la-studio-zones) |
| CO | 20% | none modeled (a discretionary +2% for enterprise zones / rural areas exists) | 64 at 20% | [1](https://leg.colorado.gov/sites/default/files/film_incentive_tax_credit_memo.pdf) · [2](https://www.ep.com/production-incentives/us/colorado/) |
| ID | 0% | none | 44 at 0% | [1](https://www.ncsl.org/fiscal/state-film-and-television-incentive-programs) · [2](https://www.wrapbook.com/production-incentives/us/idaho) |
| MT | 0% | none | 56 at 0% | [1](https://revenue.mt.gov/taxes/tax-credits/media-tax-credit) |
| NM | 25% | +10% beyond 60 miles of Albuquerque and Santa Fe city halls | 6 at 25%, 27 at 35% | [1](https://www.tax.newmexico.gov/tax-professionals/tax-credits-overview-forms/film-production-tax-credit/) · [2](https://www.nmlegis.gov/Sessions/23%20Regular/final/HB0547.pdf) |
| NV | 15% | +5% outside Clark County | 1 at 15%, 16 at 20% | [1](https://goed.nv.gov/wp-content/uploads/2026/01/Nevada-Incentive-Programs_FY-2026-Updated-Jan26.pdf) |
| OR | 25% | +2.5% effective regional uplift beyond 30 miles of Portland | 2 at 25%, 34 at 27.5% | [1](https://oregonfilm.org/article/incentives-article-1/) · [2](https://oregonfilm.org/article/oregons-regional-or-r-opif-incentive-uplift) |
| UT | 20% | +5% outside Salt Lake, Utah, Davis and Weber counties | 4 at 20%, 25 at 25% | [1](https://film.utah.gov/incentives/) · [2](https://film.utah.gov/utah-film-incentive-program-2-0/) |
| WA | 30% | +10% when most shoot days are in a rural county (not King, Pierce, Snohomish, Clark, Spokane, Thurston, Benton, Whatcom or Kitsap) | 9 at 30%, 30 at 40% | [1](https://www.washingtonfilmworks.org/wp-content/uploads/2026/07/2026.06.23_WF-Production-Incentive-Program-PIP_GC.pdf) |
| WY | 0% | none | 23 at 0% | [1](https://www.ncsl.org/fiscal/state-film-and-television-incentive-programs) · [2](https://wyoleg.gov/2023/Introduced/HB0092.pdf) |

## classic_film_locations.csv

| Column | Meaning |
|---|---|
| `location_id`, `film`, `release_year` | The film and a row ID |
| `location_name`, `state` | Where it was filmed (the specific spot) |
| `setting_type`, `landscape` | The detailed setting and the six map groups (desert & canyon, mountains & alpine, forest, coastal & waterfront, plains & rural, urban) |
| `on_location_notes` | What was filmed there |
| `latitude`, `longitude` | The filming spot (WGS84) |
| `image_url`, `image_type`, `image_credit`, `image_source_page` | The Wikimedia Commons image for the pop-up, with its license |
| `primary_source`, `source_urls`, `source_notes` | Sources for the location |
| `verification_notes` | What was checked, and any caveats |

30 of the 38 locations are in the 11 western states the scouting covers; the rest are classic locations elsewhere in the US.

## film_landcover_areas.csv

| Column | Meaning |
|---|---|
| `area_id` | The scouting area: an H3 resolution-4 cell index (text) |
| `pct_forest`, `pct_shrub`, `pct_grass`, `pct_barren`, `pct_snow`, `pct_water`, `pct_developed`, `pct_wetland`, `pct_crop` | % of the area in each land cover class. Class codes: forest 141-143 and 63; shrub 152 and 64; grass 176; barren 131 and 65; snow 112; water 111; developed 121-124; wetland 190 and 195; crops 1-61, 66-77 and 204+ |
