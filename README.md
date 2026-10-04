# Hospital Emergency Room Analysis

An interactive Power BI report exploring emergency room patient demographics, waiting times, admissions, and department referrals.

## Project Overview

This project examines emergency room activity through patient-level records and operational indicators.

The report includes demographic analysis, waiting-time monitoring, admission status, department referrals, and a patient detail view.

Although the repository name contains “PPT,” the uploaded report file is a Power BI template (`.pbit`).

## Report Pages

| Page | Purpose |
|---|---|
| ER Overview | Key indicators, demographics, and referrals |
| 2.ER Overview | Waiting-time target status and admission analysis |
| Patients Details | Detailed patient record table |
| Insights | Written observations and recommendations |
| 2.Insights | Proposed resource allocation priorities |

## Dashboard Preview

### Patient and Referral Overview

![Patient and Referral Overview](screenshort%201.jpeg)

### Operational Metrics

![Operational Metrics](screenshort%202.jpeg)

### Patient Details

![Patient Details](screenshort%203.jpeg)

### Insights

![Insights](screenshort%204.jpeg)

### Resource Allocation Recommendations

![Resource Allocation Recommendations](screenshort%205.jpeg)

## Project Objectives

- Monitor patient volume and average waiting time.
- Examine patient satisfaction scores.
- Compare admitted and non-admitted records.
- Analyse waiting-time target classifications.
- Explore department referral patterns.
- Describe patient demographics.
- Support further operational investigation.

## Tools and Technologies

| Tool | Application |
|---|---|
| Power BI Desktop | Interactive reporting and visualisation |
| Power Query | Excel import, type conversion, and date/time extraction |
| DAX | Patient counts, averages, and target calculations |
| Microsoft Excel | Source dataset format |

## Dataset Structure

The main table is `Hospital ER_Hospital ER`.

| Area | Fields |
|---|---|
| Patient identifiers | Patient Id, Patient First Inital, Patient Last Name |
| Demographics | Patient Gender, Patient Age, Patient Race |
| Admission information | Patient Admission Date, Admitted vs Not Admitted, Patient Admin Flag |
| Service information | Department Referral, Patient Waittime, Target Status |
| Experience | Patient Satisfaction Score |
| Additional source field | Patients CM |
| Derived attributes | Day, day name, hour, month, age groups, hour groups |

The dataset source, reporting period, satisfaction scale, and meaning of `Patients CM` require documentation.

## Key Findings

### 1. Waiting Time and Satisfaction

The overview displays:

| Metric | Displayed Value |
|---|---:|
| Average Waiting Time | 35.26 |
| Average Patient Satisfaction Score | 4.99 |

Waiting time is described as minutes in the report narrative. The source definition should confirm this unit.

The satisfaction scale is not documented. The patient detail screenshot includes scores of **10**, so the average should not be labelled as “4.99 out of 5.”

### 2. Waiting-Time Target Status

| Classification | Displayed Count | Displayed Share |
|---|---:|---:|
| Target Missed | 5,467 | 59.3% |
| Within Target | 3,749 | 40.7% |

The two displayed counts total **9,216**.

The model also contains a measure using a waiting-time threshold of **30**. The source `Target Status` classifications should be checked against this threshold.

### 3. Admission Status

| Status | Displayed Count | Displayed Share |
|---|---:|---:|
| Admitted | 4,612 | 50.0% |
| Not Admitted | 4,604 | 50.0% |

Admission status is almost evenly split in the displayed report.

These counts describe the selected dataset and do not independently indicate admission appropriateness or clinical outcomes.

### 4. Department Referrals

| Department Referral | Displayed Count |
|---|---:|
| None | 5,400 |
| General Practice | 1,840 |
| Orthopedics | 995 |
| Physiotherapy | 276 |
| Cardiology | 248 |
| Neurology | 193 |
| Gastroenterology | 178 |
| Renal | 86 |

General Practice has the largest displayed count among named referral departments.

The category totals sum to **9,216**. Excluding `None` gives **3,816** displayed records assigned to named departments. A distinct referred-patient total requires validation of patient identifiers and record-level structure.

### 5. Patient Demographics

The gender chart displays:

- Male: **51.05%**
- Female: **48.69%**
- NC: **0.26%**

The meaning of `NC` should be documented.

The race chart displays:

| Category | Displayed Count |
|---|---:|
| White | 2,571 |
| African American | 1,951 |
| Two or More Races | 1,557 |
| Asian | 1,060 |
| Declined to Identify | 1,030 |
| Pacific Islander | 549 |
| Native American/Alaska Native | 498 |

White is the largest displayed category, representing approximately **27.9%** of the category total. It is not a majority.

