# Olist Delivery Delays, Regional Bottlenecks & Customer Sentiment

Every order on Olist comes with an estimated delivery date—a core service promise made at checkout. This diagnostic evaluates the operational and financial cost when that promise is broken: how customer sentiment collapses, which geographic corridors fail, and how much merchandise value is exposed.

The analysis evaluates 96,470 completed deliveries between 2016 and 2018 using Python (`pandas`, `matplotlib`, `seaborn`).

---

## 📌 Core Questions

1. **Delivery Reliability & Sentiment Decay:** How frequently are delivery estimates breached, and what is the exact rate of review score degradation as delays grow?
2. **Geographical Friction:** Which states and transit corridors account for the heaviest delay concentrations?
3. **Shipping Economics:** Does high freight burden drive negative customer reviews, or is delivery timeline breach the real driver?
4. **Financial Exposure:** How much Gross Merchandise Value (GMV) sits on delayed orders?

---

## 📊 Key Findings

### 1. Delivery Delays & Review Score Collapse
* **1 in 15 delivered orders (6.77%) arrives late.** Late shipments experience a sharp drop in customer satisfaction, averaging **2.27★** compared to **4.29★** for on-time deliveries.
* **The 1-star surge:** Missing the promised delivery date causes 1-star reviews to jump eightfold, from **6.6% to 52.5%**.
* **Sentiment decay by delay tier:**

| Delivery Timeliness | Delivered Orders | Average Review Score | 1-Star Review Share |
| :--- | :---: | :---: | :---: |
| **On time or early** | 89,936 | 4.29★ | 6.6% |
| **1–3 days late** | 1,870 | 3.29★ | 24.9% |
| **4–7 days late** | 1,802 | 2.10★ | 56.8% |
| **>7 days late** | 2,862 | 1.70★ | 67.9% |

* Delivering early shows diminishing returns: beating the SLA by >3 days averages 4.30★, while 0–3 days early averages 4.11★. However, customer patience breaks sharply after day 3 of delay.

### 2. Route & State-Level Friction
* **The Northeast Bottleneck:** Seven of the ten worst-performing states by late rate are in the Northeast, led by Alagoas (AL) at **21.4%** and Maranhão (MA) at **17.4%**.
* **The Long-Haul Penalty:** Shipments moving from Southeast merchant hubs to Northeast customers breach delivery dates **12.9%** of the time (~20.1 days average transit), more than double the **6.4%** late rate for intra-Southeast shipments.
* **Volume vs Rate:** São Paulo (SP) generates the highest total late volume (1,820 orders) due to sheer demand, but maintains a healthy **4.5%** late rate. Rio de Janeiro (RJ) is the Southeast anomaly: carrying 1,495 late orders at an elevated **12.1%** late rate despite moderate transit times (15.3 days).

### 3. Freight Economics vs Lateness
* **Freight cost does not explain low ratings:** While northern buyers bear higher shipping expenses (freight averages 22.7% of product price, compared to 15.2% in the Southeast), freight value, price, and freight burden ratio show near-zero correlation with customer review scores.
* When controlling for region across freight tiers, review scores remain within ~0.1 stars of each other. Broken delivery deadlines dictate customer dissatisfaction, not shipping fees.

### 4. Financial Exposure
* **R$ 985,924.34** in product GMV (7.46% of total platform merchandise value) sits on delayed deliveries.
* **Severe Delay Concentration:** Deliveries delayed past 7 days account for **45.4%** (R$ 447k) of all delayed GMV. Because these orders were fulfilled and paid for, this represents platform exposure to damaged retention and customer churn rather than direct uncollected revenue.

---

## ⚙️ Data Engineering & Methodology Choices

Specific pipeline decisions were made to prevent skewed diagnostic metrics:

* **Calendar-Day SLA Normalization:** The estimated delivery date in the raw data lacks hour timestamps, while actual delivery timestamps include exact times. Comparing timestamps directly falsely marks orders delivered on the promised afternoon as late. Normalizing both fields with `.dt.normalize()` resolved this.
* **Order-Level Aggregation:** Multi-item orders contain repeated shipping charges and item prices across lines. Prices and freight were summed per order rather than deduplicating or dropping items. For multi-seller orders, the first seller's state was selected for geographic corridor mapping.
* **Review Deduplication:** 551 orders contained duplicate review entries; only the latest review timestamp per `order_id` was retained.
* **Weighted Freight Burden:** Freight burden was computed as `total freight / total item price` per cohort rather than averaging individual per-order ratios, preventing extreme distortions caused by low-cost items with standard shipping minimums.

---

## 🎯 Practical Recommendations

1. **Recalibrate SLAs for Outer Regions:** Pad promised delivery dates by 3 to 5 business days for long-haul routes into the Northeast and North. Customers penalize SLA breaches far more harshly than conservative initial delivery estimates.
2. **Prioritize 7+ Day Delay Escalations:** Intervene on orders approaching 3 days late with automated courier tracking alerts. The >7 day cohort carries 45.4% of exposed GMV and suffers the worst sentiment collapse (1.70★).
3. **Audit Rio de Janeiro Courier Performance:** Carrier contracts serving Rio de Janeiro require operational review, as RJ's 12.1% late rate significantly trails neighboring Southeast states with comparable transit distances.

---

## ⚠️ Analytical Limitations

* **Delivered Orders Only:** Canceled orders and shipments lost in transit are excluded; real fulfillment failure rates across all platform transactions are likely higher.
* **Observational Scope:** Identifies correlations between lead time, geography, and reviews without isolating unobserved variables like parcel dimensions, seller inventory dispatch speed, or carrier handoff delays.
* **Voluntary Review Selection:** Sentiment metrics reflect customers who opted to leave reviews, which historically skews toward polarized experiences.
* **Dataset Period:** Reflects Olist marketplace data from 2016 to 2018.

---

## **Next Steps**
* **Decompose the fulfillment pipeline:** Split total delivery time into merchant handling time (purchase approval to carrier pickup) and carrier transit time (pickup to customer delivery). This pinpoints whether late deliveries stem from slow seller dispatch or carrier transit bottlenecks.
* **Product category & bulk analysis:** Merge item weight, volume, and product categories to check if specific oversize goods (like furniture or large appliances) disproportionately cause the 7+ day severe delay cluster.
* **Carrier partner benchmarking:** If third-party logistics provider identifiers are available, benchmark individual carrier performance along the highest-breach lanes (e.g., Southeast to Northeast and Rio de Janeiro) to identify underperforming contracts.

---

---

---

## 📁 Repository Structure & Reproduction

```text
olist-logistics-sla-diagnostic/
├── data/
│   └── (Olist CSV tables: orders, items, reviews, customers, sellers)
├── notebooks/
│   └── Olist_Logistics_SLA_Diagnostic.ipynb
├── requirements.txt
└── README.md
```
## 👤 Author

## 👤 Author

- **Mohommed Rasheed**  
- **LinkedIn:** [![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mohommed-rasheed-analyst)

*Feedbacks, suggestions, and contributions are always welcome! Feel free to reach out.*
