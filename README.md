# Eniac: Is Discounting Good for the Business?

A data analysis of Eniac's online sales (January 2017 to March 2018), built in Python to settle a debate between Marketing and the board: **do discounts help the business grow, or do they just lower revenue and hurt the brand?**

Group project for the Data Analytics & AI course at WBS Coding School (Berlin). Presented to the "board" as the Eniac Data Analytics Team in a 5-minute presentation ([slides](documentation/slides/eniac-board-presentation.pdf)).

**Team:** Catalina Mayer, Ali Haydar Dogan, Louise Gorman

---

## The business problem

Eniac is an online shop for Apple products and accessories.

- **The Marketing Team Lead** thinks discounts win new customers, keep them loyal and help the company grow.
- **The board** is worried: last quarter orders went up but revenue went down. They want Eniac to be a quality brand, not the cheapest shop.

Questions we were asked to answer:

1. How many products are discounted?
2. How big are the discounts, as a % of the product price?
3. How do seasons and special dates (Black Friday, Christmas) affect sales?
4. How can products be grouped into categories to make reporting simpler?
5. How are product prices spread across categories?
6. How could data collection be improved?

---

## Key findings

| Question | Answer |
|---|---|
| **1. How many products are discounted?** | **93%** of items sold were sold below list price. 96% of the products sold were discounted at least once. Discounting is the norm, not a special event. |
| **2. How big are the discounts?** | **21.4%** on average per order line (median 17.6%). In euros, discounts took €1.53M off a list value of €9.34M (**16.3%**), because expensive items get smaller discounts. |
| **3. Seasons and special dates** | Sales and revenue peak in **November, the Black Friday month (€1.50M, 19% of all revenue)**, and stay high through Christmas, then fall. The average discount stays between 15% and 25% in every month. **The calendar moves sales much more than the size of the discount.** The board's "revenue went down last quarter" is the normal after-Christmas drop: January and February 2018 were both above the same months in 2017. |
| **4. Product categories** | 9,992 products sorted into **12 groups** (86 sub-categories) using the product type code, then word searches on product names, plus a `condition` column (New / Open box / Refurbished / Second hand). |
| **5. Prices across categories** | Very different price levels: Mac & Computers median about €2,700, NAS & Servers about €980, iPhones about €770, iPads about €730, down to Cases, Cables & Accessories about €40. |
| **6. Data collection** | See [How to improve data collection](#how-to-improve-data-collection). |

**Where the money comes from:**

- **Cheap items get the biggest discounts but bring in little revenue.** Items under €50 are discounted 26% on average and bring in only 6% of revenue. Items from €150 to €1,000 bring in **71% of revenue** with discounts of 10–16%.
- **By category:** Cases, Cables & Accessories sell the most items (19,002) with the biggest discount (29%) but bring in only €0.70M. iPhones are the opposite: 2,339 items, a 9% discount and €1.33M. Storage & Memory is the biggest earner (€2.24M, 17.5% discount).
- **By discount size:** discounts of up to 20% bring in **about two thirds of revenue** (66%) from under half of the items sold. Deeper discounts sell items but bring in little revenue.

---

## Recommendation

**Keep discounts small and targeted:**

1. **Stop blanket discounting all year.** Every month has an average discount of 15–25%.
2. **Lower discounts on items over €150 to about 10%.** They bring in over 70% of revenue, so every 1% of discount costs the most euros here.
3. **Save big discounts for key dates:** Black Friday, Christmas and clearing old stock.
4. **Test before changing everything.** Lower the discount on a small group of products and measure what happens to sales.

---

## Data and caveats

The data came from Eniac's internal database: 4 tables (orders, order lines, products, brands). It was messy, with duplicates, missing values and corrupted prices. Every cleaning step and its impact is documented in the notebooks.

**Keep in mind when reading the findings:**

- **Completed orders only:** 40,985 orders and 53,231 order lines after cleaning (from 226,909 orders in the raw data, most of them unfinished shopping baskets).
- **Every sale of €1,000 or more is missing.** Prices over €1,000 were stored with an extra dot (e.g. `1.137.99`) and couldn't be read, so those orders were removed. Expensive products, especially Macs, are under-represented.
- **No cost data,** so we measure **revenue** (money coming in), not profit.
- **March 2017 is mostly missing** (only 241 items sold), and the data ends on **14 March 2018**.
- **"Average discount"** = (list price − price paid) ÷ list price, averaged over order lines.

---

## How the data was cleaned

| Step | Notebook | What happens | Result |
|---|---|---|---|
| 1. Data cleaning | `01_data-cleaning.ipynb` | Fixes problems inside each table: 8,746 duplicate products, 5 orders with no total, 46 products with no price, text dates, 36,169 order lines with unreadable two-dot prices (whole orders removed), 542 broken product prices, an unusable promo price column (92% broken) | orders 226,904 · order lines 216,250 · products 9,992 |
| 2. Data quality | `02_data-quality.ipynb` | Checks the tables make business sense together: Completed orders only, no €0 totals, no prices of €0 or less, orders present in both tables, no unknown products, and the amount paid matches the products bought (unexplained differences removed with the IQR method) | orders 40,985 · order lines 53,231 |
| 3. Product categories | `03_product-categories.ipynb` | Adds a sub-category, a grouped category and a condition to every product | 9,992 products, 12 groups |
| 4. Analysis and charts | `04_analysis-charts.ipynb` | Answers the business questions and draws the 4 presentation charts | 4 charts |

---

## Repository structure

```
eniac-discount-strategy/
├── README.md
├── data/
│   ├── raw-data/                  original files from Eniac
│   │   ├── brands.csv
│   │   ├── orderlines.csv
│   │   ├── orders.csv
│   │   └── products.csv
│   └── clean-data/
│       ├── orders_cl.csv          ← 01
│       ├── orderlines_cl.csv      ← 01
│       ├── products_cl.csv        ← 01
│       ├── orders_qu.csv          ← 02
│       ├── orderlines_qu.csv      ← 02
│       └── products_categorised.csv  ← 03
├── scripts/
│   ├── 01_data-cleaning.ipynb
│   ├── 02_data-quality.ipynb
│   ├── 03_product-categories.ipynb
│   └── 04_analysis-charts.ipynb
└── documentation/
    └── slides/
        └── eniac-board-presentation.pdf
```

`_cl` = cleaned, `_qu` = quality-controlled. `brands.csv` needed no cleaning and was not used in the final analysis.

---

## How to run

The notebooks were written in **Google Colab**. Open them in order (01 → 04) and run all cells. Each notebook loads its input files from shared Google Drive links, so no setup is needed. Each one also ends with an export step that saves its output CSV.

**Tools:** Python, pandas, seaborn.

---

## How to improve data collection

1. **Add automated input checks.** Broken prices (`1.137.99`) cost us every sale of €1,000 or more, and March 2017 is almost empty.
2. **Record discounts and shipping separately at checkout.** Today they are hidden inside the order total, so order-level discounts (vouchers) can't be measured.
3. **Record product costs.** With costs we could measure profit, not only revenue.
4. **Keep the product catalogue complete and clean.** 1,354 orders contained products missing from the catalogue. Some product type codes were turned into scientific notation (e.g. `5,74E+15`) by a spreadsheet, and one type code (`1298`) mixes a product's condition (open box) with its type.
