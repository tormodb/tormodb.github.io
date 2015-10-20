---
title: "From midata regression to nicely formatted tables"
layout: post
---

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

```
# Error in Design(eval.parent(m)): dataset dd not found for options(datadist=)
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
# [1] "Low"        "High"       "Diff."      "Effect"     "S.E."      
# [6] "Lower 0.95" "Upper 0.95" "Type"
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

```
#                                                   Effect S.E.   OR OR_upr
# Age                                                 0.14 0.07 1.15   1.32
# Gender - F:M                                        0.31 0.08 1.36   1.61
# Highest education in family - Elementary:Higher     0.66 0.17 1.93   2.71
# Highest education in family - Intermediate:Higher   0.28 0.10 1.33   1.61
# Highest education in family - Unknown:Higher        0.14 0.12 1.15   1.44
# Ethnicity - Foreign:Norwegian                      -0.06 0.16 0.94   1.29
# Family structure - Single parent:Two parents        1.39 0.09 4.03   4.81
# Maternal work status - Benefits:Work                1.14 0.13 3.13   4.05
# Paternal work status - Benefits:Work                1.21 0.14 3.36   4.43
# Own education - Vocational:General                  0.25 0.09 1.29   1.53
# Own work status - Working:Not working              -0.07 0.09 0.94   1.11
#                                                   OR_lwr       est.se
# Age                                                 0.99  0.14 (0.07)
# Gender - F:M                                        1.16  0.31 (0.08)
# Highest education in family - Elementary:Higher     1.37  0.66 (0.17)
# Highest education in family - Intermediate:Higher   1.09  0.28 (0.10)
# Highest education in family - Unknown:Higher        0.92  0.14 (0.12)
# Ethnicity - Foreign:Norwegian                       0.68 -0.06 (0.16)
# Family structure - Single parent:Two parents        3.37  1.39 (0.09)
# Maternal work status - Benefits:Work                2.42  1.14 (0.13)
# Paternal work status - Benefits:Work                2.54  1.21 (0.14)
# Own education - Vocational:General                  1.08  0.25 (0.09)
# Own work status - Working:Not working               0.79 -0.07 (0.09)
#                                                             OR.95CI
# Age                                               1.15 (0.99--1.32)
# Gender - F:M                                      1.36 (1.16--1.61)
# Highest education in family - Elementary:Higher   1.93 (1.37--2.71)
# Highest education in family - Intermediate:Higher 1.33 (1.09--1.61)
# Highest education in family - Unknown:Higher      1.15 (0.92--1.44)
# Ethnicity - Foreign:Norwegian                     0.94 (0.68--1.29)
# Family structure - Single parent:Two parents      4.03 (3.37--4.81)
# Maternal work status - Benefits:Work              3.13 (2.42--4.05)
# Paternal work status - Benefits:Work              3.36 (2.54--4.43)
# Own education - Vocational:General                1.29 (1.08--1.53)
# Own work status - Working:Not working             0.94 (0.79--1.11)
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



|                                                  |b (se)       |OR (95 CI Lower--Upper) |
|:-------------------------------------------------|:------------|:-----------------------|
|Age                                               |0.14 (0.07)  |1.15 (0.99--1.32)       |
|Gender - F:M                                      |0.31 (0.08)  |1.36 (1.16--1.61)       |
|Highest education in family - Elementary:Higher   |0.66 (0.17)  |1.93 (1.37--2.71)       |
|Highest education in family - Intermediate:Higher |0.28 (0.10)  |1.33 (1.09--1.61)       |
|Highest education in family - Unknown:Higher      |0.14 (0.12)  |1.15 (0.92--1.44)       |
|Ethnicity - Foreign:Norwegian                     |-0.06 (0.16) |0.94 (0.68--1.29)       |
|Family structure - Single parent:Two parents      |1.39 (0.09)  |4.03 (3.37--4.81)       |
|Maternal work status - Benefits:Work              |1.14 (0.13)  |3.13 (2.42--4.05)       |
|Paternal work status - Benefits:Work              |1.21 (0.14)  |3.36 (2.54--4.43)       |
|Own education - Vocational:General                |0.25 (0.09)  |1.29 (1.08--1.53)       |
|Own work status - Working:Not working             |-0.07 (0.09) |0.94 (0.79--1.11)       |


