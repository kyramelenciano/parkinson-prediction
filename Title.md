Baseline Models and Performance Evaluation
================
Kyra Nicole Melenciano Feliz

-   [Abstract](#abstract)
-   [I. Regression Model](#i-regression-model)
    -   [Variables Correlation](#variables-correlation)
    -   [Model Summary](#model-summary)
-   [II. Baseline Model](#ii-baseline-model)
-   [III. MAE Comparison](#iii-mae-comparison)

#### Abstract

This report centers around the prediction of the total Unified
Parkinson’s Disease Rating Scale (total\_UPDRS) by partitioning the
parkinsons dataset using an 80:20 ratio into training and testing sets,
respectively.

The report is organized as follows. Section I contains the regression
model, including an explanation of the correlative nature of the
variables and the model summary with the results corresponding to each
predictor. Section II describes the baseline model. Section III includes
a comparison of the MAE for the regression and baseline models and the
answer to whether or not our regression model outperforms the baseline
model.

#### I. Regression Model

Before diving into model creation, the cleaned parkinsons dataset was
uploaded to R and partitioned into a training and testing set using an
80:20 ratio. The model will predict ‘total\_UPDRS’ with the predictors
‘age’, ‘sex’, ‘test\_time’, ‘RPDE’, ‘NHR’, ‘HNR’, ‘DFA’, ‘PPE’.
Recalling from the first assignment, all of these are continuous
variables, except for age, which is a whole number and sex, which is a
factor with 2 levels, 0 for male and 1 for female.

##### Variables Correlation

Before creating the regression model, let’s examine the correlation
between the variables. Since the response variable of our model will be
total\_UPDRS, let’s first visualize its relationship with all other
variables with correlation coefficients and scatterplots.

    ##       age test_time test_time_hr test_time_min motor_UPDRS total_UPDRS Jitter
    ## [1,] 0.31      0.08         0.08          0.08        0.95           1   0.07
    ##      Jitter.Abs Jitter.RAP Jitter.PPQ5 Jitter.DDP Shimmer Shimmer.dB
    ## [1,]       0.07       0.06        0.01       0.06    0.09        0.1
    ##      Shimmer.APQ3 Shimmer.APQ5 Shimmer.APQ11 Shimmer.DDA  NHR   HNR RPDE   DFA
    ## [1,]         0.01         0.08          0.12        0.08 0.06 -0.16 0.16 -0.11
    ##       PPE
    ## [1,] 0.16

From these correlation coefficients, we can only infer that total\_UPDRS
doesn’t follow a linear relationship with none of the rest of the
variables, except motor\_UPDRS, which is the rating for the motor
function examination of the subject, a part of the total\_UPDRS.

To check if there are any polynomial relationships with the variables
that are going to be included in the model, let’s visualize the
relationships with scatterplots.

![](Title_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Even though we can see the that the lines are trying to depict curved
relationships, by looking at the observations we can’t identify a trend
or pattern, except for motor\_UPDRS which we already know follows a
linear relationship with total\_UPDRS. The rest of the variables don’t
have much of a relationship with total\_UPDRS, as we can see the
observations are scattered all over the graph. With this information,
we’ll now use a linear regression to predict total\_UPDRS without
polynomial terms.

To check for possible multicollinearity in our model, let’s check
correlation between the predictors.

    ##             age test_time  RPDE   HNR   NHR   DFA   PPE
    ## age        1.00      0.02  0.09 -0.10  0.01 -0.09  0.12
    ## test_time  0.02      1.00 -0.04  0.04 -0.03  0.02  0.00
    ## RPDE       0.09     -0.04  1.00 -0.66  0.42  0.19  0.57
    ## HNR       -0.10      0.04 -0.66  1.00 -0.68 -0.29 -0.76
    ## NHR        0.01     -0.03  0.42 -0.68  1.00 -0.02  0.56
    ## DFA       -0.09      0.02  0.19 -0.29 -0.02  1.00  0.39
    ## PPE        0.12      0.00  0.57 -0.76  0.56  0.39  1.00

This correlation matrix shows strong correlation between RPDE and HNR
(-0.66), NHR and HNR (-0.68), and PPE and HNR (-0.76). However, to test
if this correlation translates into multicollinearity in our model,
later we’ll calculate the Variance Inflation Factors (VIF) for each
independent variable. VIFs identify correlation between independent
variables and its’ strength. A value of 1 indicates no correlation,
values between 1 and 5 indicate moderate correlation, but not enough to
warrant corrective measures, and values greater than 5 represent
critical levels of multicollinearity where the coefficients’ p-values
are questionable.

##### Model Summary

The training set was used to build a regression model predicting
total\_UPDRS using age, sex, test\_time, RPDE, NHR, HNR, DFA and PPE as
predictors. The output below shows the model summary.

    ## 
    ## Call:
    ## lm(formula = total_UPDRS ~ age + sex + test_time + RPDE + NHR + 
    ##     HNR + DFA + PPE, data = training_set)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -27.231  -7.455  -1.642   7.407  26.893 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  35.63771    3.23710  11.009  < 2e-16 ***
    ## age           0.30853    0.01665  18.529  < 2e-16 ***
    ## sex1         -1.88826    0.32451  -5.819 6.31e-09 ***
    ## test_time     0.01622    0.00266   6.099 1.15e-09 ***
    ## RPDE          2.48189    1.93687   1.281      0.2    
    ## NHR         -23.29559    3.61110  -6.451 1.22e-10 ***
    ## HNR          -0.46195    0.06493  -7.114 1.29e-12 ***
    ## DFA         -32.32538    2.39465 -13.499  < 2e-16 ***
    ## PPE          13.71251    2.61501   5.244 1.64e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 9.819 on 4790 degrees of freedom
    ## Multiple R-squared:  0.1612, Adjusted R-squared:  0.1598 
    ## F-statistic: 115.1 on 8 and 4790 DF,  p-value: < 2.2e-16

From these results we can already see that the variable RPDE is not
significant for our model, since its p-values is greater than 0.05. The
coefficient for age indicates that when everything else remains
constant, being a year older would increase approximately 0.31 points to
the total\_UPDRS. If the subject is a female that has all the same
attributes as a male, her total\_UPDRS would be approximately 1.89
points lower than his.

The model has an adjusted R-squared of 0.1598 indicating that it is able
to predict accurately approximately 16% of the times. The model is able
to predict total\_UPDRS with an average error of about 9.819.

To improve this model, let’s remove RPDE and see how the results change.

    ## 
    ## Call:
    ## lm(formula = total_UPDRS ~ age + sex + test_time + NHR + HNR + 
    ##     DFA + PPE, data = training_set)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -27.340  -7.453  -1.720   7.487  26.698 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  37.832327   2.747093  13.772  < 2e-16 ***
    ## age           0.308434   0.016652  18.522  < 2e-16 ***
    ## sex1         -1.965268   0.318916  -6.162 7.76e-10 ***
    ## test_time     0.016148   0.002659   6.073 1.36e-09 ***
    ## NHR         -23.539121   3.606340  -6.527 7.40e-11 ***
    ## HNR          -0.496643   0.059026  -8.414  < 2e-16 ***
    ## DFA         -32.555885   2.388041 -13.633  < 2e-16 ***
    ## PPE          14.149613   2.592836   5.457 5.08e-08 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 9.82 on 4791 degrees of freedom
    ## Multiple R-squared:  0.1609, Adjusted R-squared:  0.1597 
    ## F-statistic: 131.2 on 7 and 4791 DF,  p-value: < 2.2e-16

Removing RPDE only decreases the adjusted R-squared by 0.0001, proving
that this variable was not important at all for the prediction of
total\_UPDRS. Now, from our correlation analysis we know that the only
variable that has a linear relationship with total\_UPDRS is
motor\_UPDRS, which means we can add this variable to our model.

    ## 
    ## Call:
    ## lm(formula = total_UPDRS ~ age + sex + motor_UPDRS + test_time + 
    ##     NHR + HNR + DFA + PPE, data = training_set)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -8.3292 -2.0050 -0.3995  1.4684 11.7133 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  4.4590331  0.9406560   4.740  2.2e-06 ***
    ## age          0.0607685  0.0057496  10.569  < 2e-16 ***
    ## sex1        -1.5762157  0.1073728 -14.680  < 2e-16 ***
    ## motor_UPDRS  1.2189734  0.0062955 193.625  < 2e-16 ***
    ## test_time    0.0024693  0.0008979   2.750 0.005980 ** 
    ## NHR         -2.8113821  1.2186821  -2.307 0.021103 *  
    ## HNR         -0.1231227  0.0199627  -6.168  7.5e-10 ***
    ## DFA         -2.4728774  0.8187423  -3.020 0.002538 ** 
    ## PPE         -3.1337768  0.8773565  -3.572 0.000358 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.306 on 4790 degrees of freedom
    ## Multiple R-squared:  0.9049, Adjusted R-squared:  0.9048 
    ## F-statistic:  5700 on 8 and 4790 DF,  p-value: < 2.2e-16

Adding motor\_UPDRS significantly improves the model performance and now
it is able to predict total\_UPDRS with an average error of about 3.299,
much less than 9.82.

Let’s test this model for multicollinearity.

    ##         age         sex motor_UPDRS   test_time         NHR         HNR 
    ##    1.124695    1.092646    1.152463    1.010521    2.311727    3.190324 
    ##         DFA         PPE 
    ##    1.481356    2.841871

These VIFs indicate moderate correlation between the independent
variables, with HNR being the one that has the higher VIF and the most
correlated with other predictors. However, none of the VIFs is greater
than 5 so there is no need to take corrective measures.

Let’s see how the model’s results are affected by running the regression
on the test set.

    ## 
    ## Call:
    ## lm(formula = total_UPDRS ~ age + sex + motor_UPDRS + test_time + 
    ##     NHR + HNR + DFA + PPE, data = test_set)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -8.3018 -2.0261 -0.3823  1.4178 11.5144 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  3.036113   1.965690   1.545  0.12275    
    ## age          0.066272   0.011763   5.634 2.25e-08 ***
    ## sex1        -1.605085   0.222569  -7.212 1.05e-12 ***
    ## motor_UPDRS  1.227395   0.013225  92.807  < 2e-16 ***
    ## test_time    0.002412   0.001865   1.293  0.19622    
    ## NHR          0.290551   2.670864   0.109  0.91339    
    ## HNR         -0.103773   0.041007  -2.531  0.01153 *  
    ## DFA         -0.994118   1.703792  -0.583  0.55970    
    ## PPE         -5.888665   1.811425  -3.251  0.00119 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 3.253 on 1067 degrees of freedom
    ## Multiple R-squared:  0.9074, Adjusted R-squared:  0.9067 
    ## F-statistic:  1307 on 8 and 1067 DF,  p-value: < 2.2e-16

Running the regression on the test set shows that test\_time, NHR and
DFA lose statistical significance for the prediction. However, the
R-squared, adjusted R-squared and residual standard error remain pretty
similar. The residual standard error indicates that this model is able
to predict total\_UPDRS with an average error of about 3.253.

The difference between the R-squared and adjusted R-squared is that the
R-squared increases when we add more variables to the model, without
taking into consideration whether or not those variables are correlated
to the response variable. The adjusted R-squared, however, is ‘adjusted’
in a way that it increases by adding variables correlated to the
response variable and decreases when variables without a strong
correlation to the response variable are added. This means, that when
dealing with a linear regression with more than one variable, the
adjusted R-squared statistic is the one to use. In our case, the
adjusted and multiple R-squared are not far from each other and indicate
that approximately 90% of the data fits the regression model, which
applies to both the training and testing set regressions.

Let’s calculate the Mean Absolute Error (MAE) for our test set
regression model.

    ## [1] "MAE regression test set: 2.39"

The MAE for the test set regression model indicates that the average
distance between observed and predicted values is 2.39, which compared
to the test set regression’s residual standard error, 3.253, is smaller
because it is less influenced by outliers in the data set.

#### II. Baseline Model

If we take the first model we built using the training set as our
baseline model, let’s check whether or not the changes made to it
improved it.

First, let’s calculate the MAE for the baseline model using the training
set.

    ## [1] "MAE baseline: 8.12"

Now, let’s calculate the MAE for the regression model without RPDE.

    ## [1] "MAE regression training set without RPDE: 8.13"

Now, let’s calculate the MAE for the regression model without RPDE and
including motor\_UPDRS, using the training set.

    ## [1] "MAE regression training set with motor_UPDRS: 2.46"

#### III. MAE Comparison

The MAE for the baseline model is 8.12. When we remove RPDE from the
model, the MAE increases to 8.13, indicating a greater distance between
observed and predicted values. However, when we add motor\_UPDRS, the
MAE turns to be 2.46 which is 5.66 points lower than the MAE for the
baseline. This indicates that the average distance between observed and
predicted values is drastically reduced when we add motor\_UPDRS.

The MAE for the regression model for the testing set is 2.39, which is
even smaller than the MAE for the regression using the training set,
indicating that this model works better with the new data.

Based on these results, the model without RPDE does not outperform the
baseline. However, the model with motor\_UPDRS does.
