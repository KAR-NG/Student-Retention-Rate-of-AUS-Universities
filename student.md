Movement of University Students in Australia
================
Kar Ng
2022-07-23

-   [1 R PACKAGES](#1-r-packages)
-   [2 DATA PREPARATION](#2-data-preparation)
    -   [2.1 Data Import](#21-data-import)
    -   [2.2 Data Cleaning](#22-data-cleaning)
    -   [2.3 Data Exploration](#23-data-exploration)
-   [3 VISUALISATION](#3-visualisation)
    -   [3.1 Qld ranking in Australia](#31-qld-ranking-in-australia)
    -   [3.2 UQ ranking in Queensland](#32-uq-ranking-in-queensland)
    -   [3.3 UQ Attrition Ranking (2019)](#33-uq-attrition-ranking-2019)
    -   [3.4 UQ Retention Ranking (2019)](#34-uq-retention-ranking-2019)
    -   [3.5 UQ Success Ranking (2020)](#35-uq-success-ranking-2020)
    -   [3.6 PCA](#36-pca)
-   [4 CONCLUSION](#4-conclusion)
-   [5 REFERENCE](#5-reference)

# 1 R PACKAGES

Following R packages are loaded for this project.

``` r
library(tidyverse)
library(kableExtra)
library(tidytext)
library(ggh4x)
library(DT)
library(ggrepel)
library(FactoMineR)
library(factoextra)
library(cowplot)
```

# 2 DATA PREPARATION

This project uses real public data set from the Department of Education,
Skills and Employment (DESE), Australia Government.

## 2.1 Data Import

This section imports the data set that will be used for this project.
Note that this data set is a subset of the original dataset. This subset
has only the latest available data of attrition (2019), retention (2019)
and success rate (2020).

Randomly review 20 rows from the imported data set:

``` r
data <- read.csv("data.csv")

sample_n(data, 20) %>% 
  kbl() %>% 
  kable_styling(bootstrap_options = c("hover", "bordered", "stripped"))
```

<table class="table table-hover table-bordered" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">

student

</th>
<th style="text-align:left;">

Uni

</th>
<th style="text-align:left;">

New_Adjusted_Attrition_2019

</th>
<th style="text-align:left;">

New_Normal_Attrition_2019

</th>
<th style="text-align:left;">

Success_2020

</th>
<th style="text-align:left;">

New_Adjusted_Retention_2019

</th>
<th style="text-align:left;">

New_Normal_Retention_2019

</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Batchelor Institute of Indigenous Tertiary Education(f)

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

.

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

The University of Adelaide

</td>
<td style="text-align:left;">

9.09

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

University of the Sunshine Coast

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

79.73

</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Deakin University

</td>
<td style="text-align:left;">

13.39

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

University of Tasmania(e)

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

83.97

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

The University of Sydney

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

91.49

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Victoria University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

88.98

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

University of Tasmania(e)

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

23.04

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Australian Catholic University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

90.31

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

La Trobe University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

78.37

</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Macquarie University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

91.14

</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Edith Cowan University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

21.03

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Flinders University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

13.33

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Queensland University of Technology

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

82.92

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Queensland University of Technology

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

88.62

</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

The Australian National University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

94.31

</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Batchelor Institute of Indigenous Tertiary Education(f)

</td>
<td style="text-align:left;">

.

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

University of South Australia

</td>
<td style="text-align:left;">

17.76

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Australian Maritime College(e)

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

.

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

James Cook University

</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

86.91

</td>
</tr>
</tbody>
</table>

The column “student” has 3 levels, which are “domestic”,
“international”, and “overall”. Whereas, the column “Uni”, as the name
suggests, it carries all universities in Australia.

The data set has 396 rows of data and 7 columns of variables.

``` r
dim(data)
```

    ## [1] 396   7

## 2.2 Data Cleaning

There will be a lot of data cleaning tasks but the process is completed
in following code chunk. In following code chunk,

**Change-log**

-   I have changed the format of the table to a long-format  
-   I have created several new variables, which are “metrics”, “year”,
    and “method”  
-   I have changed the data type of “values_per” from character to
    numeric  
-   I have created a new variable “state” to group universities  
-   I have changed all the character variables into factor  
-   I have shifted around (relocate) the order of variables in the data
    set

``` r
mydata <- data %>% 
  # pivoting to change data from wider to longer format:
  pivot_longer(c(3:7), names_to = "method", values_to = "values_per") %>%   
  # create 3 new variables:
  mutate(metrics = case_when(method = str_detect(method, "Attrition") ~ "Attrition",   
                             method = str_detect(method, "Retention") ~ "Retention",
                             TRUE ~ "Success"),
         year = str_extract(method, "[:digit:]+"),
         method = str_remove(method, "_Attrition_2019"),
         method = str_remove(method, "_Retention_2019"),
         method = str_remove(method, "_2020")) %>% 
  # change values_per into numeric class
  mutate(values_per = as.double(values_per)) %>% 
  # create a new variables "state"
  mutate(state = fct_collapse(Uni,
                              "New South Wales" = c("Charles Sturt University", 
                                                    "Macquarie University", 
                                                    "Southern Cross University", 
                                                    "The University of New England", 
                                                    "The University of Newcastle", 
                                                    "The University of Sydney", 
                                                    "University of New South Wales(b)", 
                                                    "University of Technology Sydney",
                                                    "University of Wollongong",
                                                    "Western Sydney University"),
                              "Victoria" = c("Deakin University",
                                             "Federation University Australia(c)",
                                             "La Trobe University",
                                             "Monash University",
                                             "RMIT University",
                                             "Swinburne University of Technology",
                                             "The University of Melbourne",
                                             "University of Divinity",
                                             "Victoria University"),
                              "Queensland" = c("Bond University",
                                               "CQUniversity",
                                               "Griffith University",
                                               "James Cook University",
                                               "Queensland University of Technology",
                                               "The University of Queensland",
                                               "University of Southern Queensland",
                                               "University of the Sunshine Coast"),
                              "Western Australia" = c("Curtin University",
                                                      "Edith Cowan University",
                                                      "Murdoch University",
                                                      "The University of Notre Dame Australia",
                                                      "The University of Western Australia"),
                              "South Australia" = c("Flinders University",
                                                    "The University of Adelaide",
                                                    "Torrens University Australia(d)",
                                                    "University of South Australia"),
                              "Tasmania" = c("Australian Maritime College(e)",
                                             "University of Tasmania(e)"),
                              "Northern Territory" = c("Batchelor Institute of Indigenous Tertiary Education(f)",
                                                       "Charles Darwin University(f)"),
                              "Australian Capital Territory" = c("The Australian National University",
                                                                 "University of Canberra"),
                              "Multi-State" = c("Australian Catholic University"),
                              "Australia" = c("National Total")
                              )) %>% 
  # change all character variables into factor
    mutate_if(is.character, as.factor)  %>% 
  dplyr::select("year", "state", "Uni", "student", "metrics", "method", "values_per") %>% 
  # Remove NA
  na.omit() 
```

Lets review the cleaned data set!

-   The data set has 378 rows of data and 7 variables.

``` r
dim(mydata)
```

    ## [1] 378   7

-   And, the data set has been cleaned!

``` r
sample_n(mydata, 30) %>% 
  kbl() %>% 
  kable_styling(bootstrap_options = c("hover", "bordered", "stripped"))
```

<table class="table table-hover table-bordered" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">

year

</th>
<th style="text-align:left;">

state

</th>
<th style="text-align:left;">

Uni

</th>
<th style="text-align:left;">

student

</th>
<th style="text-align:left;">

metrics

</th>
<th style="text-align:left;">

method

</th>
<th style="text-align:right;">

values_per

</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Griffith University

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

15.84

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Western Australia

</td>
<td style="text-align:left;">

Curtin University

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

14.40

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Bond University

</td>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

92.97

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

Swinburne University of Technology

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

80.81

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Queensland University of Technology

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

88.62

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Griffith University

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

88.78

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

Western Australia

</td>
<td style="text-align:left;">

The University of Notre Dame Australia

</td>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

90.93

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

Deakin University

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

14.20

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Australia

</td>
<td style="text-align:left;">

National Total

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

86.91

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Queensland University of Technology

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

9.70

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

Swinburne University of Technology

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

15.43

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Bond University

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

95.56

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

Swinburne University of Technology

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

82.27

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

New South Wales

</td>
<td style="text-align:left;">

University of Technology Sydney

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

90.99

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

Swinburne University of Technology

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

89.81

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

The University of Queensland

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

94.09

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

New South Wales

</td>
<td style="text-align:left;">

The University of Newcastle

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

90.92

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Multi-State

</td>
<td style="text-align:left;">

Australian Catholic University

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

12.33

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

New South Wales

</td>
<td style="text-align:left;">

Macquarie University

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

86.60

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

New South Wales

</td>
<td style="text-align:left;">

University of New South Wales(b)

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

91.92

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

South Australia

</td>
<td style="text-align:left;">

Flinders University

</td>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

85.27

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

University of Divinity

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

4.35

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

Griffith University

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

87.34

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

New South Wales

</td>
<td style="text-align:left;">

University of New South Wales(b)

</td>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

93.67

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Victoria

</td>
<td style="text-align:left;">

Victoria University

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

14.68

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Western Australia

</td>
<td style="text-align:left;">

The University of Notre Dame Australia

</td>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

83.29

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

Australian Capital Territory

</td>
<td style="text-align:left;">

The Australian National University

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Retention

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

94.39

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

South Australia

</td>
<td style="text-align:left;">

University of South Australia

</td>
<td style="text-align:left;">

domestic

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Adjusted

</td>
<td style="text-align:right;">

17.76

</td>
</tr>
<tr>
<td style="text-align:left;">

2019

</td>
<td style="text-align:left;">

New South Wales

</td>
<td style="text-align:left;">

University of Wollongong

</td>
<td style="text-align:left;">

international

</td>
<td style="text-align:left;">

Attrition

</td>
<td style="text-align:left;">

New_Normal

</td>
<td style="text-align:right;">

12.93

</td>
</tr>
<tr>
<td style="text-align:left;">

2020

</td>
<td style="text-align:left;">

Queensland

</td>
<td style="text-align:left;">

CQUniversity

</td>
<td style="text-align:left;">

overall

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:left;">

Success

</td>
<td style="text-align:right;">

79.70

</td>
</tr>
</tbody>
</table>

## 2.3 Data Exploration

Summary data:

``` r
summary(mydata) %>% kbl() %>% kable_styling(bootstrap_options = c("stripped", "bordered", "hover"))
```

<table class="table table-bordered table-hover" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:left;">

year

</th>
<th style="text-align:left;">

state

</th>
<th style="text-align:left;">

Uni

</th>
<th style="text-align:left;">

student

</th>
<th style="text-align:left;">

metrics

</th>
<th style="text-align:left;">

method

</th>
<th style="text-align:left;">

values_per

</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

2019:252

</td>
<td style="text-align:left;">

New South Wales :90

</td>
<td style="text-align:left;">

Australian Catholic University: 9

</td>
<td style="text-align:left;">

domestic :126

</td>
<td style="text-align:left;">

Attrition:126

</td>
<td style="text-align:left;">

New_Adjusted: 84

</td>
<td style="text-align:left;">

Min. : 3.43

</td>
</tr>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

2020:126

</td>
<td style="text-align:left;">

Victoria :81

</td>
<td style="text-align:left;">

Bond University : 9

</td>
<td style="text-align:left;">

international:126

</td>
<td style="text-align:left;">

Retention:126

</td>
<td style="text-align:left;">

New_Normal :168

</td>
<td style="text-align:left;">

1st Qu.:20.58

</td>
</tr>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

Queensland :72

</td>
<td style="text-align:left;">

Charles Darwin University(f) : 9

</td>
<td style="text-align:left;">

overall :126

</td>
<td style="text-align:left;">

Success :126

</td>
<td style="text-align:left;">

Success :126

</td>
<td style="text-align:left;">

Median :81.38

</td>
</tr>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

Western Australia :45

</td>
<td style="text-align:left;">

Charles Sturt University : 9

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

Mean :61.94

</td>
</tr>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

South Australia :36

</td>
<td style="text-align:left;">

CQUniversity : 9

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

3rd Qu.:88.04

</td>
</tr>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

Australian Capital Territory:18

</td>
<td style="text-align:left;">

Curtin University : 9

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

Max. :97.64

</td>
</tr>
<tr>
<td style="text-align:left;">
</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

(Other) :36

</td>
<td style="text-align:left;">

(Other) :324

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
<td style="text-align:left;">

NA

</td>
</tr>
</tbody>
</table>

The column “Uni” has 42 levels, which has the data of 41 universities in
Australia and 1 “National Total” (no. 17).

``` r
table(mydata$Uni) %>% 
  as.data.frame() %>%
  filter(Freq != 0) %>%
  mutate("no" = c(1:42)) %>% 
  rename("Universities" = "Var1",
         "Sample Size" = "Freq") %>% 
  relocate(no, .before = Universities) %>% 
  kbl(align = "c") %>% 
  kable_styling(full_width = F, c("bordered", "stripped", "hover"))
```

<table class="table table-bordered table-hover" style="width: auto !important; margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:center;">

no

</th>
<th style="text-align:center;">

Universities

</th>
<th style="text-align:center;">

Sample Size

</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:center;">

1

</td>
<td style="text-align:center;">

Australian Catholic University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

2

</td>
<td style="text-align:center;">

Bond University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

3

</td>
<td style="text-align:center;">

Charles Darwin University(f)

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

4

</td>
<td style="text-align:center;">

Charles Sturt University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

5

</td>
<td style="text-align:center;">

CQUniversity

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

6

</td>
<td style="text-align:center;">

Curtin University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

7

</td>
<td style="text-align:center;">

Deakin University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

8

</td>
<td style="text-align:center;">

Edith Cowan University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

9

</td>
<td style="text-align:center;">

Federation University Australia(c)

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

10

</td>
<td style="text-align:center;">

Flinders University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

11

</td>
<td style="text-align:center;">

Griffith University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

12

</td>
<td style="text-align:center;">

James Cook University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

13

</td>
<td style="text-align:center;">

La Trobe University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

14

</td>
<td style="text-align:center;">

Macquarie University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

15

</td>
<td style="text-align:center;">

Monash University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

16

</td>
<td style="text-align:center;">

Murdoch University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

17

</td>
<td style="text-align:center;">

National Total

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

18

</td>
<td style="text-align:center;">

Queensland University of Technology

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

19

</td>
<td style="text-align:center;">

RMIT University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

20

</td>
<td style="text-align:center;">

Southern Cross University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

21

</td>
<td style="text-align:center;">

Swinburne University of Technology

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

22

</td>
<td style="text-align:center;">

The Australian National University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

23

</td>
<td style="text-align:center;">

The University of Adelaide

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

24

</td>
<td style="text-align:center;">

The University of Melbourne

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

25

</td>
<td style="text-align:center;">

The University of New England

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

26

</td>
<td style="text-align:center;">

The University of Newcastle

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

27

</td>
<td style="text-align:center;">

The University of Notre Dame Australia

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

28

</td>
<td style="text-align:center;">

The University of Queensland

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

29

</td>
<td style="text-align:center;">

The University of Sydney

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

30

</td>
<td style="text-align:center;">

The University of Western Australia

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

31

</td>
<td style="text-align:center;">

Torrens University Australia(d)

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

32

</td>
<td style="text-align:center;">

University of Canberra

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

33

</td>
<td style="text-align:center;">

University of Divinity

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

34

</td>
<td style="text-align:center;">

University of New South Wales(b)

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

35

</td>
<td style="text-align:center;">

University of South Australia

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

36

</td>
<td style="text-align:center;">

University of Southern Queensland

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

37

</td>
<td style="text-align:center;">

University of Tasmania(e)

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

38

</td>
<td style="text-align:center;">

University of Technology Sydney

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

39

</td>
<td style="text-align:center;">

University of the Sunshine Coast

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

40

</td>
<td style="text-align:center;">

University of Wollongong

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

41

</td>
<td style="text-align:center;">

Victoria University

</td>
<td style="text-align:center;">

9

</td>
</tr>
<tr>
<td style="text-align:center;">

42

</td>
<td style="text-align:center;">

Western Sydney University

</td>
<td style="text-align:center;">

9

</td>
</tr>
</tbody>
</table>

All variables in the data set have desired variable types.

``` r
glimpse(mydata)
```

    ## Rows: 378
    ## Columns: 7
    ## $ year       <fct> 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019,…
    ## $ state      <fct> Australia, New South Wales, New South Wales, New South Wale…
    ## $ Uni        <fct> National Total, Charles Sturt University, Macquarie Univers…
    ## $ student    <fct> domestic, domestic, domestic, domestic, domestic, domestic,…
    ## $ metrics    <fct> Attrition, Attrition, Attrition, Attrition, Attrition, Attr…
    ## $ method     <fct> New_Adjusted, New_Adjusted, New_Adjusted, New_Adjusted, New…
    ## $ values_per <dbl> 13.23, 21.01, 8.77, 21.85, 24.90, 12.92, 4.87, 3.86, 4.99, …

There is no missing value in the dataset (NA).

``` r
colSums(is.na(mydata))
```

    ##       year      state        Uni    student    metrics     method values_per 
    ##          0          0          0          0          0          0          0

# 3 VISUALISATION

``` r
names(mydata)
```

    ## [1] "year"       "state"      "Uni"        "student"    "metrics"   
    ## [6] "method"     "values_per"

## 3.1 Qld ranking in Australia

UQ ranked the third in attrition rate, the 9th in term of retention, and
also the 9th in term of success rate.

``` r
df1 <- mydata %>% 
  mutate(label = reorder_within(x = state, by = values_per, within = metrics),
         qld = ifelse(state == "Queensland", "Yes", 
                      "No"))
  
ggplot(df1, aes(y = label, x = values_per, color = qld)) +
  stat_boxplot(geom = "errorbar") +
  geom_boxplot(outlier.shape = NA) +
  geom_jitter(width = 0.2, alpha = 0.5) +
  facet_wrap(~metrics, scales = "free_y", nrow = 3) +
  scale_y_reordered() +
  theme_classic() +
  theme(legend.position = "none",
        strip.background = element_rect(fill = "purple"),
        strip.text = element_text(color = "white", size = 10)) +
  stat_summary(fun = "mean", shape = 4, size = 1, aes(color = "qld")) +
  labs(y = "", x = "%") +
  scale_color_manual(values = c("grey", "grey", "purple"))
```

![](student_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

## 3.2 UQ ranking in Queensland

``` r
df2 <- mydata %>% 
  mutate(label = reorder_within(x = Uni, by = values_per, within = metrics),
         uq = ifelse(Uni == "The University of Queensland", "Yes", 
                      "No")) %>% 
  filter(state == "Queensland")
  
ggplot(df2, aes(y = label, x = values_per, color = uq)) +
  stat_boxplot(geom = "errorbar") +
  geom_boxplot(outlier.shape = NA) +
  geom_jitter(width = 0.2, alpha = 0.5) +
  facet_wrap(~metrics, scales = "free_y", nrow = 3) +
  scale_y_reordered() +
  theme_classic() +
  theme(legend.position = "none",
        plot.title = element_text(hjust = 0.2, face = "bold", size = 15), 
        strip.background = element_rect(fill = "purple"),
        strip.text = element_text(color = "white", size = 10)) +
  stat_summary(fun = "mean", shape = 4, size = 1, aes(color = "qld")) +
  labs(y = "", x = "%", title = "Brilliant UQ !!!") +
  scale_color_manual(values = c("grey", "grey", "purple"))
```

![](student_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

## 3.3 UQ Attrition Ranking (2019)

This section summarise attrition rate of year 2019. It is important to
note that “**new adjusted**” attrition rate is applied to domestic
students, where as “**new normal**” attrition rate is applied to both
international students and the overall cluster.

``` r
# data frame

df3 <- mydata %>% 
  filter(metrics == "Attrition") %>% 
  dplyr::select(-metrics, -year, -state, -method) %>% 
  group_by(student) %>% 
  arrange(values_per) %>% 
  mutate(ranking = rank(-values_per),
         x2 = reorder_within(x = Uni, by = values_per, within = student),
         text_uq = ifelse(Uni == "The University of Queensland", 
                       paste0("UQ: ", values_per, "%\n", "(Ranking: ", ranking,")"),
                              NA),
         text_uq = as.factor(text_uq),
         text_national = ifelse(Uni == "National Total", 
                               paste0("National: ", values_per, "%\n", "(Ranking: ", ranking,")"),
                               NA),
         text_national = as.factor(text_national),
         uq = ifelse(Uni == "The University of Queensland", "yes_uq",
                     ifelse(Uni == "National Total", "yes_national", 
                            "no")
                     ),
         y_color = ifelse(Uni == "The University of Queensland", "purple",
                       ifelse(Uni == "National Total", "gold4",
                           "black"))) 

# plot

ggplot(df3, aes(x = values_per, y = fct_reorder(x2, values_per))) +
  geom_bar(stat = "identity", width = 0.7, color = "black", aes(fill = uq)) +
  scale_fill_manual(values = c("grey", "gold4", "purple")) +
  scale_x_continuous(limits = c(0, 100)) +
  facet_wrap(~student, scales = "free") +
  theme_bw() + 
  labs(x = "Attrition Rate (%)",
       y = "",
       title = "2019 Attrition Rate (%)",
       subtitle = "Applied both New-adjusted rate (domestic) and New-normal rate (international & Overall)") +
  scale_y_reordered() + 
  geom_text(aes(label = text_uq, hjust = -0.2), size = 6, color = "purple") +
  geom_text(aes(label = text_national, hjust = -0.2), size = 6, color = "gold4") +
  theme(legend.position = "none", 
        axis.title.y = element_blank(),  
        axis.text.y = element_text(),  
        plot.title = element_text(face = "bold", hjust = 0.5, size = 17),  
        plot.subtitle = element_text(hjust = 0.5, size = 12),
        strip.background = element_rect(fill = "red"),
        strip.text = element_text(color = "white", size = 15))
```

![](student_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

## 3.4 UQ Retention Ranking (2019)

This section summarise retention rate of year 2019. Similar to attrition
rate, it is also important to note that “**new adjusted**” retention
rate is applied to domestic students, where as “**new normal**”
retention rate is applied to both international students and the overall
cluster.

``` r
# data frame

df4 <- mydata %>% 
  filter(metrics == "Retention") %>% 
  dplyr::select(-metrics, -year, -state, -method) %>% 
  group_by(student) %>% 
  arrange(values_per) %>% 
  mutate(ranking = rank(-values_per),
         x2 = reorder_within(x = Uni, by = values_per, within = student),
         text_uq = ifelse(Uni == "The University of Queensland", 
                       paste0("UQ: ", values_per, "%", "\n", "(Ranking:", ranking,")"),
                              NA),
         text_uq = as.factor(text_uq),
         text_national = ifelse(Uni == "National Total", 
                               paste0("National: ", values_per, "%", "\n(Ranking:", ranking,")"),
                               NA),
         text_national = as.factor(text_national),
         uq = ifelse(Uni == "The University of Queensland", "yes_uq",
                     ifelse(Uni == "National Total", "yes_national", 
                            "no")
                     ),
         y_color = ifelse(Uni == "The University of Queensland", "purple",
                       ifelse(Uni == "National Total", "gold4",
                           "black"))) 

# plot

ggplot(df4, aes(x = values_per, y = fct_reorder(x2, values_per))) +
  geom_bar(stat = "identity", width = 0.7, color = "black", aes(fill = uq)) +
  scale_fill_manual(values = c("grey", "gold4", "purple")) +
  scale_x_continuous(limits = c(0, 100)) +
  facet_wrap(~student, scales = "free") +
  theme_bw() + 
  labs(x = "Retention Rate (%)",
       y = "",
       title = "2019 Retention Rate (%)",
       subtitle = "Applied both New-adjusted rate (domestic) and New-normal rate (international & Overall)") +
  scale_y_reordered() + 
  geom_label(aes(label = text_uq), size = 5, color = "purple", 
             label.padding = unit(0.5, "lines"), hjust = 1.2) +
  geom_label(aes(label = text_national), size = 4, color = "gold4", 
             label.padding = unit(0.5, "lines"), hjust = 1.2) +
  theme(legend.position = "none", 
        axis.title.y = element_blank(),  
        axis.text.y = element_text(),   
        plot.title = element_text(face = "bold", hjust = 0.5, size = 17),  
        plot.subtitle = element_text(hjust = 0.5, size = 12),
        strip.background = element_rect(fill = "red"),
        strip.text = element_text(color = "white", size = 15)) 
```

![](student_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

## 3.5 UQ Success Ranking (2020)

``` r
# data frame

df5 <- mydata %>% 
  filter(metrics == "Success") %>% 
  dplyr::select(-metrics, -year, -state, -method) %>% 
  group_by(student) %>% 
  arrange(values_per) %>% 
  mutate(ranking = rank(-values_per),
         x2 = reorder_within(x = Uni, by = values_per, within = student),
         text_uq = ifelse(Uni == "The University of Queensland", 
                       paste0(values_per, "%", " (Rank:", ranking,")"),
                              NA),
         text_uq = as.factor(text_uq),
         text_national = ifelse(Uni == "National Total", 
                               paste0(values_per, "%", " (Rank:", ranking,")"),
                               NA),
         text_national = as.factor(text_national),
         uq = ifelse(Uni == "The University of Queensland", "yes_uq",
                     ifelse(Uni == "National Total", "yes_national", 
                            "no")
                     ),
         y_color = ifelse(Uni == "The University of Queensland", "purple",
                       ifelse(Uni == "National Total", "gold4",
                           "black"))) 

# plot

ggplot(df5, aes(x = values_per, y = fct_reorder(x2, values_per))) +
  geom_bar(stat = "identity", width = 0.7, color = "black", aes(fill = uq)) +
  scale_fill_manual(values = c("grey", "gold4", "purple")) +
  scale_x_continuous(limits = c(0, 100)) +
  facet_wrap(~student, scales = "free") +
  theme_bw() + 
  labs(x = "Success Rate (%)",
       y = "",
       title = "2020 Success Rate (%)",
       subtitle = "Applied both New-adjusted rate (domestic) and New-normal rate (international & Overall)") +
  scale_y_reordered() + 
  geom_label(aes(label = text_uq, hjust = 1), 
             size = 5, color = "purple", 
             label.padding = unit(0.5, "lines"), hjust = 1.2) +
  geom_label(aes(label = text_national, hjust = 1), 
             nudge_y = c(1,1),
             size = 5, color = "gold4", 
             label.padding = unit(0.5, "lines"), hjust = 1.2) +
  theme(legend.position = "none", 
        axis.title.y = element_blank(),  
        axis.text.y = element_text(),   
        plot.title = element_text(face = "bold", hjust = 0.5, size = 17),  
        plot.subtitle = element_text(hjust = 0.5, size = 12),
        strip.background = element_rect(fill = "red"),
        strip.text = element_text(color = "white", size = 15)) 
```

![](student_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

## 3.6 PCA

``` r
# set up df

df6 <- mydata %>% 
  dplyr::select(-method, -year) %>% 
  pivot_wider(names_from = metrics, values_from = values_per) %>% 
  filter(student != "overall") %>% 
  mutate(id = paste0(Uni, "_", student)) %>% 
  relocate(id, .before = state) %>% 
  column_to_rownames(var = "id")
  
# pca

pca.res <- PCA(df6, quali.sup = c(1:3), graph = F)

# plot 

g1 <- fviz_pca_var(pca.res, 
             repel = T, 
             col.var = "blue") 

g2 <- fviz_eig(pca.res, addlabels = T, ylab = c(0, 90))

g3 <- fviz_pca_biplot(pca.res, repel = T, palette = "jco", col.var = "blue", 
                geom.ind = "point", habillage = "student", mean.point = F,
                addEllipse = T, ellipse.type = "convex")

g4 <- fviz_pca_biplot(pca.res, repel = T, palette = "jco", col.var = "blue", 
                geom.ind = "point", habillage = "state", mean.point = F,
                addEllipse = T, ellipse.type = "convex")

g5 <- fviz_pca_biplot(pca.res, repel = T, 
                palette = "jco", 
                col.var = "blue", 
                col.ind = "green4",
                mean.point = F) +
  theme(plot.margin = unit(c(1,1,1,1), "cm"))
     
top <- plot_grid(g1, g2, g3, g4)
plot_grid(top, g5, ncol = 1)
```

![](student_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

# 4 CONCLUSION

UQ is generally performing very well and certainly our goal is to make
UQ the number 1 in relevant to attrition rate, success rate and
retention rate in Australia.

# 5 REFERENCE

DESE n.d., “Selected Higher Education Statistics – 2020 Student data”,
viewed 19 July 2022,
<https://www.dese.gov.au/higher-education-statistics/student-data/selected-higher-education-statistics-2020-student-data-0>
