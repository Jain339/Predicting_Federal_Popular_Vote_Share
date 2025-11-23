# **From Survey to Population: Predicting Federal Popular Vote Shares Using Multinomial Logit Post-Stratification**

### *Authors: Narae Lee, Nikita Jain, Radia Salam*

---

## **Table of Contents**

* [Project Overview](#project-overview)
* [Research Question](#research-question)
* [Data Sources](#data-sources)
* [Data Preparation](#data-preparation)
* [Modeling Framework](#modeling-framework)
* [Post-Stratification Procedure](#post-stratification-procedure)
* [Results](#results)
* [Repository Structure](#repository-structure)
* [How to Reproduce](#how-to-reproduce)
* [Citations](#citations)

---

## **Project Overview**

This project estimates the national popular vote for Canada’s major federal parties by integrating multinomial logistic regression with census-calibrated post-stratification. By combining individual-level voting intentions from the 2021 Canadian Election Study (CES) with demographic population counts from the 2021 Canadian Census, the analysis produces population-representative predictions of partisan support. This methodological framework corrects for survey non-representativeness and demographic imbalances, enabling a structured assessment of how age, education, and regional context jointly shape predicted federal vote patterns across Canada.

---

## **Research Question**

**How do age, education, and provincial regions jointly shape predicted support for the Liberal, Conservative, and New Democratic Parties, and what do these patterns imply for the projected national popular vote?**

We hypothesize, based on established literature, that:

* Younger and more educated voters exhibit higher NDP support
* Older and less educated voters favour the Conservative Party
* Regional differences especially Prairie conservatism and BC/Quebec centre-left alignment are strong predictors of partisan choice

---

## **Data Sources**

### **Canadian Election Study (CES) 2021 – Survey Data**

Contains 15,069 respondents with detailed demographic and vote-choice variables. These data provide the basis for the multinomial model.

### **Canadian Census 2021 – Population Data**

Provides authoritative counts for combinations of age group, education level, and province. These data form the demographic cells required for post-stratification.

Both datasets were cleaned, merged, and aligned by common demographic categories.

---

## **Data Preparation**

The workflow includes:

### **1. Sample Restriction**

* Filtered CES respondents to include only those selecting:
  **Liberal, Conservative, or NDP**
* Removed missing age, education, or region values

### **2. Variable Construction**

* **Age groups:** six categories (18–24, 25–34, ..., 65+)
* **Education:** original ordered scale (1–12), cleaned and retained as an ordered factor
* **Region:** provinces aggregated into six regions

  * Atlantic
  * Quebec
  * Ontario
  * Prairies
  * British Columbia
  * Territories

### **3. Census Aggregation**

The census file was collapsed into **491 unique demographic cells**, each representing a population count for a combination of age × education × region.

### **4. Exploratory Analysis**

Visualizations of demographic vote patterns (age, education, region) confirmed expected trends and guided modelling decisions.

---

## **Modeling Framework**

To estimate partisan support across three major Canadian federal parties, we employ a **multinomial logistic regression**, which generalizes binary logistic regression to outcomes with more than two categories. The Liberal Party is treated as the baseline category.

**Model Formula:**
`vote_3cat ~ age_group + educ + prov_region`

### **Conservative vs. Liberal**

$$
\log\left(\frac{P_{iC}}{P_{iL}}\right)
= \beta_{0C}$$

* $$\beta_{C(\text{age})}\cdot \text{AgeGroup}_i$$
* $$\beta_{C(\text{educ})}\cdot \text{Education}_i$$
* $$\beta_{C(\text{prov})}\cdot \text{Region}_i$$

### **NDP vs. Liberal**

$$
\log\left(\frac{P_{iN}}{P_{iL}}\right)
= \beta_{0N}$$

* $$\beta_{N(\text{age})}\cdot \text{AgeGroup}_i$$
* $$\beta_{N(\text{educ})}\cdot \text{Education}_i$$
* $$\beta_{N(\text{prov})}\cdot \text{Region}_i$$

### **Interpretation**

* $$(P_{iL})$$: predicted probability that individual *i* votes **Liberal**
* $$(P_{iC})$$: predicted probability that individual *i* votes **Conservative**
* $$(P_{iN})$$: predicted probability that individual *i* votes **NDP**

Positive coefficients indicate higher log-odds of supporting the given party relative to the Liberal Party; negative coefficients indicate lower relative log-odds.

Predictors were chosen based on their:

* theoretical importance in political behaviour research
* availability in the census for population weighting
* strong demographic structuring observed in exploratory analysis

This model provides predicted probabilities for each demographic cell.

---

## **Post-Stratification Procedure**

Post-stratification corrects for CES sampling imbalances by combining model predictions with true population proportions.

For each cell ( j ):

* $$( \hat{y}_{j} )$$: predicted probability of supporting a party
* $$( N_{j} )$$: census population count

The post-stratified estimate is:

$$
\hat{P}_{PS} =
\frac{\sum_j N_j \hat{y}_j}{\sum_j N_j}
$$

This produces national popular vote predictions calibrated to the actual Canadian demographic structure.

---

## **Results**

### **Post-Stratified National Popular Vote Estimates**

| Party            | Predicted Vote Share |
| ---------------- | -------------------- |
| **Liberal**      | **36.5%**            |
| **Conservative** | **35.2%**            |
| **NDP**          | **28.3%**            |

### **Key Insights**

* **Age:** Younger voters strongly favour the NDP, while older voters prefer Liberal/Conservative parties.
* **Education:** Lower education groups lean Conservative; higher education groups favour the NDP and Liberals.
* **Region:**

  * Prairies: strongest Conservative preference
  * British Columbia: strong NDP support
  * Quebec: more centrist / Liberal-leaning
* **Model validity:** Patterns align closely with Canadian electoral behaviour literature, increasing confidence in the model’s predictions.

---

## **Repository Structure**

```
Predicting_Federal_Vote_Estimates/
├── Predicting_Federal_Vote.qmd    # Analysis code (multinomial model + post-stratification)
├── Predicting_Federal_Vote.pdf    # Final rendered report
├── ces2021.csv                    # CES survey data (not for public redistribution)
├── canada_census2021.csv          # Census data (not for public redistribution)
└── README.md                      # Project documentation
```

---

## **How to Reproduce**

### **Clone the Repository**

```bash
git clone https://github.com/your-username/Predicting_Federal_Vote_Estimates.git
cd Predicting_Federal_Vote_Estimates
```

### **Install Required R Packages**

```r
install.packages(c("tidyverse", "nnet", "dplyr"))
```

### **Render the Analysis**

Open **Predicting_Federal_Vote.qmd** in RStudio → click **Render**.

The script:

* loads survey and census data
* cleans and aligns demographic variables
* fits the multinomial model
* applies post-stratification
* outputs tables and plots into the final PDF

---

## **Citations**

A complete bibliography is included in the PDF report.
Sources include peer-reviewed literature on Canadian voting behaviour, partisan alignment, census methodology, and CES documentation.

---
