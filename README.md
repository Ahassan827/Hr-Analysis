# Hr-Analysis

## 📌 Project Overview
This project analyzes human resources data for **Atlas Lab** 
to understand employee attrition patterns and workforce 
demographics. The goal is to support data-driven HR 
decision-making using Excel, Power Query, and Power Pivot.

---

## ❓ Business Problem
> Atlas Lab is experiencing an employee attrition rate of
> **16.1%** (237 out of 1,470 employees), which exceeds
> the industry benchmark of **10-12%**.

This analysis aims to:
- Identify key drivers of employee attrition
- Understand workforce demographics
- Provide actionable recommendations to reduce turnover
- Support strategic HR decision-making



## 🔧 Data Preparation & Modeling

### 1️⃣ Power Query - Data Cleaning
- Imported all source files into Power Query
- Cleaned and transformed raw HR data
- Created **Conditional Column** for Age Group:

| Condition | Age Group |
|-----------|-----------|
| Age < 20  | < 20      |
| Age 20-29 | 20-29     |
| Age 30-39 | 30-39     |
| Age 40-49 | 40-49     |
| Age 50+   | 50>       |

- Built a separate **Date Table** using Advanced Editor:

```powerquery
= List.Dates(
    #date(2012,1,1),
    Duration.Days(
      #date(2022,12,31) - #date(2012,1,1))+1,
    #duration(1,0,0,0))

---


### 2️⃣ Power Pivot - Data Modeling

Loaded all tables into Power Pivot and built the following relationships:

#### 🔗 Table Relationships

| From Table          | To Table        | Type         | Key          |
|---------------------|-----------------|--------------|--------------|
| DimDate             | FactPerformance | One-to-Many  | DateKey      |
| DimEmployee         | FactPerformance | One-to-Many  | EmployeeID   |
| DimSatisfiedLevel   | FactPerformance | One-to-Many  | SatisfactionID|
| DimRatingLevel      | FactPerformance | One-to-Many  | RatingID     |
| DimEducationalLevel | FactPerformance | One-to-Many  | EducationLevelID|

---

### 3️⃣ Data Model Tables

| Table               | Key Columns                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| **DimDate**         | Date, Year, YearStart, YearEnd, MonthNumber                                 |
| **DimEmployee**     | EmployeeID, Name, Gender, Age, AgeGroup, Department, JobRole, Ethnicity, BusinessTravel |
| **DimSatisfiedLevel**  | SatisfactionID, SatisfactionLevel                                        |
| **DimRatingLevel**     | RatingID, RatingLevel                                                    |
| **DimEducationalLevel**| EducationLevelID, EducationLevel                                         |
| **FactPerformance** | PerformanceID,EmployeeID, ReviewDate, EnvironmentSatisfaction, JobSatisfaction |


### 4️⃣ DAX Measures

Created a dedicated **Measures Table** to centralize
all calculations and maintain a clean data model.

```dax
-- Total Employees
Total Employees = 
COUNTROWS(DimEmployee)

-- Active Employees
Active Employees = 
CALCULATE(
    [Total Employees],
    DimEmployee[Attrition] = "No"
)

-- Inactive Employees
Inactive Employees = 
CALCULATE(
    [Total Employees],
    DimEmployee[Attrition] = "Yes"
)

