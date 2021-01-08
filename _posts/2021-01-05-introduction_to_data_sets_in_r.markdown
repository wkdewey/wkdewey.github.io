---
layout: post
title:      "The R Language, data sets, and web applications"
date:       2021-01-05 12:02:19 -0500
permalink:  introduction_to_data_sets_in_r
---


Up until now in my technical training, especially with Flatiron School's Online Software Engineering bootcamp, I have focused mostly on web development. But my interests in data driven projects, including my [American Ancestries project](https://github.com/wkdewey/react-project-american-ancestries-frontend) which allows users to collect data on American ancestry groups and explore patterns and trends, has led me to explore tools more generally associated with data science. I do not see a hard and fast distinction between web development and data science, as many web applications rely on the rigorous analysis of data, and data science applications need to have a front end interface to be accessible to non-specialists. Data science techniques, and languages like R, could potentially enhance projects such as American Ancestries, allowing more sophisticated management and manipulation of the data that is displayed on the front end. The analysis of data is an increasingly important application of computation, and digital humanists in particular are using data science tools more and more to record information about data sets (textual corpora, historical maps, and so forth) and identify patterns and trends within them, using the power of computers and algorithms to analyze a far greater amount of data than could be done by hand, and solidify their intuitions with specific evidence and mathematical precision. University libraries and research departments rely on the R language as a scripting language for software applications that involve the analysis of statistical data, in fields including economics, public health, biology, and engineering.

**What is R?**