```r
print(xtable(rms.final.lrm.table),type="html", html.table.attributes="class='table'") # returning the same using xtable in html format
```

<!-- html table generated in R 3.2.2 by xtable 1.7-4 package -->
<!-- Tue Oct 20 13:32:30 2015 -->
<table class='table'>
<tr> <th>  </th> <th> b (se) </th> <th> OR (95 CI Lower--Upper) </th>  </tr>
  <tr> <td align="right"> Age </td> <td>  0.14 (0.07) </td> <td> 1.15 (0.99--1.32) </td> </tr>
  <tr> <td align="right"> Gender - F:M </td> <td>  0.31 (0.08) </td> <td> 1.36 (1.16--1.61) </td> </tr>
  <tr> <td align="right"> Highest education in family - Elementary:Higher </td> <td>  0.66 (0.17) </td> <td> 1.93 (1.37--2.71) </td> </tr>
  <tr> <td align="right"> Highest education in family - Intermediate:Higher </td> <td>  0.28 (0.10) </td> <td> 1.33 (1.09--1.61) </td> </tr>
  <tr> <td align="right"> Highest education in family - Unknown:Higher </td> <td>  0.14 (0.12) </td> <td> 1.15 (0.92--1.44) </td> </tr>
  <tr> <td align="right"> Ethnicity - Foreign:Norwegian </td> <td> -0.06 (0.16) </td> <td> 0.94 (0.68--1.29) </td> </tr>
  <tr> <td align="right"> Family structure - Single parent:Two parents </td> <td>  1.39 (0.09) </td> <td> 4.03 (3.37--4.81) </td> </tr>
  <tr> <td align="right"> Maternal work status - Benefits:Work </td> <td>  1.14 (0.13) </td> <td> 3.13 (2.42--4.05) </td> </tr>
  <tr> <td align="right"> Paternal work status - Benefits:Work </td> <td>  1.21 (0.14) </td> <td> 3.36 (2.54--4.43) </td> </tr>
  <tr> <td align="right"> Own education - Vocational:General </td> <td>  0.25 (0.09) </td> <td> 1.29 (1.08--1.53) </td> </tr>
  <tr> <td align="right"> Own work status - Working:Not working </td> <td> -0.07 (0.09) </td> <td> 0.94 (0.79--1.11) </td> </tr>
   </table>

9. Model comparisons


```r
rms.final.lrm.small <- rms.final.lrm.table[1:5,] # making small table as subset of larger table
#cbind.fill(rms.final.lrm.small, rms.final.lrm.table, fill="")
kable(cbind.fill(rms.final.lrm.small, rms.final.lrm.table, fill=""))
```



|                                                  |b..se.      |OR..95.CI.Lower..Upper. |b..se.       |OR..95.CI.Lower..Upper. |
|:-------------------------------------------------|:-----------|:-----------------------|:------------|:-----------------------|
|Age                                               |0.14 (0.07) |1.15 (0.99--1.32)       |0.14 (0.07)  |1.15 (0.99--1.32)       |
|Gender - F:M                                      |0.31 (0.08) |1.36 (1.16--1.61)       |0.31 (0.08)  |1.36 (1.16--1.61)       |
|Highest education in family - Elementary:Higher   |0.66 (0.17) |1.93 (1.37--2.71)       |0.66 (0.17)  |1.93 (1.37--2.71)       |
|Highest education in family - Intermediate:Higher |0.28 (0.10) |1.33 (1.09--1.61)       |0.28 (0.10)  |1.33 (1.09--1.61)       |
|Highest education in family - Unknown:Higher      |0.14 (0.12) |1.15 (0.92--1.44)       |0.14 (0.12)  |1.15 (0.92--1.44)       |
|Ethnicity - Foreign:Norwegian                     |            |                        |-0.06 (0.16) |0.94 (0.68--1.29)       |
|Family structure - Single parent:Two parents      |            |                        |1.39 (0.09)  |4.03 (3.37--4.81)       |
|Maternal work status - Benefits:Work              |            |                        |1.14 (0.13)  |3.13 (2.42--4.05)       |
|Paternal work status - Benefits:Work              |            |                        |1.21 (0.14)  |3.36 (2.54--4.43)       |
|Own education - Vocational:General                |            |                        |0.25 (0.09)  |1.29 (1.08--1.53)       |
|Own work status - Working:Not working             |            |                        |-0.07 (0.09) |0.94 (0.79--1.11)       |

