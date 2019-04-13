How does previous level of degree influence your experience in MDS?
================

Contributors
------------

-   Richard Zitomer, `rzitomer`
-   Luo Yang, `lyiris22`

Introduction
------------

In this project, we want to find how does a person's previous level of degree attainment influence how difficult they find the MDS program. The data we use is collected from current MDS students anonymously. Throughout the analysis, we will study the relationship between levels of degree and difficulties people feel, as well as other confounding variables, such as major, working experience and programming experience. We will fit a proportional odds model to analyze the data.

Survey Design
-------------

We use the service provided by [Hosted in Canada Surveys](https://www.hostedincanadasurveys.ca) to run our survey. According to [UBC Research Ethnics](https://ethics.research.ubc.ca/sites/ore.ubc.ca/files/documents/Online_Survey-GN.pdf), the data collected from the survey are stored in Canada, and the raw data would not be uploaded to public. Besides, we have a cover letter to disclose the potential risk of re-identification of the anonymous data when combining with application information in the MDS program. The survey is expected to be taken by current MDS students, and can be find here: [MDS Survey](https://trial.hostedincanadasurveys.ca/index.php/689877).

We have 5 questions in the survey, collecting the following information of the survey takers:

1.  The highest degree obtained before MDS
2.  The major of the most recent degree
3.  Whether they have programming experience
4.  How related is the work experience related to MDS
5.  How difficult they found the MDS program

The first question provides information of the main predictor, the level of degree. The respond of the last question would serve as the response. Questions 2-4 are related to the confounding variables, which are the major of the most recent degree, programming experience, and work experience.

We assume that the background in specific majors, such as computer science, statistics and mathematics, might affect both how difficult one finds the program and the likelihood of a student having a more advanced degree. Also, work experience might makes it less likely that a student has received a degree higher than a Bachelor’s, while prior programming experience might make the courses that requires coding easier. Therefore, we want to collect data from these aspects to study their relationship with the main prediction variable and the response.

To maximize the user's experience, all the questions are multiple choices, which is easier to take and less time consuming. We also considered to avoid asking questions that might be sensitive or have risk of involving in discrimination issues.

Data Collection
---------------

We have collected 56 observations from the survey. The data is stored in the private repo in github.ubc.ca, which can be found here: [survey\_results.csv](https://github.ubc.ca/MDS-2018-19/554_survey_results_rz_iy/blob/master/survey_results.csv).

For analysis, we carry out some basic data cleaning, providing the following data frame:

    ##   master         deg deg_cs deg_eng deg_stat deg_math prog_exp work_exp
    ## 1      1 engineering      0       1        0        0        1        1
    ## 2      0       other      0       0        0        0        1        1
    ## 3      0          cs      1       0        0        0        1        3
    ## 4      0       other      0       0        0        0        1        4
    ## 5      0        math      0       0        0        1        1        2
    ## 6      0 engineering      0       1        0        0        1        3
    ##   diff
    ## 1    3
    ## 2    3
    ## 3    4
    ## 4    4
    ## 5    2
    ## 6    2

The variables extract the following information from the survey takers:

-   `master`: whether the highest degree is more than Bachelors or not
-   `deg`: the major of the most recent degree
-   `deg_cs`: whether the most recent degree is related to computer science or not
-   `deg_stat`: whether the most recent degree is related to statistics or not
-   `deg_math`: whether the most recent degree is related to mathematics or not
-   `prog_exp`: whether having programming experience or not
-   `work_exp`: how the working experience is related to MDS (4 - very related, 3 - somewhat related, 2 - not related, 1 - no work experience)
-   `diff`: how diffcult they feel in MDS program (5 - very difficult, 4 - difficult, 3 - neutral, 2 - easy, 1 - very easy)

Exploratory Data Analysis
-------------------------

The plots below is based on the generated clean data. EDA based specifically on raw data can be found here: [554\_EDA.md](https://github.com/UBC-MDS/554_Survey/blob/master/docs/554_EDA.md)

<img src="Report_files/figure-markdown_github/EDA master-1.png" style="display: block; margin: auto;" />

From the distribution of the main variable `master`, we can see that the data is unbalanced, where the proportion of people who had received a Master's of Higher is only one third of those who had only received a Bachelor's. We will consider trying to balance the dataset in our analysis as a result of this.

<img src="Report_files/figure-markdown_github/EDA difficulty-1.png" style="display: block; margin: auto;" />

For how difficult people find the MDS program, the distribution is not normal; rather, it skews to the right, having the majority in 3 (Neutral) and 4 (Difficult). This result shows that most people find this program at least somewhat difficult.

<img src="Report_files/figure-markdown_github/EDA majors-1.png" style="display: block; margin: auto;" />

The boxplots with jitter show the distribution of majors along the level of degree as well the level of how difficult people find the program. For most of the current students in the program, their recent major is not related to computer science, statistics nor mathematics. It's hard to see the relationship between major and level of difficulty in this plot.

<img src="Report_files/figure-markdown_github/EDA programming experience-1.png" style="display: block; margin: auto;" />

However, as you can see above, when we compare those with programming experience and those do not, there is a more significant difference. Regardless of their previous level of degree, those who do not have programming experience seem to having more difficulty in the program. Programming experience might be an important confounding variable that needs further attention in the analysis.

Results and Anlaysis
--------------------

In order to answer our research question, we'll need to fit a model and examine the results. Because our response variable is on a Likert scale (so it's categorical and ordered), we are going to fit a proportional odds model. Our null hypothesis is that whether or not one has a Master's degree or higher doesn't effect how hard one finds the program, and our alternative hypothesis is that it does effect how hard one finds the program.

In order to fit and examine a proportional odds model, we will first need to do two things: 1) make the response variable (`diff`) into a factor and 2) make sure we can get the p-values of each predictor's signifigance from the proportional odds model (the proportional odds model implementation in R doesn't include this).

