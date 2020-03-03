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

&lt;&lt;&lt;&lt;&lt;&lt;&lt; HEAD \#\# Conservative Radiologists

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

    ## 
    ## Call:  glm(formula = recall ~ radiologist + history + symptoms + menopause + 
    ##     density, family = binomial, data = brca)
    ## 
    ## Coefficients:
    ##              (Intercept)  radiologistradiologist34  
    ##                 -3.15141                  -0.52462  
    ## radiologistradiologist66  radiologistradiologist89  
    ##                  0.35991                   0.46247  
    ## radiologistradiologist95                   history  
    ##                 -0.05245                   0.22440  
    ##                 symptoms     menopausepostmenoNoHT  
    ##                  0.72930                  -0.19536  
    ## menopausepostmenounknown          menopausepremeno  
    ##                  0.40160                   0.25149  
    ##          densitydensity2           densitydensity3  
    ##                  1.21216                   1.40535  
    ##          densitydensity4  
    ##                  0.97528  
    ## 
    ## Degrees of Freedom: 986 Total (i.e. Null);  974 Residual
    ## Null Deviance:       834.3 
    ## Residual Deviance: 800.2     AIC: 826.2

\#Predicting When Articles Go Viral

Introduction
------------
