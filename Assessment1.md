---
title: "Data Wrangling"
subtitle: "Explore data using R - White House staff salary from 1997-2020"
author: "Mohammad R. Hosseinzadeh"
date: "18/01/2021"
output:
  html_document:
    toc: true
    toc_depth: 3
    toc_float: true
    df_print: kable
    theme: readable
    highlight: tango
    keep_md: yes
    code_folding: show
    mathjax: default
fontfamily: timesnewroman
fontsize: 12pt
spacing: double
line-height: 1.5
---

# Setup


```r
# Load the required packages:
library(knitr) # dynamic report generation 
library(readr) # useful for import/reading rectangular data
library(dplyr) # useful for data manipulation tasks
library(tidyr) # tidy data
library(lubridate) # for dates and date-times
library(ggplot2) # for visualisations
library(magrittr) # for pipes and double assignment
library(kableExtra) # dynamic tables
# set chunk options
knitr::opts_chunk$set(
  echo = TRUE,
  eval = TRUE,
  message = FALSE,
  warning = FALSE,
  comment = "") 
```

R comes with a huge variety of packages which are a collection of functions, data and documentations that make the task of data wrangling more efficient (Wickham & Grolemund 2017). I used the *tidyverse* packages *readr* and *dplyr* for importing, reading and manipulating data. To load these packages, I used base R library() function and loaded the required packages to complete this assessment. I have taken use of knitr opts_chunk$set() function and used *comment = ''* argument which will remove the default *##* prefix in the code output document (Xie 2020).

I also loaded the *knitr* package by Xie (2020) which is useful for creating nice reports and tables with R. I have set the setting *df_print: kable* within the YAML header in R Markdown which will use the *knitr::kable* function as the default display of data frames for a nicer presentation.   

### Locate Data 

The data that I will be exploring for this assignment is the White House office staff salaries dataset which I found through Kaggle's website. The data set was uploaded by Kaggle member Adiquant (2020) and comes in a CSV file format. The dataset contains the complete White House staff salary data from 1997-2020 and is available for download from:

