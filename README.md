[README_StoreRevenue.md](https://github.com/user-attachments/files/34254498/README_StoreRevenue.md)
# Predicting Store Revenue from Location Characteristics

A regression analysis that estimates a store's revenue from five features of its location, shows how much each feature matters, and puts honest uncertainty around every estimate. It is built the way a retailer would want it: interpretable, validated, and clear about where it can and cannot be trusted.

**The question:** how do competition, local income, customer loyalty, population density and the age of a location relate to store revenue, and how reliably can revenue be estimated for a new site?

## The data

`location_rev.csv`: 1,000 store locations, no missing values and no duplicate rows.

| Column | Meaning |
|---|---|
| `revenue` | Outcome: store revenue |
| `num_competitors` | Number of competing stores nearby |
| `median_income` | Median income of the local area |
| `num_loyalty_members` | Number of loyalty-programme members |
| `population_density` | Population density of the area |
| `location_age` | Age of the location in years |

The data file is not included in this repository (see the data note below).

## What the analysis does

1. **Data-quality checks:** missing values, duplicates, and rows containing zeros
2. **Baseline linear regression** (75/25 train/test split, `random_state=100`)
3. **Error metrics against a naive baseline:** RMSE, MAE and R², compared with always predicting the average
4. **10-fold cross-validation** to show the results are not a lucky split
5. **Standardised coefficients** so features can be ranked fairly despite different units
6. **Statistical inference:** standard errors, p-values and 95% confidence intervals, computed from OLS theory
7. **Multicollinearity check** using variance inflation factors
8. **Residual diagnostics:** residuals against predictions, a Q-Q plot, and average residuals across each feature
9. **Model comparison** with random forest and gradient boosting
10. **Predictions with 95% intervals** for a new site, plus a check that the site lies inside the range of the data

## Results

**The model predicts well.**

| Measure | Linear regression | Predict-the-average baseline |
|---|---|---|
| Test R² | **0.821** | -0.001 |
| Test RMSE | **4,815** | 11,387 |
| Test MAE | **3,930** | 9,570 |
| 10-fold CV R² | **0.807 ± 0.030** | n/a |

The model explains about 82% of the variation in revenue on unseen data, and the cross-validated score confirms this is stable.

**Every feature matters, and they act independently.** All five are significant at p < 0.001, with variance inflation factors of about 1.0, so the coefficients are stable.

| Feature | Coefficient (per unit) | 95% CI | Effect per +1 SD |
|---|---|---|---|
| `median_income` | 1.72 | 1.62 to 1.81 | +6,684 |
| `location_age` | 2,067 | 1,948 to 2,186 | +6,381 |
| `population_density` | 4.32 | 3.94 to 4.70 | +4,186 |
| `num_competitors` | -2,148 | -2,508 to -1,788 | -2,201 |
| `num_loyalty_members` | 3.51 | 2.77 to 4.25 | +1,745 |

Median income and location age have the largest effects. Each extra competitor goes with about 2,148 lower revenue, holding the others fixed.

**A simple model is enough.** Random forest (test R² 0.745) and gradient boosting (0.762) both did worse than linear regression (0.821). Residuals scatter randomly around zero with a flat average across every feature, so there is no sign of missed nonlinear effects.

**Example prediction.** For a site with 3 competitors, median income of 30,000, 1,200 loyalty members, a population density of 2,000 and a location age of 10 years:

- Predicted revenue: **27,573**
- 95% confidence interval for the average of such sites: 26,837 to 28,309
- 95% prediction interval for a single new store: **17,470 to 37,676**

The gap between those two intervals matters: the average for sites like this is well pinned down, but any one store can land far from it. The site is inside the range of the data, though its population density is low (9th percentile).

## Limitations

- **Associations, not causes.** For example, location age may reflect how established a store is, not an effect of age itself.
- **Possibly simulated data.** The five features are almost completely uncorrelated with each other, which is unusual in real store data. Check the data's origin before drawing business conclusions.
- **A few odd rows.** Four rows contain a zero (loyalty members, population density, age or competitors) and one revenue sits exactly at 5,000. They may be genuine or placeholders.
- **Revenue units unknown.** The currency and period are not stated in the data.
- **No spatial or time structure.** Stores near each other, or measured at different times, may share influences the model does not capture.

## Possible extensions

- Interaction terms (for example, income x density) and regularised models
- Spatial analysis, if location coordinates are available
- Business metrics, such as profit per site, as the outcome

## Repository contents

```
.
├── README.md
├── Store_Location_and_Revenue_Improved.ipynb
├── requirements.txt
└── data/              # location_rev.csv goes here (see data note)
```

**Data note:** I have not included `location_rev.csv` because I am not certain I am permitted to redistribute it. If you have a copy, place it in `data/` and adjust the path in the first data cell.

## Running it

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-learn jupyter
jupyter notebook Store_Location_and_Revenue_Improved.ipynb
```

## Author

**Opoku Asante Emmanuel**, Data Scientist and Financial Economist

Email: vkpoasante@gmail.com

## License
**MIT**
