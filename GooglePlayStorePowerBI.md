# 📱 App Insights Unlocked – Google Play Store Analysis (Power BI)

An end-to-end **Power BI** project analysing **10,000+ Google Play Store apps** and **29,000+ user reviews** to find what drives app success – ratings, installs, pricing, size, update behaviour and user sentiment.

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/Power%20Query-Data%20Cleaning-blue)
![DAX](https://img.shields.io/badge/DAX-Measures-orange)

---

## 📌 Table of Contents
- [Problem Statement](#-problem-statement)
- [Dataset](#-dataset)
- [Tools Used](#-tools-used)
- [Data Cleaning](#-data-cleaning-power-query)
- [Data Model](#-data-model)
- [Dashboard Pages](#-dashboard-pages)
- [Questions & Answers](#-questions--answers)
- [Key Insights](#-key-insights)
- [Recommendations](#-recommendations)
- [How to Use](#-how-to-use)
- [Repository Structure](#-repository-structure)

---

## 🎯 Problem Statement
A mobile-app company acquired Google Play Store data and wants to know:
1. What are the key factors behind an app's success on the Google Play Store?
2. How can the company use these factors to improve its app offerings?
3. What do user preferences and behaviour look like?

The analysis focuses on **high-rated apps, popular categories, and the impact of size and price on reviews and installs**.

---

## 📂 Dataset
Source: [Kaggle – Google Play Store Apps](https://www.kaggle.com/datasets/lava18/google-play-store-apps)

| File | Rows (raw) | Description |
|---|---|---|
| `googleplaystore.csv` | 10,841 | App, Category, Rating, Reviews, Size, Installs, Type, Price, Content Rating, Genres, Last Updated, Current Ver, Android Ver |
| `googleplaystore_user_reviews.csv` | 64,295 | App, Translated_Review, Sentiment, Sentiment_Polarity, Sentiment_Subjectivity |

---

## 🛠 Tools Used
- **Power BI Desktop** – report & visuals
- **Power Query (M)** – data cleaning & transformation
- **DAX** – measures, calculated columns, rankings, correlation
- **Data modelling** – bridge table & relationships

---

## 🧹 Data Cleaning (Power Query)
Light, rule-based cleaning following the case-study guidelines – **only exact duplicate rows were removed** (apps that appear more than once with different values were kept).

| Guideline | What was done |
|---|---|
| **Handle missing values** | `"NaN"` text → blank. Ratings (1,465) and "Varies with device" sizes (1,526) left blank – **not imputed**. 1 missing Type set to **Free** (price = 0). 1 corrupted row (values shifted, Category = "1.9") removed. Empty reviews removed. |
| **Convert data types** | Installs: removed `+` and `,` → whole number. Price: removed `$` → decimal. Reviews & Rating → numbers. Size → new `Size_MB` column (k ÷ 1024). |
| **Remove duplicates** | 483 exact duplicate app rows removed → **10,357 rows**. Reviews: exact duplicates removed → **29,692 reviews**. |
| **Standardize text** | Trim & clean all text. Category `ART_AND_DESIGN` → `Art & Design`. Primary genre extracted. |
| **Date formatting** | `Last Updated` → Date (en-US locale). |

**Helper columns:** `Size_MB`, `Bucket_Size`, `Installs_Bucket` (+ sort order), `Price_Bucket`, `Rating_Bucket`, `Month`, `MonthNum`, `Year`, `Has_Rating`.

**Extra query – `Update_Gaps`:** distinct (App, Last Updated) pairs, sorted, self-merged on index to get each app's previous update date → `Days_Between_Updates` (used for Medium Q6).

---

## 🔗 Data Model
```
googleplaystore (10,357 rows)  ──many-to-one (both)──►  Bridge App  ◄──many-to-one──  googleplaystore_user_reviews (29,692 rows)
Update_Gaps (stand-alone)
```
- **Bridge App** = distinct list of app names from both tables, so review sentiment can be filtered by app rating.
- Calculated columns: `App Rating`, `App Rating Group` (High 4.5+ / Middle / Low < 4.0 / Not rated), `Review Theme` (keyword-based).

---

## 📊 Dashboard Pages
| Page | Content |
|---|---|
| **Overview** | KPIs (avg rating, total apps, installs, categories, free %), top 5 installed apps, content ratings, top 5 categories by installs, free vs paid |
| **Category & genre** | Apps rated 4.0+, top categories by rating, avg size by category, genres with most 1M+ install apps, genre ratings |
| **ContentRating** | Rating distribution & average/median rating by content rating, free vs paid content mix |
| **Price & Size** | Size distribution, price vs rating, size vs installs, rating by install range, correlations |
| **Update Trend** | Apps updated in 2018, time between updates, yearly & monthly update trends |
| **Reviews & sentiment** | Sentiment KPIs, sentiment by rating band, high vs low rated apps, review themes, review text |
| **Top Rated Apps** | Top 10 apps rated 5.0 vs their reviews & installs |
| **App Details** *(drill-through)* | Right-click any app → full details and its reviews |

<!-- To show screenshots: upload images to an "images" folder and add lines like:
![Overview](images/overview.png)
-->

> Download `PowerBiGooglePlayStore.pbix` and open it in Power BI Desktop to explore the interactive dashboard.

---

## ❓ Questions & Answers

### Basic
| # | Question | Answer |
|---|---|---|
| 1 | Average rating of apps | **4.19** (8,892 rated records) |
| 2 | Unique categories | **33** |
| 3 | Distribution of app sizes | 0–10MB **3,995** · 10–25MB 2,211 · 25–50MB 1,601 · 50–100MB 1,024 · Varies 1,526 (median 13 MB, avg 21.29 MB) |
| 4 | Free vs paid apps | Free **9,592 (92.61%)** · Paid **765 (7.39%)** |
| 5 | Most common content rating | **Everyone – 8,382 (80.93%)** |
| 6 | Top 5 most installed apps | **Facebook, WhatsApp Messenger, Instagram, Messenger, Subway Surfers** – all 1B+ installs (20 apps tie at 1B+; ties broken by reviews) |
| 7 | Apps rated 4.0 and above | **6,947 (78.13%** of rated apps) |
| 8 | Avg reviews – free vs paid | Free **437,328** · Paid **11,901** (~37×) |
| 9 | Avg app size per category | Largest **Game 44.13 MB** · Smallest **Tools 8.77 MB** |
| 10 | Apps last updated in 2018 | **6,934 (66.95%)** |

### Medium
| # | Question | Answer |
|---|---|---|
| 1 | Correlation installs vs rating | **r = 0.05** – practically no relationship |
| 2 | Highest-rated categories | **Events 4.44**, Education 4.38, Art & Design 4.36 (lowest: Dating 3.97) |
| 3 | Price vs rating (paid apps) | 72 price points, **r = −0.11**; $0.99–$3.99 ≈ 4.3, $100+ apps **3.87** |
| 4 | Rating distribution by content rating | Medians 4.2–4.3; **Mature 17+ lowest** (4.12), Everyone 10+ / Teen highest (4.25 / 4.24) |
| 5 | Genres with most apps > 1M installs | **Tools 187**, Action 182, Photography 161, Communication 146, Productivity 124 |
| 6 | Average time between updates | **125 days** (median 17.5) – based on 42 apps with more than one update date |
| 7 | Impact of size on installs | Avg installs rise from **1.2M** (0–10MB) to **27.1M** (50–100MB); r = 0.17 (weak positive) |
| 8 | Most-reviewed apps & ratings | **Facebook 78.2M (4.1)**, WhatsApp 69.1M (4.4), Instagram 66.6M (4.5), Messenger 56.6M (4.0), Clash of Clans 44.9M (4.6) |
| 9 | Content rating – free vs paid | Paid apps more family-focused (**86.54% Everyone** vs 80.48%); free apps have more Teen/Mature content |
| 10 | Top 5 categories by installs | **Game 31.54B**, Communication 24.15B, Social 12.51B, Productivity 12.46B, Tools 11.45B (**62.83%** of all installs) |

### Advanced
| # | Question | Answer |
|---|---|---|
| 1 | Top 10 highest-rated apps vs reviews & installs | **271 apps rated 5.0**, but median only **4 reviews / 100 installs** vs 4,715 reviews / 500K installs for a typical rated app – perfect ratings come from tiny user bases |
| 2 | Update trend over time | Strong growth: 789 (2016) → 1,826 (2017) → **6,934 (2018)**; updates rise towards year-end; peak **July 2018 (2,653)** |
| 3 | Rating by install bins | Dip at **1K–10K (4.03)**, then rises to **10M–100M (4.38)** |
| 4 | Sentiment – high vs low rated apps | High-rated (4.5+): **70.55% positive**; Low-rated (<4.0): **27.83% negative**. Praise: enjoyment, ease of use. Complaints: **bugs/crashes (16.7% vs 8.3%)**, updates |
| 5 | Genre vs rating (≥30 apps) | Higher: **Events 4.44**, Puzzle, Art & Design, Books & Reference. Lower: **Educational 3.87**, Dating, Tools, Maps & Navigation |

---

## 💡 Key Insights
- **Free apps dominate** – 92.6% of apps are free and they receive ~37× more reviews than paid apps.
- **Popularity ≠ perfect rating** – installs and rating are almost uncorrelated (r = 0.05); 5.0-rated apps have very few users.
- **Games and communication apps drive installs** – Game and Communication alone account for ~38% of all installs.
- **Bigger apps get more installs** – feature-rich apps (50–100MB) average 27M installs vs 1.2M for small apps.
- **Price hurts ratings slightly** – the most expensive apps ($50+) have the lowest ratings.
- **Stability matters** – low-rated apps get twice as many bug/crash complaints in reviews.
- **Apps are updated often recently** – 67% of apps were last updated in 2018.

---

## ✅ Recommendations
1. **Focus on free apps with in-app monetisation** – the free model reaches far more users.
2. **Invest in Game, Communication, Productivity and Tools** – the highest-install categories.
3. **Prioritise stability and quality updates** – bugs, crashes and broken updates are the main drivers of negative reviews.
4. **Limit intrusive ads and paywalls** – a recurring complaint in negative reviews.
5. **Keep pricing low** ($0.99–$3.99) for paid apps – these get the best ratings.
6. **Target the "Everyone" audience** – 81% of apps and the widest reach.

---

## ▶ How to Use
1. Download/clone this repository.
2. Open `PowerBiGooglePlayStore.pbix` in **Power BI Desktop**.
3. If data refresh fails, update the file path: **Home → Transform data → Data source settings → Change Source** and point to the two CSV files.
4. Use the **Category** and **Type** slicers to explore; right-click any app → **Drill through → App Details**.

---

## 📁 Repository Structure
```
├── PowerBiGooglePlayStore.pbix          # Power BI report
├── googleplaystore.csv                  # Apps dataset
├── googleplaystore_user_reviews.csv     # Reviews dataset
└── README.md
```

---

## 👤 Author
**Manisha Mali** – Data Analyst
[LinkedIn](https://www.linkedin.com/in/manisha-mali-970b44129)

⭐ If you found this project useful, please give it a star!
