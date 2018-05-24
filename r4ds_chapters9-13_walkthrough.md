R for Data Science Walkthrough Chapters 9-13
================
Erick Lu

-   [Chapter 9 Introduction](#chapter-9-introduction)
-   [Chapter 10 Tibbles](#chapter-10-tibbles)
    -   [10.5 Exercises](#exercises)
-   [Chapter 11 Data Import](#chapter-11-data-import)
    -   [11.2 Getting started - reading in files](#getting-started---reading-in-files)
    -   [11.2.2 Exercises](#exercises-1)
    -   [11.3 Parsing a vector](#parsing-a-vector)
    -   [11.3.5 Exercises](#exercises-2)
    -   [11.4 and 11.5 Parsing a file & Writing to a file](#and-11.5-parsing-a-file-writing-to-a-file)

This my walkthrough for chapters 9-13 for the book: *R for Data Science* by Hadley Wickham and Garrett Grolemund. Here I provide solutions to their exercises and some of my own notes and explorations.

Chapter 9 Introduction
======================

This chapter provides a list of the content covered in chapter 10-16, which includes tibbles, data import, tidy data, relational data, strings (regular expressions), factors, and dates/times.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.8.0     ✔ stringr 1.3.0
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## Warning: package 'ggplot2' was built under R version 3.3.2

    ## Warning: package 'readr' was built under R version 3.3.2

    ## Warning: package 'purrr' was built under R version 3.3.2

    ## Warning: package 'dplyr' was built under R version 3.3.2

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

Chapter 10 Tibbles
==================

You can create tibbles from existing data frames using as\_tibble(), or create brand new tibbles using tibble():

``` r
tibble(
  x = 1:5, 
  y = 1, 
  z = x ^ 2 + y
)
```

    ## # A tibble: 5 x 3
    ##       x     y     z
    ##   <int> <dbl> <dbl>
    ## 1     1    1.    2.
    ## 2     2    1.    5.
    ## 3     3    1.   10.
    ## 4     4    1.   17.
    ## 5     5    1.   26.

A cousin of tibble(), tribble(), can also be used as a way to manually enter data into a tibble format:

``` r
tribble(
  ~x, ~y, ~z,
  #--|--|----
  "a", 2, 3.6,
  "b", 1, 8.5
)
```

    ## # A tibble: 2 x 3
    ##   x         y     z
    ##   <chr> <dbl> <dbl>
    ## 1 a        2.  3.60
    ## 2 b        1.  8.50

You can also use non-syntactic names for variables in tibbles:

``` r
tb <- tibble(
  `:)` = "smile", 
  ` ` = "space",
  `2000` = "number"
)
tb
```

    ## # A tibble: 1 x 3
    ##   `:)`  ` `   `2000`
    ##   <chr> <chr> <chr> 
    ## 1 smile space number

When compared to a data.frame in baseR, the tibble looks more user-friendly. Calling a tibble automatically provides only the beginning chunk of the data rather than filling up your entire console (think if it as default head(data.frame) display). Other nice features include not converting strings to factors or changing variable names.

To convert tables to or from data frames, use as\_tibble() and as.data.frame():

``` r
class(iris)
```

    ## [1] "data.frame"

``` r
class(as_tibble(iris))
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

``` r
class(as.data.frame(as_tibble(iris)))
```

    ## [1] "data.frame"

You can select columns in tibbles the same way you would with a data.frame:

``` r
df <- tibble(
  x = runif(5),
  y = rnorm(5)
)
# extract column 'x' using either $ or [[]]
df$x
```

    ## [1] 0.1240509 0.9948180 0.6826519 0.2027543 0.6544553

``` r
df[["x"]]
```

    ## [1] 0.1240509 0.9948180 0.6826519 0.2027543 0.6544553

``` r
df[[1]]
```

    ## [1] 0.1240509 0.9948180 0.6826519 0.2027543 0.6544553

10.5 Exercises
--------------

### 1. How can you tell if an object is a tibble? (Hint: try printing mtcars, which is a regular data frame).

You can tell if an object is a tibble because the output you get by calling it will say "tibble"! For example, calling the diamonds tibble returns :`# A tibble: 53,940 x 10` as the first line of the output. Also you can tell something is a tibble based on the class specifications underneath each variable name. A tibble will also only print out the first 10 rows by default, whereas a data.frame will print out as many as the console allows. Last, the definitive way to tell something is a tibble is to use the class() function.

``` r
class(diamonds)
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

``` r
class(mtcars)
```

    ## [1] "data.frame"

### 2. Compare and contrast the following operations on a data.frame and equivalent tibble. What is different? Why might the default data frame behaviours cause you frustration?

On a data.frame, `df$x` will still return the values for column xyz. This behavior does not occur for a tibble, which requires the exact name of the column `df$xyz`. This data.frame feature might cause frustration if you have columns in your dataset with the same prefix, in which you might fetch the wrong column. The other functions between data.frame and tibble work the same way. One distinction to note is that, when creating the data.frame, "a" is considered a factor with 1 level. When creating the tibble, "a" is not converted into a factor.

``` r
df <- data.frame(abc = 1, xyz = "a")
df$x
```

    ## [1] a
    ## Levels: a

``` r
df[, "xyz"]
```

    ## [1] a
    ## Levels: a

``` r
df[, c("abc", "xyz")]
```

    ##   abc xyz
    ## 1   1   a

``` r
df <- tibble(abc = 1, xyz = "a")
df$x
```

    ## Warning: Unknown or uninitialised column: 'x'.

    ## NULL

``` r
df[, "xyz"]
```

    ## # A tibble: 1 x 1
    ##   xyz  
    ##   <chr>
    ## 1 a

``` r
df[, c("abc", "xyz")]
```

    ## # A tibble: 1 x 2
    ##     abc xyz  
    ##   <dbl> <chr>
    ## 1    1. a

### 3. If you have the name of a variable stored in an object, e.g. var &lt;- "mpg", how can you extract the reference variable from a tibble?

If the name of the variable is stored in an object, you can pass the object in lieu of the variable name using \[\[\]\] or \[\] just as you would do so with the explicit variable name. You can even pass the object and another variable name to obtain multiple reference variables using c(). I provide an example below using the diamonds dataset.

``` r
var <- "carat"
var2 <- c("carat","price")

# extract only carat
diamonds[,var]
```

    ## # A tibble: 53,940 x 1
    ##    carat
    ##    <dbl>
    ##  1 0.230
    ##  2 0.210
    ##  3 0.230
    ##  4 0.290
    ##  5 0.310
    ##  6 0.240
    ##  7 0.240
    ##  8 0.260
    ##  9 0.220
    ## 10 0.230
    ## # ... with 53,930 more rows

``` r
#extract carat and price
diamonds[,c(var,"price")]
```

    ## # A tibble: 53,940 x 2
    ##    carat price
    ##    <dbl> <int>
    ##  1 0.230   326
    ##  2 0.210   326
    ##  3 0.230   327
    ##  4 0.290   334
    ##  5 0.310   335
    ##  6 0.240   336
    ##  7 0.240   336
    ##  8 0.260   337
    ##  9 0.220   337
    ## 10 0.230   338
    ## # ... with 53,930 more rows

``` r
diamonds[,var2]
```

    ## # A tibble: 53,940 x 2
    ##    carat price
    ##    <dbl> <int>
    ##  1 0.230   326
    ##  2 0.210   326
    ##  3 0.230   327
    ##  4 0.290   334
    ##  5 0.310   335
    ##  6 0.240   336
    ##  7 0.240   336
    ##  8 0.260   337
    ##  9 0.220   337
    ## 10 0.230   338
    ## # ... with 53,930 more rows

### 4. Practice referring to non-syntactic names in the following data frame by:

``` r
annoying <- tibble(
  `1` = 1:10,
  `2` = `1` * 2 + rnorm(length(`1`))
)
```

-   Extracting the variable called 1.

``` r
annoying[,"1"]
```

    ## # A tibble: 10 x 1
    ##      `1`
    ##    <int>
    ##  1     1
    ##  2     2
    ##  3     3
    ##  4     4
    ##  5     5
    ##  6     6
    ##  7     7
    ##  8     8
    ##  9     9
    ## 10    10

-   Plotting a scatterplot of 1 vs 2.

``` r
ggplot(annoying, aes(`1`,`2`))+
  geom_point()
```

![](r4ds_chapters9-13_walkthrough_files/figure-markdown_github/unnamed-chunk-12-1.png)

-   Creating a new column called 3 which is 2 divided by 1.

``` r
annoying %>%
  mutate(`3` = `2`/`1`)
```

    ## Warning: package 'bindrcpp' was built under R version 3.3.2

    ## # A tibble: 10 x 3
    ##      `1`   `2`   `3`
    ##    <int> <dbl> <dbl>
    ##  1     1  1.50  1.50
    ##  2     2  4.05  2.03
    ##  3     3  6.12  2.04
    ##  4     4  9.38  2.35
    ##  5     5  8.50  1.70
    ##  6     6 13.0   2.16
    ##  7     7 13.9   1.99
    ##  8     8 16.4   2.05
    ##  9     9 16.7   1.85
    ## 10    10 18.5   1.85

-   Renaming the columns to one, two and three.

``` r
annoying %>%
  mutate(`3` = `2`/`1`) %>%
  rename(one = `1`, two = `2`, three = `3` )
```

    ## # A tibble: 10 x 3
    ##      one   two three
    ##    <int> <dbl> <dbl>
    ##  1     1  1.50  1.50
    ##  2     2  4.05  2.03
    ##  3     3  6.12  2.04
    ##  4     4  9.38  2.35
    ##  5     5  8.50  1.70
    ##  6     6 13.0   2.16
    ##  7     7 13.9   1.99
    ##  8     8 16.4   2.05
    ##  9     9 16.7   1.85
    ## 10    10 18.5   1.85

### 5. What does tibble::enframe() do? When might you use it?

Taken from the documentation: "enframe() converts named atomic vectors or lists to two-column data frames. For unnamed vectors, the natural sequence is used as name column." I might use this when I have a vector that I want to turn into a data.frame for graphing using ggplot, which requires data be in data.frame or tibble.

``` r
x = rnorm(100)
names(x) <- c(5:104)
enframe(x)
```

    ## # A tibble: 100 x 2
    ##    name    value
    ##    <chr>   <dbl>
    ##  1 5      0.590 
    ##  2 6      1.22  
    ##  3 7     -1.44  
    ##  4 8     -2.80  
    ##  5 9      0.629 
    ##  6 10     0.409 
    ##  7 11     0.781 
    ##  8 12     0.239 
    ##  9 13    -0.671 
    ## 10 14    -0.0352
    ## # ... with 90 more rows

``` r
class(enframe(x))
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

### 6. What option controls how many additional column names are printed at the footer of a tibble?

The documentation for ?format.tbl (tibble formatting) says that the n\_extra argument will control how many additional columns to print abbreviated information for. The example provided in the documentation is below, which only prints 2 of the additional columns (whereas the unmodified print(flights) would yield 5 additional columns in the footer).

``` r
print(nycflights13::flights, n_extra = 2)
```

    ## # A tibble: 336,776 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time
    ##    <int> <int> <int>    <int>          <int>     <dbl>    <int>
    ##  1  2013     1     1      517            515        2.      830
    ##  2  2013     1     1      533            529        4.      850
    ##  3  2013     1     1      542            540        2.      923
    ##  4  2013     1     1      544            545       -1.     1004
    ##  5  2013     1     1      554            600       -6.      812
    ##  6  2013     1     1      554            558       -4.      740
    ##  7  2013     1     1      555            600       -5.      913
    ##  8  2013     1     1      557            600       -3.      709
    ##  9  2013     1     1      557            600       -3.      838
    ## 10  2013     1     1      558            600       -2.      753
    ## # ... with 336,766 more rows, and 12 more variables: sched_arr_time <int>,
    ## #   arr_delay <dbl>, …

Chapter 11 Data Import
======================

11.2 Getting started - reading in files
---------------------------------------

To practice various utilities for reading in files, we can use inline csv designation, which requires proper newline designation. Below are some examples of reading in inline csv chunks with various arguments tailored for the type of data being read in.

``` r
# basic read_csv()
read_csv("a,b,c
         1,2,3 
         4,5,6")
```

    ## # A tibble: 2 x 3
    ##       a     b     c
    ##   <int> <int> <int>
    ## 1     1     2     3
    ## 2     4     5     6

``` r
# read in data, ignoring metadata lines
read_csv("The first line of metadata
The second line of metadata
  x,y,z
  1,2,3", skip = 2)
```

    ## # A tibble: 1 x 3
    ##       x     y     z
    ##   <int> <int> <int>
    ## 1     1     2     3

``` r
# designate lines to skip that start with specific symbol
read_csv("# A comment I want to skip
  x,y,z
  1,2,3", comment = "#")
```

    ## # A tibble: 1 x 3
    ##       x     y     z
    ##   <int> <int> <int>
    ## 1     1     2     3

``` r
# read in file that doesnt have column names
read_csv("1,2,3\n4,5,6", col_names = FALSE)
```

    ## # A tibble: 2 x 3
    ##      X1    X2    X3
    ##   <int> <int> <int>
    ## 1     1     2     3
    ## 2     4     5     6

``` r
# read in file and specify column names
read_csv("1,2,3\n4,5,6", col_names = c("x", "y", "z"))
```

    ## # A tibble: 2 x 3
    ##       x     y     z
    ##   <int> <int> <int>
    ## 1     1     2     3
    ## 2     4     5     6

``` r
# read in file, replacing symbol with missing values (NA)
read_csv("a,b,c\n1,2,.", na = ".")
```

    ## # A tibble: 1 x 3
    ##       a     b c    
    ##   <int> <int> <chr>
    ## 1     1     2 <NA>

11.2.2 Exercises
----------------

### 1. What function would you use to read a file where fields were separated with “|”?

I would use `read_delim()` to read in a file where fields are separated with "|". For example:

``` r
read_delim ("a|b|c
            1|2|3
            4|5|6", "|")
```

    ## # A tibble: 2 x 3
    ##   a                   b     c
    ##   <chr>           <int> <int>
    ## 1 "            1"     2     3
    ## 2 "            4"     5     6

### 2. Apart from file, skip, and comment, what other arguments do read\_csv() and read\_tsv() have in common?

Based on the documentation, read\_csv() and read\_tsv() have col\_names, col\_types, locale, na, quoted\_na, quote, trim\_ws, n\_max, guess\_max, and progress.

### 3. What are the most important arguments to read\_fwf()?

The most important arguments are the file, and the col\_positions arguments. There are many options to specify col\_positions, including fwf\_empty(), fwf\_widths(), fwf\_positions(), and fwf\_cols(). Below is the example provided in the documentation:

``` r
fwf_sample <- readr_example("fwf-sample.txt")
cat(read_lines(fwf_sample))
```

    ## John Smith          WA        418-Y11-4111 Mary Hartford       CA        319-Z19-4341 Evan Nolan          IL        219-532-c301

``` r
# You can specify column positions in several ways:
# 1. Guess based on position of empty columns
read_fwf(fwf_sample, fwf_empty(fwf_sample, col_names = c("first", "last", "state", "ssn")))
```

    ## Parsed with column specification:
    ## cols(
    ##   first = col_character(),
    ##   last = col_character(),
    ##   state = col_character(),
    ##   ssn = col_character()
    ## )

    ## # A tibble: 3 x 4
    ##   first last     state ssn         
    ##   <chr> <chr>    <chr> <chr>       
    ## 1 John  Smith    WA    418-Y11-4111
    ## 2 Mary  Hartford CA    319-Z19-4341
    ## 3 Evan  Nolan    IL    219-532-c301

``` r
# 2. A vector of field widths
read_fwf(fwf_sample, fwf_widths(c(20, 10, 12), c("name", "state", "ssn")))
```

    ## Parsed with column specification:
    ## cols(
    ##   name = col_character(),
    ##   state = col_character(),
    ##   ssn = col_character()
    ## )

    ## # A tibble: 3 x 3
    ##   name          state ssn         
    ##   <chr>         <chr> <chr>       
    ## 1 John Smith    WA    418-Y11-4111
    ## 2 Mary Hartford CA    319-Z19-4341
    ## 3 Evan Nolan    IL    219-532-c301

``` r
# 3. Paired vectors of start and end positions
read_fwf(fwf_sample, fwf_positions(c(1, 30), c(10, 42), c("name", "ssn")))
```

    ## Parsed with column specification:
    ## cols(
    ##   name = col_character(),
    ##   ssn = col_character()
    ## )

    ## # A tibble: 3 x 2
    ##   name       ssn         
    ##   <chr>      <chr>       
    ## 1 John Smith 418-Y11-4111
    ## 2 Mary Hartf 319-Z19-4341
    ## 3 Evan Nolan 219-532-c301

``` r
# 4. Named arguments with start and end positions
read_fwf(fwf_sample, fwf_cols(name = c(1, 10), ssn = c(30, 42)))
```

    ## Parsed with column specification:
    ## cols(
    ##   name = col_character(),
    ##   ssn = col_character()
    ## )

    ## # A tibble: 3 x 2
    ##   name       ssn         
    ##   <chr>      <chr>       
    ## 1 John Smith 418-Y11-4111
    ## 2 Mary Hartf 319-Z19-4341
    ## 3 Evan Nolan 219-532-c301

``` r
# 5. Named arguments with column widths
read_fwf(fwf_sample, fwf_cols(name = 20, state = 10, ssn = 12))
```

    ## Parsed with column specification:
    ## cols(
    ##   name = col_character(),
    ##   state = col_character(),
    ##   ssn = col_character()
    ## )

    ## # A tibble: 3 x 3
    ##   name          state ssn         
    ##   <chr>         <chr> <chr>       
    ## 1 John Smith    WA    418-Y11-4111
    ## 2 Mary Hartford CA    319-Z19-4341
    ## 3 Evan Nolan    IL    219-532-c301

### 4. Sometimes strings in a CSV file contain commas. To prevent them from causing problems they need to be surrounded by a quoting character, like " or '. By convention, read\_csv() assumes that the quoting character will be ", and if you want to change it you’ll need to use read\_delim() instead. What arguments do you need to specify to read the following text into a data frame? `"x,y\n1,'a,b'"`

In this example, the string is surrounded by the quoting character '. This is not what read\_csv() default assumes. In the documentation it looks like you can specify the quote argument for both read\_csv() and read\_delim(). Below I read in the text using both methods, specifying `quote = "\'"`.

``` r
read_csv ("x,y\n1,'a,b'", quote = "\'")
```

    ## # A tibble: 1 x 2
    ##       x y    
    ##   <int> <chr>
    ## 1     1 a,b

``` r
read_delim("x,y\n1,'a,b'", delim = ",", quote = "\'")
```

    ## # A tibble: 1 x 2
    ##       x y    
    ##   <int> <chr>
    ## 1     1 a,b

### 5. Identify what is wrong with each of the following inline CSV files. What happens when you run the code?

I have annotated the code below with the problems for each of the inline CSV files. The output should be displayed if you are viewing the rendered `.md file` (you won't see the output if this is a `.Rmd` file).

``` r
# There are not enough column names to go with the amount of columns in the data.
read_csv("a,b\n1,2,3\n4,5,6")
```

    ## Warning: 2 parsing failures.
    ## row # A tibble: 2 x 5 col     row col   expected  actual    file         expected   <int> <chr> <chr>     <chr>     <chr>        actual 1     1 <NA>  2 columns 3 columns literal data file 2     2 <NA>  2 columns 3 columns literal data

    ## # A tibble: 2 x 2
    ##       a     b
    ##   <int> <int>
    ## 1     1     2
    ## 2     4     5

``` r
# Mismatched numbers of columns again. The first row only has 2, whereas the 2nd row has 4, and the header only has 3.
read_csv("a,b,c\n1,2\n1,2,3,4")
```

    ## Warning: 2 parsing failures.
    ## row # A tibble: 2 x 5 col     row col   expected  actual    file         expected   <int> <chr> <chr>     <chr>     <chr>        actual 1     1 <NA>  3 columns 2 columns literal data file 2     2 <NA>  3 columns 4 columns literal data

    ## # A tibble: 2 x 3
    ##       a     b     c
    ##   <int> <int> <int>
    ## 1     1     2    NA
    ## 2     1     2     3

``` r
# Two header columns, and one column of data. Also the "1" is still being read in as an integer.
read_csv("a,b\n\"1")
```

    ## Warning: 2 parsing failures.
    ## row # A tibble: 2 x 5 col     row col   expected                     actual    file         expected   <int> <chr> <chr>                        <chr>     <chr>        actual 1     1 a     closing quote at end of file ""        literal data file 2     1 <NA>  2 columns                    1 columns literal data

    ## # A tibble: 1 x 2
    ##       a b    
    ##   <int> <chr>
    ## 1     1 <NA>

``` r
# Since there are both integer values and character values in the same column, both columns are defined as character.
read_csv("a,b\n1,2\na,b")
```

    ## # A tibble: 2 x 2
    ##   a     b    
    ##   <chr> <chr>
    ## 1 1     2    
    ## 2 a     b

``` r
# I assume ";" was meant to be the delimiter. The csv only has one observation. Use read_delim("a;b\n1;3", ";") instead.
read_csv("a;b\n1;3")
```

    ## # A tibble: 1 x 1
    ##   `a;b`
    ##   <chr>
    ## 1 1;3

11.3 Parsing a vector
---------------------

11.3.5 Exercises
----------------

### 1. What are the most important arguments to locale()?

### 2. What happens if you try and set decimal\_mark and grouping\_mark to the same character? What happens to the default value of grouping\_mark when you set decimal\_mark to “,”? What happens to the default value of decimal\_mark when you set the grouping\_mark to “.”?

### 3. I didn’t discuss the date\_format and time\_format options to locale(). What do they do? Construct an example that shows when they might be useful.

### 4. If you live outside the US, create a new locale object that encapsulates the settings for the types of file you read most commonly.

### 5. What’s the difference between read\_csv() and read\_csv2()?

### 6. What are the most common encodings used in Europe? What are the most common encodings used in Asia? Do some googling to find out.

### 7. Generate the correct format string to parse each of the following dates and times:

``` r
d1 <- "January 1, 2010"
d2 <- "2015-Mar-07"
d3 <- "06-Jun-2017"
d4 <- c("August 19 (2015)", "July 1 (2015)")
d5 <- "12/30/14" # Dec 30, 2014
t1 <- "1705"
t2 <- "11:15:10.12 PM"
```

11.4 and 11.5 Parsing a file & Writing to a file
------------------------------------------------
