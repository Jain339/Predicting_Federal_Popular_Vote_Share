# **From Survey to Population: Predicting Federal Popular Vote Shares Using Multinomial Logit Post-Stratification**

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

Both datasets were cleaned, merged, and aligned by common demographic categories. The CES 2021 and Canadian Census 2021 datasets are not included in this repository due to size limitations and licensing/restrictions. To reproduce the analysis, place the files ces2021.csv and canada_census2021.csv in the data/ directory after downloading them from the official course page.

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

## **Citations (APA 7th edition)**

1. Barisonzi, M. (2021). *Estimation of voter turnout by age group and gender at the 2021 general election.* Analytics and Performance Measurement.
   [https://www.elections.ca/res/rec/part/estim/estimation40_e.pdf](https://www.elections.ca/res/rec/part/estim/estimation40_e.pdf)

2. Daoust, J.-F., Guévremont, M., & Blais, A. (2024). The (non)-religious voter in Canadian elections. *Electoral Studies, 90*, 102812.
   [https://doi.org/10.1016/j.electstud.2024.102812](https://doi.org/10.1016/j.electstud.2024.102812)

3. Gidengil, E. (2022). Voting behaviour in Canada: The state of the discipline. *Canadian Journal of Political Science, 55*(4), 1–23.
   [https://doi.org/10.1017/s0008423922000531](https://doi.org/10.1017/s0008423922000531)

4. Kiewiet de Jonge, C. P., Langer, G., & Sinozich, S. (2018). Predicting state presidential election results using national tracking polls and multilevel regression with poststratification (MRP). *Public Opinion Quarterly, 82*(3), 419–446.
   [https://doi.org/10.1093/poq/nfy023](https://doi.org/10.1093/poq/nfy023)

5. Lucas, J., & Armstrong, D. A. (2021). Policy ideology and local ideological representation in Canada. *Canadian Journal of Political Science*, 1–18.
   [https://doi.org/10.1017/s0008423921000652](https://doi.org/10.1017/s0008423921000652)

6. McGrane, D. (2015). Socio-economic determinants of voting behaviour in Canadian provincial elections from 1988 to 2006. *ResearchGate.*
   [https://www.researchgate.net/publication/265194888_Socio-Economic_Determinants_of_Voting_Behaviour_in_Canadian_Provincial_Elections_from_1988_to_2006](https://www.researchgate.net/publication/265194888_Socio-Economic_Determinants_of_Voting_Behaviour_in_Canadian_Provincial_Elections_from_1988_to_2006)

7. Merkley, E. (2022). Polarization Eh? Ideological divergence and partisan sorting in the Canadian mass public. *Public Opinion Quarterly, 86*(4), 932–943.
   [https://doi.org/10.1093/poq/nfac047](https://doi.org/10.1093/poq/nfac047)

8. Polacko, M. (2020). Party positions, income inequality, and voter turnout in Canada, 1984–2015. *American Behavioral Scientist, 64*(9), 1324–1347.
   [https://doi.org/10.1177/0002764220941238](https://doi.org/10.1177/0002764220941238)

9. Polacko, M. (2025). Canada’s increasing class-based voting disparities amidst declining economic policy saliency. *Canadian Journal of Political Science*, 1–27.
   [https://doi.org/10.1017/s0008423925100462](https://doi.org/10.1017/s0008423925100462)

10. Stephenson, L. B., Harell, A., Rubenson, D., & Loewen, P. J. (2022). *2021 Canadian Election Study (CES).* Harvard Dataverse.
    [https://doi.org/10.7910/DVN/XBZHKC](https://doi.org/10.7910/DVN/XBZHKC)

11. Uppal, S., & Larochelle-Côté, S. (2012). Factors associated with voting. *Perspectives on Labour and Income, Statistics Canada Catalogue No. 75-001-X.*
    [https://www150.statcan.gc.ca/n1/en/pub/75-001-x/2012001/article/11629-eng.pdf?st=n3F69haT](https://www150.statcan.gc.ca/n1/en/pub/75-001-x/2012001/article/11629-eng.pdf?st=n3F69haT)

---
