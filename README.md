# 🏛️ White House Visitor Logs Analysis

**Scheduling intelligence for school trip planners — identifying the least crowded days, times, and booking windows for student visits to the White House using publicly available WAVES access records (January–May 2022).**

> DSC 640 — Data Visualization | Bellevue University | By Matthew Rice

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Audience and Purpose](#audience-and-purpose)
- [Dataset](#dataset)
- [Data Preparation](#data-preparation)
- [Visualizations](#visualizations)
- [Key Recommendations](#key-recommendations)
- [Assumptions and Limitations](#assumptions-and-limitations)
- [Ethical Considerations](#ethical-considerations)
- [Repository Structure](#repository-structure)
- [Requirements](#requirements)
- [How to Run](#how-to-run)
- [Author](#author)

---

## Project Overview

This project analyzes White House visitor access records to help school administrators make smarter scheduling decisions for student field trips. Rather than treating the data as a transparency or journalism exercise, the analysis is framed entirely around **operational decision support** — surfacing patterns in visit volume, cancellation rates, arrival timing, and booking lead times that allow planners to reduce the risk of disruption and crowding.

**Central question:** Based on historical White House visit traffic, when is the best time to schedule a school group visit — and how far in advance should it be booked?

---

## Audience and Purpose

**Primary audience:** School administrators and trip coordinators responsible for organizing student visits to the White House. This audience is time-constrained, operationally focused, and unlikely to have statistical training.

**Purpose:** Decision support — translating raw visit log data into a small set of clear, actionable scheduling recommendations.

**Deliverable:** A single-page infographic combining six visualizations that together tell a cohesive story, designed for quick scanning while preserving the evidence behind each recommendation.

---

## Dataset

**Source:** White House WAVES (Worker and Visitor Entry System) Access Records  
**Publisher:** WhiteHouse.gov (publicly released)  
**Coverage:** January–May 2022 (5 monthly CSV files)  
**Scope:** Filtered to weekday (Monday–Friday) visits only, consistent with school-year travel patterns

### Key Fields

| Field | Description |
|---|---|
| `APPT_MADE_DATE` | Date the appointment was created |
| `APPT_START_DATE` | Scheduled appointment start date |
| `APPT_END_DATE` | Scheduled appointment end date |
| `APPT_CANCEL_DATE` | Date of cancellation (if applicable) |
| `TOA` | Time of arrival (when recorded) |
| `TOD` | Time of departure |
| `TOTAL_PEOPLE` | Number of people in the visiting group |

### Engineered Features

| Feature | Description |
|---|---|
| `visit_start` | TOA when available; falls back to `APPT_START_DATE` at noon |
| `visit_date` | Date portion of `visit_start` |
| `visit_dow` | Day of week |
| `visit_hour` | Hour of arrival |
| `canceled` | Boolean flag — True if `APPT_CANCEL_DATE` is populated |
| `lead_days` | Days between appointment creation and scheduled visit |
| `time_block` | Categorical time-of-day bucket (Early / Late morning / Midday / Afternoon / Late day) |

---

## Data Preparation

Five monthly WAVES CSV files are loaded and concatenated into a single DataFrame. All date and time fields are parsed to datetime. A `visit_start` timestamp is constructed using actual arrival time (TOA) where available, falling back to the appointment start date at noon when TOA is missing. Records are then filtered to weekday visits only, lead time is calculated, and time-of-day blocks are assigned. The cleaned dataset is exported to CSV before visualization.

---

## Visualizations

The notebook produces six charts, each designed to answer a specific scheduling question:

| # | Chart Type | Title / Question Answered |
|---|---|---|
| 1 | Line chart | *How busy is White House traffic week by week?* — Weekly visit volume Jan–May 2022 |
| 2 | Bar chart | *Which arrival hours are busiest?* — Visit counts by hour of day (Mon–Fri) |
| 3 | Stacked bar | *Best "quiet windows" by weekday* — Visit volume by weekday × time block |
| 4 | Bar chart | *Which weekdays have more cancellations?* — Cancellation rate by weekday |
| 5 | Scatter plot | *How far ahead are visits booked?* — Lead days vs. arrival hour, sized by group size |
| 6 | Step chart | *Cumulative share of arrivals by hour* — Find the least crowded arrival window |

All charts are exported as high-resolution PNG files. Chart titles are written as plain-language questions rather than technical labels, consistent with the non-technical target audience.

---

## Key Recommendations

Based on the analysis of January–May 2022 WAVES records:

> **Visit midweek (Tuesday or Wednesday)** — these days show historically lower cancellation rates and more manageable visit volumes than Monday or Friday.
>
> **Arrive earlier in the day** — the majority of arrivals cluster in the late morning and midday windows; early arrivals (before 10am) face the least competition for access.
>
> **Book several weeks in advance** — the scatter plot of lead time vs. arrival hour shows most visits are planned well ahead; last-minute bookings correlate with higher uncertainty and more compressed arrival windows.

These are risk-reducing recommendations based on historical patterns, not guarantees. Conditions may vary by season, administration scheduling, or special events not captured in the data.

---

## Assumptions and Limitations

- Analysis covers only January–May 2022; patterns may differ in other months or years
- TOA (actual arrival time) is not recorded for all visits; noon is used as a fallback for `APPT_START_DATE`-only records, which may slightly distort hourly analysis
- Nominal dollar values — wait, this isn't the childcare project. Filtering to weekday-only visits assumes school trips do not occur on weekends, which is a reasonable but not universal assumption
- Historical crowding and cancellation patterns are assumed to be reasonably stable over time; White House scheduling may shift significantly based on administration priorities or security events
- No information on visit *purpose* (tours, meetings, events) is used; all records are treated equivalently

---

## Ethical Considerations

The WAVES records are publicly released by the White House and aggregated at the visit level with no sensitive personal context. Analysis is conducted at an aggregate level (by hour, weekday, and week) — no individual visitor information is surfaced in any visualization.

Transformations including weekday filtering, time-block binning, and lead-time calculation are fully documented and designed to reduce noise rather than introduce bias. Visualizations use conservative language and frame conclusions as risk-reducing guidance rather than causal claims — for example, noting that Tuesdays are *historically associated* with lower cancellation rates, not that they *cause* fewer cancellations.

**Data source:** [White House Visitor Access Records](https://www.whitehouse.gov/briefing-room/disclosures/visitor-logs/)

---

## Repository Structure

```
White-House-Visitor-Logs/
│
├── notebooks/
│   └── DSC640_Week4.ipynb     # Full data pipeline and visualization notebook
│
└── README.md
```

> **Note:** The five monthly WAVES CSV files (`2022.01` through `2022.05`) are not included in this repository. Download them from the White House visitor logs disclosure page and update the file paths in the notebook before running. See [How to Run](#how-to-run) below.

---

## Requirements

```
pandas
matplotlib
numpy
```

Install dependencies:
```bash
pip install pandas matplotlib numpy
```

No external APIs or additional libraries are required.

---

## How to Run

1. Clone the repository:
```bash
git clone https://github.com/Bizarroxela/White-House-Visitor-Logs.git
cd White-House-Visitor-Logs
```

2. Download the WAVES access records from [whitehouse.gov](https://www.whitehouse.gov/briefing-room/disclosures/visitor-logs/) and update the file paths in the notebook:
```python
files = [
    "/your/path/2022.01_WAVES-ACCESS-RECORDS.csv",
    "/your/path/2022.02_WAVES-ACCESS-RECORDS.csv",
    "/your/path/2022.03_WAVES-ACCESS-RECORDS-.csv",
    "/your/path/2022.04_WAVES-ACCESS-RECORDS.csv",
    "/your/path/2022.05-WAVES-ACCESS-RECORDS.csv",
]
```

3. Update the output paths for saved figures and the cleaned CSV:
```python
clean_path = "/your/path/wh_visits_2022_01_to_05_cleaned.csv"
```

4. Launch the notebook:
```bash
jupyter notebook notebooks/DSC640_Week4.ipynb
```

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Bizarroxela/White-House-Visitor-Logs/blob/main/notebooks/DSC640_Week4.ipynb)

---

## Author

**Matthew Rice** — [@Bizarroxela](https://github.com/Bizarroxela)

*Part of an applied data science portfolio covering data visualization, policy analytics, sports analytics, healthcare analytics, and machine learning.*
