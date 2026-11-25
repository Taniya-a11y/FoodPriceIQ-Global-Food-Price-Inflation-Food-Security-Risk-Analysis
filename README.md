<p align="center">
  <img src="images/icon.svg" alt="FoodPriceIQ icon" width="120">
</p>

<h1 align="center"> Global Food Price Inflation & Food Security Risk Analysis |</h1>

<p align="center">
  Analysis of 2.78 million global food price records (2020–2025) using Python, SQL and Power BI to track food inflation, price volatility, regional price gaps and country-level food security risk.
</p>

---

## 📌 Project Overview

Food prices decide how affordable basic staples are for millions of households. This project uses the World Food Programme (WFP) global food price data to answer four questions: how fast are prices rising, how unstable are they, how different are they across regions of the same country, and which countries face the highest combined risk.

The work has three stages:

1. **Python:** combine six yearly files, clean the data and flag unusual records.
2. **SQL (SQLite inside Python):** answer five business questions with CTEs, window functions and joins.
3. **Power BI:** a 2-page dashboard built on the aggregated SQL outputs.

## ❓ Problem Statement

Raw WFP price data is spread across multiple currencies, units and years, so it cannot be compared directly. The goal is to turn it into a clean, comparable dataset and extract decision-ready insights on:

- Year-over-year food inflation by country and commodity
- Month-to-month price volatility by category, commodity and country
- Regional price gaps within a country
- A 2020-based USD price index
- A Food Security Risk Index combining inflation, volatility and regional gap

## 📂 Dataset Description

| Item | Detail |
|---|---|
| Source | WFP Food Prices, Humanitarian Data Exchange (HDX) |
| Period | January 2020 – December 2025 (72 months, monthly data) |
| Raw size | 27,77,070 rows × 17 columns (6 yearly CSV files) |
| Markets | 4,405 unique markets |
| Key columns | countryiso3, date, admin1, admin2, market, category, commodity, unit, pricetype, currency, price, usdprice |

`price` is in local currency and `usdprice` is the USD equivalent, so `usdprice` is used for comparisons across countries.

## 🛠️ Tools & Technologies

| Area | Tools |
|---|---|
| Data cleaning | Python (pandas, NumPy) |
| Exploration | Matplotlib, seaborn |
| Analysis | SQL (SQLite): CTEs, window functions (LAG, RANK), joins |
| Visualization | Power BI (KPI cards, charts, map, slicers) |
| Environment | Jupyter Notebook |

## ⚙️ Project Workflow

**1. Data loading and checks**
- Combined 6 yearly CSVs into one DataFrame (2.78M rows).
- Converted `date` to datetime. There were 0 null dates and 0 duplicate rows.
- Filled missing `admin1` / `admin2` (0.166%) with `Unknown`.

**2. Outlier investigation**
- A plain IQR check on `price` flagged 18.96% of rows, which is misleading because prices are in different currencies.
- Switched to checks based on each group's median instead of fixed fences.
- Found that 1,323 of 1,407 early flags (94%) were Zimbabwe, caused by mixed ZWL and USD rows. Grouping by currency fixed this.

**3. Final cleaning**
- Kept only `KG` unit, non-food category removed, missing `usdprice` dropped.
- Flagged unusual records using an FX-consistency ratio and a price-consistency ratio, then removed them (about 0.03%).
- Built a Retail-only subset for SQL analysis.

**4. SQL business analysis (5 questions)**

| # | Business question | Techniques |
|---|---|---|
| Q1 | Year-over-year food inflation by year, country and commodity | `LAG`, self-join |
| Q2 | Monthly price volatility by category, commodity and country | `LAG`, `ABS` swing % |
| Q3 | Regional price gap within countries and highest-premium regions | `MAX`/`MIN` gap, joins |
| Q4 | USD price index (2020 = 100) and 2021→2022 staple jump | Base-year join, index |
| Q5 | Food Security Risk Index (inflation 40%, volatility 30%, regional gap 30%) | CTEs, `RANK` |

**5. Power BI export and dashboard**
- Exported aggregated `fact_` and `dim_` CSVs from the notebook.
- Built a 2-page dashboard on top of them with KPI cards, bar, column and line charts, a map and a risk table.
- Added interactive **slicers** (dropdown): Year and Risk Tier on Page 1, Risk Tier on Page 2. They let the viewer filter the whole page, for example to see only High-risk countries.

## 💡 Key Insights

Findings from the five SQL questions (Retail prices, KG unit, 2020–2025).

