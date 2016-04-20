---
title: "From midata regression to nicely formatted tables"
layout: post
---

# From midata regression to nicely formatted tables

# `rms`regression to tables for manuscript

Advantage of this method is that is gives access to all the great plot and summary functions in the `rms` package. Disadvantage - not easy to get p-values for coefficients in summary table. 




## Logistic regression

1. Make logistic regression model 


```r
# creating rms logistic regression object
log.rms <- fit.mult.impute(youth_desc_dik ~ gender + c_4_age_at_completion + 
                                                fam_edu +
                                                ethn_self + single_parent + 
                                                mumwork +
                                                dadwork +
                                                own_edu + own_work, fitter=lrm, mi.ses_mhp2, data=ses_mhp)
```

2. Create summary object - `not including ODDS-RATIOS`
  a. This is obtained by setting `antilog = FALSE` in the `summary.rms` command.

```r
# sumary of logistic regression object
s.log.rms <- summary(fmi2, fam_edu="Higher", gender="M", c_4_age_at_completion=c(16.7,18.1),vnames=c("labels"), antilog = FALSE) 
```

3. This creates a normal summary object that we can handle


```r
# s.log.rms # uncomment to check
```

4. Convert this to a dataframe to be able to select as per normal operations


```r
df.s.log.rms <- as.data.frame(s.log.rms)
names(df.s.log.rms)
```

```
## [1] "Low"        "High"       "Diff."      "Effect"     "S.E."      
## [6] "Lower 0.95" "Upper 0.95" "Type"
```

5. Odds ratios and their confidence intervals are obtained in the usual way by calling `exp` on the coefs.

```r
df.s.log.rms$OR <- exp(df.s.log.rms$"Effect")
df.s.log.rms$OR_upr <- exp(df.s.log.rms$"Upper 0.95") # Making 95CI of ORS
df.s.log.rms$OR_lwr <- exp(df.s.log.rms$"Lower 0.95") # Making 95CI of ORS
# df.s.log.rms # Uncomment to check results are as expected
```

6. Selective output for report

```r
rms.table_foundation <- df.s.log.rms[,c(4,5,9:11)] # Only retrieving the interesting values
```

7. Using `round` and `paste` to prepare columns


```r
rms.table_foundation <- format(round(rms.table_foundation, 2), nsmall = 2) # Rounding to two - exactly two digits 

# Pasting estimates and brackets together
rms.table_foundation$est.se <- (paste0(rms.table_foundation$Effect, " (", rms.table_foundation$S.E., ")" )) 
                  
rms.table_foundation$OR.95CI <- (paste0(rms.table_foundation$OR, " (", rms.table_foundation$OR_lwr, "--", # Pasting OR and CI
                rms.table_foundation$OR_upr,")"))                                  # into column

rms.table_foundation # uncomment to check
```

**results hidden**
```

```r
rms.final.lrm.table <- rms.table_foundation[,6:7]

colnames(rms.final.lrm.table) <- c("b (se)", "OR (95 CI Lower--Upper)")
```

8. Returning interesting variable (last two columns)

```r
# Plain text
# rms.final.lrm.table
# Markdown using kable from knitr
kable(rms.final.lrm.table)
```
**results hidden**

```r
print(xtable(rms.final.lrm.table),type="html", html.table.attributes="class='table'") # returning the same using xtable in html format
```
**results hidden**


9. Model comparisons


```r
rms.final.lrm.small <- rms.final.lrm.table[1:5,] # making small table as subset of larger table
#cbind.fill(rms.final.lrm.small, rms.final.lrm.table, fill="")
kable(cbind.fill(rms.final.lrm.small, rms.final.lrm.table, fill=""))
```
results hidden


## Linear models

1. run two lm models

```r
lm1 <- fit.mult.impute(sum.mfq ~ gender + c_4_age_at_completion + youth_described, fitter=ols, mi.ses_mhp2, data=ses_mhp)

lm2 <- fit.mult.impute(sum.mfq ~ gender + c_4_age_at_completion + youth_described + 
                                  fam_edu + own_edu + ethn_self + single_parent + 
                                  mumwork + dadwork, fitter=ols, mi.ses_mhp2, data=ses_mhp)

print(lm1)
s.lm1 <- summary(lm1)

print(lm2)
s.lm2 <- summary(lm2, fam_edu="Higher", vnames=c("labels"))
```

2. convert summaries to dataframes

```r
df.lm1 <- as.data.frame(s.lm1)
df.lm2 <- as.data.frame(s.lm2)
# names(df.lm1) # uncomment to check
```

3. round, paste and extract columns

```r
# round and formatting
df.lm1_foundation <- format(round(df.lm1, 2), nsmall = 2) # Rounding to two - exactly two digits 
df.lm2_foundation <- format(round(df.lm2, 2), nsmall = 2) # Rounding to two - exactly two digits 

# Pasting estimates and brackets together
df.lm1_foundation$"b (S.E)" <- (paste0(df.lm1_foundation$Effect, " (", df.lm1_foundation$S.E., ")" )) 
df.lm1_foundation$"Confidence interval" <- (paste(df.lm1_foundation$"Lower 0.95", "--", df.lm1_foundation$"Upper 0.95"))       
# df.lm1_foundation # uncomment to check

df.lm2_foundation$"b (S.E)" <- (paste0(df.lm2_foundation$Effect, " (", df.lm2_foundation$S.E., ")" )) 
df.lm2_foundation$"Confidence interval" <- (paste(df.lm2_foundation$"Lower 0.95", "--",df.lm2_foundation$"Upper 0.95"))       
# df.lm2_foundation # uncomment to check

