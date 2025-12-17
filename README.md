# HR Report for Nava Company (Power BI)

## Project Overview

This project is a comprehensive **HR Analytics Power BI dashboard** developed to provide clear, data-driven insights into Nava Company's workforce. The report is designed to support HR managers, business leaders, and decision-makers by transforming raw HR data into meaningful visual analytics. It focuses on employee headcount, hiring and termination trends, retention, and turnover analysis over time, enabling better workforce planning and strategic decision-making.

The dashboard follows best practices in data modeling, DAX measure design, and interactive reporting, ensuring accuracy, scalability, and ease of use.

---

## Dataset Information

The report is built using multiple structured datasets that represent different aspects of HR data:

* **People Data**: Core employee information such as employee ID, department, job role, gender, and other demographic attributes.
* **Employment History**: Hire dates, termination dates, and employment status used to track employee lifecycle events.
* **Date Table**: A dedicated calendar table used for time intelligence, trend analysis, and period-based calculations.

All datasets are cleaned and transformed in Power Query to ensure consistency, correct data types, and reliable relationships.

---

## Dashboard Features

* Interactive slicers for **date, department, and other HR dimensions**
* Dynamic KPIs that respond instantly to user selections
* Trend analysis for headcount, turnover, and retention over time
* Clear visual storytelling using cards, charts, and tables
* User-friendly layout optimized for executive-level reporting

---

## Key Performance Indicators (KPIs)

The dashboard tracks the most important HR metrics, including:

* Total Employees (Headcount)
* Starting Headcount
* Ending Headcount
* Average Number of Employees
* Departing Employees
* Retention Rate
* Turnover Percentage

These KPIs provide a full picture of workforce stability and change.

---

## Measures (DAX)

Below are the exact DAX measures used in this project. These measures are built with correct filter context and time intelligence to ensure accurate HR analytics across all date selections.

```DAX
-- Total distinct employees
All Employees =
DISTINCTCOUNT ( manager_dim[employee_id] )

-- Headcount at the end of the selected period
Headcount =
CALCULATE (
    [All Employees],
    FILTER (
        people_fact,
        people_fact[hire_date] <= LASTDATE ( 'Date'[Date] )
            && (
                people_fact[term_date] > LASTDATE ( 'Date'[Date] )
                || people_fact[term_date] = BLANK ()
            )
    )
)

-- Headcount at the beginning of the selected period
Starting Headcount =
CALCULATE (
    [All Employees],
    FILTER (
        people_fact,
        people_fact[hire_date] < FIRSTDATE ( 'Date'[Date] )
            && (
                people_fact[term_date] = BLANK ()
                || people_fact[term_date] >= FIRSTDATE ( 'Date'[Date] )
            )
    )
)

-- Headcount at the end of the selected period
Ending Headcount =
CALCULATE (
    [All Employees],
    FILTER (
        people_fact,
        people_fact[hire_date] < FIRSTDATE ( 'Date'[Date] )
            && (
                people_fact[term_date] = BLANK ()
                || people_fact[term_date] >= LASTDATE ( 'Date'[Date] )
            )
    )
)

-- Employees who left during the selected period
Departing Employees =
CALCULATE (
    [All Employees],
    FILTER (
        people_fact,
        people_fact[term_date] >= FIRSTDATE ( 'Date'[Date] )
            && people_fact[term_date] <= LASTDATE ( 'Date'[Date] )
    )
)

-- Average number of employees during the period
Average Number of Employees =
DIVIDE ( [Starting Headcount] + [Ending Headcount], 2 )

-- Retention rate
Retention Rate =
DIVIDE ( [Ending Headcount], [Starting Headcount] )

-- Turnover percentage
Turnover % =
DIVIDE ( [Departing Employees], [Average Number of Employees] )
```

---

## Installation & Setup

1. Download or clone the repository:

   ```bash
   git clone https://github.com/mostafamamdouh115/HR-Report-for-nava-company.git
   ```
2. Open the `.pbix` file using **Power BI Desktop**.
3. Ensure the dataset files (CSV / Excel) are in the correct directory or update data source paths if needed.
4. Refresh the data to load the latest information.

---

## Dashboard Pages

* **Headcount Analysis**: Employee distribution and trends over time
* **Turnover & Retention**: Detailed analysis of employee exits and retention rates

Each page is designed with a clear purpose and minimal visual clutter.

---

## Data Model & Relationships
The data model is designed using a **star schema** to ensure optimal performance, clarity, and scalability. A single fact table captures all employee lifecycle events, while multiple dimension tables provide rich descriptive context.