## Linear models

1. run two lm models

```r
lm1 <- fit.mult.impute(sum.mfq ~ gender + c_4_age_at_completion + youth_described, fitter=ols, mi.ses_mhp2, data=ses_mhp)
```

```
# Error in Design(X): dataset dd not found for options(datadist=)
```

```r
lm2 <- fit.mult.impute(sum.mfq ~ gender + c_4_age_at_completion + youth_described + 
                                  fam_edu + own_edu + ethn_self + single_parent + 
                                  mumwork + dadwork, fitter=ols, mi.ses_mhp2, data=ses_mhp)
```

```
# Error in Design(X): dataset dd not found for options(datadist=)
```

```r
print(lm1)
```

```
# Error in print(lm1): object 'lm1' not found
```

```r
s.lm1 <- summary(lm1)
```

```
# Error in summary(lm1): error in evaluating the argument 'object' in selecting a method for function 'summary': Error: object 'lm1' not found
```

```r
print(lm2)
```

```
# Error in print(lm2): object 'lm2' not found
```

```r
s.lm2 <- summary(lm2, fam_edu="Higher", vnames=c("labels"))
```

```
# Error in summary(lm2, fam_edu = "Higher", vnames = c("labels")): error in evaluating the argument 'object' in selecting a method for function 'summary': Error: object 'lm2' not found
```

2. convert summaries to dataframes

```r
df.lm1 <- as.data.frame(s.lm1)
```

```
# Error in as.data.frame(s.lm1): object 's.lm1' not found
```

```r
df.lm2 <- as.data.frame(s.lm2)
```

```
# Error in as.data.frame(s.lm2): object 's.lm2' not found
```

```r
# names(df.lm1) # uncomment to check
```

3. round, paste and extract columns

```r
# round and formatting
df.lm1_foundation <- format(round(df.lm1, 2), nsmall = 2) # Rounding to two - exactly two digits 
```

```
# Error in format(round(df.lm1, 2), nsmall = 2): object 'df.lm1' not found
```

```r
df.lm2_foundation <- format(round(df.lm2, 2), nsmall = 2) # Rounding to two - exactly two digits 
```

```
# Error in format(round(df.lm2, 2), nsmall = 2): object 'df.lm2' not found
```

```r
# Pasting estimates and brackets together
df.lm1_foundation$"b (S.E)" <- (paste0(df.lm1_foundation$Effect, " (", df.lm1_foundation$S.E., ")" )) 
```

```
# Error in paste0(df.lm1_foundation$Effect, " (", df.lm1_foundation$S.E., : object 'df.lm1_foundation' not found
```

```r
df.lm1_foundation$"Confidence interval" <- (paste(df.lm1_foundation$"Lower 0.95", "--", df.lm1_foundation$"Upper 0.95"))       
```

```
# Error in paste(df.lm1_foundation$"Lower 0.95", "--", df.lm1_foundation$"Upper 0.95"): object 'df.lm1_foundation' not found
```

```r
# df.lm1_foundation # uncomment to check

df.lm2_foundation$"b (S.E)" <- (paste0(df.lm2_foundation$Effect, " (", df.lm2_foundation$S.E., ")" )) 
```

```
# Error in paste0(df.lm2_foundation$Effect, " (", df.lm2_foundation$S.E., : object 'df.lm2_foundation' not found
```

```r
df.lm2_foundation$"Confidence interval" <- (paste(df.lm2_foundation$"Lower 0.95", "--",df.lm2_foundation$"Upper 0.95"))       
```

```
# Error in paste(df.lm2_foundation$"Lower 0.95", "--", df.lm2_foundation$"Upper 0.95"): object 'df.lm2_foundation' not found
```

```r
# df.lm2_foundation # uncomment to check

# extracting columns
df.lm1.final <- df.lm1_foundation[,9:10]
```

```
# Error in eval(expr, envir, enclos): object 'df.lm1_foundation' not found
```

```r
df.lm2.final <- df.lm2_foundation[,9:10]
```

```
# Error in eval(expr, envir, enclos): object 'df.lm2_foundation' not found
```

