R for Data Science Walkthrough Chapters 14-16
================
Erick Lu

-   [Chapter 14 Strings](#chapter-14-strings)
    -   [14.2.5 Exercises](#exercises)
    -   [14.3.1.1 Exercises](#exercises-1)
    -   [14.3.2.1 Exercises](#exercises-2)
    -   [14.3.3.1 Exercises](#exercises-3)
    -   [14.3.4.1 Exercises](#exercises-4)

This my walkthrough for chapters 14-16 for the book: *R for Data Science* by Hadley Wickham and Garrett Grolemund. Here I provide solutions to their exercises and some of my own notes and explorations.

Chapter 14 Strings
==================

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.5
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

14.2.5 Exercises
----------------

### 1. In code that doesn’t use stringr, you’ll often see paste() and paste0(). What’s the difference between the two functions? What stringr function are they equivalent to? How do the functions differ in their handling of NA?

The paste() function and paste0() functions are used to concatenate vectors after converting to character. In other words, combine converted strings together into one string. paste0() differs from paste() in that it always combines the strings without a separating value, whereas paste() allows you to specify the `sep` argument. When sep = "", paste() provides the same output as paste0(). The stringr function they are equivalent to is str\_c().

``` r
p = NA
paste("the value of p is ", p)
```

    ## [1] "the value of p is  NA"

``` r
str_c("the value of p is ", p)
```

    ## [1] NA

### 2. In your own words, describe the difference between the sep and collapse arguments to str\_c().

The sep argument allows you to specify what types of characters will lie between the items being joined. For example, you can choose to separate things with commas rather than the default no-space "". These separators will be inserted between each separate item fed into str\_c, and will not be placed if a pre-vectorized argument is provided. If you have a premade vector that you want to join into one string with a specified separator, the collapse argument should be used, in which you specify the type of separator you want inserted. Below is an example illustrating these points.

``` r
join_me <- c(88, "hello", "world")
str_c(88, "hello", "world")
```

    ## [1] "88helloworld"

``` r
str_c(88, "hello", "world", sep = ", ")
```

    ## [1] "88, hello, world"

``` r
# sep does not place the commas if a premade vector is fed into str_c
str_c(join_me, sep = ", ")
```

    ## [1] "88"    "hello" "world"

``` r
# use collapse instead if you want to place separators into premade vector
str_c(join_me, collapse = ", ")
```

    ## [1] "88, hello, world"

### 3. Use str\_length() and str\_sub() to extract the middle character from a string. What will you do if the string has an even number of characters?

To extract the middle character from a string, we can use str\_length()/2 to find the middle index of the string, then use str\_sub() to extract the character at that index. For even character strings, it would make sense to extract the two characters at the very center of the string. I wrote a function get\_middle() that would test whether a string has an even number or odd number of characters using %%, then apply the appropriate str\_sub() command to get the middle character or middle two characters.

``` r
get_middle <- function (my_str) {
  if (str_length(my_str)%%2 != 0){
    return (str_sub(my_str, ceiling(str_length(my_str)/2), ceiling(str_length(my_str)/2)))
  } else {
    return (str_sub(my_str, ceiling(str_length(my_str)/2), ceiling(str_length(my_str)/2+1)))
  }
}

get_middle ("qwert")
```

    ## [1] "e"

``` r
get_middle ("qwerty")
```

    ## [1] "er"

### 4. What does str\_wrap() do? When might you want to use it?

str\_wrap(), as the name suggests, wraps a string into a paragraph. You can specify how wide to make each line of the paragraph using the width argument. It does so by inserting a newline '' at the appropriate positions. This would be useful for printing very long strings. Below is an example of using str\_wrap().

``` r
long_string <- 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.'

long_string
```

    ## [1] "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."

``` r
str_wrap(long_string, width = 30)
```

    ## [1] "Lorem ipsum dolor sit amet,\nconsectetur adipiscing\nelit, sed do eiusmod tempor\nincididunt ut labore et dolore\nmagna aliqua."

### 5. What does str\_trim() do? What’s the opposite of str\_trim()?

str\_trim() will remove whitespace from the start and end of a string. The opposite of str\_trim() is str\_pad(), which can be used to add whitespace to either the left, right, or both sides of a string. Below is an example of using both functions.

``` r
whitespace <- "  why is there whitespace on both sides?   "
whitespace
```

    ## [1] "  why is there whitespace on both sides?   "

``` r
str_trim(whitespace)
```

    ## [1] "why is there whitespace on both sides?"

``` r
str_pad("add some whitespace to the left!", 40, side = "left")
```

    ## [1] "        add some whitespace to the left!"

### 6. Write a function that turns (e.g.) a vector c("a", "b", "c") into the string a, b, and c. Think carefully about what it should do if given a vector of length 0, 1, or 2.

We can write a function that checks the length of the vector using length() and uses if else statements to perform the appropriate action. For vectors of length 0, we should return an empty string "". For vectors of length 1, we should just return the single item as-is. For vectors of length &gt; 1, we should return the output specified in the question: "item-1, item-2, ... item-n-1, and item-n". To combine the items in the vector appropriately, we can first collapse the first n-1 items using str\_c( collapse = ", "), then use str\_c on this collapsed string with ", and ", and with the last item in the vector. This will insert the commas at the right locations, as well as the word "and" right before the last item in the vector.

``` r
# function name is convert_str
convert_str <- function (str_vector) {
  if (length(str_vector) == 0) {
    return ("")
  } else if (length (str_vector) == 1) {
    return (str_vector[1])
  } else {
    combined_minus_last <- str_c(str_vector[1:(length(str_vector)-1)], collapse = ", ") # collapse all items except the last one
    return( str_c (combined_minus_last, ", and ", str_vector[length(str_vector)])) # concatenate the word "and" and the last item
  }
}

convert_str(c("fee", "fi", "fo", "fum"))
```

    ## [1] "fee, fi, fo, and fum"

``` r
convert_str(c("fee"))
```

    ## [1] "fee"

``` r
convert_str(c())
```

    ## [1] ""

14.3.1.1 Exercises
------------------

### 1. Explain why each of these strings don’t match a : "", "\\", "\\".

To match a literal `\`, you need to use four backslashes, "\\\\". One backslash, two backslashes, and 3 backslashes wont work! One backslash "" won't work because it will "escape" the quotation, resulting in an error. Using 3 backslashes "\\" will also result in an error, and using 4 backslashes "\\\\" will finally let us match the single literal .

``` r
literal_backslash <- "hello_\\_world"
writeLines(literal_backslash)
```

    ## hello_\_world

``` r
# str_view(literal_backslash, "\\\\")

# errors
# x <- "\"
# x <- "\\\"
```

### 2. How would you match the sequence "'?

I would use the regex: ""'\\\\". This escapes the ", escapes the ', then uses the four backslashes to identify the literal .

``` r
example <- "hello_\"\'\\_world"
writeLines(example)
```

    ## hello_"'\_world

``` r
# str_view(example, "\"\'\\\\" )
```

### 3. What patterns will the regular expression ...... match? How would you represent it as a string?

If interepreted as a regular expression and not as the string-form of a regular expression, this will match a period `\.`, followed by any character `.`, followed by another period, any character, another period, and then any character. Below, I've represented it as a string in R. Because of the backslash convention, it will error if you try to plug `\..\..\..` in directly.

``` r
# test <- "\..\..\.." # errors

# need to double backslash within the string
test <- "\\..\\..\\.."
writeLines(test)
```

    ## \..\..\..

14.3.2.1 Exercises
------------------

### 1. How would you match the literal string "$^$"?

### 2. Given the corpus of common words in stringr::words, create regular expressions that find all words that:

-   Start with “y”.
-   End with “x”
-   Are exactly three letters long. (Don’t cheat by using str\_length()!)
-   Have seven letters or more. Since this list is long, you might want to use the match argument to str\_view() to show only the matching or non-matching words.

14.3.3.1 Exercises
------------------

### 1. Create regular expressions to find all words that:

-   Start with a vowel.
-   That only contain consonants. (Hint: thinking about matching “not”-vowels.)
-   End with ed, but not with eed.
-   End with ing or ise.

### 2. Empirically verify the rule “i before e except after c”.

### 3. Is “q” always followed by a “u”?

### 4. Write a regular expression that matches a word if it’s probably written in British English, not American English.

### 5. Create a regular expression that will match telephone numbers as commonly written in your country.

14.3.4.1 Exercises
------------------

### 1. Describe the equivalents of ?, +, \* in {m,n} form.

### 2. Describe in words what these regular expressions match: (read carefully to see if I’m using a regular expression or a string that defines a regular expression.)

-   `^.*$`
-   `"\\{.+\\}"`
-   `\d{4}-\d{2}-\d{2}`
-   `"\\\\{4}"`

### 3. Create regular expressions to find all words that:

-   Start with three consonants.
-   Have three or more vowels in a row.
-   Have two or more vowel-consonant pairs in a row.

### 4. Solve the beginner regexp crosswords at <https://regexcrossword.com/challenges/beginner>.
