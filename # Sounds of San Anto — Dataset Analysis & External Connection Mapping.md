
**Analyst:** Erick Lima | **Date:** March 2026 | **Project:** CEDISH Sounds of San Anto

---


## Section 1: External Dataset Connection Map

### Table 1: Variable → External Dataset → Merge Key → Potential Insights → Priority

| #   | Primary Variable        | External Dataset                                                          | Source                            | Merge Key            | Potential Qs                                                                                                            |     |     |
| --- | ----------------------- | ------------------------------------------------------------------------- | --------------------------------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------- | --- | --- |
| 1   | ZIP Code                | **ACS 5-Year Estimates (Census)** — B19013 Median HH Income               | Census Bureau / `tidycensus`      | ZIP → ZCTA crosswalk | Do higher-income ZIP codes host more concerts? Is there a music access gap by income?                                   |     |     |
| 2   | ZIP Code                | **ACS** — B02001 Race/Ethnicity by ZIP                                    | Census / `tidycensus`             | ZIP or tract         | Does venue density correlate with % Hispanic/Latino population? Southside underrepresentation test.                     |     |     |
| 3   | Lat/Lon                 | **SA Neighborhood Boundaries** (City of San Antonio Open Data)            | data.sanantonio.gov               | Spatial join         | Assign every geocoded event to a named neighborhood; enables neighborhood-level analysis                                |     |     |
| 4   | Lat/Lon                 | **Census Tract Boundaries** (TIGER/Line)                                  | Census TIGER                      | Spatial join         | Join tract-level demographics (income, race, education) directly to events                                              |     |     |
| 5   | ZIP / Tract             | **ACS** — Educational Attainment (B15003)                                 | Census / `tidycensus`             | ZIP or tract         | Is live music access stratified by neighborhood education levels?                                                       |     |     |
| 6   | Year                    | **BLS CPI / Inflation Data**                                              | BLS / FRED                        | Year                 | Normalize any economic analysis to real dollars; useful if ticket/revenue data added later                              |     |     |
| 7   | Year                    | **Bexar County Population Estimates**                                     | Census / Texas Demographic Center | Year                 | Normalize events-per-year by population growth — was the 1980s peak driven by population or cultural density?           |     |     |
| 8   | Year                    | **National Bureau of Economic Research (NBER) Recession Dates**           | NBER                              | Year range           | Overlay recession periods on concert frequency trends — test whether the 1990–95 dip correlates with economic downturns |     |     |
| 9   | Genre + Year            | **Billboard Chart Archives** (Whitburn project / public compilations)     | Academic datasets                 | Genre + Year         | Did national genre popularity predict SA concert trends? Were SA audiences ahead of or behind national trends?          |     |     |
| 10  | Venue + Address         | **Bexar County Appraisal District (BCAD)** — Property Tax Records         | bcad.net (public)                 | Address              | Correlate venue longevity with property value changes; identify displaced venues                                        |     |     |
| 11  | Venue + ZIP             | **SA City Business License Data**                                         | data.sanantonio.gov               | Address/ZIP          | Cross-reference with active business records; flag venues that closed                                                   |     |     |
| 12  | ZIP / Tract             | **ACS** — B08301 Means of Transportation to Work                          | Census                            | ZIP or tract         | Proxy for car dependency; venues in transit-poor areas may be less accessible to low-income residents                   |     |     |
| 13  | Month / Season          | **NOAA Climate Data Online** — SA weather/temperature records             | NOAA                              | Month + Year         | Do outdoor venue concerts peak in mild months? Does summer heat suppress attendance?                                    |     |     |
| 14  | Lat/Lon                 | **SA Urban Heat Island Data**                                             | City of SA / NOAA                 | Spatial              | Overlay concert geography with heat vulnerability — equity lens on outdoor music access                                 |     |     |
| 15  | Artist                  | **MusicBrainz / Discogs Open Data**                                       | musicbrainz.org                   | Artist name          | Add artist origin city, start/end year, genre (cross-check), gender                                                     |     |     |
| 16  | Artist                  | **Spotify API / Last.fm data** (historical)                               | Spotify/Last.fm                   | Artist name          | Streaming popularity proxy; identify artists whose SA presence predated national fame                                   |     |     |
| 17  | Genre (Tejano)          | **Tejano Music Awards Archives**                                          | tejanomusic.com / web scrape      | Artist name          | Identify undercounted Tejano artists; correct collector bias                                                            |     |     |
| 18  | Venue + ZIP             | **HUD Fair Market Rents**                                                 | HUD USER                          | ZIP                  | Proxy for neighborhood gentrification pressure on music venues                                                          |     |     |
| 19  | ZIP / Tract             | **CDC PLACES Dataset** — health outcomes by census tract                  | CDC                               | Tract                | Explore relationship between neighborhood health burden and music access (social determinants lens)                     |     |     |
| 20  | Lat/Lon                 | **SA Historic Districts** (COSA Planning)                                 | data.sanantonio.gov               | Spatial              | Are historic district venues more stable/longer-lived?                                                                  |     |     |
| 21  | Year                    | **SA Major Events** — Fiesta SA, Spurs championships, NBA/NFL events      | COSA / news archives              | Year                 | Do major local events correlate with concert volume spikes or dips?                                                     |     |     |
| 22  | ZIP / Tract             | **ACS** — B25003 Tenure (Rent vs. Own)                                    | Census                            | ZIP or tract         | Renter-heavy neighborhoods may face more displacement pressure on music venues                                          |     |     |
| 23  | Artist (Tejano/Mexican) | **Mexico Census / INEGI** — border region demographics                    | INEGI                             | Artist origin        | Cross-border music flows; SA as a cultural bridge between Mexico and US                                                 |     |     |
| 24  | Venue + Year            | **Texas Historical Commission (THC)** — Recorded Texas Historic Landmarks | thc.texas.gov                     | Venue name / address | Identify historically protected venues; correlate landmark status with longevity                                        |     |     |
| 25  | ZIP                     | **USPS ZIP Code Business Statistics**                                     | Census ZIP Business Patterns      | ZIP                  | Venue density relative to total business density per ZIP — is music over/underrepresented in certain areas?             |     |     |
| 26  | Year                    | **SA Convention & Visitors Bureau Annual Reports**                        | visitsanantonio.com               | Year                 | Tourism volume trends alongside concert frequency — are concerts tourism-driven?                                        |     |     |
| 27  | Lat/Lon                 | **SAMHD Alcohol Permits / TABC License Data**                             | TABC open data                    | Address              | Correlate music venues with liquor licenses; identify unlicensed or informal venues                                     |     |     |
| 28  | Month + Venue           | **TicketMaster/StubHub historical data** (via API or research datasets)   | Proprietary / academic            | Venue + Date         | Actual attendance/revenue if available — transforms economic analysis entirely                                          |     |     |

