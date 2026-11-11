# Supply Chain Delivery & Inventory Optimization Analysis

An end-to-end supply chain analytics project covering delivery performance, product segmentation, demand forecasting and inventory policy. Built with Python (pandas, statsmodels) and Power BI.

**Dataset:** DataCo Smart Supply Chain for Big Data Analysis (Kaggle). The raw file is not included in this repository because of its size. Download it from Kaggle and place it in the `data/` folder as `DataCoSupplyChainDataset.csv`.

## Business questions

1. Why are so many deliveries late, and what would fix it?
2. Which product categories matter most, and how stable is their demand?
3. Which forecasting method works best on this data?
4. How much safety stock is needed, and what drives it?

## Key findings

| Area | Finding |
|---|---|
| Delivery | 57.3% of orders arrive late. The cause is the delivery promise, not region, customer segment or order month. |
| Promises | First Class promises 1 day but always takes 2. Second Class promises 2 days but averages 4, the same real speed as Standard Class (promise 4 days). |
| Fix | Resetting promises (First Class 2 days, Second Class 4, Same Day 1) cuts the overall late rate from 57.3% to 31.5%. |
| Segmentation | 7 of 24 eligible categories (A-class) drive about 80% of revenue and have very stable demand. |
| Forecasting | Simple exponential smoothing performed best on a 6-month holdout (MAPE 4.6% vs 5.8% naive). Holt-Winters added no accuracy. |
| Safety stock | Delivery-time variability drives 79% to 93% of safety stock variance. Halving it cuts safety stock by about 42%, versus about 4% from halving forecast error. |
| Simulation | With more consistent deliveries, safety stock can fall to about 60% of today's level with no rise in stockout days, freeing roughly $21K (about 16%) of average inventory. |

## Project structure

```
supply-chain-project/
├── notebooks/
│   ├── 01_data_cleaning.ipynb        Cleaning, delivery KPIs, promise trade-off
│   ├── 02_abc_xyz.ipynb              Product lifecycle check, category ABC-XYZ matrix
│   ├── 03_forecasting.ipynb          Naive, moving average, SES, Holt-Winters
│   ├── 04_inventory_policy.ipynb     Safety stock, reorder point, EOQ, simulation
│   └── 05_logistics_scorecard.ipynb  Mode and region scorecards, root causes
├── data/                             Output tables used by the dashboard
└── dashboard/                        Power BI file
```

## Methods in brief

- **Cleaning:** removed cancelled and suspected-fraud orders; analysis window is Jan 2015 to Sep 2017 (33 stable months).
- **ABC-XYZ:** product-level XYZ mostly reflected product launches and retirements, not demand variability, so the matrix is built at category level. Only categories with at least 28 active months are classified.
- **Forecasting:** 27 months of training and a 6-month holdout, scored with MAPE, RMSE and bias.
- **Safety stock:** `Z × sqrt(LT × σ_d² + d² × σ_LT²)` with Z for a 95% service level.
- **Simulation:** 12 months, 200 runs per category, normal demand and lead times.

## Assumptions

- 95% service level
- Unit cost is 70% of selling price
- Annual holding cost is 20% of unit cost
- Ordering cost is $50 per order
- 30 days per month
- Customer delivery time (`days_real`) is used as a proxy for replenishment lead time

## Limitations

- The data appears synthetic: First Class always takes exactly 2 days, prices are constant within each category and demand is unusually smooth.
- Lead time is a proxy, not true supplier lead time.
- The 50% reduction in delivery variability is an assumed target, not a demonstrated result.
- Simulation outputs are modelled. Differences of a few hundredths of a percent are noise.
- The forecast holdout is only 6 months.
- Product-level analysis was limited by short product histories, so the matrix uses categories.

## How to run

1. Download the Kaggle dataset into `data/`.
2. Install dependencies: `pip install pandas numpy matplotlib statsmodels scipy`
3. Run the notebooks in order, `01` to `05`.

<!-- Add dashboard screenshots here once the Power BI report is finished -->
