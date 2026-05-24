# 🏬 JCPenney Retail Decline — Customer & Product Analytics

A strategic business intelligence project investigating *why* JCPenney lost market relevance — using Python, customer segmentation, sentiment analysis, and product clustering to turn raw retail data into executive-level findings.

---

## 🎯 The Business Problem

JCPenney was once one of the largest American mid-market retailers:

- **2,000+ stores** across the United States
- **650+ unique brands** and over 1,000 product categories
- A dominant presence in everyday fashion and home goods

Then came a slow, compounding collapse. The question this project answers: **does the customer and product data confirm the reasons — and what does it reveal beyond the headlines?**

The data covers **7,982 products**, **39,063 reviews**, and **5,000 customer profiles**.

---

## 📊 Datasets

| File | Records | Key Fields |
|------|---------|------------|
| `products.csv` | 7,982 | Product name, price, average score |
| `reviews.csv` | 39,063 | Username, star rating, review text |
| `users.csv` | 5,000 | Username, date of birth, US state |
| `jcpenney_products.json` | 7,982 | List price, sale price, brand, category, rating, reviews |
| `jcpenney_reviewers.json` | 5,000 | Username, DOB, state, products reviewed |

> Sample files (100 rows each) are available in `data/samples/` — full datasets not committed due to size.

---

## What I Actually Did

### Data Cleaning & Preparation
Audited all five files using Python (Pandas):

- No duplicates found across any dataset — clean uniqueness throughout
- Null values in `Price` and `list_price`/`sale_price` — filled using **median imputation**
- Rows with null `SKU` removed — a product without a key identifier has no analytical value
- **Outliers removed:** raw data contained negative prices (−$65) and unrealistically high values ($17,122) — filtered to 0–$200 range
- Score = 0 reviews excluded — system defaults with no business meaning
- DOB converted from string to datetime for accurate age calculation

### Feature Engineering

- `Age` — derived from DOB for demographic analysis
- `Age_Group` — binned into: `<25`, `25–34`, `35–44`, `45–54`, `55–64`, `65+`
- `discount_pct` — `(list_price − sale_price) / list_price × 100`
- `Sentiment` — TextBlob polarity score per review (−1 to +1)
- `Reviewed_Count` — number of products each customer engaged with
- `F_Score` / `M_Score` — frequency and satisfaction proxies for RFM segmentation

### Exploratory Data Analysis (EDA)

- **Price distribution** → right-skewed, mass under $75 — confirmed mid-market positioning
- **Score distributions** → 79.1% of reviews score 1–2 stars — not a quality blip, a structural crisis
- **Age profiling** → mean age 47.8, only 24.6% under 35 — generational pipeline is depleted
- **Geographic spread** → customers across 50 US states and territories
- **Discount analysis** → 72.3% of products permanently discounted at 42.4% average depth

### Sentiment Analysis
Applied TextBlob to 39,063 customer reviews:

- Most reviews cluster in **0.2–0.4 polarity** — mildly positive language, no brand enthusiasm
- Negative tail driven by: fabric quality, sizing inconsistency, return frustrations
- Top negative keywords: *return* (1,806), *disappointed* (797), *cheap* (412), *shrink* (383)
- Sentiment confirms star rating trends but reveals *why* — not just *how bad*

### Customer Segmentation (RFM Proxy)
Constructed RFM-style segments using review frequency (F) and average satisfaction score (M):

| Segment | Count | Avg Score | Interpretation |
|---------|-------|-----------|----------------|
| Lost Customers | 2,035 | 1.29★ | Active but deeply dissatisfied |
| Engaged but Dissatisfied | 1,677 | 1.40★ | Highest frequency, lowest satisfaction — most dangerous |
| Potential Loyalists | 630 | 2.38★ | Recoverable with targeted intervention |
| At-Risk Customers | 627 | 1.36★ | Trending toward disengagement |
| Satisfied but Dormant | 22 | 3.53★ | Happy but silent — reactivation opportunity |
| **Loyal Advocates** | **2** | 3.27★ | **The brand's rarest asset** |

### K-Means Product Clustering (k=4)
Grouped 7,982 products by price, discount depth, and rating:

| Cluster | Products | Avg List Price | Avg Discount | Avg Rating | Label |
|---------|----------|----------------|--------------|------------|-------|
| 0 | 3,018 | $98 | 36% | 2.39★ | Mid-Market Core |
| 1 | 110 | $2,409 | 29% | 3.03★ | Premium Niche |
| 3 | 2,683 | $90 | 50% | **3.67★** | Value Champions |

**Key insight:** Value Champions achieve the highest satisfaction *despite* the heaviest discounts — confirming quality-to-price ratio matters more than price alone.

### Correlation Analysis
Heatmap across pricing and rating variables:

- `list_price` ↔ `sale_price`: **r = 0.97** — pricing is structurally consistent (higher list → higher sale)
- `list_price` ↔ `average_product_rating`: **r ≈ 0.004** — expensive products are NOT rated better
- `average_product_rating` ↔ `total_number_reviews`: **r ≈ 0.007** — review volume does not predict satisfaction

