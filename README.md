# Olist E-Commerce Sales Analysis

Exploratory data analysis of the [Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (about 100k orders placed on Brazilian marketplaces between 2016 and 2018). The project answers basic business questions about sales, product categories, delivery performance and customer satisfaction.

**Tech stack:** Python, pandas, NumPy, Jupyter Notebook. Planned: SQLite / SQL, Plotly, Streamlit.

> **Status: work in progress.** The EDA notebook (`eda.ipynb`) is complete. The SQLite ETL (`etl.py`) and the interactive Streamlit dashboard (`app.py`) are the next steps and are **not in this repository yet**. See the [roadmap](#roadmap).

## Key findings

All monetary values are in Brazilian reais (R$). "Revenue" means the sum of item `price` for **delivered** orders; freight (`freight_value`) is excluded.

| Question | Finding |
|---|---|
| How did sales evolve? | Monthly revenue grew from R$ 111.8k (Jan 2017) to a peak of R$ 987.8k (Nov 2017), then stayed between R$ 826k and R$ 978k from Jan to Aug 2018. Total delivered revenue is about R$ 13.2M. |
| Which categories earn the most? | `health_beauty` (R$ 1.23M), `watches_gifts` (R$ 1.17M) and `bed_bath_table` (R$ 1.02M) lead. The top 10 categories generate 62.4% of revenue. |
| Where do customers live? | Highly concentrated: the state of São Paulo accounts for 42.0% of orders (41,746 of 99,441, all order statuses). SP, RJ and MG together account for 66.6%. |
| How long does delivery take? | Median 10.2 days, mean 12.6 days. The distribution is right-skewed: p95 = 29.3 days, p99 = 46.1 days, max = 209.6 days. |
| How do customers rate their orders? | Polarized (all reviews): 57.8% give 5 stars, 11.5% give 1 star, and only 3.2% give 2 stars. |
| Is delivery time related to the score? | Yes. Median delivery time rises from 9.2 days for 5-star orders to 16.8 days for 1-star orders. |
| Do late deliveries get lower scores? | 6.7% of reviewed, delivered orders arrived after the estimated date (6,381 of 95,824). Their mean score is 2.27, versus 4.29 for on-time orders. |

These are associations, not causal claims.

## Data quality and methodology decisions

- **Revenue definition:** only `delivered` orders count, because the seller does not earn from canceled or unavailable orders. This removes about 2.7% of the gross item revenue (R$ 13.59M to R$ 13.22M).
- **Time range:** the monthly trend excludes 2016 (three months, about R$ 40k in total, platform ramp-up). September 2018 has no delivered orders, so the trend covers 20 months (Jan 2017 to Aug 2018).
- **Timestamps:** all date columns were converted from strings to `datetime`.
- **Geolocation:** 261,831 exact duplicate rows (26.2%) were removed.
- **Products:** 610 products without category, name length, description length and photo count got the category `unknown` and median-filled numeric values. 2 products with missing dimensions were dropped, so 18 order items are excluded from the category analysis.
- **Category names:** 1,610 order items had no English category name (1,586 `unknown`, 15 `portateis_cozinha_e_preparadores_de_alimentos`, 9 `pc_gamer`). They keep their original name instead of being dropped.
- **Reviews:** 547 orders have more than one review (202 of them with conflicting scores). The latest review per order is kept. 646 delivered orders have no review and drop out of the delivery-vs-score analysis.
- **Delivery time:** measured in fractional days (delivered date minus purchase date). 8 delivered orders have no delivery date and stay `NaN`. Outliers were kept, so medians are reported next to means.
- **Late delivery:** an order is late if its delivery day is after the estimated delivery date (the delivery timestamp is normalized to the day, because estimated dates have no time component).
- **Order counts by state** use all order statuses, unlike the revenue, delivery and review analyses.

## Project structure

```
.
├── eda.ipynb          # data quality checks, cleaning and analysis
├── requirements.txt
├── README.md
└── data/              # not tracked, see "Getting started"
```

## Getting started

```bash
git clone https://github.com/<your-username>/e-commerce-sales-analysis-dashboard.git
cd e-commerce-sales-analysis-dashboard

python -m venv .venv
# Windows: .venv\Scripts\activate   |   macOS / Linux: source .venv/bin/activate
pip install -r requirements.txt
```

The raw data is not included in the repository. Download it from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), create a `data/` folder and extract the nine CSV files into it:

```
olist_customers_dataset.csv
olist_geolocation_dataset.csv
olist_order_items_dataset.csv
olist_order_payments_dataset.csv
olist_order_reviews_dataset.csv
olist_orders_dataset.csv
olist_products_dataset.csv
olist_sellers_dataset.csv
product_category_name_translation.csv
```

Then open `eda.ipynb` in Jupyter, VS Code or PyCharm and run all cells.

## Roadmap

- [x] Data quality checks and cleaning for all nine tables
- [x] EDA: sales trend, category revenue, delivery time, review scores
- [ ] `etl.py`: load the CSVs into SQLite
- [ ] SQL queries that reproduce the notebook results
- [ ] Plotly charts
- [ ] Streamlit dashboard (`app.py`) with date, state and category filters
- [ ] Seller and payment analyses

## Dataset and credits

Data: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), provided by [Olist](https://olist.com/) on Kaggle and released under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). The data is not included in this repository.

## License

Code: [MIT](LICENSE). The dataset is not covered by this license and remains under CC BY-NC-SA 4.0 (attribution, non-commercial, share-alike).