* [Dataset URL](http://www.kaggle.com/adiquant/white-house-staff-salaries-20172020?select=wh_staff_dataset.csv).

# Read/Import Data


```r
# Read and import the data and save it as a data frame
data <- read_csv("wh_staff_dataset.csv")
# display data frame
head(data) %>% 
  kable(align = "l") %>% 
  kable_paper(c("striped", "hover", "responsive"), full_width = TRUE) 
```

<table class=" lightable-paper lightable-striped lightable-hover" style='font-family: "Arial Narrow", arial, helvetica, sans-serif; margin-left: auto; margin-right: auto;'>
 <thead>
  <tr>
   <th style="text-align:left;"> year </th>
   <th style="text-align:left;"> name </th>
   <th style="text-align:left;"> gender </th>
   <th style="text-align:left;"> status </th>
   <th style="text-align:left;"> salary </th>
   <th style="text-align:left;"> pay_basis </th>
   <th style="text-align:left;"> position_title </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 1997 </td>
   <td style="text-align:left;"> Abedin,Huma M. </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Employee </td>
   <td style="text-align:left;"> 27500 </td>
   <td style="text-align:left;"> Per Annum </td>
   <td style="text-align:left;"> OFFICE MANAGER </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1997 </td>
   <td style="text-align:left;"> Abrams,Lori E. </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Employee </td>
   <td style="text-align:left;"> 35000 </td>
   <td style="text-align:left;"> Per Annum </td>
   <td style="text-align:left;"> DEPUTY DIRECTOR, WHITE HOUSE GIFTS </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1997 </td>
   <td style="text-align:left;"> Alcorn,Brian A. </td>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:left;"> Employee </td>
   <td style="text-align:left;"> 25000 </td>
   <td style="text-align:left;"> Per Annum </td>
   <td style="text-align:left;"> ASSISTANT DIRECTOR FOR ADVANCE </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1997 </td>
   <td style="text-align:left;"> Allen,Jeannetta Pam </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Employee </td>
   <td style="text-align:left;"> 22000 </td>
   <td style="text-align:left;"> Per Annum </td>
   <td style="text-align:left;"> STAFF ASSISTANT </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1997 </td>
   <td style="text-align:left;"> Allison,Donna Tate </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Employee </td>
   <td style="text-align:left;"> 27624 </td>
   <td style="text-align:left;"> Per Annum </td>
   <td style="text-align:left;"> WHITE HOUSE TELEPHONE OPERATOR </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 1997 </td>
   <td style="text-align:left;"> Alston,Mildred C. </td>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:left;"> Employee </td>
   <td style="text-align:left;"> 47000 </td>
   <td style="text-align:left;"> Per Annum </td>
   <td style="text-align:left;"> SPECIAL ASSISTANT PERSONAL CORRESPONDENCE </td>
  </tr>
</tbody>
</table>

To import and read the data into R, I used the read_csv() function from the readr package developed by Wickham and Hester (2020). I then assigned the dataset to an R object called *'data'*. I prefer to use the readr package instead of base R for reading files into R as they are typically about 10x faster (Wickham & Grolemund 2017). 

A great feature of read_csv() function is that it doesn't automatically convert characters to factors. Another useful feature is the printing of column specifications along with column names and their corresponding data types. This feature is a great way to quickly get a sense of variables and whether they are in the correct data type.

To display the first *n* rows, I used base R head() function. By default it will print the first 6 rows of the data frame and is a great way to get a view of the imported data. To control how many rows get printed, we can use the argument *n=* and specify a desired number of rows i.e. 10L or 10 (R Core Team 2020).

### Data description

This data set contains White House office staff data from 1997 to 2020. I am interested in exploring this dataset, apply tidy data principles and manipulate the data in a manner to compare salaries grouped by gender and practice the data wrangling methods learned in this module. The source of the data is as follows:

* Data was compiled and uploaded by Kaggle member Adiquant and was uploaded online on Monday July 6, 2020 which can be accessed publicly. 
* [URL for dataset](http://www.kaggle.com/adiquant/white-house-staff-salaries-20172020?select=wh_staff_dataset.csv)
* As stated by Adiquant (2020), the data was collected from multiple sources including the Annual Report to Congress on White House Staff, the official White House website, archives.gov website and George Bush library website.   

The variables in this data set are:

* **year** - Year of employment: 1997 to 2020
* **name** - Staff name, with the syntax: last name, first name middle name initial
* **gender** - Staff gender, male or female
* **status** - Staff emploment type: *'Employee'*, *'Employee (part time)'*, *'Detailee'*
* **salary** - Staff salary in US dollars
* **pay_basis** - Pay period of salary: *'Per Annum'*, *'Per Diem'*
* **position_title** - Staff position title name

# Inspect dataset and variables


```r
# make a copy of data to work with
wh <- data

# check dimensions
dim(wh)
```

```
[1] 10600     7
```

```r
# check data types
str(wh, give.attr = FALSE)
```

```
spec_tbl_df [10,600 ?? 7] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ year          : num [1:10600] 1997 1997 1997 1997 1997 ...
 $ name          : chr [1:10600] "Abedin,Huma M." "Abrams,Lori E." "Alcorn,Brian A." "Allen,Jeannetta Pam" ...
 $ gender        : chr [1:10600] "Female" "Female" "Male" "Female" ...
 $ status        : chr [1:10600] "Employee" "Employee" "Employee" "Employee" ...
 $ salary        : num [1:10600] 27500 35000 25000 22000 27624 ...
 $ pay_basis     : chr [1:10600] "Per Annum" "Per Annum" "Per Annum" "Per Annum" ...
 $ position_title: chr [1:10600] "OFFICE MANAGER" "DEPUTY DIRECTOR, WHITE HOUSE GIFTS" "ASSISTANT DIRECTOR FOR ADVANCE" "STAFF ASSISTANT" ...
```

```r
# convert the categorical variables from character to factor
wh$gender <- as.factor(wh$gender)
wh$status <- as.factor(wh$status)
wh$pay_basis <- as.factor(wh$pay_basis)
wh$position_title <- as.factor(wh$position_title)

# check factor levels for gender
levels(wh$gender)
```

```
[1] "Female" "Male"  
```

```r
# check factor levels for status
levels(wh$status)
```

```
[1] "Detailee"             "Employee"             "Employee (part-time)"
```

```r
# check factor levels for pay_bias
levels(wh$pay_basis)
```

```
[1] "Per Annum" "Per Diem" 
```

```r
# 'position_title" will have too many levels, 
# first check its length rather than having it print all with levels() function
length(levels(wh$position_title))
```

```
[1] 2384
```

```r
# one way to display only a few levels is by using print() with factor(), then use max.levels argument
print(factor(wh$position_title[1:3]), max.levels = 3)
```

```
[1] OFFICE MANAGER                     DEPUTY DIRECTOR, WHITE HOUSE GIFTS
[3] ASSISTANT DIRECTOR FOR ADVANCE    
Levels: ASSISTANT DIRECTOR FOR ADVANCE DEPUTY DIRECTOR, WHITE HOUSE GIFTS OFFICE MANAGER
```

```r
# a better way is by using str() function which will help us see if I made the changes correctly, 
# as well as see a summary of levels
str(wh, give.attr = FALSE)
```

```
spec_tbl_df [10,600 ?? 7] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ year          : num [1:10600] 1997 1997 1997 1997 1997 ...
 $ name          : chr [1:10600] "Abedin,Huma M." "Abrams,Lori E." "Alcorn,Brian A." "Allen,Jeannetta Pam" ...
 $ gender        : Factor w/ 2 levels "Female","Male": 1 1 2 1 1 1 2 1 2 1 ...
 $ status        : Factor w/ 3 levels "Detailee","Employee",..: 2 2 2 2 2 2 1 1 2 2 ...
 $ salary        : num [1:10600] 27500 35000 25000 22000 27624 ...
 $ pay_basis     : Factor w/ 2 levels "Per Annum","Per Diem": 1 1 1 1 1 1 1 1 1 1 ...
 $ position_title: Factor w/ 2384 levels ". SENIOR ADVISOR TO THE PRESIDENT",..: 1430 898 53 2288 2373 1779 396 396 2373 1534 ...
```

```r
# rename levels of 'status' for better consistency
wh <- wh %>% 
  mutate(status = recode(status,
                         Employee = 'Employee, full-time',
                         'Employee (part-time)' = 'Employee, part-time'))

# check to see if the above modification worked correctly
levels(wh$status)
```

```
[1] "Detailee"            "Employee, full-time" "Employee, part-time"
```

```r
# I am happy with level names and order
# check column names
colnames(wh)
```

```
[1] "year"           "name"           "gender"         "status"        
[5] "salary"         "pay_basis"      "position_title"
```

```r
# rename 'pay_basis' and 'position_title' to be consistent with other column names
wh <- wh %>% 
  rename('pay period' = pay_basis,
         'position' = position_title)

# check changes
colnames(wh)
```

```
[1] "year"       "name"       "gender"     "status"     "salary"    
[6] "pay period" "position"  
```

I first made a copy of my *'data'* and assigned it to *'WH'*. This ensures I have the original dataset in its non-modified form for future reference if needed. Then I inspected the data frame as follows:

* Inspected **dimensions** of the data frame using base R dim() function which returns the number of rows and columns in the data frame. It contains 1600 rows and 7 columns (10600 observations and 7 variables).
* Inspected the **data types** using utils package str() function. This is a great way to compactly display the internal structure of R objects (R Core Team 2020). This shows that our data frame is [10,600 x 7] with 2 numeric variables and 5 character variables.

The numeric variables and the character variable *'name'* are in the correct data type. However, the variables *'gender'*, *'status'*, *'pay_basis'* and *'position_title'* need to be converted from character type to factor as they are categorical variables. Factors are the correct way of storing categorical data in R as they make it easier to use categorical variables into statistical models since they get coded as numbers. I used the base R as.factor() function to convert each of these variable to factor type. To **inspect factor variables**:

* Checked the levels of factor variables by using base R levels() function. However, I chose to not use this function to check factor level for 'position_title' as I knew that variable contained too many levels and did not want to print all the levels in output.
* For *'position_title'* variable, I chose to use length() and levels() function to confirm how many levels it contained. We can now see that this variable has 2384 levels.
* One way to display only a few levels is by using print() function along with factor() function and adjust max.levels with the desired number of levels we would like to display. I chose to show the first 3 levels for a quick review.
* Another way to inspect factor levels is by using str() function which will give a compact display of the entire internal structure of 'wh' data frame which includes factor levels (R Core Team 2020).

I was happy with the order of factor levels for all variables as well as their names except for the names used for levels of *'status'*. I renamed *'Employee'* and *'Employee (part-time)'* to *'Employee, full-time'* and *'Employee, part-time'* so their names were consistent with one another as well as a more precise description. To modify the names of factor levels, I used the mutate() and recode() functions from the dplyr package (Wickham et al. 2020). These functions allow for the direct modification of factor levels values while preserving the existing order of the levels (Wickham et al. 2020). One limitation with this method of re-coding factor level labels is that it will not generate an error message if you try to re-code levels that do not exist, for example, if you make any spelling errors (McNamara & Horton 2017). Therefore, it is important to check that the changes were made correctly by using levels() function. 

To **inspect column names**: 

* I used base R colnames() function. For better consistency and a more descriptive name, I chose to rename *'pay_basis'* and *'position_title'* to *'pay period'* and *'position'*.
* To modify the names of individual variables, I used the rename() function from the dplyr package. This function changes column names whilst preserving their order as well as data frame attributes without effecting rows and uses the syntax *new_name=old_name* (Wickham et al. 2020).

# Tidy data 

This data frame meets the tidy data principles set by Wickham and Grolemund (2017) since:

* Each variable has its own column.
* Each observation has its own row.
* Each value has its own cell.

As stated by Wickham and Grolemund (2017), these three rules are inter-related which leads to two practical instructions:

* Put each dataset in a tibble/data frame.
* Put each variable in a column.

Before moving on to the next task, I wanted to **inspect missing values** in the data frame. I used base R colSums() and is.na() functions to display the total missing values in each column:

* Total of 20 NA
* 3x NA in 'name'
* 3x NA in 'gender'
* 5x NA in 'salary'
* 7x NA in 'pay period'
* 2x NA in 'position'

To check their row number within a column, I used which() and is.na() functions. 

After inspecting the NA values, I have decided to not remove any rows with NA values. I think it will be a waste to omit information in all other fields of that row just because one value was missing. From the information that was printed by using colSums() and is.na() functions, it can be deduced that there are no rows with only NA values across all its columns. However, if it was needed to specifically check which rows would meet those conditions, either of the codes using which() and rowSums() functions with conditional arguments would work. This also confirmed that there are no rows that satisfy those condition. 

Since the amount of missing data from the numeric variable of the data is small relative to the size of the observations, we can use the *na.rm = TRUE* argument to exclude missing values from mathematical operations.

Lastly, I noticed that some values in 'salary' column were 0, therefore, I replaced those values to NA by using a logical condition and subsetting to over-write those values.


```r
# Inspect for NA values, identify which columns contain NAs
colSums(is.na(wh))
```

```
      year       name     gender     status     salary pay period   position 
         0          3          3          0          5          7          2 
```

```r
# check the row number for NAs in column 'name'
which(is.na(wh$name))
```

```
[1] 3243 3406 4123
```

```r
# check the row number for NAs in column 'gender'
which(is.na(wh$gender))
```

```
[1] 3243 3406 4123
```

```r
# check the row number for NAs in column 'salary'
which(is.na(wh$salary))
```

```
[1] 10244 10289 10345 10413 10466
```

```r
# check the row number for NAs in column 'pay period'
which(is.na(wh$`pay period`))
```

```
[1]  3243  3406 10244 10289 10345 10413 10466
```

```r
# check the row number for NAs in column 'position'
which(is.na(wh$`position`))
```

```
[1] 3243 3406
```

```r
# To specifically check if any rows have only NA values, 
# either of the following codes can be used
which(rowSums(is.na(wh)) == ncol(wh))
```

```
integer(0)
```

```r
which(!rowSums(!is.na(wh))) 
```

```
integer(0)
```

```r
#replace 0 in 'salary' to NA
wh[wh == 0] <- NA
```

# Summary statistics 


```r
# Summary statistics of 'salary' grouped by 'gender'
wh_stat1 <- wh %>% 
  group_by(gender) %>% 
  filter(!is.na(gender), salary>1000) %>% 
  summarise(
    Min = min(salary, na.rm = T),
    Median = median(salary, na.rm = T),
    Max = max(salary, na.rm = T),
    Mean = mean(salary, na.rm = T),
    SD = sd(salary, na.rm = T) 
  )

# display results
head(wh_stat1)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> gender </th>
   <th style="text-align:right;"> Min </th>
   <th style="text-align:right;"> Median </th>
   <th style="text-align:right;"> Max </th>
   <th style="text-align:right;"> Mean </th>
   <th style="text-align:right;"> SD </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Female </td>
   <td style="text-align:right;"> 21000 </td>
   <td style="text-align:right;"> 55746 </td>
   <td style="text-align:right;"> 183000 </td>
   <td style="text-align:right;"> 68678.08 </td>
   <td style="text-align:right;"> 36144.52 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Male </td>
   <td style="text-align:right;"> 21500 </td>
   <td style="text-align:right;"> 72700 </td>
   <td style="text-align:right;"> 239595 </td>
   <td style="text-align:right;"> 83201.26 </td>
   <td style="text-align:right;"> 42479.72 </td>
  </tr>
</tbody>
</table>

</div>

To display summary statistics of one numeric variable, *'salary'*, grouped by one categorical variable, *'gender'*, I used summerise() and group_by() functions from the dplyr package. This easily generates descriptive statistics by tables of groups. The summarise() function creates a new data frame which will have one or more rows for each combination of grouping variables and one column for each of the statistics that have been specified (Wickham et al. 2020). I also used the filter() function from dplyr which subsets observations based on their values and takes the name of the data frame as the first argument with the second and following arguments being the expressions that filter and subset the data frame (Wickham et al. 2020). I used conditional arguments to only select rows in *'gender'* column that did not have NA values and only keep rows in *'salary'* that had values greater than 1000 as any salary less than 1000 are most likely errors. 

When using the summarise() function from dyplr, the *na.rm=TRUE* argument must be used with the functions I used inside summarise() to exclude missing values from the mathematical operations. If this is not done, most functions will just return NA. 

The result of the descriptive statistics data frame show that women have on average consistently earned less than men as staff members of the White House office. 

The result of the statistics above caused me to be interested in exploring what the summary would be for full-time staff and detailees at another time as well as explore these findings further in the future. 

# Create a list


```r
# Create a list of numeric value for each response to categorical variables
# First assign level names of each categorical variable
gender_names <- levels(wh$gender) 
status_names <- levels(wh$status)
pay_names <- levels(wh$`pay period`)
position_names <- levels(wh$position)

# Create each list to have a character vector and numeric,
# I chose to give each categorical value unique numbers
list_gender <- list(
  gender_numeric = c(1:2),
  gender_levels = c(gender_names)
)

list_status <- list(
  status_numeric = c(3:5),
  status_levels = c(status_names)
)

list_pay <- list(
  pay_numeric = c(6:7),
  pay_levels = c(pay_names)
)

list_position <- list(
  position_numeric = c(8:(7+2384)),
  position_levels = c(position_names)
)

# Check structure of lists - gender
str(list_gender)
```

```
List of 2
 $ gender_numeric: int [1:2] 1 2
 $ gender_levels : chr [1:2] "Female" "Male"
```

```r
# Check structure of lists - status
str(list_status)
```

```
List of 2
 $ status_numeric: int [1:3] 3 4 5
 $ status_levels : chr [1:3] "Detailee" "Employee, full-time" "Employee, part-time"
```

```r
# Check structure of lists - pay
str(list_pay)
```

```
List of 2
 $ pay_numeric: int [1:2] 6 7
 $ pay_levels : chr [1:2] "Per Annum" "Per Diem"
```

```r
# Check structure of lists - position
str(list_position)
```

```
List of 2
 $ position_numeric: int [1:2384] 8 9 10 11 12 13 14 15 16 17 ...
 $ position_levels : chr [1:2384] ". SENIOR ADVISOR TO THE PRESIDENT" ". WATCH OFFICER" "ACCOUNT MANAGER" "ACCOUNTING MANAGER" ...
```

In this task I had to create a list containing a numeric value for each response to the categorical variables. To achieve this, I first assigned the levels of each categorical variable to represent a character vector. To create the lists, I used base R list() function. This function allows for names to be assigned to elements; therefore, creating an association between names and its value (R Cookbook 2nd Edition). The list of each variable contained two elements. One element being a character vector of each response and the other an integer vector with unique numbers to represent each response to each variable. str() function was then used to examine the structure of the newly created lists.

# Join the list


```r
# Join the list to wh df, numeric values must match categorical variables
# Convert each list to a data frame
df_gender <- as.data.frame(list_gender)
df_status <- as.data.frame(list_status)
df_pay <- as.data.frame(list_pay)
df_position <- as.data.frame(list_position)

# Join each data frame to WH 
wh_joined <- wh %>% left_join(
  df_gender, by = c('gender' = 'gender_levels')
)

wh_joined <- wh_joined %>% left_join(
  df_status, by = c('status' = 'status_levels')
)

wh_joined <- wh_joined %>% left_join(
  df_pay, by = c('pay period' = 'pay_levels')
)

wh_joined <- wh_joined %>% left_join(
  df_position, by = c('position' = 'position_levels')
)
# check changes
str(wh_joined, give.attr = FALSE)
```

```
spec_tbl_df [10,600 ?? 11] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ year            : num [1:10600] 1997 1997 1997 1997 1997 ...
 $ name            : chr [1:10600] "Abedin,Huma M." "Abrams,Lori E." "Alcorn,Brian A." "Allen,Jeannetta Pam" ...
 $ gender          : chr [1:10600] "Female" "Female" "Male" "Female" ...
 $ status          : chr [1:10600] "Employee, full-time" "Employee, full-time" "Employee, full-time" "Employee, full-time" ...
 $ salary          : num [1:10600] 27500 35000 25000 22000 27624 ...
 $ pay period      : chr [1:10600] "Per Annum" "Per Annum" "Per Annum" "Per Annum" ...
 $ position        : chr [1:10600] "OFFICE MANAGER" "DEPUTY DIRECTOR, WHITE HOUSE GIFTS" "ASSISTANT DIRECTOR FOR ADVANCE" "STAFF ASSISTANT" ...
 $ gender_numeric  : int [1:10600] 1 1 2 1 1 1 2 1 2 1 ...
 $ status_numeric  : int [1:10600] 4 4 4 4 4 4 3 3 4 4 ...
 $ pay_numeric     : int [1:10600] 6 6 6 6 6 6 6 6 6 6 ...
 $ position_numeric: int [1:10600] 1437 905 60 2295 2380 1786 403 403 2380 1541 ...
```

To be able to join the lists on to the *wh* data frame, each list needs to be first converted to a data frame. Base R as.data.frame() function was used as this will convert each element of a list to a column in the data frame (R Core Team 2020). I then joined the data frames one by one to my original *wh* data frame by using left_join() function from the dplyr package. The *key* that will combine the data frames were the names given to the character vectors in the lists, which became the variable name for the converted data frames, as well as their matching variable names in the *wh* data frame. For example, *gender* column from wh and *gender_levels* column from df_gender will be the key that match and join the two data frames together. By checking the structure of *wh_joined*, we can see that the join was completed correctly and the variable values matched to the unique numeric values that were assigned to them.

# Subsetting I


```r
# Subset 'wh' data frame using first 10 observations
wh_sub10 <- wh %>% slice(1:10)

# Check dimensions
dim(wh_sub10)
```

```
[1] 10  7
```

```r
# Convert to matrix 
wh_matrix <- as.matrix(wh_sub10)
# inspect matrix structure
str(wh_matrix)
```

```
 chr [1:10, 1:7] "1997" "1997" "1997" "1997" "1997" "1997" "1997" "1997" ...
 - attr(*, "dimnames")=List of 2
  ..$ : NULL
  ..$ : chr [1:7] "year" "name" "gender" "status" ...
```

To subset the first 10 observations of the data frame, I used the slice() function from the dplyr package and assigned it to a new object. The slice() function allows for subsetting of rows by using their integer locations (Wickham et al. 2020). Then to convert the data frame to a matrix, I used base R as.matrix() function. When checking the structure of the matrix with str() function, it shows that all the data of the matrix has been converted to character data type. This has happened because a matrix is a homogeneous two dimensional structure and therefore, every element must be of the same type. As a result of combining different data types, they have been *coerced* to the most flexible type by default. Data types from least to most flexible are: *logical<integer<double<character* (Wickham 2015).

# Subsetting II


```r
# Subset 'wh' data frame to include only the first and last variables. 
# Assign it to new object
wh2 <- wh %>% 
  select(year, position)
# display new df
head(wh2)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> year </th>
   <th style="text-align:left;"> position </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1997 </td>
   <td style="text-align:left;"> OFFICE MANAGER </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1997 </td>
   <td style="text-align:left;"> DEPUTY DIRECTOR, WHITE HOUSE GIFTS </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1997 </td>
   <td style="text-align:left;"> ASSISTANT DIRECTOR FOR ADVANCE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1997 </td>
   <td style="text-align:left;"> STAFF ASSISTANT </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1997 </td>
   <td style="text-align:left;"> WHITE HOUSE TELEPHONE OPERATOR </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1997 </td>
   <td style="text-align:left;"> SPECIAL ASSISTANT PERSONAL CORRESPONDENCE </td>
  </tr>
</tbody>
</table>

</div>

```r
# Save object as an .RData into current work directory
save(wh2, file = "wh2.RData")
```

To subset the data frame so it only includes the first and last variable, select() function from 'dplyr' package was used. This function subsets columns using their names and types (Wickham et al. 2020). I assigned the new data frame to an object and named it *'wh2'* which was then saved as an RData file by using the base R save() function. For the save() function to save the object as an RData file, the names of the objects to be saved is used as an argument as well as setting *file = " .RData"* (R Core Team 2020).  

# Create a new Data Frame


```r
# New data frame with 1 ordinal, 1 integer variable and 4 observations
df <- data.frame(
  "Education" = c("High School Diploma", "Bachelor's",
                  "Master's", "Doctoral"),
  "Total" = c(450L, 1000L, 200L, 50L),
  stringsAsFactors = TRUE
) 

# Order factor levels from low to high education
df$Education <- df$Education %>% factor(
  levels = c("High School Diploma", "Bachelor's", "Master's", "Doctoral"),
  ordered = TRUE
)

# check structure
str(df)
```

```
'data.frame':	4 obs. of  2 variables:
 $ Education: Ord.factor w/ 4 levels "High School Diploma"<..: 1 2 3 4
 $ Total    : int  450 1000 200 50
```

```r
# check levels
levels(df$Education)
```

```
[1] "High School Diploma" "Bachelor's"          "Master's"           
[4] "Doctoral"           
```

```r
# Create additional numeric vector to represent proportions based on 'Total'
Proportion <- c(450/1700*100, 1000/1700*100,
                200/1700*100, 50/1700*100)

#Round the values of proportion to 2 digits
Proportion <- round(Proportion, 2)

# Combine new variable to data frame using cbind()
df1 <- cbind(df, Proportion)
# check dimensions
dim(df1)
```

```
[1] 4 3
```

```r
# check attributes
attributes(df1)
```

```
$names
[1] "Education"  "Total"      "Proportion"

$class
[1] "data.frame"

$row.names
[1] 1 2 3 4
```

```r
# check structure
str(df1)
```

```
'data.frame':	4 obs. of  3 variables:
 $ Education : Ord.factor w/ 4 levels "High School Diploma"<..: 1 2 3 4
 $ Total     : int  450 1000 200 50
 $ Proportion: num  26.47 58.82 11.76 2.94
```

```r
# display df1
head(df1)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Education </th>
   <th style="text-align:right;"> Total </th>
   <th style="text-align:right;"> Proportion </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> High School Diploma </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:right;"> 26.47 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Bachelor's </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:right;"> 58.82 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Master's </td>
   <td style="text-align:right;"> 200 </td>
   <td style="text-align:right;"> 11.76 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Doctoral </td>
   <td style="text-align:right;"> 50 </td>
   <td style="text-align:right;"> 2.94 </td>
  </tr>
</tbody>
</table>

</div>

To create a new data frame with 2 variables (1x ordinal & 1x integer) and 4 observations, I used base R data.frame() function. I assigned the name *'Education'* to a vector of 4 character values representing levels of education to be the ordinal variable. The name *'Total'* was assigned to a vector of 4 integer numbers to represent the number of people having the type of education level from an imaginary population sample. This is to be the integer variable. To ensure R reads the numbers as integer and not numeric (double), L must be placed after the desired number. I also used the argument *stringsAsFactors=TRUE* (default setting) so that the character column is converted to a factor column, ensuring my categorical ordinal variable is in the correct data type (R Core Team 2020).

Since R orders factor levels alphabetically by default, I used the factor() function to manually order the levels in the correct order. To achieve this, the factor() function *levels=* argument needs to be assigned a character vector of the levels and in the order required, as well as using the *ordered=TRUE* argument which is then assigned to over-write the *'Education'* column via subsetting.  

I used the str() function to then show the structure of my variable and levels() function to show the levels of the ordinal variable. 

I created an additional numeric vector called *'Proportion'* by using c() function along with R's arithmetic operators to calculate the proportion of each integer value of the *'Total'* variable. To add this vector to the data frame, I used base R cbind() function which combines R objects by columns and assigned it to *'df1'*. The new data frame now has 3 variables. I then checked the attributes and dimensions of *'df1'* by using attributes() and dim() functions.

# Create another Data Frame


```r
# create data frame with 1 common variable to df1 & 1 new categorical variable: # common variable will be 'Education' 
# new categorical variable will be 'Abbreviation'
df2 <- data.frame(
  "Education" = c("High School Diploma", "Bachelor's",
                  "Master's", "Doctoral"),
  "Abbreviation" = c("HS", "BS", "MS", "PHD"),
  stringsAsFactors = TRUE
) 

#order factor levels
df2$Education <- df2$Education %>% factor(
  levels = c("High School Diploma", "Bachelor's", "Master's", "Doctoral"),
  ordered = TRUE
) 

df2$Abbreviation <- df2$Abbreviation %>% factor(
  levels = c("HS", "BS", "MS", "PHD"),
  ordered = TRUE
)

#join df2 to df1 using left_join() function and assign it to df3
df3 <- df2 %>% 
  left_join(df1, by = "Education")
# display df3
head(df3)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> Education </th>
   <th style="text-align:left;"> Abbreviation </th>
   <th style="text-align:right;"> Total </th>
   <th style="text-align:right;"> Proportion </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> High School Diploma </td>
   <td style="text-align:left;"> HS </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:right;"> 26.47 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Bachelor's </td>
   <td style="text-align:left;"> BS </td>
   <td style="text-align:right;"> 1000 </td>
   <td style="text-align:right;"> 58.82 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Master's </td>
   <td style="text-align:left;"> MS </td>
   <td style="text-align:right;"> 200 </td>
   <td style="text-align:right;"> 11.76 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Doctoral </td>
   <td style="text-align:left;"> PHD </td>
   <td style="text-align:right;"> 50 </td>
   <td style="text-align:right;"> 2.94 </td>
  </tr>
</tbody>
</table>

</div>

```r
# display structure of df3
str(df3)
```

```
'data.frame':	4 obs. of  4 variables:
 $ Education   : Ord.factor w/ 4 levels "High School Diploma"<..: 1 2 3 4
 $ Abbreviation: Ord.factor w/ 4 levels "HS"<"BS"<"MS"<..: 1 2 3 4
 $ Total       : int  450 1000 200 50
 $ Proportion  : num  26.47 58.82 11.76 2.94
```

I created a new data frame called *'df2'* which will share one common variable with *'df1'* from the previous task and has one new categorical variable. The common variable will be *'Education'* and the new variable will be *'Abbreviation'*. This variables values will be an abbreviations of the levels of education. This data frame was created by the same method as the previous task as well as the ordinal factors being ordered in the same manner. 

To join this data frame to the first dataset, *'df1'*, I used the left_join() function from the dplyr package. This function allows for the addition of new variables to one data frame based on matching observations in another (Wickham et al. 2020). This will result in the combination of variables from my 2 data frames, with the variables being added to the right of the first data frame used as the first argument in the left_join() function. For the join to work, a **key** variable is needed, which in this case will be the *'Education'* variable and I will set the key by using *by="Education"* argument in left_join(). 

I wanted the new variable *'Abbreviation'* to maintain it's position after *'Education'*. Therefore, I chose to use *'df2'* as the first argument in left_join() and *'df1'* as the second argument. This will result in the variables from *'df1'* being added to the right of *'df2'* data frame. 

# REFERENCES

Wickham, H & Hester, J 2020, *readr: Read Rectangular Text Data*, R package version 1.4.0, viewed 20 January 2021, [URL](http://CRAN.R-project.org/package=readr).

Wickham, H et al. 2020, *dplyr: A Grammar of Data Manipulation*, R package version 1.0.2, viewed 20 January 2021, [URL](http://CRAN.R-project.org/package=dplyr).

Xie, Y 2020, *knitr: A General-Purpose Package for Dynamic Report Generation in R*, R package version 1.30, viewed 20 January 2021, [URL](http://cran.r-project.org/web/packages/knitr/index.html).

Adiquant 2020, *Complete White House Staff Data from 1997-2020*, Version 1.0, data file, viewed 20 January 2021, [URL](http://www.kaggle.com/adiquant/white-house-staff-salaries-20172020). 

Wickham, H & Grolemund, G 2017, *R for Data Science*, 1st edn, O'Reilly Media, Sebastopol, California, USA.

R Core Team 2020, *R: A Language and Environment for Statistical Computing*, R Foundation for Statistical Computing, Vienna, Austria, viewed 20 January 2021, [URL](http://www.R-project.org/).

McNamara, A & Horton, NJ 2017, 'Wrangling categorical data in R', *PeerJ Preprints*, 5:e3163v2 [URL](https://doi.org/10.7287/peerj.preprints.3163v2).

Long, JD & Teetor, P 2019, *R Cookbook*, 2nd edn, O'Reilly Media, Sebastopol, California, USA.

Wickham, H 2015, *Advanced R*, 1st edn, CRC Press, Boca Raton, Florida, USA.