**Q1: Year-over-year food inflation**
- Average YoY food inflation peaked in **2022 at about 24.8%**, then eased to 21.9% (2023), 15.3% (2024) and **8.6% (2025)**. 2021 was 15.6%.
- Highest 2025 inflation: **South Sudan 88.3%**, **Sudan 69.5%**, **Iran 67.1%**, Bolivia 43.9% and Palestine 37.8% (countries with at least 5 commodities).
- Between 2020 and 2025, **Cabbage (+168.5%)**, **Rice (+165.9%)**, **Lentils (+160.3%)** and **Pasta (+159.4%)** rose the most. Local rice (+43.7%) and millet (+44.9%) rose the least.

**Q2: Monthly price volatility**
- **Vegetables and fruits** are the most volatile category at **15.4%** average monthly swing, about 4x **oil and fats (3.8%)**, the most stable.
- Most volatile commodities: **Chili (bird's eye, red) 27.4%** and **Tomatoes 23.8%**. Rice (medium quality) and first-quality beef are the steadiest at about 1.0%.
- Most volatile countries: **Ghana 24.3%**, **Zimbabwe 22.8%** and **Lebanon 18.2%**. The steadiest are **Laos 2.8%** and Afghanistan 3.7%.

**Q3: Regional price gap**
- Within-country price gaps are highest in **Myanmar (134.9%)**, **Sudan (127.8%)** and **Yemen (121.0%)**, and lowest in **Turkey (13.8%)** and Pakistan (17.0%).
- Perishables vary most between regions: **Cowpeas and Sweet potatoes (84.1%)** and **Tomatoes (80.2%)**. Sugar (30.4%) and rice (34–36%) vary least.
- Highest regional premium: **Socotra, Yemen (+69.8%)** and **Pwani, Tanzania (+51.1%)** above their country average. The cheapest region is **Al Mahwit, Yemen (−35.6%)**.

**Q4: USD price index (2020 = 100)**
- Every food category ended above its 2020 level. In 2025 the average index ranged from **124.5 (milk and dairy)** to **150.9 (miscellaneous food)**, with oil and fats (146.0) and pulses and nuts (147.7) also high.
- Oil and fats reached the highest peak at **163.1**.
- For staples (wheat flour, rice, maize, sugar), the **median 2021→2022 jump was 12.2%** across 50 countries, and prices rose in **41 of 50**. Biggest jumps: **Ethiopia (+57.8%)**, **Rwanda (+49.2%)** and **Iran (+47.7%)**.

**Q5: Food Security Risk Index**
- Of **44 countries scored**, **7 are High risk**, 25 Moderate and 12 Low.
- High-risk countries: **Sudan (99.1)**, **South Sudan (95.9)**, Zambia (82.7), Myanmar (78.9), Uganda (77.7), Yemen (76.1) and DR Congo (75.2).
- Sudan scores highest because all three signals are high at once: 69.5% inflation, 18.1% monthly volatility and a 212.1% regional gap.
- Lowest risk: **Pakistan and Niger (16.8)**. The index is relative and uses project-defined weights (inflation 40%, volatility 30%, regional gap 30%).

## 📊 Dashboard Screenshots

**Page 1: Food Price Inflation & Volatility Monitor**

![Dashboard Page 1](images/dashboard_page1.png)

**Page 2: Food Security Risk & Regional Price Gap**

![Dashboard Page 2](images/dashboard_page2.png)

## 🗂️ Folder Structure

```
FoodPriceIQ/
├── FoodPriceIQ_Step_by_Step_SQL_PowerBI.ipynb   # cleaning, SQL analysis, CSV export
├── taniya.pbix                                  # Power BI dashboard (2 pages)
├── outputs/
│   └── powerbi/                                 # fact_ and dim_ CSVs used by Power BI
├── images/
│   ├── icon.svg
│   ├── dashboard_page1.png
│   └── dashboard_page2.png
└── README.md
```

## ▶️ How to Run

1. Download the six WFP files (`wfp_food_prices_global_2020` to `2025`) from HDX and put them in one folder.
2. In the notebook, set `folder_path` to that folder.
3. Install the libraries: `pip install pandas numpy matplotlib seaborn`.
4. Run the notebook from top to bottom. Part 4 creates the Power BI CSVs in `outputs/powerbi/`.
5. Open `taniya.pbix` in Power BI Desktop and, if asked, point the data source to `outputs/powerbi/`.

## ⚠️ Limitations

- Analysis uses only `KG` unit and Retail price records.
- Country coverage is not the same every year.
- Outlier thresholds and risk weights are project choices.
- Averages can still be affected by extreme records.
- Row count and country coverage fall after 2022 (87 to 72 countries), so trends should be read with care.
- Only 44 of 78 countries have all three risk indicators, so the rest are not scored.

## 👩‍💻 Author

**Taniya**
Entry-level Data Analyst

- LinkedIn: taniyakaushal513@gmail.com]
- GitHub: [add your link]
- Email: [add your email]
