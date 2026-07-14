# 🧱 LEGO Insights: A 50-Year Data Story — Power BI Analytics Suite

# Live Dashboard

👉 [https://app.powerbi.com/view?r=eyJrIjoiMjQwNzAyOTktYTExNC00ZmU4LWEzODItYmY0MTNiNmJiNjdlIiwidCI6ImI3MTNiNmE5LTdhYTYtNGMyOC04YTA2LTY5ZDAxMTZkNWY1MSIsImMiOjEwfQ%3D%3D]

Uncovering five decades of LEGO's evolution through pricing, complexity, licensing, and design trends — one brick at a time.

#📌 Project Overview

LEGO Insights is a multi-page, interactive Power BI analytics suite that explores over 18,000 LEGO sets spanning more than 50 years of production (1970s–2020s). The project analyzes how LEGO has evolved across four critical dimensions: set volume and theme growth, piece-count complexity, pricing economics, and the rise of licensed franchises (Star Wars, Marvel/DC Super Heroes, Harry Potter, and others).

The goal was to move beyond a single static report and build a cohesive, narrative-driven dashboard ecosystem — five linked report pages, each answering a distinct business question, all navigable through a shared header/navigation system, consistent theming, and cross-filtering interactions.

# Objective

The analysis was designed to answer six core business questions:


1. Set Evolution — How has LEGO's production volume changed over the decades, and what triggered inflection points?
2. Complexity Over Time — Are LEGO sets becoming more intricate (higher piece counts) as the brand matures?
3. Minifigures Trend — How has the inclusion of minifigures per set evolved, and how does it correlate with set size?
4. Pricing Analysis — How have retail prices moved over time, and what is the relationship between price, pieces, and theme?
5. Theme & Category Trends — Which themes and categories dominate the catalog, and how has that mix shifted?
6. Impact of Licensed IP — How much of LEGO's growth is now driven by licensed franchises like Star Wars and Super Heroes, and how do they compare to each other?

# 🗂️ Dataset Description

The project is built on a LEGO set catalog dataset (sourced in the style of the public Brickset database), containing one record per LEGO set released between 1970 and 2022.

| Column Name | Description | Data Type | Notes |
|---|---|---|---|
| `Set ID` | Unique identifier for each LEGO set | Alphanumeric | 18,457 unique values |
| `Set Name` | Name of the LEGO set | Text | 15,263 unique values |
| `Theme` | Theme of the set (e.g., Star Wars, City, Technic) | Text | 152 unique themes |
| `Theme Group` | Broader grouping the theme belongs to | Text | 17 unique groups |
| `Sub Theme` | Sub-classification within a theme | Text | 872 unique values, ~19.3% missing |
| `Category` | High-level catalog category (Normal, Gear, Book, etc.) | Text | 7 unique values |
| `Pieces` | Number of pieces in the set | Integer | Range: 0 – 11,695 |
| `Mini Figures` | Number of minifigures included | Integer | Range: 1 – 80, ~54.5% missing (not all sets include minifigs) |
| `Age Range Min` | Minimum recommended age | Integer | Range: 1 – 18, ~63.2% missing |
| `Price` | Retail price (USD) | Currency | Range: $1.49 – $849.99 |
| `Image URL` | Link to the set's product image | Text | ~5.5% missing |
| `Brickset URL` | Link to the set's Brickset catalog page | Text | Used for drill-through/reference |


Scale of the dataset:


1. 18,457 total sets released
2. 152 distinct themes across 17 theme groups
3. 3.3 million+ cumulative pieces produced
4. 22,372 cumulative minifigures
5. $262K+ in aggregated retail pricing data across analyzed sets


# 🧹 Data Preparation

The raw catalog data required substantial cleaning and enrichment before it was analytics-ready. The preparation process followed a standard ETL (Extract → Transform → Load) workflow inside Power Query:

1. Data Collection


- Source data was extracted as a structured export (CSV/table) of the LEGO set catalog, consistent with Brickset's public data structure (evidenced by the Brickset URL field retained for reference/drill-through).
- Data was loaded into Power Query as the single source of truth for all five dashboard pages.

2. Data Cleaning


- Null/missing value handling: Fields like Age Range Min (63.2% missing) and Mini Figures (54.5% missing) were profiled using Power Query's column quality/distribution tools, and missing values were treated as "not applicable" rather than imputed, to avoid skewing averages.
- Deduplication: Set records were validated against Set ID to ensure one row per unique set.
- Type correction: Numeric fields (Pieces, Mini Figures, Price, Age Range Min) were explicitly cast to correct data types; Set ID was preserved as alphanumeric text to protect leading zeros/format codes.
- Text standardization: Theme and category names were trimmed/cleaned for consistent casing and whitespace, ensuring accurate grouping in visuals.


3. Data Transformation


- Date/Year extraction: A Year field was derived to support all time-series analysis (line charts, decade groupings).
- Decade bucketing: A calculated Decade column (1970s, 1980s, 1990s, 2000s, 2010s, 2020s) was engineered to power the decade-level comparisons (piece breakdowns, box plots, heatmaps).
- Age bracket binning: Age Range Min was grouped into bands (1–5, 5–10, 10–15, 15–20) to support the age-appeal and "sweet spot" heatmap visuals.
- Piece-count bucketing: A binned Piece Range column (0–50, 50–100, …, 450+) was created to power the complexity histogram.
- Price bucketing: Sets were grouped into price bands (1–50, 50–100 … 450+) to analyze price distribution.
- License flag / franchise tagging: A custom category column was engineered to classify each set as Star Wars, Super Heroes, or Others, enabling the head-to-head "Battle of the Bricks" comparison.
- Derived ratios: Price per Piece was calculated at the row level to normalize value-for-money comparisons independent of set size.
- Theme lifecycle fields: Theme Introduction Year and Last Known Release Year were derived per theme to support the theme lifespan bubble chart (short-lived vs. long-lived themes).


4. Data Organization


- Cleaned data was structured into a single, well-shaped fact table (one row per set) with all descriptive attributes (theme, category, decade, age band) as flat dimension-like columns, optimized for Power BI's in-memory (VertiPaq) engine.
- A supporting Date/Year dimension was built (or a calculated table) to enable clean time-intelligence calculations and consistent axis sorting across all report pages.
- Report pages were organized into a logical navigation flow: Home → Set Count → Pieces Breakdown → Price Trends → Heroic Galaxy (Licensed IP deep-dive), each sharing the same filter context design pattern.


# 🧮 Data Modeling

Model Structure

The semantic model follows a single fact table + supporting calculated tables design, appropriate for a catalog-style dataset (as opposed to a transactional star schema):


1. Fact Table: LEGO Sets — one row per set, containing all core attributes (Theme, Category, Pieces, Price, Mini Figures, Age Range, Year).

2. Calculated/Reference Tables:

- Decades — supports grouped decade analysis and consistent sort order.
- Theme Summary — aggregated theme-level table (Set Count, Total Pieces, Total Price, Top 3 concentration %) powering the summary tables on Dashboards 2 and 3.
- License Category (Star Wars / Super Heroes / Others) — supports the franchise comparison visuals.


# Key DAX Measures & KPIs

The following categories of measures were built to power the KPI cards, YoY indicators, and dynamic titles across the report:

| Measure Category | Examples |
|---|---|
| **Volume Measures** | `Total Sets`, `Total Pieces`, `Total Mini Figures`, `Total Price` |
| **Statistical Measures** | `Min Price`, `Max Price`, `Average Price`, `Min Pieces`, `Avg Pieces`, `Max Pieces`, `Min/Avg/Max Minifigures` |
| **Time Intelligence** | `YoY % Change`, `YoY Absolute Change` (e.g., +7.5% | +2,016 in Price; +3.6% | +9,706 in Pieces; -2.2% | -27 in Minifigures) |
| **Ratio/Efficiency Measures** | `Price per Piece`, `% Contribution by License Type`, `% Price from Top 3 Sets`, `% Pieces from Top 3 Sets` |
| **Growth Measures** | `Set Count Growth %` (YoY theme-level growth, e.g., +2.4%), `Theme Growth %` (e.g., 37.6% growth in set count) |
| **Dynamic Title Measures** | Auto-updating chart titles/subtitles that call out the "so what" of each visual (e.g., *"Super Heroes surpass Star Wars in 2021, equal in 2022"*) |
| **Concentration Measures** | `% Total from Top 3 Categories` (90.4% from Normal, Gear, Other) |

# Visualization Techniques Used

Across the five dashboards, the project deliberately uses a diverse visualization vocabulary rather than defaulting to bar/line charts alone — demonstrating breadth of BI visualization skill:


1. KPI cards with YoY delta indicators
2. Stacked percentage bars (composition visuals)
3. Multi-series line charts with dynamic annotations
4. Clustered/ranked bar charts
5. Histograms (binned distribution analysis)
6. Box-and-whisker plots (statistical spread by decade)
7. Scatter plots with trendlines (correlation analysis)
8. Bubble charts (3-variable comparison: X, Y, size)
9. Heatmaps / matrix visuals (decade × category, decade × age band)
10. Stacked area / streamgraphs (theme group evolution over time)
11. Dynamic summary tables with conditional formatting and data bars
12. Slicers, dropdown filters, button-based navigation, and a "Clear All Slicers" reset control


# 🖥️ Dashboard Features & Page-by-Page Insights

The report suite consists of five pages, each independently filterable but visually and structurally unified through a shared color palette, header design, and navigation bar.

# 📘 Page 1 — Guide & Context (Landing/Documentation Page)

<img width="1579" height="888" alt="Image" src="https://github.com/user-attachments/assets/9d375100-a300-472f-9de1-6a0558af1e2c" />

Purpose: Orients the viewer before they dive into the data — explains the dataset, LEGO's brand history, the guiding analytical questions, and how to navigate the report.


Key Elements:


1. Data Profile table — a transparent data dictionary listing every column, its data type, min/max range, unique value count, and missing-value percentage, directly supporting analytical credibility.
2. Brand narrative — a short history of The LEGO Group (founded 1932 by Ole Kirk Christiansen), highlighting its evolution from a wooden toy maker to a global brand.
3. Headline growth stat: LEGO produced 1,794 unique sets pre-1990s vs. 16,663 sets post-1990s — an 828.8% increase, underscoring an inflection point in the brand's production strategy.
4. Guiding questions panel — the six analytical questions (Set Evolution, Complexity, Minifigures, Pricing, Theme Trends, Licensed Impact) that structure the rest of the report.
5. Navigation guidance — instructions on drill-through (right-click on data points to access Brickset URLs), hover tooltips, and page navigation buttons.



Key Insight: LEGO's transformation from a niche brick manufacturer to a design-and-licensing powerhouse is not gradual — it is a step-change beginning in the 1990s, setting up all subsequent analysis.


# 🦸 Page 2 — "Battle of the Bricks: Star Wars vs. Super Heroes"

<img width="1548" height="878" alt="Image" src="https://github.com/user-attachments/assets/ccfb012a-2622-48a5-8901-f35f725211c8" />

Purpose: A head-to-head competitive analysis between LEGO's two flagship licensed franchises, Star Wars and Marvel/DC Super Heroes, benchmarked against all "Other" themes.


Visuals & KPIs:


1. Composition bars (3 metrics): Star Wars vs. Super Heroes vs. Others as a share of Pieces (39.5% / 15.2% / 45.4%), Set Count (34.4% / 17.0% / 48.6%), and Minifigures (35.4% / 18.1% / 46.5%).
2. Line chart — Pieces Count over time: Star Wars has consistently led Super Heroes in cumulative pieces produced every year since 2000.
3. Line chart — Set Count over time: Star Wars held the top spot until 2021, when Super Heroes overtook it in number of sets released.
4. Line chart — Minifigures Count over time: Super Heroes surpassed Star Wars in minifigure output in 2021, before the two converged and became roughly equal in 2022.
5. Bar chart — Pieces Breakdown by Year: highlights 2022 as the peak year for total pieces licensed.
6. Bar chart — Licensed Releases by Year: highlights 2021 as the peak year for number of licensed sets released.
7. Line chart — Average Price: In 2015, Super Heroes pricing spiked 29.13% above Star Wars, a notable divergence in pricing strategy.
8. Line chart — Price per Piece: Both franchises converge and stabilize around $0.10–$0.13 per piece after 2010, suggesting a maturing, standardized pricing model.
9. Bubble chart — Top 10 Licensed Sets (Set vs. Pieces, sized by Minifigures): The Millennium Falcon stands out as the largest, most piece-dense set with a high minifigure count; the Star Wars Advent Calendar has the largest minifigure count by bubble size despite modest piece count.



Business Insight & Recommendation:
Star Wars remains LEGO's most prolific licensed franchise by volume (pieces), but Super Heroes has become the faster-growing franchise by set count and minifigure output since 2021. This signals a strategic shift in licensing investment. Recommendation: LEGO (and retail/marketing partners) should treat Super Heroes as the emerging growth engine while continuing to leverage Star Wars' flagship, high-piece-count "hero sets" (like the Millennium Falcon) for premium/collector positioning.


# 💲 Page 3 — "The Economics of LEGO Sets"

<img width="1549" height="879" alt="Image" src="https://github.com/user-attachments/assets/ddd11165-28b2-49cf-98de-a0fe5448cf51" />

Purpose: A pricing-focused deep dive into how LEGO sets are priced, what drives price, and which themes command premium pricing.

Filters: Select Year, Select Dimension (Theme), Select Set Count (Top N, default 10).


Visuals & KPIs:


1. KPI cards: Total Price $262.068K, Min Price $1.49, Average Price $37.53, Max Price $849.99, with YoY change of +7.5% (+$2,016).
2. Line chart — Price per Pieces by Year: A dramatic structural shift is visible — price-per-piece jumps from near $0.01 in the early 2000s to ~$0.13 by 2010 and has remained elevated since, indicating a repricing of LEGO's cost-per-brick model.
3. Scatter plot — Avg Pieces vs. Retail Price: A strong positive correlation between piece count and price, confirming pieces are a primary price driver, with a few high-value outliers (large, premium sets).
4. Bar chart — Average Price by Theme (Set Count > 10): Icons, Creator Expert, Advanced Models, and Education command the highest average prices — these are LEGO's premium adult/collector-oriented lines.
5. Heatmap — Average Price by Age Group and Decade: Price intensity is highest in the 2010s for the 15–20 age bracket, confirming LEGO's premiumization strategy has targeted older/adult builders in the last decade.
6. Line chart — Average Price by Year: Prices rose from $4.99 in 1999 to $52.95 at peak, settling around $52.58 currently — over a 10x increase in average set price in two decades.
7. Bar chart — Set Count by Price Bucket: The $1–$50 bucket dominates set count, showing LEGO still anchors its portfolio in accessible price points despite premium growth.
8. Bar chart — Top 10 Themes by Max Price: Star Wars has the single highest-priced set in the catalog, followed by Serious Play, Icons, Marvel Super Heroes, Harry Potter, Technic, Ideas, and Education.
9. Summary table (Theme-level): Reveals concentration risk/opportunity — e.g., Icons (43 sets) derives 25.4% of its total theme revenue from just its top 3 sets, and Marvel Super Heroes derives 20.4% from its top 3, versus Star Wars at a much more diversified 11.9% — showing Star Wars' pricing power is spread across a broad catalog, while Icons/Marvel rely more heavily on flagship sets.



Business Insight & Recommendation:
LEGO has executed a clear premiumization strategy since 2010, roughly doubling price-per-piece and targeting the 15–20 age bracket with high-price, high-piece "display" sets (Icons, Creator Expert). Recommendation: Continue investing in premium adult-targeted lines, but monitor revenue concentration risk in themes like Icons and Marvel, where a small number of hero SKUs drive a disproportionate share of revenue — diversify the mid-tier catalog to reduce single-SKU dependency.


# 🧩 Page 4 — "Brick by Brick: Unraveling the Complexity"

<img width="1549" height="880" alt="Image" src="https://github.com/user-attachments/assets/1543ad69-7b4b-4203-9718-208e01f4864b" />

Purpose: Examines how physically complex (piece-heavy) LEGO sets have become over time, and how that complexity relates to minifigure inclusion.

Filters: Select Dimension, Select Life Span (default 10 years), Select Year, Select Category.


Visuals & KPIs:


1. KPI cards: Total Pieces 3.303M (YoY +3.6% / +9,706); Min/Avg/Max Pieces (0 / 225.3 / 12,000); Total Minifigures 22.372K (YoY -2.2% / -27); Min/Avg/Max Minifigures (1 / 2.66 / 80).
2. Bar chart — Total Pieces by Decade: The 2010s account for 42.6% of all pieces ever produced, followed by the 2020s at 23.0% — meaning two-thirds of LEGO's entire cumulative piece output has occurred in just the last ~13 years.
3. Histogram — Piece Count Distribution: Most sets fall in the 0–50 piece bucket (entry-level sets dominate by count), but there's a notable secondary spike in the 450+ bucket, reflecting LEGO's bifurcated strategy of simple starter sets alongside large showpiece builds.
4. Box plot — Piece Distribution by Decade: Median and IQR piece counts, along with max outliers, expand progressively each decade, visually confirming increasing set complexity and growing variance (bigger "big sets") over time.
5. Line chart — Total Pieces by Year (with milestones): Annotated with key innovation turning points — "Life of George" (first interactive game set), Mindstorms "A Robotic Journey", and Technic "Electric Era" — tying piece-count growth to genuine product innovation events, not just volume growth.
6. Line chart — Avg Pieces by Age Band and Decade: Piece counts scale sharply with target age group in recent decades — the 15–20 age band shows the steepest growth curve, again reinforcing the premium/adult complexity trend.
7. Scatter plot — Set Count vs. Minifigures correlation: A steep inverse relationship — the vast majority of sets include very few minifigures (0–5), with a long tail of rare, high-minifigure specialty sets (e.g., collectible multi-packs).
8. Bar chart — Top 10 Sets by Pieces & 10+ Year Lifespan: Eiffel Tower, Millennium Falcon, At-At, Hogwarts Castle, Taj Mahal, and Imperial Star Destroyer represent LEGO's most enduring, piece-dense flagship builds.
9. Theme-level table: Icons shows the highest piece concentration (30.4% of its pieces from top 3 sets) and Harry Potter the highest at 32.7%, versus Technic at just 5.7% — indicating Technic's complexity is spread evenly across its catalog, while Icons/Harry Potter rely on a few architecturally massive sets.



Business Insight & Recommendation:
LEGO's complexity growth is not evenly distributed — it is concentrated in flagship/display sets within specific themes (Icons, Harry Potter), while volume-driver themes (Technic, City) maintain more even complexity distribution. Recommendation: Continue the dual-track strategy (simple entry sets + iconic large builds), but use the piece-distribution data to identify whitespace opportunities in the 150–350 piece "mid-complexity" range, which is currently underrepresented relative to the 0–50 and 450+ extremes.


# 🎨 Page 5 — "LEGO Dashboard: A Colorful Journey" (Theme & Category Evolution)

<img width="1544" height="887" alt="Image" src="https://github.com/user-attachments/assets/f5c3f5ed-d8f9-4d63-8eb6-5688b1728fce" />

Purpose: Explores the structural evolution of LEGO's theme and category portfolio — how many themes exist, how long they last, and how age-targeting has shifted.

Filters: Dimension (Category), Top/Bottom 3, Select Category, Select Year, Theme Status (New Theme), Clear All Slicers button.


Visuals & KPIs:


1. KPI card: 18,457 total sets released, with a year-selectable YoY population comparison.
2. Bar chart — Top 3 Category Contribution: Normal, Gear, and Other categories account for 90.4% of all sets released — confirming most of LEGO's catalog sits in mainstream, non-niche categories.
3. Area chart — "Decoding LEGO Age Appeal": Average minimum recommended age has trended from a historical average of ~6 years old, dipping as low as 1.0–3.8 in certain years (younger-skewing releases), peaking at 10.5, and settling at 8.1 most recently — showing LEGO has gradually shifted toward older target audiences over time.
4. Bar chart — Yearly Introduction of New Themes: New theme launches are volatile year to year (ranging from 1 to 8 new themes annually), but a dotted upward trendline confirms an overall increase in new-theme velocity — LEGO is diversifying its portfolio faster than in previous decades.
5. Line chart — "Decades of Innovation" (Set Count with milestones): Annotated with major brand milestones: Duplo (bricks for small hands), Education (products for schools), the Star Wars licensing launch (evergreen product), and the first MMOG launch — culminating in 37.6% growth to a set count of 967 in the most recent period, alongside a "Top 5 Themes" reference trendline.
6. Bubble chart — Theme Lifespan (Introduction Year vs. Last Release Year, sized by set count): Segments themes into four quadrants — "Long-Lived, Early Themes" (introduced early, still active), "Long-Lived, Recent Themes, Post-2020" (highlighted in pink — new themes already proving durable), and "Short-Lived, Early Themes" (introduced and discontinued quickly) — a powerful lens for theme portfolio health.
7. Heatmap — Category by Decade: The "Normal" category dominates the 2000s most intensely, showing when LEGO's core mainstream catalog scaled fastest.
8. Heatmap — "LEGO Sweet Spot" (Age Band by Decade): Confirms the 5–10 age bracket in the 2010s as LEGO's single densest target demographic historically.
9. Streamgraph — Theme Group Evolution Over Time: "Miscellaneous" (31.9%) and "Licensed" (13.6%) are the largest theme groups by share, visualizing how licensed IP has grown from a negligible share pre-1999 to a major layer of the modern catalog.




Business Insight & Recommendation:
LEGO's catalog strategy has matured from a narrow, young-skewing product line into a broad, faster-iterating portfolio targeting older age groups, with licensed IP now a structurally significant (and growing) share of the theme mix. Recommendation: Use the theme-lifespan quadrant analysis to proactively identify "Short-Lived, Early Themes" patterns and apply those learnings to de-risk new theme launches — post-2020 themes are already showing better durability, suggesting current theme-selection criteria are improving.



# 🔑 Key Findings (Cross-Dashboard Summary)


1. Structural Inflection Point (1990s): LEGO's set production grew 828.8% from the pre-1990s era to post-1990s, marking the single largest structural shift in the brand's history — driven by Duplo, Education, and licensed product lines like Star Wars.
2. Complexity Concentration in the Last Decade: 42.6% of all LEGO pieces ever produced came in the 2010s alone, with the 2020s already at 23% — meaning ~two-thirds of LEGO's cumulative complexity has been built in roughly the last 13 years.
3. Premiumization Since 2010: Average price-per-piece roughly doubled starting in 2010 and average set price grew over 10x since 1999 ($4.99 → ~$53), driven by adult-targeted lines (Icons, Creator Expert) and the 15–20 age bracket.
4. Licensed IP Power Shift: Star Wars remains the volume/pieces leader, but Super Heroes overtook it in set count and minifigures starting 2021 — a meaningful shift in licensing strategy and consumer demand.
5. Aging-Up Audience: LEGO's average minimum recommended age has trended upward over time (toward ~8+ years), alongside a densest historical "sweet spot" of ages 5–10 in the 2010s — evidence of a deliberate broadening beyond young children.
6. Revenue/Complexity Concentration Risk: Niche premium themes (Icons, Harry Potter, Marvel Super Heroes) derive a disproportionate share (20–32%) of their pieces/revenue from just their top 3 sets, versus workhorse themes (Technic, Star Wars) which are far more diversified (5.7–11.9%).
7. Portfolio Diversification Accelerating: New theme introduction velocity is trending upward, and themes launched post-2020 are already showing better long-term durability than early-era theme launches.


# 💡 Business Recommendations


1. Double down on Super Heroes' growth trajectory in set count and minifigure design, while preserving Star Wars' role as the flagship, high-piece-count premium franchise.
2. Continue premium/adult-targeted product investment (Icons, Creator Expert, Advanced Models), but pair it with mid-complexity catalog expansion (150–350 piece range) to avoid over-indexing on entry-level vs. showpiece extremes.
3. Diversify revenue within concentrated themes (Icons, Harry Potter, Marvel) by expanding the mid-tier catalog, reducing dependency on a handful of flagship SKUs.
4. Leverage the theme lifecycle model (short-lived vs. long-lived quadrants) as a forward-looking screening tool before greenlighting new theme launches.
5. Monitor age-targeting drift — as LEGO skews older, ensure the youngest demographic (ages 1–5) remains adequately served to protect brand entry points for new customers.


# 🛠️ Tools & Technologies

| Category | Tools/Techniques |
|---|---|
| **BI Platform** | Microsoft Power BI Desktop |
| **Data Preparation** | Power Query (M language) — cleaning, type casting, binning, custom columns |
| **Data Modeling** | Power BI semantic model, calculated tables, DAX |
| **Analytics/Measures** | DAX — time intelligence, ratio measures, dynamic titles, statistical aggregations |
| **Visualization** | Native Power BI visuals (line, bar, scatter, bubble, box plot, histogram, heatmap/matrix, streamgraph, KPI cards, tables) |
| **UX/Navigation** | Bookmarks, buttons, slicers, drill-through, tooltips, "Clear All Slicers" reset control |
| **Design** | Custom color theming, consistent header/navigation bar across all 5 pages |


# 🎯 Project Outcomes


- Delivered a 5-page, fully interactive Power BI report analyzing 18,457 LEGO sets across 50+ years.
- Built a clean semantic model with 20+ custom DAX measures covering time intelligence, ratios, and concentration analysis.
- Surfaced 7 concrete, data-backed business insights with actionable recommendations spanning pricing, licensing strategy, and portfolio management.
- Demonstrated end-to-end BI capability: data cleaning → modeling → DAX → visualization design → business storytelling — all documented for a portfolio audience.


# 🚀 Future Improvements


- Integrate real-time Brickset API refresh to keep the dataset current with new releases.
- Add predictive modeling (e.g., forecasting future set counts, price trends using Power BI's built-in forecasting or an external Python/R integration).
- Build a sentiment/rating layer by joining in LEGO set review data (e.g., from Brickset or retailer review APIs) to correlate price/complexity with customer satisfaction.
- Expand the licensed franchise comparison beyond Star Wars vs. Super Heroes to include Harry Potter, Ninjago, and other major franchises in a unified head-to-head view.
- Add row-level security (RLS) and a Power BI Service deployment for live, shareable access instead of static screenshots.
- Incorporate natural language Q&A (Power BI Q&A visual) for ad hoc business user queries.