### Fact Table

* **people_fact**: The core fact table containing employee lifecycle data, including `hire_date`, `term_date`, and foreign keys linking to all related dimension tables.

### Dimension Tables

The following dimension tables provide descriptive attributes for employee analysis:

* **Demographic_dim**: Contains employee demographic details such as age, age group, and gender.
* **Manager_dim**: Stores the organizational hierarchy, including multiple levels of management.
* **Location_dim**: Provides location information, including location and location_city.
* **Department_dim**: Holds department and sub-department details.
* **Job_level_dim**: Categorizes employees based on their specific job level.
* **Education_dim**: Tracks the education level of the workforce.
* **Marital_dim**: Provides marital_status information.
* **Term_dim**: Contains employee exit details, including term_reason and term_type.
* **Date**: A dedicated calendar table supporting advanced time intelligence (Day, Month, Year, and Timeframe).

This structure enables accurate filtering, flexible slicing, and robust time-based analysis across all HR metrics.

---
## Key Insights by Dashboard Page

### 1. Headcount Dashboard (Workforce Composition)

This page provides a snapshot of the current workforce scale and demographic breakdown.

* **Software Dominance**: The Software department is the largest by a significant margin (660 employees), nearly 30% larger than Sales (510). This indicates that Nava Company is a technology-driven or product-led organization.
* **Junior-Heavy Structure**: Over 74% of the workforce consists of Individual Contributors (2,551 employees). The management hierarchy appears balanced, and the presence of a single CEO confirms strong data integrity.
* **Gender Gap**: There is a clear gender imbalance, with 60.87% Male versus 39.01% Female, highlighting a potential diversity improvement opportunity.
* **Education Level**: The workforce is highly educated, with the majority holding a Bachelor’s degree or higher, indicating strong human capital quality.
* **Work Preference**: Approximately 80% of employees work On-site. For a software-heavy organization, this may reduce competitiveness compared to companies offering remote or hybrid options.

---

### 2. Retention Dashboard (Stability Trends)

This page evaluates how effectively the organization retains its employees over time.

* **Overall Health**: An overall retention rate of 83.62% is considered healthy, although it reflects a noticeable reduction from the starting headcount (409) to the ending headcount (342) during the selected period.
* **Top-Tier Loyalty**: Team Leads demonstrate the highest retention rate at 91.84%, suggesting strong engagement and stability at the mid-management level.
* **Departmental Red Flags**: Product Development (76.19%) and Finance (76.47%) show the lowest retention rates, potentially signaling workload pressure, burnout, or compensation challenges.
* **Positive Historical Trend**: Retention dipped around 2014–2015 but has steadily improved since then, reaching 100% in 2023. This may be influenced by recent hiring freezes or the year not yet being fully completed.

---

### 3. Turnover Dashboard (Exit Analysis)

This page focuses on employee exits, reasons for leaving, and organizational impact.

* **High Management Churn**: Directors show a high turnover rate (45.83%). Due to the small population size, even a few departures significantly impact turnover percentages at this level.
* **Primary Exit Drivers**: The leading reasons for leaving—better opportunities, more flexible benefits, and higher salaries—indicate strong external competition and a need to modernize compensation and benefits strategies.
* **Voluntary vs. Involuntary**: A substantial 82.20% of exits are voluntary, confirming that employees are choosing to leave rather than being involuntarily separated.
* **R&D Volatility**: The R&D department records the highest turnover rate (44.97%), posing a serious risk to innovation continuity and institutional knowledge.
* **Turnover Cliff**: A sharp spike in turnover occurred in 2021 (5.10%), followed by a dramatic drop to 0.15% in the most recent period. This suggests a major organizational policy shift or external market influence that significantly reduced employee exits.

---

## Technologies Used

* **Power BI Desktop**
* **DAX (Data Analysis Expressions)**
* **Power Query (M Language)**
* **CSV & Excel Data Sources**

---

## Project Structure

```
HR-Report-for-nava-company/
│
├── color palette/
│   └── download (2).json
│
├── dataset/
│   ├── Dates.xlsx
│   ├── people_data.csv
│   └── people_employment_history.csv
│
├── hr dashboard/
│   └── hr project.pbix
│
└── logo/
    ├── Adobe Express - file.png
    └── create a logo for company nam...
```

---

## Author

**Mostafa Mamdouh**
Data Analyst | Power BI Developer

This project demonstrates strong skills in HR analytics, data modeling, and interactive dashboard development using Power BI.