4. Putting it all together

```r
df.lm1_lm2_final <- cbind.fill(df.lm1.final, df.lm2.final, fill="")
```

```
# Error in cbind.fill(df.lm1.final, df.lm2.final, fill = ""): object 'df.lm1.final' not found
```

5. Output

```r
# text
#df.lm1_lm2_final

# markdown
kable(df.lm1_lm2_final)
```

```
# Error in is.data.frame(x): object 'df.lm1_lm2_final' not found
```


```r
# xtable
print(xtable(df.lm1_lm2_final),type="html", html.table.attributes="class='table'") # returning the same using xtable in html 
```

```
# Error in xtable(df.lm1_lm2_final): object 'df.lm1_lm2_final' not found
```

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
#                                             b (se)        OR (95 CI)
# Intercept                         -5.61 (0.93) *** 0.00 (0.00--0.02)
# Gender: Female                     0.30 (0.08) *** 1.36 (1.15--1.60)
# Age                                  0.10 (0.05)   1.11 (1.00--1.22)
# Maternal education: Elementary       0.18 (0.15)   1.20 (0.89--1.62)
# Maternal education: Intermediate    -0.06 (0.12)   0.94 (0.75--1.18)
# Maternal education: Unknonwn        -0.13 (0.15)   0.88 (0.66--1.17)
# Paternal education: Elementary     0.82 (0.17) *** 2.28 (1.64--3.15)
# Paternal education: Intermediate   0.49 (0.13) *** 1.64 (1.28--2.10)
# Paternal education: Unknonwn       0.59 (0.16) *** 1.80 (1.32--2.45)
# Ethnicit: Foreign                   -0.09 (0.16)   0.91 (0.66--1.26)
# Family structure: Single parent    1.31 (0.09) *** 3.72 (3.12--4.44)
# Maternal work status: Benefits     1.19 (0.15) *** 3.28 (2.42--4.45)
# Paternal work status: Benefits     1.23 (0.16) *** 3.42 (2.45--4.76)
# Own education: Vocational studies   0.20 (0.09) *  1.22 (1.03--1.45)
# Own work status: Not working         0.05 (0.09)   1.05 (0.89--1.25)
```

```r
kable(NEW_Table2, caption="Predictors of Poor rating of perceived family economy") # returning as markdown table - using kable function from knitr
```



|                                  |b (se)           |OR (95 CI)        |
|:---------------------------------|:----------------|:-----------------|
|Intercept                         |-5.61 (0.93) *** |0.00 (0.00--0.02) |
|Gender: Female                    |0.30 (0.08) ***  |1.36 (1.15--1.60) |
|Age                               |0.10 (0.05)      |1.11 (1.00--1.22) |
|Maternal education: Elementary    |0.18 (0.15)      |1.20 (0.89--1.62) |
|Maternal education: Intermediate  |-0.06 (0.12)     |0.94 (0.75--1.18) |
|Maternal education: Unknonwn      |-0.13 (0.15)     |0.88 (0.66--1.17) |
|Paternal education: Elementary    |0.82 (0.17) ***  |2.28 (1.64--3.15) |
|Paternal education: Intermediate  |0.49 (0.13) ***  |1.64 (1.28--2.10) |
|Paternal education: Unknonwn      |0.59 (0.16) ***  |1.80 (1.32--2.45) |
|Ethnicit: Foreign                 |-0.09 (0.16)     |0.91 (0.66--1.26) |
|Family structure: Single parent   |1.31 (0.09) ***  |3.72 (3.12--4.44) |
|Maternal work status: Benefits    |1.19 (0.15) ***  |3.28 (2.42--4.45) |
|Paternal work status: Benefits    |1.23 (0.16) ***  |3.42 (2.45--4.76) |
|Own education: Vocational studies |0.20 (0.09) *    |1.22 (1.03--1.45) |
|Own work status: Not working      |0.05 (0.09)      |1.05 (0.89--1.25) |


```r
print(xtable(NEW_Table2),type="html", html.table.attributes="class='table'") # returning the same using xtable in html format
```

<!-- html table generated in R 3.2.2 by xtable 1.7-4 package -->
<!-- Tue Oct 20 13:32:31 2015 -->
<table class='table'>
<tr> <th>  </th> <th> b (se) </th> <th> OR (95 CI) </th>  </tr>
  <tr> <td align="right"> Intercept </td> <td> -5.61 (0.93) *** </td> <td> 0.00 (0.00--0.02) </td> </tr>
  <tr> <td align="right"> Gender: Female </td> <td>  0.30 (0.08) *** </td> <td> 1.36 (1.15--1.60) </td> </tr>
  <tr> <td align="right"> Age </td> <td>  0.10 (0.05)   </td> <td> 1.11 (1.00--1.22) </td> </tr>
  <tr> <td align="right"> Maternal education: Elementary </td> <td>  0.18 (0.15)   </td> <td> 1.20 (0.89--1.62) </td> </tr>
  <tr> <td align="right"> Maternal education: Intermediate </td> <td> -0.06 (0.12)   </td> <td> 0.94 (0.75--1.18) </td> </tr>
  <tr> <td align="right"> Maternal education: Unknonwn </td> <td> -0.13 (0.15)   </td> <td> 0.88 (0.66--1.17) </td> </tr>
  <tr> <td align="right"> Paternal education: Elementary </td> <td>  0.82 (0.17) *** </td> <td> 2.28 (1.64--3.15) </td> </tr>
  <tr> <td align="right"> Paternal education: Intermediate </td> <td>  0.49 (0.13) *** </td> <td> 1.64 (1.28--2.10) </td> </tr>
  <tr> <td align="right"> Paternal education: Unknonwn </td> <td>  0.59 (0.16) *** </td> <td> 1.80 (1.32--2.45) </td> </tr>
  <tr> <td align="right"> Ethnicit: Foreign </td> <td> -0.09 (0.16)   </td> <td> 0.91 (0.66--1.26) </td> </tr>
  <tr> <td align="right"> Family structure: Single parent </td> <td>  1.31 (0.09) *** </td> <td> 3.72 (3.12--4.44) </td> </tr>
  <tr> <td align="right"> Maternal work status: Benefits </td> <td>  1.19 (0.15) *** </td> <td> 3.28 (2.42--4.45) </td> </tr>
  <tr> <td align="right"> Paternal work status: Benefits </td> <td>  1.23 (0.16) *** </td> <td> 3.42 (2.45--4.76) </td> </tr>
  <tr> <td align="right"> Own education: Vocational studies </td> <td>  0.20 (0.09) *  </td> <td> 1.22 (1.03--1.45) </td> </tr>
  <tr> <td align="right"> Own work status: Not working </td> <td>  0.05 (0.09)   </td> <td> 1.05 (0.89--1.25) </td> </tr>
   </table>


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



|                                  |b..se.           |OR..95.CI.        |b..se.           |OR..95.CI.        |
|:---------------------------------|:----------------|:-----------------|:----------------|:-----------------|
|Intercept                         |-5.61 (0.93) *** |0.00 (0.00--0.02) |-5.61 (0.93) *** |0.00 (0.00--0.02) |
|Gender: Female                    |0.30 (0.08) ***  |1.36 (1.15--1.60) |0.30 (0.08) ***  |1.36 (1.15--1.60) |
|Age                               |0.10 (0.05)      |1.11 (1.00--1.22) |0.10 (0.05)      |1.11 (1.00--1.22) |
|Maternal education: Elementary    |0.18 (0.15)      |1.20 (0.89--1.62) |0.18 (0.15)      |1.20 (0.89--1.62) |
|Maternal education: Intermediate  |-0.06 (0.12)     |0.94 (0.75--1.18) |-0.06 (0.12)     |0.94 (0.75--1.18) |
|Maternal education: Unknonwn      |                 |                  |-0.13 (0.15)     |0.88 (0.66--1.17) |
|Paternal education: Elementary    |                 |                  |0.82 (0.17) ***  |2.28 (1.64--3.15) |
|Paternal education: Intermediate  |                 |                  |0.49 (0.13) ***  |1.64 (1.28--2.10) |
|Paternal education: Unknonwn      |                 |                  |0.59 (0.16) ***  |1.80 (1.32--2.45) |
|Ethnicit: Foreign                 |                 |                  |-0.09 (0.16)     |0.91 (0.66--1.26) |
|Family structure: Single parent   |                 |                  |1.31 (0.09) ***  |3.72 (3.12--4.44) |
|Maternal work status: Benefits    |                 |                  |1.19 (0.15) ***  |3.28 (2.42--4.45) |
|Paternal work status: Benefits    |                 |                  |1.23 (0.16) ***  |3.42 (2.45--4.76) |
|Own education: Vocational studies |                 |                  |0.20 (0.09) *    |1.22 (1.03--1.45) |
|Own work status: Not working      |                 |                  |0.05 (0.09)      |1.05 (0.89--1.25) |

Xtable solution

```r
print(xtable(Table1_2),type="html", html.table.attributes="class='table'") # returning the same using xtable in html format
```

<!-- html table generated in R 3.2.2 by xtable 1.7-4 package -->
<!-- Tue Oct 20 13:32:31 2015 -->
<table class='table'>
<tr> <th>  </th> <th> b..se. </th> <th> OR..95.CI. </th> <th> b..se. </th> <th> OR..95.CI. </th>  </tr>
  <tr> <td align="right"> Intercept </td> <td> -5.61 (0.93) *** </td> <td> 0.00 (0.00--0.02) </td> <td> -5.61 (0.93) *** </td> <td> 0.00 (0.00--0.02) </td> </tr>
  <tr> <td align="right"> Gender: Female </td> <td>  0.30 (0.08) *** </td> <td> 1.36 (1.15--1.60) </td> <td>  0.30 (0.08) *** </td> <td> 1.36 (1.15--1.60) </td> </tr>
  <tr> <td align="right"> Age </td> <td>  0.10 (0.05)   </td> <td> 1.11 (1.00--1.22) </td> <td>  0.10 (0.05)   </td> <td> 1.11 (1.00--1.22) </td> </tr>
  <tr> <td align="right"> Maternal education: Elementary </td> <td>  0.18 (0.15)   </td> <td> 1.20 (0.89--1.62) </td> <td>  0.18 (0.15)   </td> <td> 1.20 (0.89--1.62) </td> </tr>
  <tr> <td align="right"> Maternal education: Intermediate </td> <td> -0.06 (0.12)   </td> <td> 0.94 (0.75--1.18) </td> <td> -0.06 (0.12)   </td> <td> 0.94 (0.75--1.18) </td> </tr>
  <tr> <td align="right"> Maternal education: Unknonwn </td> <td>  </td> <td>  </td> <td> -0.13 (0.15)   </td> <td> 0.88 (0.66--1.17) </td> </tr>
  <tr> <td align="right"> Paternal education: Elementary </td> <td>  </td> <td>  </td> <td>  0.82 (0.17) *** </td> <td> 2.28 (1.64--3.15) </td> </tr>
  <tr> <td align="right"> Paternal education: Intermediate </td> <td>  </td> <td>  </td> <td>  0.49 (0.13) *** </td> <td> 1.64 (1.28--2.10) </td> </tr>
  <tr> <td align="right"> Paternal education: Unknonwn </td> <td>  </td> <td>  </td> <td>  0.59 (0.16) *** </td> <td> 1.80 (1.32--2.45) </td> </tr>
  <tr> <td align="right"> Ethnicit: Foreign </td> <td>  </td> <td>  </td> <td> -0.09 (0.16)   </td> <td> 0.91 (0.66--1.26) </td> </tr>
  <tr> <td align="right"> Family structure: Single parent </td> <td>  </td> <td>  </td> <td>  1.31 (0.09) *** </td> <td> 3.72 (3.12--4.44) </td> </tr>
  <tr> <td align="right"> Maternal work status: Benefits </td> <td>  </td> <td>  </td> <td>  1.19 (0.15) *** </td> <td> 3.28 (2.42--4.45) </td> </tr>
  <tr> <td align="right"> Paternal work status: Benefits </td> <td>  </td> <td>  </td> <td>  1.23 (0.16) *** </td> <td> 3.42 (2.45--4.76) </td> </tr>
  <tr> <td align="right"> Own education: Vocational studies </td> <td>  </td> <td>  </td> <td>  0.20 (0.09) *  </td> <td> 1.22 (1.03--1.45) </td> </tr>
  <tr> <td align="right"> Own work status: Not working </td> <td>  </td> <td>  </td> <td>  0.05 (0.09)   </td> <td> 1.05 (0.89--1.25) </td> </tr>
   </table>
