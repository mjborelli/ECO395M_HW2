A Hospital Audit
================

Introduction
------------

The main goal of this statistical analysis is to audit the performance
of your radiologists for mammogram screenings. Each doctor wants to
limit the amount of false positives and false negatives as much as
possible, but mammograms are not perfect. Therefore, it is reasonable to
not expect 100% accuracy from your radiologists. However, we can utilize
statistical analyses in order to see how efficient the radiologists are
at recalling patients. Using the data you provided, approximately 1000
mammogram screenings from five randomly selected radiologists, we want
to examing two important questions for you:

-   Are some radiologists more clinically conservative than others in
    recalling patients, holding patient risk factors equal?
-   When the radiologists at this hospital interpret a mammogram to make
    a decision on whether to recall the patient, does the data suggest
    that they should be weighing some clinical risk factors more heavily
    than they currently are?

Conservative Radiologists
-------------------------

The naive method for analyzing conservativeness amongst your
radiologists would be to look at the raw recall rates. Radiologists with
a lower than average recall rate would be considered “conservative”.
Below is a bar chart showing the raw recall rates for each of the
radiologists in our sample.

    conserv = as.data.frame(xtabs(~ radiologist + recall, brca))

    radio_conserv = data.frame(Radiologist=levels(conserv$radiologist),
                               stringsAsFactors = FALSE)

    for (i in 1:5) {
      no_recall_raw = conserv[1, 3]
      recall_raw = conserv[i + 5, 3]
      radio_conserv[i, 2] = (recall_raw)/(no_recall_raw + recall_raw)
    }
    colnames(radio_conserv)[2] <- "Recall_PCT"

    ggplot(data = radio_conserv, mapping = aes(x = Radiologist, y = Recall_PCT)) +
      geom_col(position = "stack")

![](Homework_2_files/figure-markdown_strict/initial-1.png)

Judging from this chart, we would say that radiologist 34 is more
conservative in recalling patients than the other radiologists. However,
this simple analysis overlooks an important nuance: Doctors might see
different patients in systematic ways that affect their recall rates.
The next step we took was to control for patient factors to see if any
radiologists are more conservative than others, holding all else fixed.
The following is the results from a logit regression model with controls
for age, family history of breast biopsy, breat cancer symptoms,
menopause status, breast density classification, and whether the patient
has had a previous mammogram.

    ## 
    ## Call:
    ## glm(formula = recall ~ radiologist + age + history + symptoms + 
    ##     menopause + density, family = binomial, data = brca)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.0445  -0.6185  -0.5186  -0.3761   2.7939  
    ## 
    ## Coefficients:
    ##                          Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)              -3.27515    0.64028  -5.115 3.13e-07 ***
    ## radiologistradiologist34 -0.52171    0.32764  -1.592  0.11132    
    ## radiologistradiologist66  0.35466    0.27895   1.271  0.20358    
    ## radiologistradiologist89  0.46376    0.28026   1.655  0.09797 .  
    ## radiologistradiologist95 -0.05219    0.29380  -0.178  0.85900    
    ## ageage5059                0.11121    0.29534   0.377  0.70651    
    ## ageage6069                0.15683    0.36212   0.433  0.66494    
    ## ageage70plus              0.10782    0.36923   0.292  0.77028    
    ## history                   0.21588    0.23301   0.926  0.35419    
    ## symptoms                  0.72928    0.35897   2.032  0.04219 *  
    ## menopausepostmenoNoHT    -0.19342    0.23732  -0.815  0.41506    
    ## menopausepostmenounknown  0.40267    0.46399   0.868  0.38548    
    ## menopausepremeno          0.34208    0.31269   1.094  0.27396    
    ## densitydensity2           1.22015    0.53897   2.264  0.02358 *  
    ## densitydensity3           1.41907    0.53562   2.649  0.00806 ** 
    ## densitydensity4           1.00034    0.60196   1.662  0.09656 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 834.25  on 986  degrees of freedom
    ## Residual deviance: 799.99  on 971  degrees of freedom
    ## AIC: 831.99
    ## 
    ## Number of Fisher Scoring iterations: 5

Predicting When Articles Go Viral
=================================

Introduction
------------
