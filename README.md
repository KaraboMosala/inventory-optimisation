# Inventory Optimisation
### Safety stock · Reorder points · EOQ · $93,942 annual saving identified · 100 SKUs

---

## Overview

Built a complete inventory optimisation system for a beauty supply chain covering 100 SKUs across skincare, haircare, and cosmetics. Starting from raw supply chain data, calculated scientifically-derived safety stock levels, reorder points, and economic order quantities — identifying $99,770 tied up in overstock and $93,942 in annual cost savings from order quantity optimisation.

This is the bridge between demand forecasting and operations: knowing what will sell is only valuable if you have the right stock, in the right place, at the right time.

---

## Results

| Metric | Value |
|---|---|
| SKUs analysed | 100 |
| Total inventory value | $121,736 |
| **Overstock value** | **$99,770 (82% of total)** |
| SKUs at stockout risk | 11 |
| SKUs below reorder point | 37 |
| **Annual EOQ saving** | **$93,942** |

![Inventory Dashboard](inventory_dashboard.png)

---

## Dataset

**Source:** [Supply Chain Analysis — Kaggle](https://www.kaggle.com/datasets/harshsingh2209/supply-chain-analysis)

**Categories:** Skincare (40 SKUs) · Haircare (34 SKUs) · Cosmetics (26 SKUs)

| Column | Used for |
|---|---|
| `Stock levels` | Current inventory position |
| `Number of products sold` | Demand signal |
| `Lead times` · `Lead time` · `Manufacturing lead time` | Total replenishment lead time |
| `Order quantities` | Benchmark vs EOQ |
| `Price` · `Manufacturing costs` | EOQ holding cost |
| `Defect rates` | Effective demand adjustment |
| `Availability` | Demand variability proxy |

---

## Method

### Part 1 — Demand Statistics
```
Effective demand = units sold × (1 − defect rate)
Daily demand     = effective demand / 365
Demand CV        = 0.35 − (availability% × 0.25)
                   [high availability → stable demand]
Total lead time  = max(supplier LT, total LT, manufacturing LT)
                   [conservative planning assumption]
```

### Part 2 — Safety Stock
Uses the combined demand and lead time variability formula:

```
SS = Z × √(LT × σ_d² + d² × σ_LT²)

Where:
  Z     = service level Z-score
  LT    = average total lead time (days)
  σ_d   = standard deviation of daily demand
  d     = average daily demand
  σ_LT  = standard deviation of lead time (20% of mean)
```

**Service levels by category:**

| Category | Service Level | Z-Score | Rationale |
|---|---|---|---|
| Skincare | 95% | 1.645 | High loyalty — stockout = lost customer |
| Haircare | 90% | 1.282 | Moderate substitutability |
| Cosmetics | 85% | 1.036 | More impulse — lower loyalty risk |

### Part 3 — Reorder Point (ROP)
```
ROP = (daily demand × total lead time) + safety stock

"When stock drops to this level, place a replenishment order"
```

**Inventory health classification:**

| Status | Condition |
|---|---|
| CRITICAL | Stock < safety stock |
| WARNING — Below ROP | Safety stock ≤ stock < ROP |
| HEALTHY | ROP ≤ stock ≤ 2× ROP |
| OVERSTOCK | Stock > 2× ROP |

### Part 4 — Economic Order Quantity (EOQ)
```
EOQ = √(2DS / H)

Where:
  D = annual demand (units)
  S = ordering cost per purchase order ($50)
  H = holding cost per unit per year (25% of selling price)
```

EOQ minimises total inventory cost = ordering cost + holding cost.

---

## Key Findings

### 1. The business has the wrong inventory problem
Only 20% of SKUs (20/100) are in a healthy inventory state. The dominant problem is **overstock** — 43 SKUs with $99,770 of excess inventory — not stockouts. Yet 11 SKUs are simultaneously at critical stockout risk. Classic retail inventory paradox: too much of the wrong stock, not enough of the right stock.

### 2. $99,770 in working capital is trapped in slow movers
Overstock represents 82% of total inventory value. This capital is earning nothing and incurring holding costs of ~$24,943/year (25% rate). Identifying and liquidating overstock through markdowns or supplier returns would free significant working capital.

### 3. 74 SKUs are under-ordering — paying excess in ordering costs
74 out of 100 SKUs place more frequent, smaller orders than EOQ recommends — incurring unnecessary ordering costs. Consolidating to EOQ-sized orders saves **$93,942/year** across the range.

### 4. Haircare drives the biggest EOQ saving — $64,693
Haircare's combination of high order frequency and unit cost makes it the highest-opportunity category for order quantity optimisation. A buyer placing manual haircare orders is leaving $64k on the table annually.

### 5. Longer lead times require disproportionately more safety stock
The bottom-left safety stock vs lead time chart shows a clear positive relationship — a 30-day lead time product requires ~3× the safety stock of a 10-day product. Reducing supplier lead times from 30 to 15 days would allow a ~50% reduction in safety stock investment.

---

## Top 3 Actions

| Priority | Action | Impact |
|---|---|---|
| 🔴 Immediate | Place orders for 37 SKUs below ROP — 11 are at stockout risk | Prevent lost sales |
| 🟡 Short-term | Liquidate $99,770 of overstock via markdowns or supplier returns | Release working capital |
| 🟢 Structural | Switch to EOQ-based ordering across all 100 SKUs | Save $93,942/year |

---

## Pepkor Context

This analysis maps directly to challenges at large South African retailers:

- **PEP / Ackermans beauty ranges** carry hundreds of SKUs with variable lead times from Asian suppliers — the same overstock/stockout paradox appears at scale
- **Long supply chains** (30–90 day lead times from China) make safety stock calculation critical — every day of excess lead time requires additional buffer stock investment
- **Seasonal demand** (summer/winter switching) creates exactly the overstock problem visible here — slow-moving winter stock blocking open-to-buy for summer ranges

---

## Project Structure

```
inventory-optimisation/
│
├── notebooks/
│   └── 01_inventory_optimisation.ipynb  ← full analysis
│
├── charts/
│   └── inventory_dashboard.png          ← 6-panel dashboard
│
└── README.md
```

---

## Setup

```bash
git clone https://github.com/KaraboMosala/inventory-optimisation
cd inventory-optimisation
pip install pandas numpy matplotlib scipy
```

Download the dataset from [Kaggle](https://www.kaggle.com/datasets/harshsingh2209/supply-chain-analysis) and update the `PATH` variable in the notebook.

---

## Portfolio Context

Project 4 of a structured path to world-class retail data science:

| Project | Focus | Highlight |
|---|---|---|
| [1 — Demand Forecasting](https://github.com/KaraboMosala/retail-demand-forecasting) | LightGBM · 3M rows | RMSLE 0.3896 · Top 5% Kaggle |
| [2 — Customer Segmentation](https://github.com/KaraboMosala/customer-segmentation-rfm) | RFM · K-means | 27 features · £330k opportunity |
| [3 — Pricing System](https://github.com/KaraboMosala/retail-pricing-system) | Elasticity · Promo · Markdown | 17M rows · Recommendation engine |
| **4 — Inventory Optimisation** | **Safety stock · ROP · EOQ** | **$93k annual saving identified** |

---

## Author

**Karabo Mosala**
 
---

*Dataset: Supply Chain Analysis via Kaggle.*