``` r
# Make response variable into a factor
clean <- clean %>% 
  mutate(diff = diff %>% 
           factor(levels = 1:5))

# Function for getting p-values for predictor signifigance
get_summary <- function(po_output) {
  summary_table <- coef(summary(po_output))
  pval <- pnorm(abs(summary_table[, "t value"]),lower.tail = FALSE)* 2
  summary_table <- cbind(summary_table, "p value" = round(pval,3))
  summary_table
}
# Note: much of the code in the function above was taken from here: https://towardsdatascience.com/implementing-and-interpreting-ordinal-logistic-regression-1ee699274cf5
```

To begin, we'll fit a model with all potential covariates.

``` r
# Fit a model that includes all covariates
everything_po <- MASS::polr(diff~master+deg_cs+deg_eng+deg_stat+deg_math+prog_exp+work_exp, data=clean)

# Examine this model
get_summary(everything_po)
```

    ## 
    ## Re-fitting to get Hessian

    ##                Value Std. Error    t value p value
    ## master    0.37366084  0.6771899  0.5517815   0.581
    ## deg_cs    0.61902424  1.0347393  0.5982418   0.550
    ## deg_eng  -1.20208555  0.8007536 -1.5011929   0.133
    ## deg_stat -0.47941527  1.0109037 -0.4742442   0.635
    ## deg_math -1.32925963  0.7724205 -1.7209016   0.085
    ## prog_exp -3.26209699  0.8687694 -3.7548479   0.000
    ## work_exp  0.04694267  0.3565699  0.1316507   0.895
    ## 1|2      -6.65754879  1.4478674 -4.5981757   0.000
    ## 2|3      -5.36158027  1.3161461 -4.0736968   0.000
    ## 3|4      -2.87637345  1.2027075 -2.3915818   0.017
    ## 4|5       0.80532248  1.0130790  0.7949257   0.427