# extracting columns
df.lm1.final <- df.lm1_foundation[,9:10]
df.lm2.final <- df.lm2_foundation[,9:10]
```

4. Putting it all together

```r
df.lm1_lm2_final <- cbind.fill(df.lm1.final, df.lm2.final, fill="")
```

5. Output

```r
# text
#df.lm1_lm2_final

# markdown
kable(df.lm1_lm2_final)
```
**results hidden**

```r
# xtable
print(xtable(df.lm1_lm2_final),type="html", html.table.attributes="class='table'") # returning the same using xtable in html 
```

**results hidden**


# Using `with` in mice

Advantage of this method is that is uses the normal `lm` and `glm` functions, and do not rely on external packages. 

## Pooled results from logistic regression


```r
mi.log.perceco <- with(data = mi.ses_mhp, exp = glm(youth_desc_dik ~ I(gender=="F") + c_4_age_at_completion + 
                                                I(y_4_SES_edu_mum.cat=="Elementary") +
                                                I(y_4_SES_edu_mum.cat=="Intermediate") +
                                                I(y_4_SES_edu_mum.cat=="Unknown") +
                                                I(y_4_SES_edu_dad.cat=="Elementary") +
                                                I(y_4_SES_edu_dad.cat=="Intermediate") +
                                                I(y_4_SES_edu_dad.cat=="Unknown") +
                                                I(ethn_self=="Foreign") +
                                                I(single_parent=="Single parent") + 
                                                I(mumwork=="Benefits") +
                                                I(dadwork=="Benefits") +
                                                I(own_edu=="Vocational") + 
                                                I(own_work=="Not working") , family=binomial))
#mi.log.perceco # Uncomment for checking...


p.mi.log.perceco <- pool(mi.log.perceco) # pooling across imputations

s.p.mi.log.perceco <- summary(p.mi.log.perceco) # summary of results

s.p.mi.log.perceco <- as.data.frame(s.p.mi.log.perceco) # converting to data.frame
s.p.mi.log.perceco$OR <- exp(s.p.mi.log.perceco$est) # Making ORs
s.p.mi.log.perceco$OR_upr <- exp(s.p.mi.log.perceco$"hi 95") # Making 95CI of ORS
s.p.mi.log.perceco$OR_lwr <- exp(s.p.mi.log.perceco$"lo 95") # Making 95CI of ORS

#s.p.mi.log.perceco # Uncomment for checking...

s.p.mi.log.perceco <- format(round(s.p.mi.log.perceco, 2), nsmall = 2) # Rounding to two - exactly two digits returned (nsmall)

report_log.perceco <- s.p.mi.log.perceco[,c(1,2,3,5,11,12,13)] # Returning selected columns
# Making a p-value evaluator
report_log.perceco$sig <- ifelse(report_log.perceco$`Pr(>|t|)` < .001, "***", 
                                 ifelse(report_log.perceco$`Pr(>|t|)` < .01, "** ",
                                        ifelse(report_log.perceco$`Pr(>|t|)` < .05, "* ", " ")))

#report_log.perceco # Uncomment for checking...


est.se <- (paste0(report_log.perceco$est, " (",  # Pasting estimates and brackets together
                  report_log.perceco$se, ") ",   # With s.e. and brackets into column
                  report_log.perceco$sig))


OR.95CI <- (paste0(report_log.perceco$OR, " (", report_log.perceco$OR_lwr, "--", # Pasting OR and CI
                report_log.perceco$OR_upr,")"))                                  # into column

NEW_Table2 <- cbind(round(report_log.perceco[,0],3), est.se, OR.95CI) # Putting it all together

colnames(NEW_Table2) <- c("b (se)", "OR (95 CI)")  # Naming columns

rownames(NEW_Table2) <-  c("Intercept",   # Adding proper rownames to table
  "Gender: Female",
  "Age",
  "Maternal education: Elementary",
  "Maternal education: Intermediate",
  "Maternal education: Unknonwn",
  "Paternal education: Elementary",
  "Paternal education: Intermediate",
  "Paternal education: Unknonwn",
  "Ethnicit: Foreign",
  "Family structure: Single parent",
  "Maternal work status: Benefits",
  "Paternal work status: Benefits",
  "Own education: Vocational studies",
  "Own work status: Not working")
```


```r
NEW_Table2  # returning as plain text
```

```
**results hidden**
```

```r
kable(NEW_Table2, caption="Predictors of Poor rating of perceived family economy") # returning as markdown table - using kable function from knitr
```

**results hidden**

```r
print(xtable(NEW_Table2),type="html", html.table.attributes="class='table'") # returning the same using xtable in html format
```

**results hidden**

# Presenting side-by-side models using `rowr::cbind.fill`

**Problem:** print nice model comparisons using imputed data. 

**Case:** using regression and testing predictors adjusted for covariates. 

In order to print the tables side by side:

1. Simplest solution appears to be using the `rowr` package. 


```r
NEW_Table1 <- NEW_Table2[1:5,] # Making table1 as subset of table2 for show
Table1_2 <- cbind.fill(NEW_Table1, NEW_Table2, fill="") # Specifies that the two tables should be combined and that empty cells are filled with nothing " "
```

 Kable solution

```r
kable(Table1_2, caption="Two models side by side in markdown")
```


**results hidden**


Xtable solution

```r
print(xtable(Table1_2),type="html", html.table.attributes="class='table'") # returning the same using xtable in html format
```

**results hidden**