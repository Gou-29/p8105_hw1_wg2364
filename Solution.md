Homework 1
================
Wenhao Gou - wg2364
2020.9.15

## 1\. Overview and preparation

This is the solution of homework 1 for course P8105. To setup, we need
to import package `tidyvers` and `palmerpenguins`

``` r
#Include packages
library(tidyverse)
library(palmerpenguins)
```

## 2.Solution of Question 1

### 2.1 Overview of the quesiton

This problem is intended to emphasize variable types and introduce
coercion; some awareness of how R treats numeric, character, and factor
variables is necessary for working with these data types in practice.

### 2.2 Create the dataframe

Use function `tibble`, we can create a data frame with random variables,
numbers, logical vectors and factor vectors. We can view this dataframe
use `solution_df`to see the dataframe and the data type of each vector.
(`dbl` indicate “double”, `lgl` indicate logic, `chr` indicate
character, and `fct` indicate factor)

``` r
#Create the dataframe:
solution_df <- tibble(
  vec_sample = rnorm(10) , 
  vec_logical = vec_sample > 0 ,
  vec_char = LETTERS [1:10] ,
  vec_factor = factor(c("A","B","C","C","B","A","A","B","C","C"))
)

#View the dataframe
solution_df
## # A tibble: 10 x 4
##    vec_sample vec_logical vec_char vec_factor
##         <dbl> <lgl>       <chr>    <fct>     
##  1     -0.963 FALSE       A        A         
##  2     -2.28  FALSE       B        B         
##  3     -1.64  FALSE       C        C         
##  4      0.802 TRUE        D        C         
##  5     -0.329 FALSE       E        B         
##  6     -0.125 FALSE       F        A         
##  7      0.528 TRUE        G        A         
##  8      0.496 TRUE        H        B         
##  9     -1.09  FALSE       I        C         
## 10      1.19  TRUE        J        C
```

Also, we can try to take mean of each of subsets:

``` r
#Take mean of each vector (we can also ues function "mean(pull(solution_df,col_you_want))" )
mean(solution_df$vec_sample)
## [1] -0.34219
mean(solution_df$vec_logical)
## [1] 0.4
mean(solution_df$vec_char)
## Warning in mean.default(solution_df$vec_char): argument is not numeric or
## logical: returning NA
## [1] NA
mean(solution_df$vec_factor)
## Warning in mean.default(solution_df$vec_factor): argument is not numeric or
## logical: returning NA
## [1] NA
```

This indicate that we can only do numeric calculation on numbers and
logic values. Also, we can deduce from the result that in the logic
vector, `TURE` equals to 1 and `FALSE` equal to 0. ( `4` numbers in
`vec_sample` are greater than 0 and `6` numbers in `vec_sample` are not
greater than 0)

For characters and factors, the function `mean` will return `NA` (Not a
number). This indicate that we cannot take mean or other numeric
calculation on them

### 2.3 Data type conversion:

In this part, we try to do these transformations:

  - Convert the logical vector to numeric, and multiply the random
    sample by the result

  - Convert the logical vector to a factor, and multiply the random
    sample by the result

  - Convert the logical vector to a factor and then convert the result
    to numeric, and multiply the random sample by the result

<!-- end list -->

``` r
#All the results were hided
#1 Logic -> Numeric, this part can operate with no error.
logic_to_numeric = as.numeric(solution_df$vec_logical)
logic_to_numeric * solution_df$vec_sample

#2 Logic -> Factor, error occured in line #2, but not in line #1
logic_to_factor = as.factor(solution_df$vec_logical)
logic_to_factor * solution_df$vec_sample

#3 Logic -> Factor -> Numeric, this part can operate with no error. 
factor_to_numeric = as.numeric(logic_to_factor)
factor_to_numeric * solution_df$vec_sample
```