``` r
summary(everything_po)
```

    ## 
    ## Re-fitting to get Hessian

    ## Call:
    ## MASS::polr(formula = diff ~ master + deg_cs + deg_eng + deg_stat + 
    ##     deg_math + prog_exp + work_exp, data = clean)
    ## 
    ## Coefficients:
    ##             Value Std. Error t value
    ## master    0.37366     0.6772  0.5518
    ## deg_cs    0.61902     1.0347  0.5982
    ## deg_eng  -1.20209     0.8008 -1.5012
    ## deg_stat -0.47942     1.0109 -0.4742
    ## deg_math -1.32926     0.7724 -1.7209
    ## prog_exp -3.26210     0.8688 -3.7548
    ## work_exp  0.04694     0.3566  0.1317
    ## 
    ## Intercepts:
    ##     Value   Std. Error t value
    ## 1|2 -6.6575  1.4479    -4.5982
    ## 2|3 -5.3616  1.3161    -4.0737
    ## 3|4 -2.8764  1.2027    -2.3916
    ## 4|5  0.8053  1.0131     0.7949
    ## 
    ## Residual Deviance: 114.3637 
    ## AIC: 136.3637

As you can see, this model has an AIC of 136.36. The only signifigant variables at p&lt;.05 is programming experience, which, as mentioned above, seems to closely correlate with how difficult a person finds the program. Lets look at just the effect of the predictor (`master`), the programming experience covariate (`prog_exp`) and the target (`diff`):

``` r
# Fit simpler model
just_sig_po <- MASS::polr(diff~master+prog_exp, data=clean)

# Examine this model
get_summary(just_sig_po)
```

    ## 
    ## Re-fitting to get Hessian

    ##               Value Std. Error    t value p value
    ## master    0.1173799  0.6380514  0.1839662   0.854
    ## prog_exp -2.9721093  0.8298714 -3.5814094   0.000
    ## 1|2      -5.8922988  1.0776022 -5.4679720   0.000
    ## 2|3      -4.6818535  0.9067148 -5.1635351   0.000
    ## 3|4      -2.4363535  0.7978456 -3.0536653   0.002
    ## 4|5       1.0655539  0.5817047  1.8317780   0.067

``` r
summary(just_sig_po)
```

    ## 
    ## Re-fitting to get Hessian

    ## Call:
    ## MASS::polr(formula = diff ~ master + prog_exp, data = clean)
    ## 
    ## Coefficients:
    ##            Value Std. Error t value
    ## master    0.1174     0.6381   0.184
    ## prog_exp -2.9721     0.8299  -3.581
    ## 
    ## Intercepts:
    ##     Value   Std. Error t value
    ## 1|2 -5.8923  1.0776    -5.4680
    ## 2|3 -4.6819  0.9067    -5.1635
    ## 3|4 -2.4364  0.7978    -3.0537
    ## 4|5  1.0656  0.5817     1.8318
    ## 
    ## Residual Deviance: 120.4367 
    ## AIC: 132.4367

Despite including less potential covariates, this model has a lower AIC (132.43). Note that in both model we've looked at, having a Master's or higher (when controlling for everything else) is not significant in predicting program difficulty.

Can we do even better by modeling interaction effects?

``` r
# Fit simpler model with interaction effect
just_sig_interact_po <- MASS::polr(diff~master*prog_exp, data=clean)

# Examine this model
get_summary(just_sig_interact_po)
```

    ## 
    ## Re-fitting to get Hessian

    ##                     Value Std. Error   t value p value
    ## master           1.107631  1.1028244  1.004358   0.315
    ## prog_exp        -2.619985  0.8821359 -2.970047   0.003
    ## master:prog_exp -1.493231  1.3553171 -1.101758   0.271
    ## 1|2             -5.635927  1.0979577 -5.133100   0.000
    ## 2|3             -4.425781  0.9303203 -4.757266   0.000
    ## 3|4             -2.175808  0.8227795 -2.644461   0.008
    ## 4|5              1.423488  0.7010802  2.030421   0.042

``` r
summary(just_sig_interact_po)
```

    ## 
    ## Re-fitting to get Hessian

    ## Call:
    ## MASS::polr(formula = diff ~ master * prog_exp, data = clean)
    ## 
    ## Coefficients:
    ##                  Value Std. Error t value
    ## master           1.108     1.1028   1.004
    ## prog_exp        -2.620     0.8821  -2.970
    ## master:prog_exp -1.493     1.3553  -1.102
    ## 
    ## Intercepts:
    ##     Value   Std. Error t value
    ## 1|2 -5.6359  1.0980    -5.1331
    ## 2|3 -4.4258  0.9303    -4.7573
    ## 3|4 -2.1758  0.8228    -2.6445
    ## 4|5  1.4235  0.7011     2.0304
    ## 
    ## Residual Deviance: 119.2124 
    ## AIC: 133.2124