---

## Section 3: Hypothetical Merges & Derived Insights (Detailed)

### Merge A: Concert Geography × ACS Income & Race (ZIP/Tract Level)

**How:** Use `tidycensus::get_acs()` in R to pull B19013 (median HH income) and B02001 (race) at the ZIP Code Tabulation Area (ZCTA) level. Join to `Ven_Ev_Ar_Address` via the `Zip` field. For higher precision, use the lat/lon coordinates + `sf::st_join()` to join directly to Census tract geometries (TIGER/Line shapefiles, `tigris::tracts(state="TX", county="Bexar")`).

**Direct insights:**
- Map concert density (events per ZIP over time) against median income — is live music a spatially privileged good in SA?
- Compute a "music access score" per neighborhood: events per capita, weighted by venue variety
- Test the journal's explicit hypothesis: southside vs. northside concert disparity by income and race

**Indirect/creative insights:**
- Build a **Cultural Redlining Index** — borrowing from housing redlining analysis — to show historical patterns of music venue exclusion in predominantly Hispanic south/west SA
- Compare the ZIP-level income gradient of venues over time: did the 1980s boom disproportionately benefit wealthy northside ZIPs?
- Use a **Dissimilarity Index** (standard segregation metric) adapted to measure how evenly concert access is distributed across racial groups in SA

### Merge B: Artist Names × MusicBrainz Open Data

**How:** MusicBrainz provides a full Creative Commons database dump (XML or via their API) with artist hometown, genre, gender, formation/dissolution date. Fuzzy-match `artistsIndex.Name` against MusicBrainz `artist.name` using `fuzzyjoin` or `stringdist` in R.

**Direct insights:**
- What % of SA concert artists are local vs. touring?
- Did SA develop a local music ecosystem or rely primarily on national touring acts?
- Gender breakdown of performers across decades

**Indirect/creative insights:**
- Identify "SA-first" artists: acts who performed in SA before breaking nationally — did SA play a role in launching careers?
- Track artist "career arc" relative to SA performance frequency: do artists perform more in SA early, mid, or late career?

### Merge C: Venue Geography × SA Neighborhood Change (Property Values / HUD FMR)

**How:** Use BCAD (Bexar County Appraisal District) parcel data (downloadable as CSV) matched by address to venue locations. HUD Fair Market Rent data is ZIP-level, annual, going back to 1983.

**Direct insights:**
- Which neighborhoods saw the largest property value increases and simultaneously lost venues?
- Quantify the economic displacement of music venues through gentrification

**Indirect/creative insights:**
- Build a **"venue survival curve"** (Kaplan-Meier-style) stratified by neighborhood income level at time of opening — do venues in low-income areas close faster?
- Test whether venue closures in a neighborhood preceded or followed rent increases (Granger causality-style temporal analysis)

### Merge D: Concert Frequency × Recession/Economic Data (NBER + BLS)

**How:** NBER publishes official recession start/end dates. BLS provides SA-specific unemployment rates (BLS Local Area Unemployment Statistics, LAUS). Merge on `Year` (and `Month` for finer granularity).

**Direct insights:**
- Do concert counts drop during recessions? (Preliminary evidence: 1990–95 dip)
- Is live music a luxury good (income elasticity > 1) or a recession-resilient industry?

**Indirect/creative insights:**
- Compare SA's recession sensitivity to national concert industry trends (available from RIAA/NARM historical data)
- Identify whether specific genres (Country, Classical, Tejano) show different recession elasticity — genre as a proxy for audience economic class

### 