R is a domain specific language designed for statistics and data analysis; it is not a general purpose language like Ruby or JavaScript. Unlike some other competitors,  R can be downloaded at (https://cran.r-project.org/). It comes with a console, but RStudio has a better user interface and more tools; it can be downloaded at (https://rstudio.com/products/rstudio/download/). You can save your work as scripts with this software. Although R is not originally designed for web development (it predates the web), integration with applications written in Ruby or JavaScript is possible through RServe: https://www.rforge.net/Rserve/.

**R packages**

R has a number of packages that extend its functionality. To install the dslabs and tidyverse packages, type in the console
```
install.packages(c("tidyverse", "dslabs")）
```
"c("tidyverse", "dslabs") is an example of a character vector, about which more later. The dslabs package contains datasets and functions for learning data science and data analysis. Tidyverse is a more widely used set of packages that organizes information into *data frames *in a "tidy" format organized into rows and labeled columns. After installation, packages can be loaded like `library(dslabs)`.

**Data types**
*Variables* can simply be assigned with the `<-` symbol, for instance `a <- 2`. There are a number of possible data types, including integer, numeric (floating point), logical (Boolean values), character (strings that act as labels), and factors (like characters, but they can sort subsets of data) . To  determine the data type of a variable or other object you can use `class(a)`.

More commonly, we work with *vectors* and *data frames*.  A vector is a collection of entries that are all of the same data type. They can be created like `numbers <- c(1, 2, 3, 42)`, a numeric vector. This is an example of a character vector:
```
state <- c("Iowa", "Illinois", "Minnesota", "Pennsylvania")
```

Entries of a vector can be named: `numbers <- c(iowa = 1, illinois = 2, minnesota = 3, pennsylvania = 42)`. You can also assign names from one vector to another as follows
```
names(numbers) <- states
```
**Data frames**
A data frame is a collection of vectors, representing the columns of the data set. For instance, dslabs includes a dataset from [Gapminder](https://www.gapminder.org/), an organization that collects data about trends in human development. This data frame can be accessed with `data(gapminder)`. It contains the columns country, year, infant_mortality, life_expectancy, fertility, population

In order to examine the data set more closely, you can use the `str()` method which shows the structure of the data frame, and `head()` which shows the first six lines of the frame.
```
str(gapminder)

'data.frame':	10545 obs. of  9 variables:
 $ country         : Factor w/ 185 levels "Albania","Algeria",..: 1 2 3 4 5 6 7 8 9 10 ...
 $ year            : int  1960 1960 1960 1960 1960 1960 1960 1960 1960 1960 ...
 $ infant_mortality: num  115.4 148.2 208 NA 59.9 ...
 $ life_expectancy : num  62.9 47.5 36 63 65.4 ...
 $ fertility       : num  6.19 7.65 7.32 4.43 3.11 4.55 4.82 3.45 2.7 5.57 ...
 $ population      : num  1636054 11124892 5270844 54681 20619075 ...
 $ gdp             : num  NA 1.38e+10 NA NA 1.08e+11 ...
 $ continent       : Factor w/ 5 levels "Africa","Americas",..: 4 1 1 2 2 3 2 5 4 3 ...
 $ region          : Factor w/ 22 levels "Australia and New Zealand",..: 19 11 10 2 15 21 2 1 22 21 ...
```
This shows all of the columns, their data type, as well as  samples of data.
```
head(gapminder)

              country year infant_mortality life_expectancy fertility population
1             Albania 1960           115.40           62.87      6.19    1636054
2             Algeria 1960           148.20           47.50      7.65   11124892
3              Angola 1960           208.00           35.98      7.32    5270844
4 Antigua and Barbuda 1960               NA           62.97      4.43      54681
5           Argentina 1960            59.87           65.39      3.11   20619075
6             Armenia 1960               NA           66.86      4.55    1867396
           gdp continent          region
1           NA    Europe Southern Europe
2  13828152297    Africa Northern Africa
3           NA    Africa   Middle Africa
4           NA  Americas       Caribbean
5 108322326649  Americas   South America
6           NA      Asia    Western Asia
```
This shows a sample of data in tabular form, so that we can see (for instance) that Albania, in 1960, had a population of 1, 636,054, a life expectancy of 62.9 years.

You can view the full column with the accessor operator $:
```
gapminder$country
gapminder$country
   [1] Albania                        Algeria                       
   [3] Angola                         Antigua and Barbuda           
   [5] Argentina                      Armenia                       
   [7] Aruba                          Australia                     
   [9] Austria                        Azerbaijan                    
  [11] Bahamas                        Bahrain                       
  [13] Bangladesh                     Barbados                      
  [15] Belarus                        Belgium                       
  [17] Belize                         Benin                         
  [19] Bhutan                         Bolivia                       
  [21] Bosnia and Herzegovina         Botswana                      
  [23] Brazil                         Brunei                        
  [25] Bulgaria                       Burkina Faso                  
  [27] Burundi                        Cambodia                      
  [29] Cameroon                       Canada                        
  [31] Cape Verde                     Central African Republic   
	....
	[ reached getOption("max.print") -- omitted 9545 entries ]
185 Levels: Albania Algeria Angola Antigua and Barbuda Argentina Armenia ... Zimbabwe
```
The sort function sorts a column in increasing order
```
sort(gapminder$life_expectancy)
   [1] 13.20 18.10 19.04 19.55 21.69 21.91 28.16 29.61 29.83 30.08 30.40 30.53 30.79
  [14] 31.26 31.63 31.80 32.20 32.41 32.64 32.95 33.07 33.47 33.58 33.77 34.51 34.52
  [27] 34.94 35.21 35.24 35.27 35.43 35.45 35.54 35.70 35.71 35.72 35.88 35.94 35.95
	....
	 [ reached getOption("max.print") -- omitted 9545 entries ]
```
This shows that there was some country--perhaps due to war or disease or famine--that had an average life expectancies of 13.20 in a given year. (By the way, this does not necessarily mean that people typically die at age 13. Life expectancies are averages that can be heavily skewed, for instance, by high infant mortality).

****

In a future blog post, I will show how to manipulate data in a way that is more practical and finds more specific information.
**References:**
[HarvardX's course Data Science: R Basics.](https://courses.edx.org/courses/course-v1:HarvardX+PH125.1x+2T2020/course/)
[Rafael A. Irizarry's Introduction to Data Science (free online textbook)](https://rafalab.github.io/dsbook/)
[dslabs package](https://cran.r-project.org/web/packages/dslabs/index.html)
[Tidyverse packages](https://www.tidyverse.org/)