These demographic distributions are descriptive and do not establish differences in healthcare access or outcomes.

> Findings are based on the uploaded screenshots and inspected model definitions. The source workbook is not included, so the figures have not been independently recalculated.

## Existing DAX Measures

### Total Patients

```dax
Total patients =
DISTINCTCOUNT(
    'Hospital ER_Hospital ER'[Patient Id]
)
```

This measure counts distinct patient identifiers, which may differ from visit counts if patients have multiple records.

### Average Waiting Time

```dax
Avg Wait Time =
AVERAGE(
    'Hospital ER_Hospital ER'[Patient Waittime]
)
```

### Average Satisfaction Score

```dax
Patient Satisfaction Score =
AVERAGE(
    'Hospital ER_Hospital ER'[Patient Satisfaction Score]
)
```

Blank scores are excluded from the average.

### Patients Waiting 30 or Less

```dax
% of patients =
DIVIDE(
    CALCULATE(
        [Total patients],
        'Hospital ER_Hospital ER'[Patient Waittime] <= 30
    ),
    [Total patients]
)
```

Validate blank waiting times before using this measure for target reporting.

## Referral Measure Correction

The existing measure is:

```dax
Number of Patients Referred =
DISTINCTCOUNT(
    'Hospital ER_Hospital ER'[Department Referral]
)
```

This counts distinct referral categories, not referred patients. It explains the displayed card value of **8**.

A proposed replacement is:

```dax
Referred Patients =
CALCULATE(
    [Total patients],
    KEEPFILTERS(
        FILTER(
            VALUES(
                'Hospital ER_Hospital ER'[Department Referral]
            ),
            NOT ISBLANK(
                'Hospital ER_Hospital ER'[Department Referral]
            )
            &&
            TRIM(
                'Hospital ER_Hospital ER'[Department Referral]
            ) <> ""
            &&
            TRIM(
                'Hospital ER_Hospital ER'[Department Referral]
            ) <> "None"
        )
    )
)
```

This proposed measure counts distinct patient IDs associated with a nonblank, named referral department. Validate source labels before replacing the existing card.

## Analysis Workflow

1. Import the Excel worksheet into Power BI.
2. Promote column headers and assign data types.
3. Extract day, weekday, hour, and month from admission timestamps.
4. Create age and hour groups.
5. Define patient counts, average metrics, and waiting-time calculations.
6. Build overview, operational, and patient-detail pages.
7. Add navigation buttons and report filters.
8. Validate written insights against the current filter context.

## Operational Recommendations

- Investigate waiting-time distributions by day and hour.
- Compare patient volume with staffing schedules before changing resources.
- Review the General Practice referral workload.
- Validate admission, referral, and target-status definitions.
- Report satisfaction response counts alongside average scores.
- Replace static narrative figures with verified, consistently filtered results.

These recommendations are proposed analytical actions. No operational improvement or clinical benefit has been measured.

## Repository Contents

| File | Description |
|---|---|
| `Hopital Emergency Room PPT.pbit` | Power BI report template |
| `screenshort 1.jpeg` | Demographic and referral overview |
| `screenshort 2.jpeg` | Operational metrics |
| `screenshort 3.jpeg` | Patient details |
| `screenshort 4.jpeg` | Written insights |
| `screenshort 5.jpeg` | Resource allocation recommendations |
| `README.md` | Project documentation |

## How to Open the Project

1. Download or clone this repository.
2. Open `Hopital Emergency Room PPT.pbit` in Power BI Desktop.
3. Obtain a compatible `Hospital ER_Hospital ER.xlsx` workbook.
4. Update the Source path in Power Query.
5. Confirm the `Hospital ER_Hospital ER` worksheet.
6. Apply changes and refresh the report.

> The template references an Excel file on the author's local computer. The source workbook is not included in the repository.

## Limitations and Future Improvements

- Document dataset provenance, reporting dates, and record-level structure.
- Distinguish unique patients from emergency room visits.
- Correct the referral card measure.
- Confirm the satisfaction scale and remove unsupported “out of 5” labels.
- Reconcile the static Insights page with the dashboard totals.
- Remove unsupported month-over-month improvement claims until verified.
- Correct the claim that the largest race category represents a majority.
- Label the day/hour matrix with the exact metric being displayed.
- Sort weekdays, hour groups, and age groups chronologically.
- Review age grouping: the current “0–10” expression includes ages 1–10 but excludes age 0.
- Expand truncated KPI values and labels.
- Parameterise the source workbook path.
- Validate missing waiting times, scores, and patient identifiers.
- Confirm that published patient details are synthetic or appropriately de-identified.

## Author

**Junaed Bogdadi**

[GitHub Profile](https://github.com/junaed-bogdadi)