This model has a slightly higher AIC (133.21) and the interaction effect doesn't appear significant (p-value of .271). The best model of this data still seems to be `just_sig_po`.

Because we have a large class imbalance problem (there are only 12 out of our 56 respondents with a Master's degree or higher), we also wanted to build a model using balanced data from a cohort strategy and see how the results from that model compare with the results we got above.

``` r
# Get length of y==1 to compute sample size
sample_len <- sum(clean$master==1)

# Sample as many non-masters students as there are master's students
sampled_masters0 <- clean %>% 
  filter(master==0) %>% 
  sample_n(sample_len)

masters1 <- clean %>% 
  filter(master==1)

# Finalize cohort sample
cohort <- bind_rows(sampled_masters0, masters1)
head(cohort)
```

    ##   master         deg deg_cs deg_eng deg_stat deg_math prog_exp work_exp
    ## 1      0  statistics      0       0        1        0        1        3
    ## 2      0       other      0       0        0        0        0        2
    ## 3      0        math      0       0        0        1        0        2
    ## 4      0 engineering      0       1        0        0        0        3
    ## 5      0        math      0       0        0        1        1        2
    ## 6      0        math      0       0        0        1        0        3
    ##   diff
    ## 1    3
    ## 2    3
    ## 3    5
    ## 4    3
    ## 5    4
    ## 6    4

``` r
# Fit cohort model (using the best model from above)
just_sig_po_cohort <- MASS::polr(diff~master+prog_exp, data=cohort)
```

    ## Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

``` r
# Examine this model
get_summary(just_sig_po_cohort)
```

    ## 
    ## Re-fitting to get Hessian

    ##                Value Std. Error     t value p value
    ## master   -0.02111154  0.8023252 -0.02631295   0.979
    ## prog_exp -2.12712125  0.9472900 -2.24548058   0.025
    ## 1|2      -4.79317445  1.3893767 -3.44987393   0.001
    ## 2|3      -4.79296193  1.3893023 -3.44990559   0.001
    ## 3|4      -1.75906148  0.9175926 -1.91703970   0.055
    ## 4|5       1.04914736  0.8424359  1.24537354   0.213

``` r
summary(just_sig_po_cohort)
```

    ## 
    ## Re-fitting to get Hessian

    ## Call:
    ## MASS::polr(formula = diff ~ master + prog_exp, data = cohort)
    ## 
    ## Coefficients:
    ##             Value Std. Error  t value
    ## master   -0.02111     0.8023 -0.02631
    ## prog_exp -2.12712     0.9473 -2.24548
    ## 
    ## Intercepts:
    ##     Value   Std. Error t value
    ## 1|2 -4.7932  1.3894    -3.4499
    ## 2|3 -4.7930  1.3893    -3.4499
    ## 3|4 -1.7591  0.9176    -1.9170
    ## 4|5  1.0491  0.8424     1.2454
    ## 
    ## Residual Deviance: 47.63817 
    ## AIC: 59.63817

As you can see above, using this cohort strategy doesn't change our fundemental understanding of the relationship between the variables. Having programming experience still corresponds to finding the program easier, and we still fail to reject the null hypothesis that having a Master's or higher corresponds with how difficult one finds the program.

Conclusion
----------

We failed to reject the null hypothesis that having a Master's degree or higher changes how difficult a person finds the program.

#### Next Steps/Future Improvements

-   This study used self-reported difficulty to get at how difficult MDS was for each student. This could have led to potential biases (people could over- or understate how difficult they found MDS). A follow-up study could help correct this issue by looking at students' grades as well, which might give a more unbiased view of how easy or difficult a time students are having with the program.
-   There are other potential covariates that we didn't consider that we could have collected. In particular, the amount of time a person has to dedicate to the program might influence their perceived difficulty of the program.
-   We had very few respondents who had a Master's or higher. We only polled current MDS students, but a follow-up study might want to include alumni of the program as well to get more people with at least a Master's.
