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



## 📁 Dataset Overview

The dataset consists of multiple tables
loaded and modeled in Power Pivot:

| Table | Rows | Columns | Description |
|-------|------|---------|-------------|
| DimEmployee | 1,470 | 24 | Employee personal & job info |
| FactPerformance | 6,709 | 11 | Performance review records |
| DimDate | 1,192| 28| Date dimension table |
| DimSatisfiedLevel | 5 | 2 | Satisfaction rating lookup |
| DimRatingLevel | 5 | 2 | Performance rating lookup |
| DimEducationalLevel | 5 | 2 | Education level lookup |


             
  ## Data Source
  
  Dource: DataCamp
  Domain : Human Resourses
         
## Tools Used

Excdel / Power Query / PowerPivot



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
employees are most vulnerable during their *first two years.*

| Period | Attrition Rate |
|--------|---------------|
| Year 0 | 34.5% 🔴 |
| Year 1 | 31.6% 🔴 |
| Year 2 | 20.2% 🟡 |
| Year 3 | 16.2% 🟡 |
| Year 4 | 17.4% 🟡 |

> *Key Finding:* 66% of total attrition is concentrated
> in the first 2 years of employment. This pattern strongly
> suggests a systemic gap in the onboarding experience
> and a misalignment between employee expectations
> and organizational reality — not a compensation issue.

---

### 2️⃣ Role-Specific Attrition Reveals Structural Problems

Attrition is not evenly distributed across job roles.
The data points to a clear concentration in
*client-facing and high-pressure positions.*

| Job Role | Attrition Rate | Risk Level |
|----------|---------------|------------|
| Sales Representative | 39.8% | 🔴 Critical |
| Recruiter | 37.5% | 🔴 Critical |
| Data Scientist | 23.8% | 🟡 Moderate |
| Sales Executive | 17.4% | 🟡 Moderate |
| Software Engineer | 16.0% | 🟢 Acceptable |
| Manager | 5.4% | 🟢 Healthy |

> *Key Finding:* The two highest attrition roles —
> Sales Representative and Recruiter — share common
> characteristics: target-driven environments, high
> performance pressure, and variable compensation.
> This is not a people problem, it is a
> *structural design problem.*

---

### 3️⃣ Business Travel Is a Silent Attrition Driver

When segmenting attrition by travel frequency,
a clear linear relationship emerges:
*the more employees travel, the more likely they are to leave.*

| Travel Frequency | Attrition Rate |
|-----------------|---------------|
| No Travel | 8.0% | |
| Some Travel | 15.0% | 
| Frequent Traveler | 24.5% | 

> *Key Finding:* Frequent travelers are *3x more likely*
> to leave compared to non-traveling employees.
> This indicates that excessive travel is significantly
> degrading Work-Life Balance and accelerating Burnout —
> a factor that is often overlooked in retention strategies.

---

### 4️⃣ Overtime Is Quietly Burning Out Employees

Analyzing attrition by overtime status reveals
a stark contrast between employees who work overtime
and those who do not.

| Overtime Status | Attrition Rate | Risk Level |
|----------------|---------------|------------|
| No Overtime | 10.4% | 🟢 Acceptable |
| Works Overtime | 30.5% | 🔴 Critical |

> *Key Finding:* Employees working overtime are.
> A 30.5% attrition rate among overtime workers signals
> a serious Burnout and unsustainable workload problem.
> This is especially dangerous when combined with
> high-pressure roles like Sales and Recruitment,
> where overtime is likely the norm rather than the exception.

---

### 5️⃣ Young Workforce Demands a Different Retention Strategy

The workforce demographic skews heavily young,
with the majority falling in the *20-29 age bracket.*

| Age Group | Headcount | % of Workforce |
|-----------|-----------|---------------|
| < 20 | 81 | 5.5% |
| *20-29* | *874* | *59.5%* |
| 30-39 | 289 | 19.7% |
| 40-49 | 219 | 14.9% |
| 50+ | 7 | 0.5% |

