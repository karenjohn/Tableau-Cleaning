# Tableau-Cleaning
Tableau Cleaning Fire Incidents in Cape Town
# Cape Town Fire Incidents: Data Cleaning and Dashboard

A data preparation and visual analytics project built on the City of Cape Town fire incidents open dataset. Raw incident records were cleaned in **Tableau Prep Builder**, exported as a clean dataset (Hyper extract and Excel copy), and then analysed in **Tableau Desktop** through four visualisations combined into one interactive dashboard.

The project was completed as a workshop for a Business Process Reports course at the University of Cape Town (BSc Computer Science and Business Computing).

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Tools Used](#tools-used)
3. [Dataset](#dataset)
4. [Repository Structure](#repository-structure)
5. [End to End Workflow](#end-to-end-workflow)
6. [Data Quality Problems and How They Were Fixed](#data-quality-problems-and-how-they-were-fixed)
7. [Calculated Fields Created](#calculated-fields-created)
8. [Visualisations](#visualisations)
9. [Dashboard](#dashboard)
10. [Key Findings](#key-findings)
11. [Limitations and Notes](#limitations-and-notes)
12. [Further Improvements](#further-improvements)
13. [How to Reproduce](#how-to-reproduce)
14. [Skills Demonstrated](#skills-demonstrated)
15. [Author](#author)

---

## Project Overview

Open data is rarely ready for analysis. The Cape Town fire incidents dataset contains thousands of inconsistent place names, placeholder values, invalid classifications and unusable time fields. This project documents the full journey from raw data to insight:

1. **Profile** the raw data and find what is wrong with it.
2. **Clean and transform** it in Tableau Prep using a documented, repeatable flow.
3. **Output** the cleaned data to a Hyper extract and an Excel file.
4. **Visualise** it in Tableau Desktop with four purpose built charts.
5. **Combine** the charts into one filterable dashboard that different audiences can use, from fire departments to insurers and researchers.

---

## Tools Used

| Tool | Purpose |
|---|---|
| Tableau Prep Builder | Data profiling, cleaning, transformation and output |
| Tableau Desktop | Calculated measures, visualisations and dashboard |
| Microsoft Excel | Source file and exported copy of the cleaned data |
| Mapbox and OpenStreetMap | Base map for the geographic visualisation |

---

## Dataset

| Property | Detail |
|---|---|
| Subject | Fire and emergency incidents recorded in Cape Town |
| Source file | `Fire_Incident.xlsx` |
| Original size | 98,752 records and 32 fields |
| Period covered | 2009 to 2016 (2016 contains only the first four days of January) |
| Key fields used | TOWN, CLASSIFICATION, FPA_SUSPEC (fire cause), TIME_OF_INCIDENT, Full Date, PRED_LSMGR (living standard measure), LATITUDE, LONGITUDE |

---

## Repository Structure

Adjust the names below to match the files you upload.

```
cape-town-fire-incidents/
├── README.md
├── data/
│   ├── Fire_Incident.xlsx              (raw source data)
│   ├── Fire Incidents Cleaned.hyper    (cleaned output from Tableau Prep)
│   └── Fire Incidents Cleaned.xlsx     (Excel copy of the cleaned output)
├── tableau_prep/
│   └── Fire Incidents Report.tfl       (the full Tableau Prep flow)
├── tableau_desktop/
│   └── Fire Incidents Analysis.twbx    (packaged workbook with dashboard)
├── report/
│   └── Fire Incidents Workshop Report.pdf
└── images/
    └── (screenshots referenced in this README)
```

---

## End to End Workflow

### Stage 1: Connect and profile the raw data (Tableau Prep)

1. Opened Tableau Prep Builder and connected to `Fire_Incident.xlsx` (about 100,000 rows).
2. Used the profile pane to inspect every field, looking at unique value counts, null counts and value distributions.
3. Identified the main problems: inconsistent town names, placeholder values in TOWN, spelling variants in the fire cause field, a time field with a meaningless placeholder date, a combined date field, and blank or invalid incident classifications.

### Stage 2: Clean the data (Tableau Prep)

Each cleaning task was placed in its own **named clean step**, so the flow reads like documentation. Original columns were left untouched wherever possible and new fields were created as duplicates or calculations.

| Order | Clean step | What it does |
|---|---|---|
| 1 | Correct Town Names | Groups spelling variants and decorated values in TOWN into single clean town names |
| 2 | Fire Causation clean up | Renames and groups fire cause values into clear categories |
| 3 | Full Date and LSM clean up | Splits the full date into Month, Day and Year, and groups LSM codes into descriptive bands |
| 4 | Create Time of Day Fields | Builds the Incident Hour and Time of Day calculated fields |
| 5 | Filter Invalid Classification | Removes records with a missing or invalid incident classification |

Details of each step are in the next section.

### Stage 3: Output the clean data (Tableau Prep)

1. Added output steps at the end of the flow.
2. Exported the cleaned dataset as a **Tableau Hyper extract (.hyper)** for fast use in Tableau Desktop.
3. Exported an **Excel copy (.xlsx)** of the same cleaned data so it can be opened and checked without Tableau.
4. Saved the whole flow as a `.tfl` file so every step can be reopened, reviewed and rerun.

### Stage 4: Build the analysis (Tableau Desktop)

1. Connected Tableau Desktop to the cleaned Hyper extract.
2. Created the **Number of Incidents** measure (a record count).
3. Built four visualisations, one per question (when, when and where within the day, why, and where geographically).
4. Added a map layer using Mapbox and OpenStreetMap for the geographic view.
5. Assembled the visualisations into the **Fire Incidents Analysis Dashboard**.
6. Added three filters (Year, Time of Day, Fire Causation) that apply to all four views.
7. Saved the work as a packaged workbook (`.twbx`) so the data travels with the file.

---

## Data Quality Problems and How They Were Fixed

### 1. Town names (TOWN column)

**Problem.** The same place was written many different ways, and some entries were not towns at all.

**Fix.** Used **Group Values by Manual Selection** in Tableau Prep to merge variants into one clean value. This reduced the TOWN field to **161 unique values**. Examples of groups created:

| Clean value | Variants merged into it |
|---|---|
| Cape Town | 50, Cape Btown, Cape Town, Cape Town (district East), Cc, City, City Of Cape Town |
| Khayelitsha | Khayelistha, Khayelithsa, Khayelitsha, Khayelitsha Site B, Khayelitsha Tygerberg, Makhaya Khayelitsha |
| Blaauwberg | Bb, Blaauwberg, Blaauwberg/cape Town |
| Mitchell's Plain | Mitchell Plain, Mitchell's Plain, Mitchells Plain / Tygerberg |
| Brackenfell | Brackenfell, Brackenfell (s) |
| Elsies River | Elsies, Elsies River |
| Kraaifontein | Kraaifontein, Kraaifontein/oostenberg |
| Melkbos | Melkbos, Melkbosstrand |
| Nomzamo | Nomzamo, Nomzamo Strand |
| Philippi | Philippi, Philippi East |
| Parklands | As Per Caller Parklands Area, Parklands |
| Null | 93, Not specified, Town 2 |



### 2. Fire cause (Fire Causation column)

**Problem.** Many cause values were equipment descriptions or spelling variants rather than real causes.

**Fix.** Used the replace function and group by spelling to standardise the field to **13 values**:

| Original values | Standardised to |
|---|---|
| Hose descriptions (for example 1 x 65mm and 1 x 45mm hose), CO2 and Dry Chemical Powder | Other |
| Incendiarism | Arson |
| Electrical short, Electrical | Electrical Faults |

> **Note:** Group by Spelling also merged Cooking into Smoking, so the Smoking category includes cooking fires. This is recorded as a known limitation.



### 3. Full Date and Living Standard Measure

**Problem.** The date was held in a single combined field, which made month and year analysis difficult. The LSM field held 15 coded values such as LSM 01 to LSM 11.

**Fix.**
* Split the full date into separate **Month**, **Day** and **Year** fields.
* Grouped the LSM codes into the descriptive **Living Standard Measure** field (Very Low, Low, Moderately Low, Medium, Moderately High, High and Very High LSM).



### 4. Time of incident

**Problem.** `TIME_OF_INCIDENT` was stored as a date and time where every value carried the same placeholder date of 1899/12/31, so the date part held no information. The time itself was recorded to the minute, giving roughly 1,440 distinct values, which made it impossible to see when fires tend to happen. A further 38 records had no usable time.

**Fix.** Created two calculated fields (see the next section) and left the original column unchanged.

### 5. Incident classification

**Problem.** `CLASSIFICATION` had 1,660 blank entries and one invalid entry, "South Arm Road", which is a street name entered in the wrong column.

**Fix.** Added a filter step that excluded the nulls and the invalid value.

| Measure | Before | After |
|---|---|---|
| Unique classification values | 9 | 7 |
| Records | 98,752 | 97,091 |
| Records removed | | 1,661 |

The remaining valid classifications are: False Alarm Good Intent, False Alarm Malicious, Fire automatically extinguished, Fire manually extinguished, Special Service, Special Service False Good Intent, and Special Service False Malicious.

> Filtering removes entire records, so it applies to all columns and cannot be applied to a duplicate field.

---

## Calculated Fields Created

| Field | Created in | Logic | Problem solved |
|---|---|---|---|
| Incident Hour | Tableau Prep | `DATEPART` extracts the hour (0 to 23) from the time field, ignoring the placeholder date | Replaces about 1,440 unique times with 24 usable hours |
| Time of Day | Tableau Prep | An `IF` statement assigns each hour to Morning, Afternoon, Evening or Night, and labels records with no time as Not Specified | Gives a small, readable set of categories and flags missing times |
| Number of Incidents | Tableau Desktop | Record count | The main measure plotted in every visualisation |

---

## Visualisations

All four visualisations were built on the filtered data (97,091 records), so totals are slightly lower than the full dataset.

### 1. Monthly Fire Incident Trend by Year

* **Type:** Line chart with one line per year.
* **Fields:** Month and Year (dimensions), Number of Incidents (measure).
* **Purpose:** Shows how fire numbers change through the year and whether the pattern repeats. Useful to fire departments and disaster management teams for staffing and campaign planning, to insurers anticipating claims, and to researchers and journalists studying seasonal change. Most useful during annual planning and budgeting.
* **Caution:** 2016 contains only four days of January, so its single point is low because of missing data, not fewer fires.



### 2. Fire Incidents by Hour of Day and Month (Heat Map)

* **Type:** Heat map, with colour intensity showing the number of incidents.
* **Fields:** Incident Hour and Month (dimensions), Number of Incidents (colour), Time of Day (optional filter).
* **Purpose:** Shows which hours and months carry the highest fire numbers. Emergency services can use it to schedule crews, building owners and schools can use it for safety planning, and utility providers can link it to peak demand periods.



### 3. Fire Causes by Area

* **Type:** Stacked horizontal bar chart, coloured by cause.
* **Fields:** Town and Fire Causation (cleaned dimensions), Number of Incidents (measure), with a Top N filter on Town to keep the chart readable.
* **Purpose:** Shows which areas have the most fires and what causes them. Municipal planners and fire prevention officers can target inspections and education, electricity providers can focus on electrical faults, housing departments on heating fires, and community groups on local risks.



### 4. Fire Incident Map by Living Standard Measure

* **Type:** Point map with small semi transparent dots, coloured by Living Standard Measure group.
* **Fields:** LATITUDE, LONGITUDE and Living Standard Measure.
* **Purpose:** Shows where fires occur and how that relates to living standards. City planners can use it to direct fire safety programmes and housing improvements, researchers and NGOs can study how inequality relates to fire risk, and insurers can assess risk by area. Useful when deciding where to place fire stations and community programmes.



---

## Dashboard

**Fire Incidents Analysis Dashboard** brings all four visualisations onto one screen, with three filters that apply to every view.

| Filter | Usefulness |
|---|---|
| Year | View a single year or compare several, to see whether fire numbers are rising or falling |
| Time of Day | Narrow the dashboard to morning, afternoon, evening or night, so crews can be planned around the busiest periods |
| Fire Causation | Focus on one cause, such as electrical faults or arson, and see where and when it happens so prevention work targets the right problem |



---

## Key Findings

These observations come directly from the visualisations and describe patterns in the data, not proven causes.

* **Strong seasonality.** Fire numbers rise sharply from about October and peak in the summer months (November to January), then fall to a low from around May to September. The highest monthly count shown is 2,464 incidents.
* **Afternoon peak.** The heat map shows the highest concentration of incidents in the middle of the day and afternoon, and the darkest cells appear in the summer months.
* **Geographic concentration.** Cape Town, Tygerberg, South Peninsula and Blaauwberg record the largest numbers of incidents among the areas shown.
* **Open flames stand out.** In the largest areas, open flames make up a large share of incidents, followed by other categories such as electrical faults and heating.
* **Wide spread across living standards.** The map shows incidents spread across all LSM groups, with dense clusters in the metro core.

---

## Limitations and Notes

* The 2016 data covers only four days, so it should not be read as a year on year drop.
* Group by Spelling merged Cooking into Smoking, so Smoking includes cooking fires.
* Visualisations use filtered data, so counts are slightly lower than the full dataset.
* The town cleaning removed the most obvious inconsistencies but was not exhaustive. Some district names and suburb names still sit in the same column.
* Time of Day boundaries are a judgement call and can be adjusted in the calculated field.

---

## Further Improvements

Three further standardisations were identified that would improve the data quality and make it easier to analyse.

1. **Replace placeholder values and vague descriptions with null.** Entries such as "Not specified", "Town 2", the number 93, and descriptions like "Near The Grave Yard" or "At Nature Reserve" are not towns. They inflate the unique value count and create meaningless labels on charts.
2. **Split combined entries and trim to one consistent format.** Some cells hold two places separated by a comma or slash (for example "Khayelitsha, Tygerberg" and "Kraaifontein/oostenberg"). A custom split on those separators, plus consistent capitalisation and trimmed spaces, would give one value per place.
3. **Separate district names from suburb names, and remove locations outside Cape Town.** District level names (such as Tygerberg and Blaauwberg) sit beside suburbs (such as Bellville and Athlone), and a few records fall outside the metro. An Area Level calculated field (District or Suburb) and a filter would prevent double counting and allow drill down from district to suburb.

---

## How to Reproduce

1. Clone this repository.
2. Open `tableau_prep/Fire Incidents Report.tfl` in Tableau Prep Builder and point the input step at `data/Fire_Incident.xlsx` if prompted.
3. Review each named clean step, then run the flow to regenerate the Hyper and Excel outputs.
4. Open `tableau_desktop/Fire Incidents Analysis.twbx` in Tableau Desktop (or Tableau Public) to explore the dashboard.
5. If you rebuild the workbook from scratch, connect to the cleaned Hyper extract, create the Number of Incidents measure, and recreate the four visualisations and three filters described above.

---

## Skills Demonstrated

* Data profiling and quality assessment on a real open dataset
* Data cleaning, grouping, filtering and transformation in Tableau Prep
* Building repeatable, documented data flows
* Calculated fields and measures in Tableau
* Choosing the right chart for the question being asked
* Dashboard design with interactive filters for multiple audiences
* Writing clear technical documentation and recognising data limitations

---

## Author

**Karen John**
BSc Computer Science and Business Computing, University of Cape Town

[LinkedIn](https://www.linkedin.com/in/karenjohn050230277) | [GitHub](https://github.com/karenjohn)
