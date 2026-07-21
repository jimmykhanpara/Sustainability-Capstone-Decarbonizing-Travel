<div align="center">

# 🌍 GreenTravel Intelligence Challenge
### Decarbonizing Business Travel with Process Mining + Predictive ML

*Uncovering hidden emission patterns in corporate travel — and predicting high-carbon trips before they're ever booked.*

![Python](https://img.shields.io/badge/Python-ML_Pipeline-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Celonis](https://img.shields.io/badge/Celonis-Process_Mining-1FC8DB?style=for-the-badge)
![XGBoost](https://img.shields.io/badge/XGBoost-Ensemble-EB6231?style=for-the-badge)
![LightGBM](https://img.shields.io/badge/LightGBM-Ensemble-2C9E45?style=for-the-badge)
![CatBoost](https://img.shields.io/badge/CatBoost-Ensemble-FFCC00?style=for-the-badge&logoColor=black)
![Status](https://img.shields.io/badge/Status-Submitted-2ea44f?style=for-the-badge)

</div>

---

## 📖 Table of Contents
- [Mission Briefing](#-mission-briefing)
- [Project Architecture](#-project-architecture)
- [✨ Feature Highlights](#-feature-highlights)
- [Part 1 — Process Intelligence & Dashboarding](#-part-1--process-intelligence--dashboarding-celonis)
- [Part 2 — High-Carbon Trip Prediction](#-part-2--high-carbon-trip-prediction-ml)
- [Model Performance](#-model-performance)
- [Repository Structure](#-repository-structure)
- [Tech Stack](#️-tech-stack)
- [License](#-license)

---

## 🛰️ Mission Briefing

> *"The greenest trip is the one you optimise before it happens."*

Business travel is one of the hardest categories to decarbonize — a single transatlantic business-class flight emits **2,000+ kg of CO2e**, roughly equal to driving a car for six months. As part of Celonis' Road to Net Zero by 2030, this project attacks the problem from **two directions simultaneously**:

<table>
<tr>
<td width="50%" valign="top">

### 🔎 Part 1 — Diagnose
**Process Mining & Dashboarding**
Mine ~65,000 real travel events to expose *where, why,* and *how* emissions leak into the booking → approval → travel → reimbursement pipeline.

</td>
<td width="50%" valign="top">

### 🎯 Part 2 — Predict
**Machine Learning Classification**
Build an ensemble model that flags a trip as **high-carbon before it's booked** — enabling real-time intervention.

</td>
</tr>
</table>

---

## 🏗️ Project Architecture

```
                    ┌──────────────────────────┐
                    │   Raw Travel Event Data   │
                    │  (public + private sets)  │
                    └────────────┬──────────────┘
                                 │
                 ┌───────────────┴───────────────┐
                 ▼                                ▼
     ┌───────────────────────┐       ┌───────────────────────────┐
     │   PART 1 — CELONIS     │       │   PART 2 — ML PIPELINE     │
     │  Process & Variant     │       │  Feature Engineering →     │
     │  Explorer + Dashboards │       │  LGBM + CatBoost + XGBoost │
     │  (Emissions, Benchmark,│       │  → Stratified 5-Fold CV →  │
     │   Drilldown)           │       │  Weighted Ensemble         │
     └───────────┬────────────┘       └─────────────┬─────────────┘
                 ▼                                   ▼
     Executive Pitch Deck                  submission.csv (probabilities)
     (7–10 slides)                          ROC-AUC ≈ 0.9998
```

---

## ✨ Feature Highlights

<table>
<tr><td>🛫</td><td><b>65K+ trip records</b> integrated across trip data, event logs, and contextual attribute tables via a shared <code>TripID</code> key</td></tr>
<tr><td>🧭</td><td><b>Process & Variant Explorer</b> built in Celonis to surface booking → approval → travel → reimbursement bottlenecks and out-of-policy variants</td></tr>
<tr><td>📊</td><td><b>3-tab interactive dashboard</b> — Emissions Overview, Regional Benchmarking, Emissions Drilldown — covering total CO2, cost, and mode-of-transport splits</td></tr>
<tr><td>🧪</td><td><b>Leak-proof feature engineering</b> — all emission-derived columns (<code>TotalCO2e</code>, <code>Departure_CO2e</code>, etc.) excluded from model features to keep predictions honest</td></tr>
<tr><td>🌐</td><td><b>Frequency-encoded route intelligence</b> — <code>CityRoute_Freq</code> and <code>Route_Freq</code> emerged as the single strongest predictors of high-carbon trips</td></tr>
<tr><td>🤖</td><td><b>3-model gradient-boosted ensemble</b> (LightGBM + CatBoost + XGBoost) combined via weighted blending on out-of-fold predictions</td></tr>
<tr><td>🎯</td><td><b>Near-perfect discrimination</b> — ensemble ROC-AUC of <b>0.9999</b> on 5-fold stratified cross-validation</td></tr>
<tr><td>📈</td><td><b>Threshold-optimized classification</b> — probability cutoff tuned beyond the default 0.5 to balance precision and recall</td></tr>
<tr><td>💡</td><td><b>Quantified policy recommendations</b> — every insight tied to a $ and kg CO2e impact, not vague directives</td></tr>
</table>

---

## 🔎 Part 1 — Process Intelligence & Dashboarding (Celonis)

Business travel events were modeled as a connected process graph inside Celonis, linking three data layers on `TripID`:

| Layer | File | Purpose |
|---|---|---|
| **Trip Data** | `public_trip_data.csv` | Locations, transport mode, cost, and pre-calculated emissions per trip |
| **Event Log** | `public_trip_event_log.csv` | Timestamped activity trail: Booking → Approval → Departure → Reimbursement |
| **Event Attributes** | `public_trip_event_attributes.csv` | Contextual reasons for policy exceptions, cancellations, and hotel changes |

**Dashboard Tabs (mandatory naming):**
1. **Emissions Overview** — high-level KPIs: Total CO2, Total Costs
2. **Benchmarking** — emissions by region and Business Unit
3. **Emissions Drilldown** — actionable breakdown (e.g., Flight vs. Train contribution)

**Key Business Questions Addressed:**
- 🌍 What does the current carbon footprint look like in total emissions?
- 🌐 How do travel processes compare across North America, Europe, and Asia?
- 🔥 Which region, Business Unit, or travel purpose drives disproportionate emissions?
- ⚙️ What inefficiencies and pain points exist in the booking/approval process?
- 📋 What specific, quantified policy updates would reduce emissions?

Full findings and the executive pitch are captured in **`Celonis_Final_Updated.pptx`**.

---

## 🎯 Part 2 — High-Carbon Trip Prediction (ML)

A binary classification pipeline predicts `HighCarbon` (0/1) for ~22,000 unseen trips, using only information available **before** a trip is booked.

### Guardrails
To keep the task honest, all emission-derived columns were excluded from the feature set:
`Departure_CO2e` · `Return_CO2e` · `Hotel_CO2e` · `Spend_CO2e` · `TotalCO2e` · `HighCarbon`

### Top Predictive Signals

| Rank | Feature | Importance |
|---|---|---|
| 1 | `CityRoute_Freq` | 0.239 |
| 2 | `Route_Freq` | 0.129 |
| 3 | `DepartureLocationCity_Freq` | 0.110 |
| 4 | `RouteFrequency` | 0.088 |
| 5 | `ShippingType` | 0.084 |
| 6 | `ShippingTypeDescription` | 0.072 |
| 7 | `DepartureLocationCountry_Freq` | 0.064 |
| 8 | `IsInternational` | 0.063 |

> Route familiarity and transport class dominate — *how well-traveled a route is* and *what class of travel it uses* are far stronger high-carbon signals than cost or hotel nights.

### Modeling Approach
1. **Feature Engineering** — frequency encodings for routes/cities, transport-mode flags, international-trip indicator, stay-length flags
2. **Cross-Validation** — Stratified 5-Fold to preserve class balance across folds
3. **Base Models** — `LGBMClassifier`, `CatBoostClassifier`, `XGBClassifier`, each tuned independently
4. **Ensembling** — weighted blend of out-of-fold predictions from all three models
5. **Threshold Tuning** — optimal probability cutoff selected to maximize F1 rather than defaulting to 0.5

---

## 📈 Model Performance

<table>
<tr>
<th>Model</th><th>ROC-AUC</th><th>F1</th><th>Precision</th><th>Recall</th>
</tr>
<tr><td>LightGBM</td><td>0.99985</td><td>0.98759</td><td>0.99083</td><td>0.98438</td></tr>
<tr><td>CatBoost</td><td>0.99985</td><td>0.98772</td><td>0.99215</td><td>0.98334</td></tr>
<tr><td>XGBoost</td><td>0.99984</td><td>0.98761</td><td>0.99100</td><td>0.98426</td></tr>
<tr><td><b>Ensemble (final)</b></td><td><b>0.99985</b></td><td><b>0.98853</b></td><td><b>0.99819</b></td><td><b>0.97905</b></td></tr>
</table>

**Confusion Matrix (Ensemble, threshold-tuned):**

|              | Predicted: Low | Predicted: High |
|---|---|---|
| **Actual: Low**  | 48,934 | 29 |
| **Actual: High** | 342 | 15,984 |

Final probability scores for the private test set are in **`submission.csv`**, formatted per **`sample_submission.csv`**.

---

## 📂 Repository Structure

```
greentravel-intelligence-challenge/
│
├── 📄 README.md
│
├── 📁 part1_process_intelligence/
│   ├── Celonis_Final_Updated.pptx           # Final pitch deck + dashboard screenshots
│   └── _FullName__Capstone.pptx             # Submission template used
│
├── 📁 part2_ml_prediction/
│   ├── Code_Submission.ipynb                # Feature engineering + ensemble model
│   ├── feature_importance.csv               # Ranked feature importances
│   ├── submission.csv                       # Final predicted probabilities
│   └── sample_submission.csv                # Required submission format
│
├── 📁 data/
│   ├── public_trip_data.csv                 # Training set (with emissions + label)
│   ├── public_trip_event_log.csv            # Public process event log
│   ├── public_trip_event_attributes.csv     # Public contextual attributes
│   ├── private_trip_data.csv                # Test set (features only, ~22K trips)
│   ├── private_trip_event_log.csv           # Private process event log
│   └── private_trip_event_attributes.csv    # Private contextual attributes
│
├── 📁 docs/
│   ├── Problem_Statements.pdf                                  # Overall Summer Projects brief
│   ├── Part2.pdf                                                # High-carbon prediction task spec
│   ├── README__Sustainability_Capstone_-_Decarbonizing_Travel.pdf
│   ├── Sustainability_Capstone_Outline__Decarbonizing_Travel.pdf
│   └── Sustainability_Data_Dictionary.xlsx   # Full column definitions
│
└── reports/
    └── dashboard_screenshots/                # PNG exports of Celonis dashboard tabs
```

---

## 🛠️ Tech Stack

<div align="left">

![Python](https://img.shields.io/badge/-Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/-Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![scikit--learn](https://img.shields.io/badge/-scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)
![XGBoost](https://img.shields.io/badge/-XGBoost-EB6231?style=for-the-badge)
![LightGBM](https://img.shields.io/badge/-LightGBM-2C9E45?style=for-the-badge)
![CatBoost](https://img.shields.io/badge/-CatBoost-FFCC00?style=for-the-badge&logoColor=black)
![Celonis](https://img.shields.io/badge/-Celonis-1FC8DB?style=for-the-badge)
![Jupyter](https://img.shields.io/badge/-Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white)

</div>

---

## 🌱 Strategic Recommendations

1. **Route-level intervention** — since route familiarity is the top emissions predictor, flag *first-time* or *low-frequency* international routes for automatic low-carbon alternative suggestions at booking time.
2. **Transport-class policy** — tighten approval thresholds for Business/First Class flights, the second-strongest driver behind route frequency.
3. **Real-time carbon scoring** — integrate the trained ensemble into the booking workflow to score trips *before* confirmation, not after.
4. **Regional focus** — direct the largest decarbonization investment toward the region/business unit combination identified as the emissions hotspot in the Benchmarking dashboard tab.

---

## 📄 License

Released under the [MIT License](LICENSE). Dataset and business scenario are fictional, provided for educational purposes as part of the Consulting & Analytics Club, IIT Guwahati × Celonis Summer Projects '26.

<div align="center">

**⭐ Star this repo if the ensemble's 0.9999 AUC impressed you as much as it impressed us.**

</div>