> *Key Finding:* With nearly *60% of the workforce*
> being Gen Z and Millennials, traditional retention
> approaches focused solely on salary are insufficient.
> This generation prioritizes **career growth, purpose,
> flexibility, and continuous learning.**
> The absence of a structured career development framework
> is likely a hidden driver of attrition in this segment.

---

### 6️⃣ Lack of Promotion Is a Primary Attrition Trigger

Segmenting attrition by *Years Since Last Promotion* reveals
one of the strongest predictors of employee turnover.
Employees who have *never received a promotion* show
the highest attrition rate across all segments.

| Years Since Last Promotion | Attrition Rate | Risk Level |
|---------------------------|---------------|------------|
| 0 - Never Promoted | 37.9% | 🔴 Critical |
| 1 | 24.6% | 🔴 High |
| 2 | 10.0% | 🟡 Moderate |
| 3 | 12.0% | 🟡 Moderate |
| 4 | 7.5% | 🟢 Acceptable |
| 5 | 9.5% | 🟢 Acceptable |
| 6 | 4.6% | 🟢 Healthy |
| 7 | 4.5% | 🟢 Healthy |
| 8 | 2.9% | 🟢 Healthy |
| 9 | 3.9% | 🟢 Healthy |

> *Key Finding:* Employees who have *never been promoted*
> exhibit an attrition rate of *37.9%* 
> the overall company rate of 16.1%.**
> The sharp decline after Year 1 confirms that the absence
> of any career progression signal, even early on,
> is a critical attrition trigger.
> This is not solely a tenure issue — it is a
> *career visibility and recognition problem.*

---

## ✅ Recommendations

### 1️⃣ Fix Early Attrition 🔴 High Priority
- Develop strong *90-Day Onboarding Program*
- Implement *Buddy System* for every new hire
- Schedule *30-60-90 Day Check-ins* with direct managers
- Set clear *job expectations* before hiring

### 2️⃣ Retain High-Pressure Roles 🔴 High Priority
- Review *Compensation & Commission Structure*
- Add *Non-monetary Benefits*
  - Flexible working hours
  - Remote work options
  - Professional development budget
- Review *Workload & KPIs* to ensure they are realistic

### 3️⃣ Control Overtime & Prevent Burnout 🔴 High Priority
- Set a *maximum overtime cap* per employee per month
- Monitor overtime patterns using a *Burnout Risk Score*
- Hire additional headcount in *high-overtime departments*
- Introduce *Compensatory Time Off* for overtime workers
- Conduct *monthly Workload Review* with team managers

### 4️⃣ Manage Business Travel 🟡 Medium Priority
- Set *maximum travel limit* per employee per month
- Provide *Travel Allowance* & additional bonuses
- Replace unnecessary trips with *Virtual Meetings*
- Offer *extra days off* after long travel periods

### 5️⃣ Build a Promotion & Career Growth Framework 🟡 Medium Priority
- Introduce an *Early Promotion Track* for high performers
- Establish *Career Milestone Rewards* as interim recognition
- Ensure every new hire has a clearly defined
  *Promotion Criteria & Timeline* from Day 1
- Conduct *quarterly career conversations* between
  managers and employees
- Implement a *Performance Visibility System*

### 6️⃣ Develop Young Talent 🟡 Medium Priority
- Create clear *Career Path* for each role
- Launch *Learning & Development* programs
- Set transparent *Promotion Timeline*
- Offer *Mentorship Programs* with senior employees

<img width="1841" height="873" alt="Overview" src="https://github.com/user-attachments/assets/bd950dfd-bd2c-413a-9b04-3de2666aed03" />

<img width="1842" height="897" alt="Demographics" src="https://github.com/user-attachments/assets/f2d2617a-404a-4edf-87f9-91577802a44e" />

<img width="1860" height="892" alt="Attrition" src="https://github.com/user-attachments/assets/a51b2ad6-f083-4d53-9415-75522e270fcc" />