This confirms the core finding: **customer satisfaction is driven by product quality, not price point.**

---

## Key Findings

### 1. A Satisfaction Crisis, Not a Pricing Problem
- **79.1%** of all customer reviews score 1 or 2 stars
- **43%** of products average below 3.0★
- Mean sentiment polarity sits at a modest 0.25 — mildly positive language masking deep dissatisfaction
- Only **10.6%** of reviews score 4 or 5 stars — an 8:1 dissatisfaction ratio

### 2. Structural Discount Dependency
- **72.3%** of all products are permanently marked down
- Average markdown depth: **42.4%**
- **47.3%** of the catalogue is discounted by more than 40%
- Discounting does NOT improve ratings (correlation ≈ 0) — margin is being destroyed without satisfaction recovery

### 3. Generational Brand Abandonment
- Mean customer age: **47.8 years**
- Only **24.6%** of customers are under 35
- **56.4%** are aged 45 and above
- Younger customers have identical low engagement levels when present — the problem is acquisition, not activation

### 4. Loyalty Collapse
- From 5,000 customers: only **2 Loyal Advocates** identified
- **4,725 customers** show high dissatisfaction risk signals
- **971 registered users (19.4%)** have never reviewed a single product — silent disengagement

### 5. Brand Portfolio Has Hidden Strengths
- **Levi** (3.14★, 34.5% discount), **Adidas** (3.08★, 21.1%), **Nike** (2.98★, 18.8%) are the best-performing brands
- These national brands carry the lowest discount dependency — yet are buried in a sea of sub-3-star private-label products
- Top brands by rating (T-Fal, Q-T Intimates, Bialetti) reveal niche categories with genuine customer satisfaction

---

## 💡 Recommendations

**Priority 1 — Immediate**
- Emergency product quality audit: discontinue SKUs with persistent <2★ ratings (20+ reviews)
- Break the permanent discount cycle: reduce discounted products from 72% to <35% over 18 months

**Priority 2 — 0–6 Months**
- Amplify national brands: lead with Levi, Nike, Adidas in-store and online — rebuild quality perception fast
- Activate the 630 Potential Loyalists: targeted emails, satisfaction recovery offers, free return incentives

**Priority 3 — 6–18 Months**
- Under-35 acquisition strategy: curated sub-brand, TikTok commerce integration, sustainability product lines
- Redesign loyalty around product experience (tailoring, early access) not points programmes

**Priority 4 — Strategic (12–24 Months)**
- Digital commerce transformation: rebuild catalogue around lifestyle occasions, not product taxonomy
- Real-time satisfaction monitoring dashboard: auto-flag any SKU falling below 3.0★ after 15+ reviews

---

## 🌱 Why This Project Matters

JCPenney's story is a textbook case of how retail brands fail when they optimise for short-term transaction volume instead of long-term customer experience. This project demonstrates:

- How customer and product data can diagnose structural business decline — not just describe it
- The difference between correlation and causation in retail analytics (discounting ≠ satisfaction)
- How demographic data reveals long-term brand trajectory, not just current customer profile
- Why RFM segmentation reveals *who* to rescue before transactional churn data is even available
- How K-Means clustering uncovers commercially distinct product groups that pricing analysis alone misses

---

## 📓 View the Full Analysis

Click [`notebooks/jcpenney_analytics.ipynb`](notebooks/jcpenney_analytics.ipynb) to explore the complete pipeline — data cleaning, EDA, sentiment analysis, RFM segmentation, and K-Means clustering with all visualisations rendered inline.

📊 Interactive HTML report: [`reports/jcpenney_analytics_report.html`](reports/jcpenney_analytics_report.html)

---

## 📁 Repository Structure

```
├── data/
│   └── samples/
│       ├── products_sample.csv
│       ├── reviews_sample.csv
│       ├── users_sample.csv
│       ├── jcpenney_products_sample.json
│       └── jcpenney_reviewers_sample.json
├── images/                             ← All visualisation outputs
├── notebooks/
│   └── jcpenney_analytics.ipynb        ← Full analysis pipeline
├── reports/
│   └── jcpenney_analytics_report.html  ← Interactive executive report
└── README.md
```

---

## Reproducing the Analysis

```bash
git clone https://github.com/Imran3285/jcpenney-retail-analytics.git
cd jcpenney-retail-analytics
pip install pandas numpy matplotlib seaborn scikit-learn textblob jupyter
jupyter notebook
```

Open `notebooks/jcpenney_analytics.ipynb` and run all cells top to bottom.

> **Note:** Sample data (100 rows) is included for reproducibility. Full dataset results referenced throughout the README and report reflect analysis on the complete 7,982-product / 39,063-review dataset.

---

## Stack

**Python** · **Pandas** · **NumPy** · **Matplotlib** · **Seaborn** · **Scikit-learn** · **TextBlob** · **Jupyter Notebook**

---

## Author

**Muhammad Imran**  
