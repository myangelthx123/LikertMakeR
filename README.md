
[![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)
[![metacran downloads total](https://cranlogs.r-pkg.org/badges/grand-total/LikertMakeR)](https://cran.r-project.org/package=LikertMakeR)
[![metacran downloads last month](https://cranlogs.r-pkg.org/badges/last-month/LikertMakeR)](https://cran.r-project.org/package=LikertMakeR)


# LikertMakeR

Synthesise and correlate rating-scale data with predefined first & second moments (mean and standard deviation)

<p align="center">
  <img src="vignettes/LikertMakeR_hex.png" width="250" alt="LikertMakeR logo">
</p>


**_LikertMakeR_** synthesises Likert scale and related rating-scale data. 
Such scales are constrained by upper and lower bounds and discrete increments. 


## Purpose

The package is intended for 

  1. "reproducing" rating-scale data for further analysis and visualisation 
  when only summary statistics have been reported, 
   
  2. teaching. Helping researchers and students to better understand the 
  relationships among scale properties, sample size, number of items, etc.. 

  3. checking the feasibility of scale moments with given scale and 
  correlation properties 


Functions in this development version of **_LikertMakeR_** are:

  -  **_lfast()_** draws a random sample from a scaled _Beta_ distribution to 
  approximate predefined first and second moments
  
  -  **_lfast_R()_** draws repeated random sample from a scaled _Beta_ 
  distribution to selecting the best vector with predefined first and second 
  moments. Slightly slower than _lfast()_, but much faster than _lexact()_.

  -  **_lexact()_** attempts to produce a vector with exact predefined
  first and second moments 

  - **_lcor()_** rearranges the values in the columns of a dataframe so that 
  they are correlated to match a predefined correlation matrix

  - **_lcor_C()_** is a **C++** implementation of the _lcor()_ function intended
  to replace _lcor()_ in my next CRAN submission



## Rating scale properties

A Likert scale is the mean, or sum, of several ordinal rating scales. 
They are bipolar (usually “agree-disagree”) responses to propositions 
that are determined to be moderately-to-highly correlated and 
capturing various facets of a theoretical construct.
    
Rating scales are not continuous or unbounded. 
    
For example, a 5-point Likert scale that is constructed with, say, 
five items (questions) will have a summed range of between 5 
(all rated ‘1’) and 25 (all rated ‘5’) with all integers in between, 
and the mean range will be ‘1’ to ‘5’ with intervals of 1/5=0.20.
A 7-point Likert scale constructed from eight items will have a 
summed range between 8 (all rated ‘1’) and 56 (all rated ‘7’) with 
all integers in between, and the mean range will be ‘1’ to ‘7’ with 
intervals of 1/8=0.125.


#### Alternative approaches

Typically, a researcher will synthesise rating-scale data by sampling with a predetermined probability distribution. For example, the following code will generate a vector of values for a single Likert-scale item, with approximately the given probabilities. 

          n <- 128
          sample(1:5, n, replace = TRUE,
            prob = c(0.1, 0.2, 0.4, 0.2, 0.1)
          )

This approach is good for testing Likert items but it does not help when 
working on complete Likert scales, or for when we want to specify means 
and standard deviations as they might be reported in published research.  

The functions `lfast()`, `lfast_R()`, and
   `lexact()` allow the user to specify exact 
univariate statistics as they might ordinarily be reported. 



## Install _LikertMakeR_

To download and install the package, run the following code from your R console.

From __CRAN__:


     install.packages('LikertMakeR')
    

The latest development version is available from the 
author's _GitHub_ repository.


     library(devtools)
     install_github("WinzarH/LikertMakeR")
     

## Generate synthetic rating scales

To synthesise a rating scale, the user must input the following parameters:

-  **_n_**: sample size 
  
-  **_mean_**: desired mean 
  
-  **_sd_**: desired standard deviation
  
-  **_lowerbound_**: desired lower bound
  
-  **_upperbound_**: desired upper bound 
  
-  **_items_**: number of items making the scale. Default&nbsp;=&nbsp;1 
  
-  **_seed_**: optional seed for reproducibility 
  
    
**_LikertMakeR_** offers two different functions for synthesising a rating 
scale: **_lfast()_**&nbsp;and&nbsp;**_lexact()_**, and a new addition: 
**_lfast_R()_** which is a more accurate version of _lfast()_.


### lfast() & lfast_R()

  -  **_lfast()_** draws a random sample from a scaled _Beta_ distribution. 
    It is very fast but does not guarantee that the mean and standard deviation 
    are exact. 
    Recommended for relatively large sample sizes.
 
   -  **_lfast_R()_** draws repeated random samples from a scaled _Beta_ 
   distributionm, selecting the best-fit for the predefined moments, 
   usually accurate to two decimal places.
   It is expected to replace _lexact()_ in the next submission to _CRAN_.

#### _lfast()_ Example: a five-item, seven-point Likert scale

     
     x <- lfast(
       n = 256, 
       mean = 4.5, sd = 1.0, 
       lowerbound = 1, 
       upperbound = 7, 
       items = 5
       )
     

#### _lfast()_ Example:  an 11-point likelihood-of-purchase scale
 
     
     x <- lfast(256, 2.5, 2.5, 0, 10)
     


#### _lfast_R()_ Example: a five-item, seven-point Likert scale

     
     x <- lfast_R(
       n = 256, 
       mean = 4.5, sd = 1.0, 
       lowerbound = 1, 
       upperbound = 7, 
       items = 5
       )
     

#### _lfast_R()_ Example:  an 11-point likelihood-of-purchase scale
 
     
     x <- lfast_R(256, 2.5, 2.5, 0, 10)
     




### lexact()  

  -  **_lexact()_** attempts to produce a vector with exact first and 
  second moments. It uses the _Differential&nbsp;Evolution_ algorithm in 
  the ['DEoptim'](https://CRAN.R-project.org/package=DEoptim) package to 
  find appropriate values within the desired constraints. 
  
**_lexact()_** may take some time to complete the optimisation task, 
but is excellent for simulating data from already-published reports 
where only summary statistics are reported. 

 
 #### _lexact()_ Example: a five-item, seven-point Likert scale


     x <- lexact(
       n = 32, 
       mean = 4.5, 
       sd = 1.0, 
       lowerbound = 1, 
       upperbound = 7, 
       items = 5
       )
     

 #### _lexact()_ Example:  an 11-point likelihood-of-purchase scale


     x <- lexact(32, 2.5, 2.5, 0, 10)
     

 #### _lexact()_ Example:  a seven-point negative-to-positive scale with 4 items

     x <- lexact(
       n = 32, 
       mean = 1.25, 
       sd = 1.00, 
       lowerbound = -3, 
       upperbound = 3, 
       items = 4
       )
     

## Correlating vectors of synthetic rating scales

**_LikertMakeR_** offers another function, **_lcor()_**, which rearranges 
the values in the columns of a data set so that they are correlated at 
a specified level. 
**_lcor()_** does not change the values - it swaps their positions in each 
column so that univariate statistics do not change, 
but their correlations with other columns do.

**_lcor_C()_** is an implementation of _lcor()_ written in **C++**. 
_lcor_C()_ runs more than 100 times faster than _lcor()_, producing the same outcome.
It will replace _lcor()_ in the next CRAN submission.

To create the desired correlations, the user must define the 
following objects: 

  -  **_data_**: a starter data set of rating-scales 
  
  -  **_target_**: the target correlation matrix 




### **_lcor()_** & **_lcor_C()_** Example #1


####  generate synthetic data
     
     set.seed(42) ## for reproducibility
     
     n <- 64
     x1 <- lfast_R(n, 3.5, 1.00, 1, 5, 5) 
     x2 <- lfast_R(n, 1.5, 0.75, 1, 5, 5) 
     x3 <- lfast_R(n, 3.0, 1.70, 1, 5, 5) 
     x4 <- lfast_R(n, 2.5, 1.50, 1, 5, 5)   
     
     mydat4 <- data.frame(x1, x2, x3, x4) 
     
     head(mydat4)
     cor(mydat4) |> round(3)
     

####  Define a target correlation matrix

     
     tgt4 <- matrix(
     c(
       1.00, 0.50, 0.50, 0.75,
       0.50, 1.00, 0.25, 0.65,
       0.50, 0.25, 1.00, 0.80,
       0.75, 0.65, 0.80, 1.00
     ),
     nrow = 4
     )
     

####  Apply _lcor()_ to rearrange values in each column achieving desired correlations


##### _lcor()_ application


     new4 <- lcor(data = mydat4, target = tgt4)
     
     cor(new4) |> round(3)


     
####  Apply _lcor_C()_ produces the same result more quickly

     new4C <- lcor(data = mydat4, target = tgt4)
     
     cor(new4C) |> round(3)



### _lcor()_ & _lcor_C()_ example #2


#####  three starting columns and a different target correlation matrix

     mydat3 <- data.frame(x1, x2, x3) 
    
     tgt3 <- matrix(
       c(
          1.00, -0.50, -0.85,
         -0.50,  1.00,  0.60,
         -0.85,  0.60,  1.00
       ),
       nrow = 3
     )
     

##### Apply _lcor()_      


     new3 <- lcor(mydat3, tgt3) 
     
     cor(new3) |> round(3)

     
####  Apply _lcor_C()_ 

     new3C <- lcor_C(mydat3, tgt3) 
     
     cor(new3C) |> round(3)     



****

### To cite _LikertMakeR_

Here’s how to cite this package:

     Winzar, H. (2022). LikertMakeR: Synthesise and correlate rating-scale 
    data with predefined first & second moments, 
    The Comprehensive R Archive Network (CRAN),
    <https://CRAN.R-project.org/package=LikertMakeR>
        
#### BIB:    

    @software{winzar2022,
    title = {LikertMakeR: Synthesise and correlate rating-scale data with predefined first & second moments},
    author = {Hume Winzar},
    abstract = {LikertMakeR synthesises Likert scale and related rating-scale data with predefined means and standard deviations, and optionally correlates these vectors to fit a predefined correlation matrix.},
    journal = {The Comprehensive R Archive Network (CRAN)},
    month = {12},
    year = {2022},
    url = {https://CRAN.R-project.org/package=LikertMakeR},
    }
