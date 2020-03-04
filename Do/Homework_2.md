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
    ## ====================================================
    ##                              Dependent variable:    
    ##                          ---------------------------
    ##                                    recall           
    ## ----------------------------------------------------
    ## radiologistradiologist34           -0.522           
    ##                                    (0.328)          
    ##                                                     
    ## radiologistradiologist66            0.355           
    ##                                    (0.279)          
    ##                                                     
    ## radiologistradiologist89           0.464*           
    ##                                    (0.280)          
    ##                                                     
    ## radiologistradiologist95           -0.052           
    ##                                    (0.294)          
    ##                                                     
    ## ageage5059                          0.111           
    ##                                    (0.295)          
    ##                                                     
    ## ageage6069                          0.157           
    ##                                    (0.362)          
    ##                                                     
    ## ageage70plus                        0.108           
    ##                                    (0.369)          
    ##                                                     
    ## history                             0.216           
    ##                                    (0.233)          
    ##                                                     
    ## symptoms                           0.729**          
    ##                                    (0.359)          
    ##                                                     
    ## menopausepostmenoNoHT              -0.193           
    ##                                    (0.237)          
    ##                                                     
    ## menopausepostmenounknown            0.403           
    ##                                    (0.464)          
    ##                                                     
    ## menopausepremeno                    0.342           
    ##                                    (0.313)          
    ##                                                     
    ## densitydensity2                    1.220**          
    ##                                    (0.539)          
    ##                                                     
    ## densitydensity3                   1.419***          
    ##                                    (0.536)          
    ##                                                     
    ## densitydensity4                    1.000*           
    ##                                    (0.602)          
    ##                                                     
    ## Constant                          -3.275***         
    ##                                    (0.640)          
    ##                                                     
    ## ----------------------------------------------------
    ## Observations                         987            
    ## Log Likelihood                    -399.993          
    ## Akaike Inf. Crit.                  831.986          
    ## ====================================================
    ## Note:                    *p<0.1; **p<0.05; ***p<0.01

The logit regression results do not show significant coefficients for
any of the radiologists. To note, radiologist 13 is the omitted
radiologist. We see no signficanct difference for any of the other
radiologists other than radiologist 85, who has a positive (less
conservative) difference at the 10% level. It is possible that
radiologists 34 and 85 are significantly different, but we can’t really
be certain that radiologist 34 is more conservative than the others.
Overall, since we see that there aren’t any radiologists in our sample
that are significantally more conservative in recalling patients after
using a logit model to hold risk factors constant.

Importance of Clinical Risk Factors
-----------------------------------

Predicting When Articles Go Viral
=================================

Introduction
------------
