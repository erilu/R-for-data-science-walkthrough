R for Data Science Walkthrough Chapters 14-16
================
Erick Lu

-   [Chapter 14 Strings](#chapter-14-strings)
    -   [14.2.5 Exercises](#exercises)
    -   [14.3.1.1 Exercises](#exercises-1)
    -   [14.3.2.1 Exercises](#exercises-2)
    -   [14.3.3.1 Exercises](#exercises-3)
    -   [14.3.4.1 Exercises](#exercises-4)
    -   [14.3.5.1 Exercises](#exercises-5)
    -   [14.4.2 Exercises](#exercises-6)
    -   [14.4.3.1 Exercises](#exercises-7)
    -   [14.4.4.1 Exercises](#exercises-8)
    -   [14.4.5.1 Exercises](#exercises-9)
    -   [14.4.6.1 Exercises](#exercises-10)
    -   [14.5.1 Exercises](#exercises-11)
    -   [14.7.1 Exercises](#exercises-12)
-   [Chapter 15: Factors](#chapter-15-factors)
    -   [15.3.1 Exercise](#exercise)
    -   [15.4.1 Exercises](#exercises-13)
    -   [15.5.1 Exercises](#exercises-14)
-   [Chapter 16: Dates and times](#chapter-16-dates-and-times)
    -   [16.2.4 Exercises](#exercises-15)
    -   [16.3.4 Exercises](#exercises-16)
    -   [16.4.5 Exercises](#exercises-17)

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

For Chapter 14, since the github\_document R markdown format does not support html output, I will comment out the "str\_view()" commands and replace them with str\_extract() or str\_subset(), as appropriate. This will still let you observe which words or strings are matched by the regex. You can also type the str\_view() command into your own RStudio instance to observe the output.

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

str\_wrap(), as the name suggests, wraps a string into a paragraph. You can specify how wide to make each line of the paragraph using the width argument. It does so by inserting a newline `'\n'` at the appropriate positions. This would be useful for printing very long strings. Below is an example of using str\_wrap().

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

### 1. Explain why each of these strings don’t match a `\`: `"\", "\\", "\\\"`.

To match a literal `\`, you need to use four backslashes, `"\\\\"`. One backslash, two backslashes, and 3 backslashes wont work! One backslash `"\"` won't work because it will "escape" the quotation, resulting in an error. Using 3 backslashes `"\\\"` will also result in an error, and using 4 backslashes `"\\\\"` will finally let us match the single literal `\`.

``` r
literal_backslash <- "hello_\\_world"
# str_view(literal_backslash, "\\\\")
str_extract(literal_backslash, "\\\\")
```

    ## [1] "\\"

``` r
# errors
# x <- "\"
# x <- "\\\"
```

### 2. How would you match the sequence `"'\`?

I would use the regex: `"\"\'\\\\"`. This escapes the ", escapes the ', then uses the four backslashes to identify the literal `\`.

``` r
example <- "hello_\"\'\\_world"
writeLines(example)
```

    ## hello_"'\_world

``` r
# str_view(example, "\"\'\\\\" )
str_extract(example, "\"\'\\\\" )
```

    ## [1] "\"'\\"

### 3. What patterns will the regular expression `\..\..\..` match? How would you represent it as a string?

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

### 1. How would you match the literal string `"$^$"`?

Below, i use `"\\$\\^\\$"` to match the literal string `"$^$"`. This is because you have to double backslash the `$` and ^ characters, which usually serve the purpose of matching the end or start of strings, respectively.

``` r
test <- "$^$hello"
writeLines(test)
```

    ## $^$hello

``` r
# str_view(test, "\\$\\^\\$")
str_extract(test, "\\$\\^\\$")
```

    ## [1] "$^$"

### 2. Given the corpus of common words in stringr::words, create regular expressions that find all words that:

-   Start with “y”.
-   End with “x”
-   Are exactly three letters long. (Don’t cheat by using str\_length()!)
-   Have seven letters or more. Since this list is long, you might want to use the match argument to str\_view() to show only the matching or non-matching words.

To match words that start with y, use the "^" anchor before "y". To match words that end with x, use the `"\$"` anchor after "x". To match words that are exactly three letters long, we can use both anchors "^" and `"\$"` wrapped around 3 dots, which specify a series of any 3 characters. To match words that have seven letters or more, we can use the "^" anchor plus seven dots, which specify a series of any seven characters.

``` r
# display a sample of stringr::words
head(words)
```

    ## [1] "a"        "able"     "about"    "absolute" "accept"   "account"

``` r
# starts with "y"
# str_view(words, "^y", match = T)
str_subset(words, "^y")
```

    ## [1] "year"      "yes"       "yesterday" "yet"       "you"       "young"

``` r
# ends with "x"
# str_view(words, "x$", match = T)
str_subset(words, "x$")
```

    ## [1] "box" "sex" "six" "tax"

``` r
# are exactly 3 letters long
# str_view(words, "^...$")
str_subset(words, "^...$") %>% head(10)
```

    ##  [1] "act" "add" "age" "ago" "air" "all" "and" "any" "arm" "art"

``` r
# have seven letters or more
# str_view(words, "^.......")
str_subset(words, "^.......") %>% head(10)
```

    ##  [1] "absolute"  "account"   "achieve"   "address"   "advertise"
    ##  [6] "afternoon" "against"   "already"   "alright"   "although"

14.3.3.1 Exercises
------------------

### 1. Create regular expressions to find all words that:

-   Start with a vowel.
-   That only contain consonants. (Hint: thinking about matching “not”-vowels.)
-   End with ed, but not with eed.
-   End with ing or ise.

To find words that start with vowels, we can use the "^" anchor along with a selection of vowels using the brackets. To find words that only contain consonants, we can specify that, from start to end (using ^ and `\$`), there are no vowels (using ^ within brackets, along with the + sign, which means 1 or more of something). To find words that end in ed, but not eed, we can specify that we want words that end with ed, but have anything other than an e beforehand using \[^e\]. For words that end in ing or ise, we can use the "or" character "|" to combine two separate regexes, one that looks for `ing\$` and one that looks for `ise\$`. Below are the regexes I have described in words above.

``` r
# starts with a vowel
# str_view(words, "^[aeiou]")
str_subset(words, "^[aeiou]") %>% head(10)
```

    ##  [1] "a"        "able"     "about"    "absolute" "accept"   "account" 
    ##  [7] "achieve"  "across"   "act"      "active"

``` r
# only contain consonants
# str_view(words, "^[^aeiou]+$", match = T)
str_subset(words, "^[^aeiou]+$")
```

    ## [1] "by"  "dry" "fly" "mrs" "try" "why"

``` r
# ends with ed, but not eed
# str_view(words, "[^e]ed$", match = T)
str_subset(words, "[^e]ed$")
```

    ## [1] "bed"     "hundred" "red"

``` r
# ends with ing or ise
# str_view(words, "ing$|ise$", match = T)
str_subset(words, "ing$|ise$")
```

    ##  [1] "advertise" "bring"     "during"    "evening"   "exercise" 
    ##  [6] "king"      "meaning"   "morning"   "otherwise" "practise" 
    ## [11] "raise"     "realise"   "ring"      "rise"      "sing"     
    ## [16] "surprise"  "thing"

### 2. Empirically verify the rule “i before e except after c”.

To examine whether any words in the dataset break this rule, we can search for words with the sequence of characters "cie", which contain an i before e even after a c. This yields the words "science" an "society". This tells us that the rule is not 100% correct! Searching for "cei", which will show words that adhere to the rule, yields "receive", which tells us that the rule might be correct for certain words. Is i always before e, even after something that is not c? Searching for words that do not follow this convention (i AFTER e, after anything other than a c) using the regex "\[^c\]ei" yields the word "weigh", which again disproves the rule. Searching for words that follow the rule (ie before e, after anything other than a c) using the regex "\[^c\]ie" yields many results, which shows us that the rule applies for most words, but not all of them.

``` r
# list words that contain i before e, after a c. if this yields matches, then the rule is false!
# str_view(words, "cie", match = T)
# str_view(words, "cei", match = T)
# str_view(words, "[^c]ei", match = T)
# str_view(words, "[^c]ie", match = T)

str_subset(words, "cie")
```

    ## [1] "science" "society"

``` r
str_subset(words, "cei")
```

    ## [1] "receive"

``` r
str_subset(words, "[^c]ei")
```

    ## [1] "weigh"

``` r
str_subset(words, "[^c]ie")
```

    ##  [1] "achieve"    "believe"    "brief"      "client"     "die"       
    ##  [6] "experience" "field"      "friend"     "lie"        "piece"     
    ## [11] "quiet"      "tie"        "view"

### 3. Is “q” always followed by a “u”?

In the words dataset, we can identify words that contain q followed by any character using the regex "q.". All the results contain "q" followed by a "u". Alternatively, we can identify words that contain q followed by anything other than a "u", using "q\[^u\]". Because this does not yield any results, it does seem like "q" is followed by a "u" in this dataset.

``` r
# list words that contain q followed by any character
# str_view(words, "q.", match = T)
str_subset(words, "q.")
```

    ##  [1] "equal"    "quality"  "quarter"  "question" "quick"    "quid"    
    ##  [7] "quiet"    "quite"    "require"  "square"

``` r
# list words that contain q followed by something other than u (no matches)
# str_view(words, "q[^u]", match = T)
str_subset(words, "q[^u]")
```

    ## character(0)

### 4. Write a regular expression that matches a word if it’s probably written in British English, not American English.

Some common differences between British and American English include colour instead of color, so we could look for the sequence "lou". Other differences include "summarize" vs "summarise", so we could also include a search for the sequence "ise". Of course, this will also fetch words such as "practice" that do not have two forms.

``` r
# str_view(words, "lou|ise", match = T)
str_subset(words, "lou|ise")
```

    ## [1] "advertise" "colour"    "exercise"  "otherwise" "practise"  "raise"    
    ## [7] "realise"   "rise"      "surprise"

### 5. Create a regular expression that will match telephone numbers as commonly written in your country.

Phone numbers in the US can have the form (XXX) XXX - XXXX. Below is the regex that can be used to identify, very strictly, numbers that adhere to this exact form (including spacing and parentheses). Note the double backslash required to specify a digit. There is a more efficient way to write the regex below using brackets `{}`, but that is covered in the next section!

``` r
# str_view ( c("(123) 456 - 7890 is a fake number,","and (555) 555 - 5555 is also a fake number"), "\\(\\d\\d\\d\\)\\s\\d\\d\\d\\s-\\s\\d\\d\\d\\d")
str_extract ( c("(123) 456 - 7890 is a fake number,","and (555) 555 - 5555 is also a fake number"), "\\(\\d\\d\\d\\)\\s\\d\\d\\d\\s-\\s\\d\\d\\d\\d")
```

    ## [1] "(123) 456 - 7890" "(555) 555 - 5555"

``` r
# same regex using brackets
# str_view ( c("(123) 456 - 7890 is a fake number,","and (555) 555 - 5555 is also a fake number"), "\\(\\d{3}\\)\\s\\d{3}\\s-\\s\\d{4}")
str_extract ( c("(123) 456 - 7890 is a fake number,","and (555) 555 - 5555 is also a fake number"), "\\(\\d{3}\\)\\s\\d{3}\\s-\\s\\d{4}")
```

    ## [1] "(123) 456 - 7890" "(555) 555 - 5555"

``` r
writeLines(c("(\\d\\d\\d)\\d\\d\\d-\\d\\d\\d\\d", "\\(\\d{3}\\)\\s\\d{3}\\s-\\s\\d{4}"))
```

    ## (\d\d\d)\d\d\d-\d\d\d\d
    ## \(\d{3}\)\s\d{3}\s-\s\d{4}

14.3.4.1 Exercises
------------------

### 1. Describe the equivalents of `?, +, *` in `{m,n}` form.

? is equivalent to `{0,1}` in that it matches either 0 or 1 occurances of the preceding regex or character. + is equivalent to `{1,}` in that it matches 1 or more occurances of the character/regex. \* is equivalent to `{0,}` in that it matches from 0 to any number of occurances of the character/regex.

### 2. Describe in words what these regular expressions match: (read carefully to see if I’m using a regular expression or a string that defines a regular expression.)

-   `^.*$` This matches any series of characters of 0 to any length. The anchor ^ specifies the start of the string, the . specifies any character, and the `*` specifies that the previous regex, (.), can be of 0 to any length, and the `$` specifies the end of the string.

-   `"\\{.+\\}"` This matches a literal bracket, `{`, followed by 1 or more series of any characters, followed by a literal closing bracket, `}`. This will match strings like: `"{hello world}"`.

-   `\d{4}-\d{2}-\d{2}` This will match 4 digits, followed by a hyphen, two digits, a hyphen, and another 2 digits. For example: "5555-55-55"

-   `"\\\\{4}"` This will match a literal backslash repeated 4 times. For example: `\\\\`.

Here are the above regexes and some sample input so you can see what types of character sequences they match:

``` r
# str_view(words, "^.*$")
# str_view("hello world, {hello world} hello world", "\\{.+\\}")
# str_view("5555-55-55asdf", "\\d{4}-\\d{2}-\\d{2}")
# str_view("\\\\\\\\asdf", "\\\\{4}")

str_subset(words, "^.*$") %>% head(10)
```

    ##  [1] "a"        "able"     "about"    "absolute" "accept"   "account" 
    ##  [7] "achieve"  "across"   "act"      "active"

``` r
str_extract("hello world, {hello world} hello world", "\\{.+\\}")
```

    ## [1] "{hello world}"

``` r
str_extract("5555-55-55asdf", "\\d{4}-\\d{2}-\\d{2}")
```

    ## [1] "5555-55-55"

``` r
str_extract("\\\\\\\\asdf", "\\\\{4}")
```

    ## [1] "\\\\\\\\"

### 3. Create regular expressions to find all words that:

-   Start with three consonants.
-   Have three or more vowels in a row.
-   Have two or more vowel-consonant pairs in a row.

``` r
# three consonants
# str_view("street", '[^aeiou]{3}')
str_extract("street", '[^aeiou]{3}')
```

    ## [1] "str"

``` r
#three or more vowels in a row:
# str_view("streeet", '[aeiou]{3,}')
str_extract("streeet", '[aeiou]{3,}')
```

    ## [1] "eee"

``` r
# two or more vowel-consonant pairs in a row:
# str_view("streettttt", '([aeiou][^aeiou]|[^aeiou][aeiou]){2,}')
str_extract("streettttt", '([aeiou][^aeiou]|[^aeiou][aeiou]){2,}')
```

    ## [1] "reet"

### 4. Solve the beginner regexp crosswords at <https://regexcrossword.com/challenges/beginner>.

Here are the solutions with some explanations to how I arrived at the answers:

Beginner puzzle 1, Beatles: row 1: HE|LL|O+, row2: \[PLEASE\]+, column 1: \[^SPEAK\]+, column 2: EP|IP|EF. Solution: row1: HE, row2: LP.

First examine row 1 vs column 2- column 2 can either start with the letter E or I, but the only option in row 1 that will fit with this requirement is "HE". For row 2, the only letter that can be in the bottom left is L, since P, E, A, and S are not allowed by column 1. The bottom right can only contain letters from PLEASE, which means that the option EF for column 2 is not allowed. This leaves EP as the only possibility for column 2. So row 2 must be "LP".

Beginner puzzle 2, Naughty: row 1: `.*M?O.*`, row2: (AN|FE|BE), column 1: `(A|B|C)\1`, column 2: (AB|OE|SK). Solution: row1: BO, row2: BE.

Row 1 specifies that there can be 0-1 of any character, followed by 0-1 of M, followed by O, followed by 0-1 of any character. The only stringent requirement is that there exist an O in row 1. Looking at column 1, this does not allow the letter O, so O must be in column 2 of row 1. The only option that allows this is the "OE" option in column 2. The two options for row 2 that end with letter E are "FE" or "BE". However, column 1 dictates that the first letter of row2 must be either A, B, or C. The only option that fits this is "BB" for column 2.

Beginner puzzle 3, Ghost: row 1: `(.)+\1`, row2: \[^ABRC\]+, column 1: \[COBRA\]+, column 2: (AB|O|OR)+. Solution: row1: OO, row2: OO.

Row 1 specifies that 1 or more of any character should be repeated. Given the 2x2 box, this means a pair of letters. Column 1 gives COBRA as choices, but row2 specifies that ABRC cannot be present. This leaves us with O. Also, for column 2, the only option that is available that would not violate row2's rules is O+.

Beginner puzzle 4, Symbolism: row 1: `[*]+`, row2: /+, column 1: .?.+, column 2: .+. Solution: row1: `**`, row2:`//`.

Row1 wants 1 or more `*`, whereas row 2 wants one or more /. Column 1 states that any character can occur 0-1 times followed by another character at least 1 or more times. Column 2 states that any character must appear 1 or more times.

Beginner puzzle 5, Airstrip One: row 1: 18|19|20, row2: `[6789]\d`, column 1: `\d[2480]`, column 2: 56|94|73. Solution: row1: 19, row2: 84.

The only choice for row1 that would satisfy the requirements for column2 is 19 (row1 ends with 9 and col 2 begins with 9). This means that column2 downwards is 94. The only choice for the bottom left that satisfies row2, and column 1 is 8. This leaves us with 19 in row1 and 84 in row2 as the answer.

14.3.5.1 Exercises
------------------

### 1. Describe, in words, what these expressions will match:

-   `(.)\1\1`

This will match any character repeated 3 times. For example, "ooo" in the string "woohooo!"

``` r
# str_view("woohooo!", "(.)\\1\\1")
str_extract("woohooo!", "(.)\\1\\1")
```

    ## [1] "ooo"

-   `"(.)(.)\\2\\1"`

This will match a pair of characters followed by the reverse of the pair. For example, "elle" in "belle".

``` r
# str_view("belle", "(.)(.)\\2\\1")
str_extract("belle", "(.)(.)\\2\\1")
```

    ## [1] "elle"

-   `(..)\1`

This will match any two characters repeated twice. For example, "caca" in "cacao beans".

``` r
# str_view("cacao beans", "(..)\\1")
str_extract("cacao beans", "(..)\\1")
```

    ## [1] "caca"

-   `"(.).\\1.\\1"`

This matches any character repeated every other character, such as "lulul" in "lululemon".

``` r
# str_view("lululemon", "(.).\\1.\\1")
str_extract("lululemon", "(.).\\1.\\1")
```

    ## [1] "lulul"

-   `"(.)(.)(.).*\\3\\2\\1"`

This matches 3 characters followed by any number of characters followed by the first 3 characters in reverse, such as "but the tub" in "but the tub is full".

``` r
# str_view("but the tub is full", "(.)(.)(.).*\\3\\2\\1")
str_extract("but the tub is full", "(.)(.)(.).*\\3\\2\\1")
```

    ## [1] "but the tub"

### 2. Construct regular expressions to match words that:

-   Start and end with the same character.

`"^(.).*\\1$"` performs this function. The ^ anchor specifies that any character (.) must also be present at the end of the string using the `\$` anchor after the backreference. Below, it will match "regular" but not "expression".

``` r
# str_view(c("regular","expressions", "a", "aa"), "^(.).*\\1$")
str_subset(c("regular","expressions", "a", "aa"), "^(.).*\\1$")
```

    ## [1] "regular" "aa"

-   Contain a repeated pair of letters (e.g. “church” contains “ch” repeated twice.)

`"(..).*\\1"` will work by specifying that any two characters (..) can be separated by any amount of characters `.*` followed by the same two characters using a backreference.

``` r
# str_view(c("church", "no repeats", "papaya"), "(..).*\\1")
str_subset(c("church", "no repeats", "papaya"), "(..).*\\1")
```

    ## [1] "church" "papaya"

-   Contain one letter repeated in at least three places (e.g. “eleven” contains three “e”s.)

`"(.).*\\1.*\\1"` would work for single word strings. If there is a sentence with multiple words, this regex would match the first occurance of 3 characters, including whitespace, even if they were in different words. If we don't want this to happen, we would use a "anything except whitespace" instead of `.*`. This would be a regexp for that purpose: `"([^\\s])[^\\s]*\\1[^\\s]*\\1"`. We could also use the boundary regexp `\\b`.

``` r
# this only works for individual words
# str_view(c("eleven", "apply", "papaya", "bananas", "will this match spaces?","letters next"), "(.).*\\1.*\\1")
str_subset(c("eleven", "apply", "papaya", "bananas", "will this match spaces?","letters next"), "(.).*\\1.*\\1")
```

    ## [1] "eleven"                  "papaya"                 
    ## [3] "bananas"                 "will this match spaces?"
    ## [5] "letters next"

``` r
# this will exclude whitespace and only match if a word within a sentence has a letter repeated 3 times.
# str_view(c("eleven", "apply", "papaya", "bananas", "will this match spaces?", "letters next"), "([^\\s])[^\\s]*\\1[^\\s]*\\1")
str_subset(c("eleven", "apply", "papaya", "bananas", "will this match spaces?", "letters next"), "([^\\s])[^\\s]*\\1[^\\s]*\\1")
```

    ## [1] "eleven"  "papaya"  "bananas"

14.4.2 Exercises
----------------

### 1. For each of the following challenges, try solving it by using both a single regular expression, and a combination of multiple str\_detect() calls.

-   Find all words that start or end with x.

``` r
# single expression
str_subset(words, "^x|x$")
```

    ## [1] "box" "sex" "six" "tax"

``` r
# multiple str_detect() calls
c(words[str_detect(words, "^x")], words[str_detect(words, "x$")])
```

    ## [1] "box" "sex" "six" "tax"

-   Find all words that start with a vowel and end with a consonant.

``` r
# single expression
str_subset(words, "^[aeiou].*[^aeiou]$")
```

    ##   [1] "about"       "accept"      "account"     "across"      "act"        
    ##   [6] "actual"      "add"         "address"     "admit"       "affect"     
    ##  [11] "afford"      "after"       "afternoon"   "again"       "against"    
    ##  [16] "agent"       "air"         "all"         "allow"       "almost"     
    ##  [21] "along"       "already"     "alright"     "although"    "always"     
    ##  [26] "amount"      "and"         "another"     "answer"      "any"        
    ##  [31] "apart"       "apparent"    "appear"      "apply"       "appoint"    
    ##  [36] "approach"    "arm"         "around"      "art"         "as"         
    ##  [41] "ask"         "at"          "attend"      "authority"   "away"       
    ##  [46] "awful"       "each"        "early"       "east"        "easy"       
    ##  [51] "eat"         "economy"     "effect"      "egg"         "eight"      
    ##  [56] "either"      "elect"       "electric"    "eleven"      "employ"     
    ##  [61] "end"         "english"     "enjoy"       "enough"      "enter"      
    ##  [66] "environment" "equal"       "especial"    "even"        "evening"    
    ##  [71] "ever"        "every"       "exact"       "except"      "exist"      
    ##  [76] "expect"      "explain"     "express"     "identify"    "if"         
    ##  [81] "important"   "in"          "indeed"      "individual"  "industry"   
    ##  [86] "inform"      "instead"     "interest"    "invest"      "it"         
    ##  [91] "item"        "obvious"     "occasion"    "odd"         "of"         
    ##  [96] "off"         "offer"       "often"       "okay"        "old"        
    ## [101] "on"          "only"        "open"        "opportunity" "or"         
    ## [106] "order"       "original"    "other"       "ought"       "out"        
    ## [111] "over"        "own"         "under"       "understand"  "union"      
    ## [116] "unit"        "university"  "unless"      "until"       "up"         
    ## [121] "upon"        "usual"

``` r
# multiple str_detect() calls
words [ str_detect(words, "^[aeiou]") & str_detect(words, "[^aeiou]$")]
```

    ##   [1] "about"       "accept"      "account"     "across"      "act"        
    ##   [6] "actual"      "add"         "address"     "admit"       "affect"     
    ##  [11] "afford"      "after"       "afternoon"   "again"       "against"    
    ##  [16] "agent"       "air"         "all"         "allow"       "almost"     
    ##  [21] "along"       "already"     "alright"     "although"    "always"     
    ##  [26] "amount"      "and"         "another"     "answer"      "any"        
    ##  [31] "apart"       "apparent"    "appear"      "apply"       "appoint"    
    ##  [36] "approach"    "arm"         "around"      "art"         "as"         
    ##  [41] "ask"         "at"          "attend"      "authority"   "away"       
    ##  [46] "awful"       "each"        "early"       "east"        "easy"       
    ##  [51] "eat"         "economy"     "effect"      "egg"         "eight"      
    ##  [56] "either"      "elect"       "electric"    "eleven"      "employ"     
    ##  [61] "end"         "english"     "enjoy"       "enough"      "enter"      
    ##  [66] "environment" "equal"       "especial"    "even"        "evening"    
    ##  [71] "ever"        "every"       "exact"       "except"      "exist"      
    ##  [76] "expect"      "explain"     "express"     "identify"    "if"         
    ##  [81] "important"   "in"          "indeed"      "individual"  "industry"   
    ##  [86] "inform"      "instead"     "interest"    "invest"      "it"         
    ##  [91] "item"        "obvious"     "occasion"    "odd"         "of"         
    ##  [96] "off"         "offer"       "often"       "okay"        "old"        
    ## [101] "on"          "only"        "open"        "opportunity" "or"         
    ## [106] "order"       "original"    "other"       "ought"       "out"        
    ## [111] "over"        "own"         "under"       "understand"  "union"      
    ## [116] "unit"        "university"  "unless"      "until"       "up"         
    ## [121] "upon"        "usual"

-   Are there any words that contain at least one of each different vowel?

To determine whether there are any words that contain at least one of each vowel, we can look for words that have just one of each vowel to make things simpler (any words with more than one of any vowell will still be matched). This is much easier done using multiple str\_detect() calls, beacuse we do not care where the vowels show up within the word. "a" can be before "i", or after "i", or between "o" and "u", which makes things complicated if we want to use a single regular expression. We would have to type out all the permutations and chain them using `.*` and OR (|). This would be a huge expression! Below is the simpler way of chaining multiple str\_detect() calls. No words have at least one of each vowell, but some have a,e i, and o (no u).

``` r
# multiple str_detect() calls
words[str_detect(words, "a") & str_detect(words, "e") &  str_detect(words, "i") & str_detect(words, "o") & str_detect (words, "u")]
```

    ## character(0)

``` r
# words with a, e, i, and o (no u)
words[str_detect(words, "a") & str_detect(words, "e") &  str_detect(words, "i") & str_detect(words, "o") ]
```

    ## [1] "appropriate" "associate"   "organize"    "relation"

### 2. What word has the highest number of vowels? What word has the highest proportion of vowels? (Hint: what is the denominator?)

To figure this out, we need a way to count the number of vowels in each word in `words`, which we can do with str\_count() and the regexp `[aeiou]`. This will let us know the number of vowels in each word, but we also want to keep the information about what the original word was. This means we should perform str\_count() on a data frame that contains the original words as well as their index. The book provides this as a tibble. We can perform a mutate() using str\_count() and also get the length of each word using str\_length(), then calculate the proportion by dividing the two metrics. Then we can use arrange() to find the words with the highest vowel count and vowel proportion. The highest vowel count is 5, which includes appropriate, associate, available, colleague, encourage, experience, individual, and television. The highest vowel proportion is the word "a", which has a proportion of 1 (it itself is a vowel). Non-1 letter words with the highest vowel proportion include area and idea, at 0.75.

``` r
# create indexed words tibble
df <- tibble(
  word = words, 
  i = seq_along(word)
)
# calculate number of vowels and proportion of vowels by word
vowels <- df %>%
  mutate ( num_vowels = str_count(words, "[aeiou]"),
           length_word = str_length(words),
           proportion_vowel = num_vowels/length_word)
# sort by words with highest number or proportion of vowels
vowels %>%
  arrange (desc(num_vowels))
```

    ## # A tibble: 980 x 5
    ##    word            i num_vowels length_word proportion_vowel
    ##    <chr>       <int>      <int>       <int>            <dbl>
    ##  1 appropriate    48          5          11            0.455
    ##  2 associate      57          5           9            0.556
    ##  3 available      62          5           9            0.556
    ##  4 colleague     166          5           9            0.556
    ##  5 encourage     268          5           9            0.556
    ##  6 experience    292          5          10            0.5  
    ##  7 individual    423          5          10            0.5  
    ##  8 television    846          5          10            0.5  
    ##  9 absolute        4          4           8            0.5  
    ## 10 achieve         7          4           7            0.571
    ## # ... with 970 more rows

``` r
vowels %>%
  arrange (desc(proportion_vowel))
```

    ## # A tibble: 980 x 5
    ##    word       i num_vowels length_word proportion_vowel
    ##    <chr>  <int>      <int>       <int>            <dbl>
    ##  1 a          1          1           1            1    
    ##  2 area      49          3           4            0.75 
    ##  3 idea     412          3           4            0.75 
    ##  4 age       22          2           3            0.667
    ##  5 ago       24          2           3            0.667
    ##  6 air       26          2           3            0.667
    ##  7 die      228          2           3            0.667
    ##  8 due      250          2           3            0.667
    ##  9 eat      256          2           3            0.667
    ## 10 europe   278          4           6            0.667
    ## # ... with 970 more rows

14.4.3.1 Exercises
------------------

### 1. In the previous example, you might have noticed that the regular expression matched “flickered”, which is not a colour. Modify the regex to fix the problem.

We can modify the regex to detect only legitimate colors by adding a whitespace character on both sides of the name of each color. This will match sentences where a colour is only one word. However, dual-colors like "orangered" or "orange-red" will not be matched unless explicitly stated in the colours vector. To do this, we could change the whitespace to a boundary regex `"\\b"`.

``` r
colours <- c("red", "orange", "yellow", "green", "blue", "purple")
colours_fixed <- str_c("\\s", colours, "\\s")
colours_fixed
```

    ## [1] "\\sred\\s"    "\\sorange\\s" "\\syellow\\s" "\\sgreen\\s" 
    ## [5] "\\sblue\\s"   "\\spurple\\s"

``` r
colour_match <- str_c(colours_fixed, collapse = "|")
has_colour <- str_subset(sentences, colour_match)
has_colour
```

    ##  [1] "Glue the sheet to the dark blue background."     
    ##  [2] "Two blue fish swam in the tank."                 
    ##  [3] "A wisp of cloud hung in the blue air."           
    ##  [4] "Leaves turn brown and yellow in the fall."       
    ##  [5] "The spot on the blotter was made by green ink."  
    ##  [6] "The sofa cushion is red and of light weight."    
    ##  [7] "A blue crane is a tall wading bird."             
    ##  [8] "It is hard to erase blue or red ink."            
    ##  [9] "The lamp shone with a steady green flame."       
    ## [10] "The box is held by a bright red snapper."        
    ## [11] "The houses are built of red clay bricks."        
    ## [12] "The red tape bound the smuggled food."           
    ## [13] "The plant grew large and green in the window."   
    ## [14] "The purple tie was ten years old."               
    ## [15] "Bathe and relax in the cool green grass."        
    ## [16] "The lake sparkled in the red hot sun."           
    ## [17] "A man in a blue sweater sat at the desk."        
    ## [18] "The small red neon lamp went out."               
    ## [19] "Wake and rise, and step into the green outdoors."
    ## [20] "The green light in the brown box flickered."     
    ## [21] "Tear a thin sheet from the yellow pad."          
    ## [22] "The sky in the west is tinged with orange red."  
    ## [23] "The red paper brightened the dim stage."         
    ## [24] "The big red apple fell to the ground."

``` r
more <- sentences[str_count(sentences, colour_match) > 1]
# str_view_all(more, colour_match)
str_extract_all (more, colour_match)
```

    ## [[1]]
    ## [1] " blue " " red "

### 2. From the Harvard sentences data, extract:

-   The first word from each sentence.

To do this, use the ^ anchor and look for all characters that lie before the first whitespace.

``` r
first_words <- str_extract(sentences, "^.+?\\s")
head(first_words,20)
```

    ##  [1] "The "   "Glue "  "It's "  "These " "Rice "  "The "   "The "  
    ##  [8] "The "   "Four "  "Large " "The "   "A "     "The "   "Kick " 
    ## [15] "Help "  "A "     "Smoky " "The "   "The "   "The "

-   All words ending in ing.

To do this, first find the subset of sentences that have "ing" before a boundary character. Then, within these sentences, extract the word containing "ing" before the boundary character.

``` r
with_ing <- str_subset(sentences, "ing\\b")
head(with_ing)
```

    ## [1] "The source of the huge river is the clear spring."
    ## [2] "A pot of tea helps to pass the evening."          
    ## [3] "It snowed, rained, and hailed the same morning."  
    ## [4] "Take the winding path to reach the lake."         
    ## [5] "What joy there is in living."                     
    ## [6] "A king ruled the state in the early days."

``` r
words_ing <- str_extract(with_ing, "\\b\\w+ing\\b")
words_ing
```

    ##  [1] "spring"    "evening"   "morning"   "winding"   "living"   
    ##  [6] "king"      "Adding"    "making"    "raging"    "playing"  
    ## [11] "sleeping"  "ring"      "glaring"   "sinking"   "dying"    
    ## [16] "Bring"     "lodging"   "filing"    "making"    "morning"  
    ## [21] "wearing"   "Bring"     "wading"    "swing"     "nothing"  
    ## [26] "ring"      "morning"   "sing"      "sleeping"  "painting" 
    ## [31] "king"      "walking"   "bring"     "bring"     "shipping" 
    ## [36] "spring"    "ring"      "winding"   "puzzling"  "spring"   
    ## [41] "landing"   "thing"     "waiting"   "whistling" "nothing"  
    ## [46] "timing"    "thing"     "spring"    "changing"  "drenching"
    ## [51] "moving"    "working"   "ring"

-   All plurals.

Below is a way to find all words that end in "s", which should capture plurals that end in "s", as well as other non-plural words that also end in "s", such as "press". To decipher which of these are plurals of singular words, we could subtract the "s" from the words and see if the resulting word matches a dictionary of specified singular words, which we don't have here. To decipher which words are plurals using only a regex doesn't seem straightforward and would require something very convoluted.

``` r
with_plural <- str_subset(sentences, "\\b\\w+s\\s")
head(with_plural)
```

    ## [1] "These days a chicken leg is a rare dish."   
    ## [2] "Rice is often served in round bowls."       
    ## [3] "The juice of lemons makes fine punch."      
    ## [4] "The box was thrown beside the parked truck."
    ## [5] "The hogs were fed chopped corn and garbage."
    ## [6] "Four hours of steady work faced us."

``` r
words_plural <- str_extract(with_plural, "\\b\\w+s\\s")
head(words_plural)
```

    ## [1] "days "   "is "     "lemons " "was "    "hogs "   "hours "

14.4.4.1 Exercises
------------------

### 1. Find all words that come after a “number” like “one”, “two”, “three” etc. Pull out both the number and the word.

I modeled the answers after the example provided in the book, and modified the code to work with the new regex. The regex I made has numbers one - ten, then followed by a suffix "teen" for cases such as "fourteen" or "ty" for cases such as "sixty". There are some other cases that I added in that do not follow this convention such as "twenty" or "thirteen". This should be followed by a whitespace then 1 or more non-whitespace character. We can then extract the results using a combination of str\_subset() and str\_extract(). Str\_match() or tidyr::extract() can pull out both the number and the words that are encased in the parentheses.

``` r
# regex to detect numbers with words after them
afternumber <- "\\b((one|two|three|four|five|six|seven|eight|nine|ten|eleven|twelve|thirteen|fifteen|eighteen|twenty|thirty|fifty|eighty)(ty|teen)?) ([^ ]+)"

# find all sentences that have a match with the regex
has_number <- sentences %>%
  str_subset(afternumber) %>%
  head(10)

# display what the match for each sentence was
has_number %>% 
  str_extract(afternumber)
```

    ##  [1] "fifty bonds."   "seven books"    "two met"        "sixteen weeks."
    ##  [5] "two factors"    "three lists"    "thirty times."  "seven is"      
    ##  [9] "two when"       "ten inches."

``` r
# display the separate parts of each match.
has_number %>% 
  str_match(afternumber)
```

    ##       [,1]             [,2]      [,3]     [,4]   [,5]     
    ##  [1,] "fifty bonds."   "fifty"   "fifty"  NA     "bonds." 
    ##  [2,] "seven books"    "seven"   "seven"  NA     "books"  
    ##  [3,] "two met"        "two"     "two"    NA     "met"    
    ##  [4,] "sixteen weeks." "sixteen" "six"    "teen" "weeks." 
    ##  [5,] "two factors"    "two"     "two"    NA     "factors"
    ##  [6,] "three lists"    "three"   "three"  NA     "lists"  
    ##  [7,] "thirty times."  "thirty"  "thirty" NA     "times." 
    ##  [8,] "seven is"       "seven"   "seven"  NA     "is"     
    ##  [9,] "two when"       "two"     "two"    NA     "when"   
    ## [10,] "ten inches."    "ten"     "ten"    NA     "inches."

``` r
# use tidyr::extract() to extract matches for each part of the regex and display output in a neat table
tibble(sentence = sentences) %>% 
  tidyr::extract(
    sentence, c("complete_number", "number","teen_or_ty", "word"), afternumber, 
    remove = FALSE
  ) %>%
  select(sentence, complete_number, word) %>%
  na.omit()
```

    ## # A tibble: 26 x 3
    ##    sentence                                    complete_number word   
    ##    <chr>                                       <chr>           <chr>  
    ##  1 The girl at the booth sold fifty bonds.     fifty           bonds. 
    ##  2 The rope will bind the seven books at once. seven           books  
    ##  3 The two met while playing on the sand.      two             met    
    ##  4 The lease ran out in sixteen weeks.         sixteen         weeks. 
    ##  5 There are more than two factors here.       two             factors
    ##  6 Type out three lists of orders.             three           lists  
    ##  7 He said the same phrase thirty times.       thirty          times. 
    ##  8 Two plus seven is less than ten.            seven           is     
    ##  9 Drop the two when you add the figures.      two             when   
    ## 10 There the flood mark is ten inches.         ten             inches.
    ## # ... with 16 more rows

### 2. Find all contractions. Separate out the pieces before and after the apostrophe.

To find contractions, we can search for a sequence of letters using `\w`, followed by a contraction with either "' or -", and then another sequence of words after that, followed by a boundary. Using this regex we can then use str\_subset(), str\_extract(), str\_match(), and tidyr::extract() to obtain the pieces before and after the apostrophe. If we don't care about hyphens, we can remove it from the regex.

``` r
contractions <- "\\b(\\w+)('|-)(\\w+)\\b"
has_contraction <- sentences %>%
  str_subset(contractions) %>%
  head(10)
has_contraction %>% 
  str_extract(contractions)
```

    ##  [1] "It's"         "man's"        "don't"        "store's"     
    ##  [5] "apple-shaped" "hot-cross"    "workmen's"    "Let's"       
    ##  [9] "sun's"        "child's"

``` r
has_contraction %>% 
  str_match(contractions)
```

    ##       [,1]           [,2]      [,3] [,4]    
    ##  [1,] "It's"         "It"      "'"  "s"     
    ##  [2,] "man's"        "man"     "'"  "s"     
    ##  [3,] "don't"        "don"     "'"  "t"     
    ##  [4,] "store's"      "store"   "'"  "s"     
    ##  [5,] "apple-shaped" "apple"   "-"  "shaped"
    ##  [6,] "hot-cross"    "hot"     "-"  "cross" 
    ##  [7,] "workmen's"    "workmen" "'"  "s"     
    ##  [8,] "Let's"        "Let"     "'"  "s"     
    ##  [9,] "sun's"        "sun"     "'"  "s"     
    ## [10,] "child's"      "child"   "'"  "s"

``` r
tibble(sentence = sentences) %>% 
  tidyr::extract(
    sentence, c("before_contraction", "contraction","after_contraction"), contractions, 
    remove = FALSE
  ) %>%
  mutate (
    complete = str_extract (sentence, contractions)
  )%>%
  na.omit()
```

    ## # A tibble: 17 x 5
    ##    sentence        before_contracti… contraction after_contracti… complete
    ##    <chr>           <chr>             <chr>       <chr>            <chr>   
    ##  1 It's easy to t… It                '           s                It's    
    ##  2 The soft cushi… man               '           s                man's   
    ##  3 Open the crate… don               '           t                don't   
    ##  4 Add the store'… store             '           s                store's 
    ##  5 The fruit of a… apple             -           shaped           apple-s…
    ##  6 A zestful food… hot               -           cross            hot-cro…
    ##  7 The beam dropp… workmen           '           s                workmen…
    ##  8 Let's all join… Let               '           s                Let's   
    ##  9 The copper bow… sun               '           s                sun's   
    ## 10 A child's wit … child             '           s                child's 
    ## 11 A ripe plum is… king              '           s                king's  
    ## 12 It's a dense c… It                '           s                It's    
    ## 13 We don't get m… don               '           t                don't   
    ## 14 Ripe pears are… queen             '           s                queen's 
    ## 15 We don't like … don               '           t                don't   
    ## 16 Dig deep in th… pirate            '           s                pirate's
    ## 17 She saw a cat … neighbor          '           s                neighbo…

14.4.5.1 Exercises
------------------

### 1. Replace all forward slashes in a string with backslashes.

``` r
sample <- "this/has/too/many/slashes/!"
sample
```

    ## [1] "this/has/too/many/slashes/!"

``` r
str_replace_all (sample, "/", "\\\\")
```

    ## [1] "this\\has\\too\\many\\slashes\\!"

### 2. Implement a simple version of str\_to\_lower() using replace\_all().

It looks like stringr doesn't have a "to lowercase" regex such as `\\L` in perl, which can convert a backreference to lower case (`\\U` will convert it to uppercase). This makes us write bulky and inefficient code, having to specify "A = a", "B = b", etc. Instead of using str\_replace\_all(), we can use the baseR gsub(), and group all caps (\[A-Z\]) in parentheses, and then use `"\\L\\1"` to turn every instance of a capital to lowercase.

``` r
sample <- "The CaPS iS gOiNg CrAZy!"

# gsub does this more efficiently!
gsub ("([A-Z])", "\\L\\1", perl=TRUE, sample)
```

    ## [1] "the caps is going crazy!"

``` r
# this doesn't work in stringr!!
str_replace_all ( sample, "([A-Z])", "\\L\\1")
```

    ## [1] "LThe LCaLPLS iLS gLOiLNg LCrLALZy!"

``` r
# if we must use stringr, we have to painfully write out all the replacements...
str_replace_all (sample, c("A" ="a", "C" = "c", "T" = "t", "P" = "p", "S" = "s", "O" = "o", "N" = "n", "Z" = "z"))
```

    ## [1] "the caps is going crazy!"

### 3. Switch the first and last letters in words. Which of those strings are still words?

To swich the first and last letters, we can use paretheses along with anchor ^ and `$`, and then swap the backreferences. Then, we can use the function intersect() to determine which of these swapped words are still present in the original words dataset.

``` r
# swap the first and last letters in the words dataset
swapped <- str_replace (words, "^(.)(.*)(.)$", "\\3\\2\\1")
head(swapped, 10)
```

    ##  [1] "a"        "ebla"     "tboua"    "ebsoluta" "tccepa"   "tccouna" 
    ##  [7] "echieva"  "scrosa"   "tca"      "ectiva"

``` r
# find out which words in the swapped list still match words in the original words dataset
intersect(words, swapped)
```

    ##  [1] "a"          "america"    "area"       "dad"        "dead"      
    ##  [6] "deal"       "dear"       "depend"     "dog"        "educate"   
    ## [11] "else"       "encourage"  "engine"     "europe"     "evidence"  
    ## [16] "example"    "excuse"     "exercise"   "expense"    "experience"
    ## [21] "eye"        "god"        "health"     "high"       "knock"     
    ## [26] "lead"       "level"      "local"      "nation"     "no"        
    ## [31] "non"        "on"         "rather"     "read"       "refer"     
    ## [36] "remember"   "serious"    "stairs"     "test"       "tonight"   
    ## [41] "transport"  "treat"      "trust"      "window"     "yesterday"

14.4.6.1 Exercises
------------------

### 1. Split up a string like "apples, pears, and bananas" into individual components.

``` r
sample <- "apples, pears, and bananas"
# use ", " to split the string
str_split(sample, " ")[[1]]
```

    ## [1] "apples," "pears,"  "and"     "bananas"

``` r
# use boundary() to split the string
str_split(sample, boundary("word"))[[1]]
```

    ## [1] "apples"  "pears"   "and"     "bananas"

### 2. Why is it better to split up by boundary("word") than " "?

It is better to split up by boundary("word") than by " " because splitting by " " will cause some words to include puncutation marks, such as commmas or periods. boundary("word") takes these into consideration when splitting, and will not include them in the output. See the comparison provided in my answer to the previous exercise, which uses both ways to split the string.

### 3. What does splitting with an empty string ("") do? Experiment, and then read the documentation.

Splitting with an empty string ("") will split the string into each individual character. The empty string can be thought of as the "space" between each individual character in the string. The documentation states that "an empty pattern, "", is equivalent to boundary("character")". Below I compare the two methods, which indeed provide the same output.

``` r
sample <- "apples, pears, and bananas"
# use ", " to split the string
str_split(sample, "")[[1]]
```

    ##  [1] "a" "p" "p" "l" "e" "s" "," " " "p" "e" "a" "r" "s" "," " " "a" "n"
    ## [18] "d" " " "b" "a" "n" "a" "n" "a" "s"

``` r
str_split(sample, boundary("character"))[[1]]
```

    ##  [1] "a" "p" "p" "l" "e" "s" "," " " "p" "e" "a" "r" "s" "," " " "a" "n"
    ## [18] "d" " " "b" "a" "n" "a" "n" "a" "s"

14.5.1 Exercises
----------------

### 1. How would you find all strings containing  with regex() vs. with fixed()?

When using regex(), we must use four backslashes for the regex to detect literal backslashes in a string. When using fixed(), we can get away with only using two backslashes to detect literal backslashes in a string. Below is an example of using both regex() and fixed() in str\_subset.

``` r
sample <- c("this has a \\ backslash", "this doesn't have one", "this has a \\ backslash" )
sample
```

    ## [1] "this has a \\ backslash" "this doesn't have one"  
    ## [3] "this has a \\ backslash"

``` r
str_subset(sample, regex("\\\\"))
```

    ## [1] "this has a \\ backslash" "this has a \\ backslash"

``` r
str_subset(sample, fixed("\\"))
```

    ## [1] "this has a \\ backslash" "this has a \\ backslash"

### 2. What are the five most common words in sentences?

The top five most common words are "the"/"The", "of", "a", "to", and "and". To answer this question, we first use str\_extract\_all() with boundary("word"), to get all the words in a character matrix. Then, we convert the matrix into a vector, then in to a tibble so we can perform dplyr commands. Then, we can use group\_by() to group by individual words, then count() to determine how many times each word was used, then arrange() to find the top words.

``` r
# extract all the words in sentences
word_matrix <- str_extract_all(sentences, boundary("word"), simplify = T)
word_vector <- as.vector(word_matrix)
head(word_vector)
```

    ## [1] "The"   "Glue"  "It's"  "These" "Rice"  "The"

``` r
# use group_by, count(), and arrange() to find the top 5 words
tibble(word_vector) %>%
  group_by(word_vector) %>%
  count() %>%
  arrange(desc(n))
```

    ## # A tibble: 2,062 x 2
    ## # Groups:   word_vector [2,062]
    ##    word_vector     n
    ##    <chr>       <int>
    ##  1 ""           2892
    ##  2 the           489
    ##  3 The           262
    ##  4 of            132
    ##  5 a             130
    ##  6 to            119
    ##  7 and           118
    ##  8 in             85
    ##  9 is             81
    ## 10 A              72
    ## # ... with 2,052 more rows

14.7.1 Exercises
----------------

### 1. Find the stringi functions that:

-   Count the number of words.
-   Find duplicated strings.
-   Generate random text.

To find these functions, first load the stringi package and type "stri\_". If using rstudio, you can then see all the various functions that show up and read a short description if you hover over their names. Below are the functions that perform the actions specified.

``` r
library(stringi)
# count number of words
stri_count_words(sentences) %>% head(10)
```

    ##  [1] 8 8 9 9 7 7 8 8 7 8

``` r
# find duplicated strings
sample <- c("hello", "hello", "world", "this", "hello")
stri_duplicated(sample)
```

    ## [1] FALSE  TRUE FALSE FALSE  TRUE

``` r
# generate random text
stri_rand_strings(5, 10)
```

    ## [1] "8XqqQD8Dqt" "QtgmOxhzLF" "aVuxVhWAwh" "JYo9wm9OXq" "imiHZ2evmi"

### 2. How do you control the language that stri\_sort() uses for sorting?

To determine the language that stri\_sort() uses for sorting, you must specify the locale argument. For stri\_sort(), you input a character vector, and then specify the decreasing argument to get a sorted version of the character vector. Decreasing = T will sort the vector such that the strings that start with a letter closer to the end of the alphabet are ranked earlier.

``` r
# sort the sentences vector, locale = en_US
stri_sort(sentences, decreasing = TRUE, locale = "en_US") %>% head(10)
```

    ##  [1] "You cannot brew tea in a cold pot."          
    ##  [2] "Yell and clap as the curtain slides back."   
    ##  [3] "Xew pants lack cuffs and pockets."           
    ##  [4] "Write fast, if you want to finish early."    
    ##  [5] "Write at once or you may forget it."         
    ##  [6] "Write a fond note to the friend you cherish."
    ##  [7] "Wood is best for making toys and blocks."    
    ##  [8] "Women form less than half of the group."     
    ##  [9] "Wipe the grease off his dirty face."         
    ## [10] "Will you please answer that phone."

Chapter 15: Factors
===================

15.3.1 Exercise
---------------

### 1. Explore the distribution of rincome (reported income). What makes the default bar chart hard to understand? How could you improve the plot?

### 2. What is the most common relig in this survey? What’s the most common partyid?

### 3. Which relig does denom (denomination) apply to? How can you find out with a table? How can you find out with a visualisation?

15.4.1 Exercises
----------------

### 1. There are some suspiciously high numbers in tvhours. Is the mean a good summary?

### 2. For each factor in gss\_cat identify whether the order of the levels is arbitrary or principled.

### 3. Why did moving “Not applicable” to the front of the levels move it to the bottom of the plot?

15.5.1 Exercises
----------------

### 1. How have the proportions of people identifying as Democrat, Republican, and Independent changed over time?

### 2. How could you collapse rincome into a small set of categories?

Chapter 16: Dates and times
===========================

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

16.2.4 Exercises
----------------

### 1. What happens if you parse a string that contains invalid dates?

``` r
ymd(c("2010-10-10", "bananas"))
```

    ## Warning: 1 failed to parse.

    ## [1] "2010-10-10" NA

### 2. What does the tzone argument to today() do? Why is it important?

### 3. Use the appropriate lubridate function to parse each of the following dates:

``` r
d1 <- "January 1, 2010"
d2 <- "2015-Mar-07"
d3 <- "06-Jun-2017"
d4 <- c("August 19 (2015)", "July 1 (2015)")
d5 <- "12/30/14" # Dec 30, 2014
```

16.3.4 Exercises
----------------

### 1. How does the distribution of flight times within a day change over the course of the year?

### 2. Compare dep\_time, sched\_dep\_time and dep\_delay. Are they consistent? Explain your findings.

### 3. Compare air\_time with the duration between the departure and arrival. Explain your findings. (Hint: consider the location of the airport.)

### 4. How does the average delay time change over the course of a day? Should you use dep\_time or sched\_dep\_time? Why?

### 5. On what day of the week should you leave if you want to minimise the chance of a delay?

### 6. What makes the distribution of diamonds*c**a**r**a**t**a**n**d**f**l**i**g**h**t**s*sched\_dep\_time similar?

### 7. Confirm my hypothesis that the early departures of flights in minutes 20-30 and 50-60 are caused by scheduled flights that leave early. Hint: create a binary variable that tells you whether or not a flight was delayed.

16.4.5 Exercises
----------------

### 1. Why is there months() but no dmonths()?

### 2. Explain days(overnight \* 1) to someone who has just started learning R. How does it work?

### 3. Create a vector of dates giving the first day of every month in 2015. Create a vector of dates giving the first day of every month in the current year.

### 4. Write a function that given your birthday (as a date), returns how old you are in years.

### 5. Why can’t (today() %--% (today() + years(1)) / months(1) work?

------------------------------------------------------------------------

Thanks for reading! I hope you found my solutions to the exercises informative. A walkthrough of chapters 17 - 21 can be found at [r4ds\_p6\_chapters17-21\_walkthrough.md](https://github.com/erilu/R-for-data-science-walkthrough/blob/master/r4ds_p6_chapters17-21_walkthrough.md).