-- Attrition Rate
Attrition Rate = 
DIVIDE(
    [Inactive Employees],
    [Total Employees],
    0
)
```

| Measure | Logic | Output |
|---------|-------|--------|
| Total Employees | COUNT all rows in DimEmployee | 1,470 |
| Active Employees | FILTER where Attrition = "No" | 1,233 |
| Inactive Employees | FILTER where Attrition = "Yes" | 237 |
| Attrition Rate | Inactive ÷ Total | 16.1% |


## 💡 Insights

### 1️⃣ Early Attrition Is the Biggest Risk

Analysis of attrition by tenure reveals a critical pattern:
employees are most vulnerable during their **first two years**.

| Period | Attrition Rate |
|--------|---------------|
| Year 0 | 34.5% 🔴 |
| Year 1 | 31.6% 🔴 |
| Year 2 | 20.2% 🟡 |
| Year 3 | 16.2% 🟡 |
| Year 4 | 17.4% 🟡 |

> **Key Finding:** 66% of total attrition is concentrated
> in the first 2 years of employment. This pattern strongly
> suggests a systemic gap in the onboarding experience
> and a misalignment between employee expectations
> and organizational reality — not a compensation issue.

---

### 2️⃣ Role-Specific Attrition Reveals Structural Problems

Attrition is not evenly distributed across job roles.
The data points to a clear concentration in
**client-facing and high-pressure positions.**

| Job Role | Attrition Rate | Risk Level |
|----------|---------------|------------|
| Sales Representative | 39.8% | 🔴 Critical |
| Recruiter | 37.5% | 🔴 Critical |
| Data Scientist | 23.8% | 🟡 Moderate |
| Sales Executive | 17.4% | 🟡 Moderate |
| Software Engineer | 16.0% | 🟢 Acceptable |
| Manager | 5.4% | 🟢 Healthy |

> **Key Finding:** The two highest attrition roles —
> Sales Representative and Recruiter — share common
> characteristics: target-driven environments, high
> performance pressure, and variable compensation.
> This is not a people problem, it is a
> **structural design problem.**

---

### 3️⃣ Business Travel Is a Silent Attrition Driver

When segmenting attrition by travel frequency,
a clear linear relationship emerges:
**the more employees travel, the more likely they are to leave.**

| Travel Frequency | Attrition Rate | vs. No Travel |
|-----------------|---------------|---------------|
| No Travel | 8.0% | 🟢 Baseline |
| Some Travel | 15.0% | 🟡 +87% higher |
| Frequent Traveler | 24.5% | 🔴 +206% higher |

> **Key Finding:** Frequent travelers are **3x more likely**
> to leave compared to non-traveling employees.
> This indicates that excessive travel is significantly
> degrading Work-Life Balance and accelerating Burnout —
> a factor that is often overlooked in retention strategies.

---

### 4️⃣ A Young Workforce Demands a Different Retention Strategy

The workforce demographic skews heavily young,
with the majority falling in the **20-29 age bracket.**

| Age Group | Headcount | % of Workforce |
|-----------|-----------|---------------|
| < 20 | 81 | 5.5% |
| **20-29** | **874** | **59.5%** |
| 30-39 | 289 | 19.7% |
| 40-49 | 219 | 14.9% |
| 50+ | 7 | 0.5% |

> **Key Finding:** With nearly **60% of the workforce**
> being Gen Z and Millennials, traditional retention
> approaches focused solely on salary are insufficient.
> This generation prioritizes **career growth, purpose,
> flexibility, and continuous learning.**
> The absence of a structured career development framework
> is likely a hidden driver of attrition in this segment.

---

### 5️⃣ Department-Level Attrition Signals Resource Imbalance

| Department | Active Employees | Attrition Exposure |
|------------|-----------------|-------------------|
| Technology | 828 | High volume = High risk |
| Sales | 354 | High attrition roles |
| Human Resources | 51 | Small team = High impact |

> **Key Finding:** The Technology department holds
> **67% of active headcount** yet contains roles
> like Data Scientist with 23.8% attrition.
> Losing even a small number of technical employees
> creates disproportionate operational impact
> due to the high cost and long lead time
> of replacing specialized talent.



## ✅ Recommendations

### 1️⃣ Fix Early Attrition 🔴 High Priority
- Develop strong **90-Day Onboarding Program**
- Implement **Buddy System** for every new hire
- Schedule **30-60-90 Day Check-ins** with direct managers
- Set clear **job expectations** before hiring

---

### 2️⃣ Retain High-Pressure Roles 🔴 High Priority
- Review **Compensation & Commission Structure**
- Add **Non-monetary Benefits**
  - Flexible working hours
  - Remote work options
  - Professional development budget
- Review **Workload & KPIs** to ensure they are realistic

---

### 3️⃣ Manage Business Travel 🟡 Medium Priority
- Set **maximum travel limit** per employee per month
- Provide **Travel Allowance** & additional bonuses
- Replace unnecessary trips with **Virtual Meetings**
- Offer **extra days off** after long travel periods

---

### 4️⃣ Develop Young Talent 🟡 Medium Priority
- Create clear **Career Path** for each role
- Launch **Learning & Development** programs
- Set transparent **Promotion Timeline**
- Offer **Mentorship Programs** with senior employees

---

## 📋 Recommendations Priority Matrix

| Priority | Problem |7 Solution | Expected Impact |
|----------|---------|----------|----------------|
| 🔴 Urgent | Early Attrition | Onboarding Program | -10 to 15% |
| 🔴 Urgent | Sales & Recruitment | Compensation Review | -8 to 12% |
| 🟡 Medium | Frequent Travel | New Travel Policy | -5 to 8% |
| 🟡 Medium | Young Employees | Career Development | +Retention |