The result can tell us more about the characteristic of these three data
type and how they were converted within each function:

  - Calculation result after the first transformation is:
    `0, 0, 0, 0.8023027, 0, 0, 0.5278827, 0.4964182, 0, 1.1884768`. In
    this transformation, R converted Logic vector to numeric term in
    this manner: `TURE = 1` and `FALSE = 0`. For example, `0.8023027`,
    position `4` in the `vec_sample` remain the same in this result,
    while `-0.9632361`, position `1` in the sample is equal to `0` in
    this result.

  - Calculation result after the second transformation are all `NA`. But
    in the first line of code in this transformation executed
    successfully, and have the result `FALSE, FALSE, FALSE, TRUE, FALSE,
    FALSE, TRUE, TRUE, FALSE, TRUE` as a roll of factor, which have two
    levels `FALSE`and `TRUE`.Note that is not same as the logic term,
    which can be convert into 0 and 1. As proved in the calculation of
    mean, this term cannot do numeric calculations.

  - Calculation result after the third transformation is `-0.9632361,
    -2.2823624, -1.6426644, 1.6046054, -0.3291619,
    -0.1246108, 1.0557655, 0.9928364, -1.0949451, 2.3769536`. In this
    transformation, R converted factor vector to numeric term in this
    manner: Factor\_Level 1 = 1, Factor\_Level 2 = 2 etc. The factor
    name are arranged alphabetically (increasing order, see `?factor`
    for detail). In this sample, level `FALSE` equal to 1 and level
    `TRUE` equal to 2. For example, Data `-0.9632361`, position `1` in
    the `vec_sample` is `-0.9632361` (`FALSE = 1`) in this result. Data
    `0.8023027`, position `4` in the `vec_sample` is `1.6046054`(`TRUE
    = 2`) in this result.

## Solution of Question 2

### 3.1 Overview of the question

This problem focuses the use of inline R code, plotting, and the
behavior of `ggplot` for variables of different types.

### 3.2 Load the dataset

Firstly, we need to load the dataset and have an overview about it. The
first line of the code will load two dataset in the package, `penguins`
and `penguins_raw` into the environment directly.In this quesiton, we
will use dataset `penguins` only.

``` r
#Load the dataset
data("penguins", package = "palmerpenguins")
```

### 3.2 Description of the dataset

We can use `str` function to see an overview of this dataset. We can
also type `?penguins` in the console for a detailed description of all
the variables. This dataset have `344` rows and `8` columns. In this
dataset, we have 8 variables `species, island, bill_length_mm,
bill_depth_mm, flipper_length_mm, body_mass_g, sex, year`. In these
variables, `species`,`island` are factors with 3 levels, `Adelie,
Chinstrap, Gentoo` and `Biscoe, Dream, Torgersen` respectively;
`bill_length_mm`, `bill_depth_mm`,`flipper_length_mm`,`body_mass_g` are
numbers denoting bill length(millimeters), bill depth(millimeters),
flipper length (millimeters), and body mass(grams), have means
`43.9219298`, `17.1511696`, `200.9152047`, `4201.754386` (missing values
are excluded); `sex` is a factor denote the sex of the penguin and
`year` is an integer range from 2007 to 2009. Missing value of variable
`flipper_length_mm` and `penguins$bill_length_mm` are in line `4` and
`272`. For a more detailed descriptive statistics, we can use these
functions:

``` r
#Other useful functions for descriptive statistics
str(penguins)
summary(penguins)
```

### 3.3 Plotting and saving

Next, we make a scatter plot of `flipper_length_mm` (y) vs
`bill_length_mm` (x); color points using the `species` variable.
Finally, we use `ggsave` function to save the result in the working
directory.

``` r
#Plotting
Penguins_Scatter <- penguins %>% 
  ggplot(aes(x=bill_length_mm,y=flipper_length_mm, color = species)) + 
  geom_point()

#Display the plot
Penguins_Scatter
```

![](Solution_files/figure-gfm/Plotting%20and%20saving-1.png)<!-- -->

``` r
#Saving the plot
ggsave('Penguins_Scatter.png', plot = Penguins_Scatter)
```
