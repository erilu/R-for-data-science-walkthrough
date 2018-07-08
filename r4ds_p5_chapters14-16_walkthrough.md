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
writeLines(literal_backslash)
```

    ## hello_\_world

``` r
str_view(literal_backslash, "\\\\")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-7fd4c9bfbc56810b2ff6">{"x":{"html":"<ul>\n  <li>hello_<span class='match'>\\<\/span>_world<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view(example, "\"\'\\\\" )
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-66533909d6181a077998">{"x":{"html":"<ul>\n  <li>hello_<span class='match'>\"'\\<\/span>_world<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view(test, "\\$\\^\\$")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-ec2f7f49713eb0af4bf7">{"x":{"html":"<ul>\n  <li><span class='match'>$^$<\/span>hello<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view(words, "^y", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-099b953bde7f16439a09">{"x":{"html":"<ul>\n  <li><span class='match'>y<\/span>ear<\/li>\n  <li><span class='match'>y<\/span>es<\/li>\n  <li><span class='match'>y<\/span>esterday<\/li>\n  <li><span class='match'>y<\/span>et<\/li>\n  <li><span class='match'>y<\/span>ou<\/li>\n  <li><span class='match'>y<\/span>oung<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# ends with "x"
str_view(words, "x$", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-120fd94af230c6d9b616">{"x":{"html":"<ul>\n  <li>bo<span class='match'>x<\/span><\/li>\n  <li>se<span class='match'>x<\/span><\/li>\n  <li>si<span class='match'>x<\/span><\/li>\n  <li>ta<span class='match'>x<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# are exactly 3 letters long
str_view(words, "^...$")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-63dd28e557c9d478eeeb">{"x":{"html":"<ul>\n  <li>a<\/li>\n  <li>able<\/li>\n  <li>about<\/li>\n  <li>absolute<\/li>\n  <li>accept<\/li>\n  <li>account<\/li>\n  <li>achieve<\/li>\n  <li>across<\/li>\n  <li><span class='match'>act<\/span><\/li>\n  <li>active<\/li>\n  <li>actual<\/li>\n  <li><span class='match'>add<\/span><\/li>\n  <li>address<\/li>\n  <li>admit<\/li>\n  <li>advertise<\/li>\n  <li>affect<\/li>\n  <li>afford<\/li>\n  <li>after<\/li>\n  <li>afternoon<\/li>\n  <li>again<\/li>\n  <li>against<\/li>\n  <li><span class='match'>age<\/span><\/li>\n  <li>agent<\/li>\n  <li><span class='match'>ago<\/span><\/li>\n  <li>agree<\/li>\n  <li><span class='match'>air<\/span><\/li>\n  <li><span class='match'>all<\/span><\/li>\n  <li>allow<\/li>\n  <li>almost<\/li>\n  <li>along<\/li>\n  <li>already<\/li>\n  <li>alright<\/li>\n  <li>also<\/li>\n  <li>although<\/li>\n  <li>always<\/li>\n  <li>america<\/li>\n  <li>amount<\/li>\n  <li><span class='match'>and<\/span><\/li>\n  <li>another<\/li>\n  <li>answer<\/li>\n  <li><span class='match'>any<\/span><\/li>\n  <li>apart<\/li>\n  <li>apparent<\/li>\n  <li>appear<\/li>\n  <li>apply<\/li>\n  <li>appoint<\/li>\n  <li>approach<\/li>\n  <li>appropriate<\/li>\n  <li>area<\/li>\n  <li>argue<\/li>\n  <li><span class='match'>arm<\/span><\/li>\n  <li>around<\/li>\n  <li>arrange<\/li>\n  <li><span class='match'>art<\/span><\/li>\n  <li>as<\/li>\n  <li><span class='match'>ask<\/span><\/li>\n  <li>associate<\/li>\n  <li>assume<\/li>\n  <li>at<\/li>\n  <li>attend<\/li>\n  <li>authority<\/li>\n  <li>available<\/li>\n  <li>aware<\/li>\n  <li>away<\/li>\n  <li>awful<\/li>\n  <li>baby<\/li>\n  <li>back<\/li>\n  <li><span class='match'>bad<\/span><\/li>\n  <li><span class='match'>bag<\/span><\/li>\n  <li>balance<\/li>\n  <li>ball<\/li>\n  <li>bank<\/li>\n  <li><span class='match'>bar<\/span><\/li>\n  <li>base<\/li>\n  <li>basis<\/li>\n  <li>be<\/li>\n  <li>bear<\/li>\n  <li>beat<\/li>\n  <li>beauty<\/li>\n  <li>because<\/li>\n  <li>become<\/li>\n  <li><span class='match'>bed<\/span><\/li>\n  <li>before<\/li>\n  <li>begin<\/li>\n  <li>behind<\/li>\n  <li>believe<\/li>\n  <li>benefit<\/li>\n  <li>best<\/li>\n  <li><span class='match'>bet<\/span><\/li>\n  <li>between<\/li>\n  <li><span class='match'>big<\/span><\/li>\n  <li>bill<\/li>\n  <li>birth<\/li>\n  <li><span class='match'>bit<\/span><\/li>\n  <li>black<\/li>\n  <li>bloke<\/li>\n  <li>blood<\/li>\n  <li>blow<\/li>\n  <li>blue<\/li>\n  <li>board<\/li>\n  <li>boat<\/li>\n  <li>body<\/li>\n  <li>book<\/li>\n  <li>both<\/li>\n  <li>bother<\/li>\n  <li>bottle<\/li>\n  <li>bottom<\/li>\n  <li><span class='match'>box<\/span><\/li>\n  <li><span class='match'>boy<\/span><\/li>\n  <li>break<\/li>\n  <li>brief<\/li>\n  <li>brilliant<\/li>\n  <li>bring<\/li>\n  <li>britain<\/li>\n  <li>brother<\/li>\n  <li>budget<\/li>\n  <li>build<\/li>\n  <li><span class='match'>bus<\/span><\/li>\n  <li>business<\/li>\n  <li>busy<\/li>\n  <li><span class='match'>but<\/span><\/li>\n  <li><span class='match'>buy<\/span><\/li>\n  <li>by<\/li>\n  <li>cake<\/li>\n  <li>call<\/li>\n  <li><span class='match'>can<\/span><\/li>\n  <li><span class='match'>car<\/span><\/li>\n  <li>card<\/li>\n  <li>care<\/li>\n  <li>carry<\/li>\n  <li>case<\/li>\n  <li><span class='match'>cat<\/span><\/li>\n  <li>catch<\/li>\n  <li>cause<\/li>\n  <li>cent<\/li>\n  <li>centre<\/li>\n  <li>certain<\/li>\n  <li>chair<\/li>\n  <li>chairman<\/li>\n  <li>chance<\/li>\n  <li>change<\/li>\n  <li>chap<\/li>\n  <li>character<\/li>\n  <li>charge<\/li>\n  <li>cheap<\/li>\n  <li>check<\/li>\n  <li>child<\/li>\n  <li>choice<\/li>\n  <li>choose<\/li>\n  <li>Christ<\/li>\n  <li>Christmas<\/li>\n  <li>church<\/li>\n  <li>city<\/li>\n  <li>claim<\/li>\n  <li>class<\/li>\n  <li>clean<\/li>\n  <li>clear<\/li>\n  <li>client<\/li>\n  <li>clock<\/li>\n  <li>close<\/li>\n  <li>closes<\/li>\n  <li>clothe<\/li>\n  <li>club<\/li>\n  <li>coffee<\/li>\n  <li>cold<\/li>\n  <li>colleague<\/li>\n  <li>collect<\/li>\n  <li>college<\/li>\n  <li>colour<\/li>\n  <li>come<\/li>\n  <li>comment<\/li>\n  <li>commit<\/li>\n  <li>committee<\/li>\n  <li>common<\/li>\n  <li>community<\/li>\n  <li>company<\/li>\n  <li>compare<\/li>\n  <li>complete<\/li>\n  <li>compute<\/li>\n  <li>concern<\/li>\n  <li>condition<\/li>\n  <li>confer<\/li>\n  <li>consider<\/li>\n  <li>consult<\/li>\n  <li>contact<\/li>\n  <li>continue<\/li>\n  <li>contract<\/li>\n  <li>control<\/li>\n  <li>converse<\/li>\n  <li>cook<\/li>\n  <li>copy<\/li>\n  <li>corner<\/li>\n  <li>correct<\/li>\n  <li>cost<\/li>\n  <li>could<\/li>\n  <li>council<\/li>\n  <li>count<\/li>\n  <li>country<\/li>\n  <li>county<\/li>\n  <li>couple<\/li>\n  <li>course<\/li>\n  <li>court<\/li>\n  <li>cover<\/li>\n  <li>create<\/li>\n  <li>cross<\/li>\n  <li><span class='match'>cup<\/span><\/li>\n  <li>current<\/li>\n  <li><span class='match'>cut<\/span><\/li>\n  <li><span class='match'>dad<\/span><\/li>\n  <li>danger<\/li>\n  <li>date<\/li>\n  <li><span class='match'>day<\/span><\/li>\n  <li>dead<\/li>\n  <li>deal<\/li>\n  <li>dear<\/li>\n  <li>debate<\/li>\n  <li>decide<\/li>\n  <li>decision<\/li>\n  <li>deep<\/li>\n  <li>definite<\/li>\n  <li>degree<\/li>\n  <li>department<\/li>\n  <li>depend<\/li>\n  <li>describe<\/li>\n  <li>design<\/li>\n  <li>detail<\/li>\n  <li>develop<\/li>\n  <li><span class='match'>die<\/span><\/li>\n  <li>difference<\/li>\n  <li>difficult<\/li>\n  <li>dinner<\/li>\n  <li>direct<\/li>\n  <li>discuss<\/li>\n  <li>district<\/li>\n  <li>divide<\/li>\n  <li>do<\/li>\n  <li>doctor<\/li>\n  <li>document<\/li>\n  <li><span class='match'>dog<\/span><\/li>\n  <li>door<\/li>\n  <li>double<\/li>\n  <li>doubt<\/li>\n  <li>down<\/li>\n  <li>draw<\/li>\n  <li>dress<\/li>\n  <li>drink<\/li>\n  <li>drive<\/li>\n  <li>drop<\/li>\n  <li><span class='match'>dry<\/span><\/li>\n  <li><span class='match'>due<\/span><\/li>\n  <li>during<\/li>\n  <li>each<\/li>\n  <li>early<\/li>\n  <li>east<\/li>\n  <li>easy<\/li>\n  <li><span class='match'>eat<\/span><\/li>\n  <li>economy<\/li>\n  <li>educate<\/li>\n  <li>effect<\/li>\n  <li><span class='match'>egg<\/span><\/li>\n  <li>eight<\/li>\n  <li>either<\/li>\n  <li>elect<\/li>\n  <li>electric<\/li>\n  <li>eleven<\/li>\n  <li>else<\/li>\n  <li>employ<\/li>\n  <li>encourage<\/li>\n  <li><span class='match'>end<\/span><\/li>\n  <li>engine<\/li>\n  <li>english<\/li>\n  <li>enjoy<\/li>\n  <li>enough<\/li>\n  <li>enter<\/li>\n  <li>environment<\/li>\n  <li>equal<\/li>\n  <li>especial<\/li>\n  <li>europe<\/li>\n  <li>even<\/li>\n  <li>evening<\/li>\n  <li>ever<\/li>\n  <li>every<\/li>\n  <li>evidence<\/li>\n  <li>exact<\/li>\n  <li>example<\/li>\n  <li>except<\/li>\n  <li>excuse<\/li>\n  <li>exercise<\/li>\n  <li>exist<\/li>\n  <li>expect<\/li>\n  <li>expense<\/li>\n  <li>experience<\/li>\n  <li>explain<\/li>\n  <li>express<\/li>\n  <li>extra<\/li>\n  <li><span class='match'>eye<\/span><\/li>\n  <li>face<\/li>\n  <li>fact<\/li>\n  <li>fair<\/li>\n  <li>fall<\/li>\n  <li>family<\/li>\n  <li><span class='match'>far<\/span><\/li>\n  <li>farm<\/li>\n  <li>fast<\/li>\n  <li>father<\/li>\n  <li>favour<\/li>\n  <li>feed<\/li>\n  <li>feel<\/li>\n  <li><span class='match'>few<\/span><\/li>\n  <li>field<\/li>\n  <li>fight<\/li>\n  <li>figure<\/li>\n  <li>file<\/li>\n  <li>fill<\/li>\n  <li>film<\/li>\n  <li>final<\/li>\n  <li>finance<\/li>\n  <li>find<\/li>\n  <li>fine<\/li>\n  <li>finish<\/li>\n  <li>fire<\/li>\n  <li>first<\/li>\n  <li>fish<\/li>\n  <li><span class='match'>fit<\/span><\/li>\n  <li>five<\/li>\n  <li>flat<\/li>\n  <li>floor<\/li>\n  <li><span class='match'>fly<\/span><\/li>\n  <li>follow<\/li>\n  <li>food<\/li>\n  <li>foot<\/li>\n  <li><span class='match'>for<\/span><\/li>\n  <li>force<\/li>\n  <li>forget<\/li>\n  <li>form<\/li>\n  <li>fortune<\/li>\n  <li>forward<\/li>\n  <li>four<\/li>\n  <li>france<\/li>\n  <li>free<\/li>\n  <li>friday<\/li>\n  <li>friend<\/li>\n  <li>from<\/li>\n  <li>front<\/li>\n  <li>full<\/li>\n  <li><span class='match'>fun<\/span><\/li>\n  <li>function<\/li>\n  <li>fund<\/li>\n  <li>further<\/li>\n  <li>future<\/li>\n  <li>game<\/li>\n  <li>garden<\/li>\n  <li><span class='match'>gas<\/span><\/li>\n  <li>general<\/li>\n  <li>germany<\/li>\n  <li><span class='match'>get<\/span><\/li>\n  <li>girl<\/li>\n  <li>give<\/li>\n  <li>glass<\/li>\n  <li>go<\/li>\n  <li><span class='match'>god<\/span><\/li>\n  <li>good<\/li>\n  <li>goodbye<\/li>\n  <li>govern<\/li>\n  <li>grand<\/li>\n  <li>grant<\/li>\n  <li>great<\/li>\n  <li>green<\/li>\n  <li>ground<\/li>\n  <li>group<\/li>\n  <li>grow<\/li>\n  <li>guess<\/li>\n  <li><span class='match'>guy<\/span><\/li>\n  <li>hair<\/li>\n  <li>half<\/li>\n  <li>hall<\/li>\n  <li>hand<\/li>\n  <li>hang<\/li>\n  <li>happen<\/li>\n  <li>happy<\/li>\n  <li>hard<\/li>\n  <li>hate<\/li>\n  <li>have<\/li>\n  <li>he<\/li>\n  <li>head<\/li>\n  <li>health<\/li>\n  <li>hear<\/li>\n  <li>heart<\/li>\n  <li>heat<\/li>\n  <li>heavy<\/li>\n  <li>hell<\/li>\n  <li>help<\/li>\n  <li>here<\/li>\n  <li>high<\/li>\n  <li>history<\/li>\n  <li><span class='match'>hit<\/span><\/li>\n  <li>hold<\/li>\n  <li>holiday<\/li>\n  <li>home<\/li>\n  <li>honest<\/li>\n  <li>hope<\/li>\n  <li>horse<\/li>\n  <li>hospital<\/li>\n  <li><span class='match'>hot<\/span><\/li>\n  <li>hour<\/li>\n  <li>house<\/li>\n  <li><span class='match'>how<\/span><\/li>\n  <li>however<\/li>\n  <li>hullo<\/li>\n  <li>hundred<\/li>\n  <li>husband<\/li>\n  <li>idea<\/li>\n  <li>identify<\/li>\n  <li>if<\/li>\n  <li>imagine<\/li>\n  <li>important<\/li>\n  <li>improve<\/li>\n  <li>in<\/li>\n  <li>include<\/li>\n  <li>income<\/li>\n  <li>increase<\/li>\n  <li>indeed<\/li>\n  <li>individual<\/li>\n  <li>industry<\/li>\n  <li>inform<\/li>\n  <li>inside<\/li>\n  <li>instead<\/li>\n  <li>insure<\/li>\n  <li>interest<\/li>\n  <li>into<\/li>\n  <li>introduce<\/li>\n  <li>invest<\/li>\n  <li>involve<\/li>\n  <li>issue<\/li>\n  <li>it<\/li>\n  <li>item<\/li>\n  <li>jesus<\/li>\n  <li><span class='match'>job<\/span><\/li>\n  <li>join<\/li>\n  <li>judge<\/li>\n  <li>jump<\/li>\n  <li>just<\/li>\n  <li>keep<\/li>\n  <li><span class='match'>key<\/span><\/li>\n  <li><span class='match'>kid<\/span><\/li>\n  <li>kill<\/li>\n  <li>kind<\/li>\n  <li>king<\/li>\n  <li>kitchen<\/li>\n  <li>knock<\/li>\n  <li>know<\/li>\n  <li>labour<\/li>\n  <li><span class='match'>lad<\/span><\/li>\n  <li>lady<\/li>\n  <li>land<\/li>\n  <li>language<\/li>\n  <li>large<\/li>\n  <li>last<\/li>\n  <li>late<\/li>\n  <li>laugh<\/li>\n  <li><span class='match'>law<\/span><\/li>\n  <li><span class='match'>lay<\/span><\/li>\n  <li>lead<\/li>\n  <li>learn<\/li>\n  <li>leave<\/li>\n  <li>left<\/li>\n  <li><span class='match'>leg<\/span><\/li>\n  <li>less<\/li>\n  <li><span class='match'>let<\/span><\/li>\n  <li>letter<\/li>\n  <li>level<\/li>\n  <li><span class='match'>lie<\/span><\/li>\n  <li>life<\/li>\n  <li>light<\/li>\n  <li>like<\/li>\n  <li>likely<\/li>\n  <li>limit<\/li>\n  <li>line<\/li>\n  <li>link<\/li>\n  <li>list<\/li>\n  <li>listen<\/li>\n  <li>little<\/li>\n  <li>live<\/li>\n  <li>load<\/li>\n  <li>local<\/li>\n  <li>lock<\/li>\n  <li>london<\/li>\n  <li>long<\/li>\n  <li>look<\/li>\n  <li>lord<\/li>\n  <li>lose<\/li>\n  <li><span class='match'>lot<\/span><\/li>\n  <li>love<\/li>\n  <li><span class='match'>low<\/span><\/li>\n  <li>luck<\/li>\n  <li>lunch<\/li>\n  <li>machine<\/li>\n  <li>main<\/li>\n  <li>major<\/li>\n  <li>make<\/li>\n  <li><span class='match'>man<\/span><\/li>\n  <li>manage<\/li>\n  <li>many<\/li>\n  <li>mark<\/li>\n  <li>market<\/li>\n  <li>marry<\/li>\n  <li>match<\/li>\n  <li>matter<\/li>\n  <li><span class='match'>may<\/span><\/li>\n  <li>maybe<\/li>\n  <li>mean<\/li>\n  <li>meaning<\/li>\n  <li>measure<\/li>\n  <li>meet<\/li>\n  <li>member<\/li>\n  <li>mention<\/li>\n  <li>middle<\/li>\n  <li>might<\/li>\n  <li>mile<\/li>\n  <li>milk<\/li>\n  <li>million<\/li>\n  <li>mind<\/li>\n  <li>minister<\/li>\n  <li>minus<\/li>\n  <li>minute<\/li>\n  <li>miss<\/li>\n  <li>mister<\/li>\n  <li>moment<\/li>\n  <li>monday<\/li>\n  <li>money<\/li>\n  <li>month<\/li>\n  <li>more<\/li>\n  <li>morning<\/li>\n  <li>most<\/li>\n  <li>mother<\/li>\n  <li>motion<\/li>\n  <li>move<\/li>\n  <li><span class='match'>mrs<\/span><\/li>\n  <li>much<\/li>\n  <li>music<\/li>\n  <li>must<\/li>\n  <li>name<\/li>\n  <li>nation<\/li>\n  <li>nature<\/li>\n  <li>near<\/li>\n  <li>necessary<\/li>\n  <li>need<\/li>\n  <li>never<\/li>\n  <li><span class='match'>new<\/span><\/li>\n  <li>news<\/li>\n  <li>next<\/li>\n  <li>nice<\/li>\n  <li>night<\/li>\n  <li>nine<\/li>\n  <li>no<\/li>\n  <li><span class='match'>non<\/span><\/li>\n  <li>none<\/li>\n  <li>normal<\/li>\n  <li>north<\/li>\n  <li><span class='match'>not<\/span><\/li>\n  <li>note<\/li>\n  <li>notice<\/li>\n  <li><span class='match'>now<\/span><\/li>\n  <li>number<\/li>\n  <li>obvious<\/li>\n  <li>occasion<\/li>\n  <li><span class='match'>odd<\/span><\/li>\n  <li>of<\/li>\n  <li><span class='match'>off<\/span><\/li>\n  <li>offer<\/li>\n  <li>office<\/li>\n  <li>often<\/li>\n  <li>okay<\/li>\n  <li><span class='match'>old<\/span><\/li>\n  <li>on<\/li>\n  <li>once<\/li>\n  <li><span class='match'>one<\/span><\/li>\n  <li>only<\/li>\n  <li>open<\/li>\n  <li>operate<\/li>\n  <li>opportunity<\/li>\n  <li>oppose<\/li>\n  <li>or<\/li>\n  <li>order<\/li>\n  <li>organize<\/li>\n  <li>original<\/li>\n  <li>other<\/li>\n  <li>otherwise<\/li>\n  <li>ought<\/li>\n  <li><span class='match'>out<\/span><\/li>\n  <li>over<\/li>\n  <li><span class='match'>own<\/span><\/li>\n  <li>pack<\/li>\n  <li>page<\/li>\n  <li>paint<\/li>\n  <li>pair<\/li>\n  <li>paper<\/li>\n  <li>paragraph<\/li>\n  <li>pardon<\/li>\n  <li>parent<\/li>\n  <li>park<\/li>\n  <li>part<\/li>\n  <li>particular<\/li>\n  <li>party<\/li>\n  <li>pass<\/li>\n  <li>past<\/li>\n  <li><span class='match'>pay<\/span><\/li>\n  <li>pence<\/li>\n  <li>pension<\/li>\n  <li>people<\/li>\n  <li><span class='match'>per<\/span><\/li>\n  <li>percent<\/li>\n  <li>perfect<\/li>\n  <li>perhaps<\/li>\n  <li>period<\/li>\n  <li>person<\/li>\n  <li>photograph<\/li>\n  <li>pick<\/li>\n  <li>picture<\/li>\n  <li>piece<\/li>\n  <li>place<\/li>\n  <li>plan<\/li>\n  <li>play<\/li>\n  <li>please<\/li>\n  <li>plus<\/li>\n  <li>point<\/li>\n  <li>police<\/li>\n  <li>policy<\/li>\n  <li>politic<\/li>\n  <li>poor<\/li>\n  <li>position<\/li>\n  <li>positive<\/li>\n  <li>possible<\/li>\n  <li>post<\/li>\n  <li>pound<\/li>\n  <li>power<\/li>\n  <li>practise<\/li>\n  <li>prepare<\/li>\n  <li>present<\/li>\n  <li>press<\/li>\n  <li>pressure<\/li>\n  <li>presume<\/li>\n  <li>pretty<\/li>\n  <li>previous<\/li>\n  <li>price<\/li>\n  <li>print<\/li>\n  <li>private<\/li>\n  <li>probable<\/li>\n  <li>problem<\/li>\n  <li>proceed<\/li>\n  <li>process<\/li>\n  <li>produce<\/li>\n  <li>product<\/li>\n  <li>programme<\/li>\n  <li>project<\/li>\n  <li>proper<\/li>\n  <li>propose<\/li>\n  <li>protect<\/li>\n  <li>provide<\/li>\n  <li>public<\/li>\n  <li>pull<\/li>\n  <li>purpose<\/li>\n  <li>push<\/li>\n  <li><span class='match'>put<\/span><\/li>\n  <li>quality<\/li>\n  <li>quarter<\/li>\n  <li>question<\/li>\n  <li>quick<\/li>\n  <li>quid<\/li>\n  <li>quiet<\/li>\n  <li>quite<\/li>\n  <li>radio<\/li>\n  <li>rail<\/li>\n  <li>raise<\/li>\n  <li>range<\/li>\n  <li>rate<\/li>\n  <li>rather<\/li>\n  <li>read<\/li>\n  <li>ready<\/li>\n  <li>real<\/li>\n  <li>realise<\/li>\n  <li>really<\/li>\n  <li>reason<\/li>\n  <li>receive<\/li>\n  <li>recent<\/li>\n  <li>reckon<\/li>\n  <li>recognize<\/li>\n  <li>recommend<\/li>\n  <li>record<\/li>\n  <li><span class='match'>red<\/span><\/li>\n  <li>reduce<\/li>\n  <li>refer<\/li>\n  <li>regard<\/li>\n  <li>region<\/li>\n  <li>relation<\/li>\n  <li>remember<\/li>\n  <li>report<\/li>\n  <li>represent<\/li>\n  <li>require<\/li>\n  <li>research<\/li>\n  <li>resource<\/li>\n  <li>respect<\/li>\n  <li>responsible<\/li>\n  <li>rest<\/li>\n  <li>result<\/li>\n  <li>return<\/li>\n  <li><span class='match'>rid<\/span><\/li>\n  <li>right<\/li>\n  <li>ring<\/li>\n  <li>rise<\/li>\n  <li>road<\/li>\n  <li>role<\/li>\n  <li>roll<\/li>\n  <li>room<\/li>\n  <li>round<\/li>\n  <li>rule<\/li>\n  <li><span class='match'>run<\/span><\/li>\n  <li>safe<\/li>\n  <li>sale<\/li>\n  <li>same<\/li>\n  <li>saturday<\/li>\n  <li>save<\/li>\n  <li><span class='match'>say<\/span><\/li>\n  <li>scheme<\/li>\n  <li>school<\/li>\n  <li>science<\/li>\n  <li>score<\/li>\n  <li>scotland<\/li>\n  <li>seat<\/li>\n  <li>second<\/li>\n  <li>secretary<\/li>\n  <li>section<\/li>\n  <li>secure<\/li>\n  <li><span class='match'>see<\/span><\/li>\n  <li>seem<\/li>\n  <li>self<\/li>\n  <li>sell<\/li>\n  <li>send<\/li>\n  <li>sense<\/li>\n  <li>separate<\/li>\n  <li>serious<\/li>\n  <li>serve<\/li>\n  <li>service<\/li>\n  <li><span class='match'>set<\/span><\/li>\n  <li>settle<\/li>\n  <li>seven<\/li>\n  <li><span class='match'>sex<\/span><\/li>\n  <li>shall<\/li>\n  <li>share<\/li>\n  <li><span class='match'>she<\/span><\/li>\n  <li>sheet<\/li>\n  <li>shoe<\/li>\n  <li>shoot<\/li>\n  <li>shop<\/li>\n  <li>short<\/li>\n  <li>should<\/li>\n  <li>show<\/li>\n  <li>shut<\/li>\n  <li>sick<\/li>\n  <li>side<\/li>\n  <li>sign<\/li>\n  <li>similar<\/li>\n  <li>simple<\/li>\n  <li>since<\/li>\n  <li>sing<\/li>\n  <li>single<\/li>\n  <li><span class='match'>sir<\/span><\/li>\n  <li>sister<\/li>\n  <li><span class='match'>sit<\/span><\/li>\n  <li>site<\/li>\n  <li>situate<\/li>\n  <li><span class='match'>six<\/span><\/li>\n  <li>size<\/li>\n  <li>sleep<\/li>\n  <li>slight<\/li>\n  <li>slow<\/li>\n  <li>small<\/li>\n  <li>smoke<\/li>\n  <li>so<\/li>\n  <li>social<\/li>\n  <li>society<\/li>\n  <li>some<\/li>\n  <li><span class='match'>son<\/span><\/li>\n  <li>soon<\/li>\n  <li>sorry<\/li>\n  <li>sort<\/li>\n  <li>sound<\/li>\n  <li>south<\/li>\n  <li>space<\/li>\n  <li>speak<\/li>\n  <li>special<\/li>\n  <li>specific<\/li>\n  <li>speed<\/li>\n  <li>spell<\/li>\n  <li>spend<\/li>\n  <li>square<\/li>\n  <li>staff<\/li>\n  <li>stage<\/li>\n  <li>stairs<\/li>\n  <li>stand<\/li>\n  <li>standard<\/li>\n  <li>start<\/li>\n  <li>state<\/li>\n  <li>station<\/li>\n  <li>stay<\/li>\n  <li>step<\/li>\n  <li>stick<\/li>\n  <li>still<\/li>\n  <li>stop<\/li>\n  <li>story<\/li>\n  <li>straight<\/li>\n  <li>strategy<\/li>\n  <li>street<\/li>\n  <li>strike<\/li>\n  <li>strong<\/li>\n  <li>structure<\/li>\n  <li>student<\/li>\n  <li>study<\/li>\n  <li>stuff<\/li>\n  <li>stupid<\/li>\n  <li>subject<\/li>\n  <li>succeed<\/li>\n  <li>such<\/li>\n  <li>sudden<\/li>\n  <li>suggest<\/li>\n  <li>suit<\/li>\n  <li>summer<\/li>\n  <li><span class='match'>sun<\/span><\/li>\n  <li>sunday<\/li>\n  <li>supply<\/li>\n  <li>support<\/li>\n  <li>suppose<\/li>\n  <li>sure<\/li>\n  <li>surprise<\/li>\n  <li>switch<\/li>\n  <li>system<\/li>\n  <li>table<\/li>\n  <li>take<\/li>\n  <li>talk<\/li>\n  <li>tape<\/li>\n  <li><span class='match'>tax<\/span><\/li>\n  <li><span class='match'>tea<\/span><\/li>\n  <li>teach<\/li>\n  <li>team<\/li>\n  <li>telephone<\/li>\n  <li>television<\/li>\n  <li>tell<\/li>\n  <li><span class='match'>ten<\/span><\/li>\n  <li>tend<\/li>\n  <li>term<\/li>\n  <li>terrible<\/li>\n  <li>test<\/li>\n  <li>than<\/li>\n  <li>thank<\/li>\n  <li><span class='match'>the<\/span><\/li>\n  <li>then<\/li>\n  <li>there<\/li>\n  <li>therefore<\/li>\n  <li>they<\/li>\n  <li>thing<\/li>\n  <li>think<\/li>\n  <li>thirteen<\/li>\n  <li>thirty<\/li>\n  <li>this<\/li>\n  <li>thou<\/li>\n  <li>though<\/li>\n  <li>thousand<\/li>\n  <li>three<\/li>\n  <li>through<\/li>\n  <li>throw<\/li>\n  <li>thursday<\/li>\n  <li><span class='match'>tie<\/span><\/li>\n  <li>time<\/li>\n  <li>to<\/li>\n  <li>today<\/li>\n  <li>together<\/li>\n  <li>tomorrow<\/li>\n  <li>tonight<\/li>\n  <li><span class='match'>too<\/span><\/li>\n  <li><span class='match'>top<\/span><\/li>\n  <li>total<\/li>\n  <li>touch<\/li>\n  <li>toward<\/li>\n  <li>town<\/li>\n  <li>trade<\/li>\n  <li>traffic<\/li>\n  <li>train<\/li>\n  <li>transport<\/li>\n  <li>travel<\/li>\n  <li>treat<\/li>\n  <li>tree<\/li>\n  <li>trouble<\/li>\n  <li>true<\/li>\n  <li>trust<\/li>\n  <li><span class='match'>try<\/span><\/li>\n  <li>tuesday<\/li>\n  <li>turn<\/li>\n  <li>twelve<\/li>\n  <li>twenty<\/li>\n  <li><span class='match'>two<\/span><\/li>\n  <li>type<\/li>\n  <li>under<\/li>\n  <li>understand<\/li>\n  <li>union<\/li>\n  <li>unit<\/li>\n  <li>unite<\/li>\n  <li>university<\/li>\n  <li>unless<\/li>\n  <li>until<\/li>\n  <li>up<\/li>\n  <li>upon<\/li>\n  <li><span class='match'>use<\/span><\/li>\n  <li>usual<\/li>\n  <li>value<\/li>\n  <li>various<\/li>\n  <li>very<\/li>\n  <li>video<\/li>\n  <li>view<\/li>\n  <li>village<\/li>\n  <li>visit<\/li>\n  <li>vote<\/li>\n  <li>wage<\/li>\n  <li>wait<\/li>\n  <li>walk<\/li>\n  <li>wall<\/li>\n  <li>want<\/li>\n  <li><span class='match'>war<\/span><\/li>\n  <li>warm<\/li>\n  <li>wash<\/li>\n  <li>waste<\/li>\n  <li>watch<\/li>\n  <li>water<\/li>\n  <li><span class='match'>way<\/span><\/li>\n  <li>we<\/li>\n  <li>wear<\/li>\n  <li>wednesday<\/li>\n  <li><span class='match'>wee<\/span><\/li>\n  <li>week<\/li>\n  <li>weigh<\/li>\n  <li>welcome<\/li>\n  <li>well<\/li>\n  <li>west<\/li>\n  <li>what<\/li>\n  <li>when<\/li>\n  <li>where<\/li>\n  <li>whether<\/li>\n  <li>which<\/li>\n  <li>while<\/li>\n  <li>white<\/li>\n  <li><span class='match'>who<\/span><\/li>\n  <li>whole<\/li>\n  <li><span class='match'>why<\/span><\/li>\n  <li>wide<\/li>\n  <li>wife<\/li>\n  <li>will<\/li>\n  <li><span class='match'>win<\/span><\/li>\n  <li>wind<\/li>\n  <li>window<\/li>\n  <li>wish<\/li>\n  <li>with<\/li>\n  <li>within<\/li>\n  <li>without<\/li>\n  <li>woman<\/li>\n  <li>wonder<\/li>\n  <li>wood<\/li>\n  <li>word<\/li>\n  <li>work<\/li>\n  <li>world<\/li>\n  <li>worry<\/li>\n  <li>worse<\/li>\n  <li>worth<\/li>\n  <li>would<\/li>\n  <li>write<\/li>\n  <li>wrong<\/li>\n  <li>year<\/li>\n  <li><span class='match'>yes<\/span><\/li>\n  <li>yesterday<\/li>\n  <li><span class='match'>yet<\/span><\/li>\n  <li><span class='match'>you<\/span><\/li>\n  <li>young<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# have seven letters or more
str_view(words, "^.......")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-6439fb4f4a9c04ef3790">{"x":{"html":"<ul>\n  <li>a<\/li>\n  <li>able<\/li>\n  <li>about<\/li>\n  <li><span class='match'>absolut<\/span>e<\/li>\n  <li>accept<\/li>\n  <li><span class='match'>account<\/span><\/li>\n  <li><span class='match'>achieve<\/span><\/li>\n  <li>across<\/li>\n  <li>act<\/li>\n  <li>active<\/li>\n  <li>actual<\/li>\n  <li>add<\/li>\n  <li><span class='match'>address<\/span><\/li>\n  <li>admit<\/li>\n  <li><span class='match'>adverti<\/span>se<\/li>\n  <li>affect<\/li>\n  <li>afford<\/li>\n  <li>after<\/li>\n  <li><span class='match'>afterno<\/span>on<\/li>\n  <li>again<\/li>\n  <li><span class='match'>against<\/span><\/li>\n  <li>age<\/li>\n  <li>agent<\/li>\n  <li>ago<\/li>\n  <li>agree<\/li>\n  <li>air<\/li>\n  <li>all<\/li>\n  <li>allow<\/li>\n  <li>almost<\/li>\n  <li>along<\/li>\n  <li><span class='match'>already<\/span><\/li>\n  <li><span class='match'>alright<\/span><\/li>\n  <li>also<\/li>\n  <li><span class='match'>althoug<\/span>h<\/li>\n  <li>always<\/li>\n  <li><span class='match'>america<\/span><\/li>\n  <li>amount<\/li>\n  <li>and<\/li>\n  <li><span class='match'>another<\/span><\/li>\n  <li>answer<\/li>\n  <li>any<\/li>\n  <li>apart<\/li>\n  <li><span class='match'>apparen<\/span>t<\/li>\n  <li>appear<\/li>\n  <li>apply<\/li>\n  <li><span class='match'>appoint<\/span><\/li>\n  <li><span class='match'>approac<\/span>h<\/li>\n  <li><span class='match'>appropr<\/span>iate<\/li>\n  <li>area<\/li>\n  <li>argue<\/li>\n  <li>arm<\/li>\n  <li>around<\/li>\n  <li><span class='match'>arrange<\/span><\/li>\n  <li>art<\/li>\n  <li>as<\/li>\n  <li>ask<\/li>\n  <li><span class='match'>associa<\/span>te<\/li>\n  <li>assume<\/li>\n  <li>at<\/li>\n  <li>attend<\/li>\n  <li><span class='match'>authori<\/span>ty<\/li>\n  <li><span class='match'>availab<\/span>le<\/li>\n  <li>aware<\/li>\n  <li>away<\/li>\n  <li>awful<\/li>\n  <li>baby<\/li>\n  <li>back<\/li>\n  <li>bad<\/li>\n  <li>bag<\/li>\n  <li><span class='match'>balance<\/span><\/li>\n  <li>ball<\/li>\n  <li>bank<\/li>\n  <li>bar<\/li>\n  <li>base<\/li>\n  <li>basis<\/li>\n  <li>be<\/li>\n  <li>bear<\/li>\n  <li>beat<\/li>\n  <li>beauty<\/li>\n  <li><span class='match'>because<\/span><\/li>\n  <li>become<\/li>\n  <li>bed<\/li>\n  <li>before<\/li>\n  <li>begin<\/li>\n  <li>behind<\/li>\n  <li><span class='match'>believe<\/span><\/li>\n  <li><span class='match'>benefit<\/span><\/li>\n  <li>best<\/li>\n  <li>bet<\/li>\n  <li><span class='match'>between<\/span><\/li>\n  <li>big<\/li>\n  <li>bill<\/li>\n  <li>birth<\/li>\n  <li>bit<\/li>\n  <li>black<\/li>\n  <li>bloke<\/li>\n  <li>blood<\/li>\n  <li>blow<\/li>\n  <li>blue<\/li>\n  <li>board<\/li>\n  <li>boat<\/li>\n  <li>body<\/li>\n  <li>book<\/li>\n  <li>both<\/li>\n  <li>bother<\/li>\n  <li>bottle<\/li>\n  <li>bottom<\/li>\n  <li>box<\/li>\n  <li>boy<\/li>\n  <li>break<\/li>\n  <li>brief<\/li>\n  <li><span class='match'>brillia<\/span>nt<\/li>\n  <li>bring<\/li>\n  <li><span class='match'>britain<\/span><\/li>\n  <li><span class='match'>brother<\/span><\/li>\n  <li>budget<\/li>\n  <li>build<\/li>\n  <li>bus<\/li>\n  <li><span class='match'>busines<\/span>s<\/li>\n  <li>busy<\/li>\n  <li>but<\/li>\n  <li>buy<\/li>\n  <li>by<\/li>\n  <li>cake<\/li>\n  <li>call<\/li>\n  <li>can<\/li>\n  <li>car<\/li>\n  <li>card<\/li>\n  <li>care<\/li>\n  <li>carry<\/li>\n  <li>case<\/li>\n  <li>cat<\/li>\n  <li>catch<\/li>\n  <li>cause<\/li>\n  <li>cent<\/li>\n  <li>centre<\/li>\n  <li><span class='match'>certain<\/span><\/li>\n  <li>chair<\/li>\n  <li><span class='match'>chairma<\/span>n<\/li>\n  <li>chance<\/li>\n  <li>change<\/li>\n  <li>chap<\/li>\n  <li><span class='match'>charact<\/span>er<\/li>\n  <li>charge<\/li>\n  <li>cheap<\/li>\n  <li>check<\/li>\n  <li>child<\/li>\n  <li>choice<\/li>\n  <li>choose<\/li>\n  <li>Christ<\/li>\n  <li><span class='match'>Christm<\/span>as<\/li>\n  <li>church<\/li>\n  <li>city<\/li>\n  <li>claim<\/li>\n  <li>class<\/li>\n  <li>clean<\/li>\n  <li>clear<\/li>\n  <li>client<\/li>\n  <li>clock<\/li>\n  <li>close<\/li>\n  <li>closes<\/li>\n  <li>clothe<\/li>\n  <li>club<\/li>\n  <li>coffee<\/li>\n  <li>cold<\/li>\n  <li><span class='match'>colleag<\/span>ue<\/li>\n  <li><span class='match'>collect<\/span><\/li>\n  <li><span class='match'>college<\/span><\/li>\n  <li>colour<\/li>\n  <li>come<\/li>\n  <li><span class='match'>comment<\/span><\/li>\n  <li>commit<\/li>\n  <li><span class='match'>committ<\/span>ee<\/li>\n  <li>common<\/li>\n  <li><span class='match'>communi<\/span>ty<\/li>\n  <li><span class='match'>company<\/span><\/li>\n  <li><span class='match'>compare<\/span><\/li>\n  <li><span class='match'>complet<\/span>e<\/li>\n  <li><span class='match'>compute<\/span><\/li>\n  <li><span class='match'>concern<\/span><\/li>\n  <li><span class='match'>conditi<\/span>on<\/li>\n  <li>confer<\/li>\n  <li><span class='match'>conside<\/span>r<\/li>\n  <li><span class='match'>consult<\/span><\/li>\n  <li><span class='match'>contact<\/span><\/li>\n  <li><span class='match'>continu<\/span>e<\/li>\n  <li><span class='match'>contrac<\/span>t<\/li>\n  <li><span class='match'>control<\/span><\/li>\n  <li><span class='match'>convers<\/span>e<\/li>\n  <li>cook<\/li>\n  <li>copy<\/li>\n  <li>corner<\/li>\n  <li><span class='match'>correct<\/span><\/li>\n  <li>cost<\/li>\n  <li>could<\/li>\n  <li><span class='match'>council<\/span><\/li>\n  <li>count<\/li>\n  <li><span class='match'>country<\/span><\/li>\n  <li>county<\/li>\n  <li>couple<\/li>\n  <li>course<\/li>\n  <li>court<\/li>\n  <li>cover<\/li>\n  <li>create<\/li>\n  <li>cross<\/li>\n  <li>cup<\/li>\n  <li><span class='match'>current<\/span><\/li>\n  <li>cut<\/li>\n  <li>dad<\/li>\n  <li>danger<\/li>\n  <li>date<\/li>\n  <li>day<\/li>\n  <li>dead<\/li>\n  <li>deal<\/li>\n  <li>dear<\/li>\n  <li>debate<\/li>\n  <li>decide<\/li>\n  <li><span class='match'>decisio<\/span>n<\/li>\n  <li>deep<\/li>\n  <li><span class='match'>definit<\/span>e<\/li>\n  <li>degree<\/li>\n  <li><span class='match'>departm<\/span>ent<\/li>\n  <li>depend<\/li>\n  <li><span class='match'>describ<\/span>e<\/li>\n  <li>design<\/li>\n  <li>detail<\/li>\n  <li><span class='match'>develop<\/span><\/li>\n  <li>die<\/li>\n  <li><span class='match'>differe<\/span>nce<\/li>\n  <li><span class='match'>difficu<\/span>lt<\/li>\n  <li>dinner<\/li>\n  <li>direct<\/li>\n  <li><span class='match'>discuss<\/span><\/li>\n  <li><span class='match'>distric<\/span>t<\/li>\n  <li>divide<\/li>\n  <li>do<\/li>\n  <li>doctor<\/li>\n  <li><span class='match'>documen<\/span>t<\/li>\n  <li>dog<\/li>\n  <li>door<\/li>\n  <li>double<\/li>\n  <li>doubt<\/li>\n  <li>down<\/li>\n  <li>draw<\/li>\n  <li>dress<\/li>\n  <li>drink<\/li>\n  <li>drive<\/li>\n  <li>drop<\/li>\n  <li>dry<\/li>\n  <li>due<\/li>\n  <li>during<\/li>\n  <li>each<\/li>\n  <li>early<\/li>\n  <li>east<\/li>\n  <li>easy<\/li>\n  <li>eat<\/li>\n  <li><span class='match'>economy<\/span><\/li>\n  <li><span class='match'>educate<\/span><\/li>\n  <li>effect<\/li>\n  <li>egg<\/li>\n  <li>eight<\/li>\n  <li>either<\/li>\n  <li>elect<\/li>\n  <li><span class='match'>electri<\/span>c<\/li>\n  <li>eleven<\/li>\n  <li>else<\/li>\n  <li>employ<\/li>\n  <li><span class='match'>encoura<\/span>ge<\/li>\n  <li>end<\/li>\n  <li>engine<\/li>\n  <li><span class='match'>english<\/span><\/li>\n  <li>enjoy<\/li>\n  <li>enough<\/li>\n  <li>enter<\/li>\n  <li><span class='match'>environ<\/span>ment<\/li>\n  <li>equal<\/li>\n  <li><span class='match'>especia<\/span>l<\/li>\n  <li>europe<\/li>\n  <li>even<\/li>\n  <li><span class='match'>evening<\/span><\/li>\n  <li>ever<\/li>\n  <li>every<\/li>\n  <li><span class='match'>evidenc<\/span>e<\/li>\n  <li>exact<\/li>\n  <li><span class='match'>example<\/span><\/li>\n  <li>except<\/li>\n  <li>excuse<\/li>\n  <li><span class='match'>exercis<\/span>e<\/li>\n  <li>exist<\/li>\n  <li>expect<\/li>\n  <li><span class='match'>expense<\/span><\/li>\n  <li><span class='match'>experie<\/span>nce<\/li>\n  <li><span class='match'>explain<\/span><\/li>\n  <li><span class='match'>express<\/span><\/li>\n  <li>extra<\/li>\n  <li>eye<\/li>\n  <li>face<\/li>\n  <li>fact<\/li>\n  <li>fair<\/li>\n  <li>fall<\/li>\n  <li>family<\/li>\n  <li>far<\/li>\n  <li>farm<\/li>\n  <li>fast<\/li>\n  <li>father<\/li>\n  <li>favour<\/li>\n  <li>feed<\/li>\n  <li>feel<\/li>\n  <li>few<\/li>\n  <li>field<\/li>\n  <li>fight<\/li>\n  <li>figure<\/li>\n  <li>file<\/li>\n  <li>fill<\/li>\n  <li>film<\/li>\n  <li>final<\/li>\n  <li><span class='match'>finance<\/span><\/li>\n  <li>find<\/li>\n  <li>fine<\/li>\n  <li>finish<\/li>\n  <li>fire<\/li>\n  <li>first<\/li>\n  <li>fish<\/li>\n  <li>fit<\/li>\n  <li>five<\/li>\n  <li>flat<\/li>\n  <li>floor<\/li>\n  <li>fly<\/li>\n  <li>follow<\/li>\n  <li>food<\/li>\n  <li>foot<\/li>\n  <li>for<\/li>\n  <li>force<\/li>\n  <li>forget<\/li>\n  <li>form<\/li>\n  <li><span class='match'>fortune<\/span><\/li>\n  <li><span class='match'>forward<\/span><\/li>\n  <li>four<\/li>\n  <li>france<\/li>\n  <li>free<\/li>\n  <li>friday<\/li>\n  <li>friend<\/li>\n  <li>from<\/li>\n  <li>front<\/li>\n  <li>full<\/li>\n  <li>fun<\/li>\n  <li><span class='match'>functio<\/span>n<\/li>\n  <li>fund<\/li>\n  <li><span class='match'>further<\/span><\/li>\n  <li>future<\/li>\n  <li>game<\/li>\n  <li>garden<\/li>\n  <li>gas<\/li>\n  <li><span class='match'>general<\/span><\/li>\n  <li><span class='match'>germany<\/span><\/li>\n  <li>get<\/li>\n  <li>girl<\/li>\n  <li>give<\/li>\n  <li>glass<\/li>\n  <li>go<\/li>\n  <li>god<\/li>\n  <li>good<\/li>\n  <li><span class='match'>goodbye<\/span><\/li>\n  <li>govern<\/li>\n  <li>grand<\/li>\n  <li>grant<\/li>\n  <li>great<\/li>\n  <li>green<\/li>\n  <li>ground<\/li>\n  <li>group<\/li>\n  <li>grow<\/li>\n  <li>guess<\/li>\n  <li>guy<\/li>\n  <li>hair<\/li>\n  <li>half<\/li>\n  <li>hall<\/li>\n  <li>hand<\/li>\n  <li>hang<\/li>\n  <li>happen<\/li>\n  <li>happy<\/li>\n  <li>hard<\/li>\n  <li>hate<\/li>\n  <li>have<\/li>\n  <li>he<\/li>\n  <li>head<\/li>\n  <li>health<\/li>\n  <li>hear<\/li>\n  <li>heart<\/li>\n  <li>heat<\/li>\n  <li>heavy<\/li>\n  <li>hell<\/li>\n  <li>help<\/li>\n  <li>here<\/li>\n  <li>high<\/li>\n  <li><span class='match'>history<\/span><\/li>\n  <li>hit<\/li>\n  <li>hold<\/li>\n  <li><span class='match'>holiday<\/span><\/li>\n  <li>home<\/li>\n  <li>honest<\/li>\n  <li>hope<\/li>\n  <li>horse<\/li>\n  <li><span class='match'>hospita<\/span>l<\/li>\n  <li>hot<\/li>\n  <li>hour<\/li>\n  <li>house<\/li>\n  <li>how<\/li>\n  <li><span class='match'>however<\/span><\/li>\n  <li>hullo<\/li>\n  <li><span class='match'>hundred<\/span><\/li>\n  <li><span class='match'>husband<\/span><\/li>\n  <li>idea<\/li>\n  <li><span class='match'>identif<\/span>y<\/li>\n  <li>if<\/li>\n  <li><span class='match'>imagine<\/span><\/li>\n  <li><span class='match'>importa<\/span>nt<\/li>\n  <li><span class='match'>improve<\/span><\/li>\n  <li>in<\/li>\n  <li><span class='match'>include<\/span><\/li>\n  <li>income<\/li>\n  <li><span class='match'>increas<\/span>e<\/li>\n  <li>indeed<\/li>\n  <li><span class='match'>individ<\/span>ual<\/li>\n  <li><span class='match'>industr<\/span>y<\/li>\n  <li>inform<\/li>\n  <li>inside<\/li>\n  <li><span class='match'>instead<\/span><\/li>\n  <li>insure<\/li>\n  <li><span class='match'>interes<\/span>t<\/li>\n  <li>into<\/li>\n  <li><span class='match'>introdu<\/span>ce<\/li>\n  <li>invest<\/li>\n  <li><span class='match'>involve<\/span><\/li>\n  <li>issue<\/li>\n  <li>it<\/li>\n  <li>item<\/li>\n  <li>jesus<\/li>\n  <li>job<\/li>\n  <li>join<\/li>\n  <li>judge<\/li>\n  <li>jump<\/li>\n  <li>just<\/li>\n  <li>keep<\/li>\n  <li>key<\/li>\n  <li>kid<\/li>\n  <li>kill<\/li>\n  <li>kind<\/li>\n  <li>king<\/li>\n  <li><span class='match'>kitchen<\/span><\/li>\n  <li>knock<\/li>\n  <li>know<\/li>\n  <li>labour<\/li>\n  <li>lad<\/li>\n  <li>lady<\/li>\n  <li>land<\/li>\n  <li><span class='match'>languag<\/span>e<\/li>\n  <li>large<\/li>\n  <li>last<\/li>\n  <li>late<\/li>\n  <li>laugh<\/li>\n  <li>law<\/li>\n  <li>lay<\/li>\n  <li>lead<\/li>\n  <li>learn<\/li>\n  <li>leave<\/li>\n  <li>left<\/li>\n  <li>leg<\/li>\n  <li>less<\/li>\n  <li>let<\/li>\n  <li>letter<\/li>\n  <li>level<\/li>\n  <li>lie<\/li>\n  <li>life<\/li>\n  <li>light<\/li>\n  <li>like<\/li>\n  <li>likely<\/li>\n  <li>limit<\/li>\n  <li>line<\/li>\n  <li>link<\/li>\n  <li>list<\/li>\n  <li>listen<\/li>\n  <li>little<\/li>\n  <li>live<\/li>\n  <li>load<\/li>\n  <li>local<\/li>\n  <li>lock<\/li>\n  <li>london<\/li>\n  <li>long<\/li>\n  <li>look<\/li>\n  <li>lord<\/li>\n  <li>lose<\/li>\n  <li>lot<\/li>\n  <li>love<\/li>\n  <li>low<\/li>\n  <li>luck<\/li>\n  <li>lunch<\/li>\n  <li><span class='match'>machine<\/span><\/li>\n  <li>main<\/li>\n  <li>major<\/li>\n  <li>make<\/li>\n  <li>man<\/li>\n  <li>manage<\/li>\n  <li>many<\/li>\n  <li>mark<\/li>\n  <li>market<\/li>\n  <li>marry<\/li>\n  <li>match<\/li>\n  <li>matter<\/li>\n  <li>may<\/li>\n  <li>maybe<\/li>\n  <li>mean<\/li>\n  <li><span class='match'>meaning<\/span><\/li>\n  <li><span class='match'>measure<\/span><\/li>\n  <li>meet<\/li>\n  <li>member<\/li>\n  <li><span class='match'>mention<\/span><\/li>\n  <li>middle<\/li>\n  <li>might<\/li>\n  <li>mile<\/li>\n  <li>milk<\/li>\n  <li><span class='match'>million<\/span><\/li>\n  <li>mind<\/li>\n  <li><span class='match'>ministe<\/span>r<\/li>\n  <li>minus<\/li>\n  <li>minute<\/li>\n  <li>miss<\/li>\n  <li>mister<\/li>\n  <li>moment<\/li>\n  <li>monday<\/li>\n  <li>money<\/li>\n  <li>month<\/li>\n  <li>more<\/li>\n  <li><span class='match'>morning<\/span><\/li>\n  <li>most<\/li>\n  <li>mother<\/li>\n  <li>motion<\/li>\n  <li>move<\/li>\n  <li>mrs<\/li>\n  <li>much<\/li>\n  <li>music<\/li>\n  <li>must<\/li>\n  <li>name<\/li>\n  <li>nation<\/li>\n  <li>nature<\/li>\n  <li>near<\/li>\n  <li><span class='match'>necessa<\/span>ry<\/li>\n  <li>need<\/li>\n  <li>never<\/li>\n  <li>new<\/li>\n  <li>news<\/li>\n  <li>next<\/li>\n  <li>nice<\/li>\n  <li>night<\/li>\n  <li>nine<\/li>\n  <li>no<\/li>\n  <li>non<\/li>\n  <li>none<\/li>\n  <li>normal<\/li>\n  <li>north<\/li>\n  <li>not<\/li>\n  <li>note<\/li>\n  <li>notice<\/li>\n  <li>now<\/li>\n  <li>number<\/li>\n  <li><span class='match'>obvious<\/span><\/li>\n  <li><span class='match'>occasio<\/span>n<\/li>\n  <li>odd<\/li>\n  <li>of<\/li>\n  <li>off<\/li>\n  <li>offer<\/li>\n  <li>office<\/li>\n  <li>often<\/li>\n  <li>okay<\/li>\n  <li>old<\/li>\n  <li>on<\/li>\n  <li>once<\/li>\n  <li>one<\/li>\n  <li>only<\/li>\n  <li>open<\/li>\n  <li><span class='match'>operate<\/span><\/li>\n  <li><span class='match'>opportu<\/span>nity<\/li>\n  <li>oppose<\/li>\n  <li>or<\/li>\n  <li>order<\/li>\n  <li><span class='match'>organiz<\/span>e<\/li>\n  <li><span class='match'>origina<\/span>l<\/li>\n  <li>other<\/li>\n  <li><span class='match'>otherwi<\/span>se<\/li>\n  <li>ought<\/li>\n  <li>out<\/li>\n  <li>over<\/li>\n  <li>own<\/li>\n  <li>pack<\/li>\n  <li>page<\/li>\n  <li>paint<\/li>\n  <li>pair<\/li>\n  <li>paper<\/li>\n  <li><span class='match'>paragra<\/span>ph<\/li>\n  <li>pardon<\/li>\n  <li>parent<\/li>\n  <li>park<\/li>\n  <li>part<\/li>\n  <li><span class='match'>particu<\/span>lar<\/li>\n  <li>party<\/li>\n  <li>pass<\/li>\n  <li>past<\/li>\n  <li>pay<\/li>\n  <li>pence<\/li>\n  <li><span class='match'>pension<\/span><\/li>\n  <li>people<\/li>\n  <li>per<\/li>\n  <li><span class='match'>percent<\/span><\/li>\n  <li><span class='match'>perfect<\/span><\/li>\n  <li><span class='match'>perhaps<\/span><\/li>\n  <li>period<\/li>\n  <li>person<\/li>\n  <li><span class='match'>photogr<\/span>aph<\/li>\n  <li>pick<\/li>\n  <li><span class='match'>picture<\/span><\/li>\n  <li>piece<\/li>\n  <li>place<\/li>\n  <li>plan<\/li>\n  <li>play<\/li>\n  <li>please<\/li>\n  <li>plus<\/li>\n  <li>point<\/li>\n  <li>police<\/li>\n  <li>policy<\/li>\n  <li><span class='match'>politic<\/span><\/li>\n  <li>poor<\/li>\n  <li><span class='match'>positio<\/span>n<\/li>\n  <li><span class='match'>positiv<\/span>e<\/li>\n  <li><span class='match'>possibl<\/span>e<\/li>\n  <li>post<\/li>\n  <li>pound<\/li>\n  <li>power<\/li>\n  <li><span class='match'>practis<\/span>e<\/li>\n  <li><span class='match'>prepare<\/span><\/li>\n  <li><span class='match'>present<\/span><\/li>\n  <li>press<\/li>\n  <li><span class='match'>pressur<\/span>e<\/li>\n  <li><span class='match'>presume<\/span><\/li>\n  <li>pretty<\/li>\n  <li><span class='match'>previou<\/span>s<\/li>\n  <li>price<\/li>\n  <li>print<\/li>\n  <li><span class='match'>private<\/span><\/li>\n  <li><span class='match'>probabl<\/span>e<\/li>\n  <li><span class='match'>problem<\/span><\/li>\n  <li><span class='match'>proceed<\/span><\/li>\n  <li><span class='match'>process<\/span><\/li>\n  <li><span class='match'>produce<\/span><\/li>\n  <li><span class='match'>product<\/span><\/li>\n  <li><span class='match'>program<\/span>me<\/li>\n  <li><span class='match'>project<\/span><\/li>\n  <li>proper<\/li>\n  <li><span class='match'>propose<\/span><\/li>\n  <li><span class='match'>protect<\/span><\/li>\n  <li><span class='match'>provide<\/span><\/li>\n  <li>public<\/li>\n  <li>pull<\/li>\n  <li><span class='match'>purpose<\/span><\/li>\n  <li>push<\/li>\n  <li>put<\/li>\n  <li><span class='match'>quality<\/span><\/li>\n  <li><span class='match'>quarter<\/span><\/li>\n  <li><span class='match'>questio<\/span>n<\/li>\n  <li>quick<\/li>\n  <li>quid<\/li>\n  <li>quiet<\/li>\n  <li>quite<\/li>\n  <li>radio<\/li>\n  <li>rail<\/li>\n  <li>raise<\/li>\n  <li>range<\/li>\n  <li>rate<\/li>\n  <li>rather<\/li>\n  <li>read<\/li>\n  <li>ready<\/li>\n  <li>real<\/li>\n  <li><span class='match'>realise<\/span><\/li>\n  <li>really<\/li>\n  <li>reason<\/li>\n  <li><span class='match'>receive<\/span><\/li>\n  <li>recent<\/li>\n  <li>reckon<\/li>\n  <li><span class='match'>recogni<\/span>ze<\/li>\n  <li><span class='match'>recomme<\/span>nd<\/li>\n  <li>record<\/li>\n  <li>red<\/li>\n  <li>reduce<\/li>\n  <li>refer<\/li>\n  <li>regard<\/li>\n  <li>region<\/li>\n  <li><span class='match'>relatio<\/span>n<\/li>\n  <li><span class='match'>remembe<\/span>r<\/li>\n  <li>report<\/li>\n  <li><span class='match'>represe<\/span>nt<\/li>\n  <li><span class='match'>require<\/span><\/li>\n  <li><span class='match'>researc<\/span>h<\/li>\n  <li><span class='match'>resourc<\/span>e<\/li>\n  <li><span class='match'>respect<\/span><\/li>\n  <li><span class='match'>respons<\/span>ible<\/li>\n  <li>rest<\/li>\n  <li>result<\/li>\n  <li>return<\/li>\n  <li>rid<\/li>\n  <li>right<\/li>\n  <li>ring<\/li>\n  <li>rise<\/li>\n  <li>road<\/li>\n  <li>role<\/li>\n  <li>roll<\/li>\n  <li>room<\/li>\n  <li>round<\/li>\n  <li>rule<\/li>\n  <li>run<\/li>\n  <li>safe<\/li>\n  <li>sale<\/li>\n  <li>same<\/li>\n  <li><span class='match'>saturda<\/span>y<\/li>\n  <li>save<\/li>\n  <li>say<\/li>\n  <li>scheme<\/li>\n  <li>school<\/li>\n  <li><span class='match'>science<\/span><\/li>\n  <li>score<\/li>\n  <li><span class='match'>scotlan<\/span>d<\/li>\n  <li>seat<\/li>\n  <li>second<\/li>\n  <li><span class='match'>secreta<\/span>ry<\/li>\n  <li><span class='match'>section<\/span><\/li>\n  <li>secure<\/li>\n  <li>see<\/li>\n  <li>seem<\/li>\n  <li>self<\/li>\n  <li>sell<\/li>\n  <li>send<\/li>\n  <li>sense<\/li>\n  <li><span class='match'>separat<\/span>e<\/li>\n  <li><span class='match'>serious<\/span><\/li>\n  <li>serve<\/li>\n  <li><span class='match'>service<\/span><\/li>\n  <li>set<\/li>\n  <li>settle<\/li>\n  <li>seven<\/li>\n  <li>sex<\/li>\n  <li>shall<\/li>\n  <li>share<\/li>\n  <li>she<\/li>\n  <li>sheet<\/li>\n  <li>shoe<\/li>\n  <li>shoot<\/li>\n  <li>shop<\/li>\n  <li>short<\/li>\n  <li>should<\/li>\n  <li>show<\/li>\n  <li>shut<\/li>\n  <li>sick<\/li>\n  <li>side<\/li>\n  <li>sign<\/li>\n  <li><span class='match'>similar<\/span><\/li>\n  <li>simple<\/li>\n  <li>since<\/li>\n  <li>sing<\/li>\n  <li>single<\/li>\n  <li>sir<\/li>\n  <li>sister<\/li>\n  <li>sit<\/li>\n  <li>site<\/li>\n  <li><span class='match'>situate<\/span><\/li>\n  <li>six<\/li>\n  <li>size<\/li>\n  <li>sleep<\/li>\n  <li>slight<\/li>\n  <li>slow<\/li>\n  <li>small<\/li>\n  <li>smoke<\/li>\n  <li>so<\/li>\n  <li>social<\/li>\n  <li><span class='match'>society<\/span><\/li>\n  <li>some<\/li>\n  <li>son<\/li>\n  <li>soon<\/li>\n  <li>sorry<\/li>\n  <li>sort<\/li>\n  <li>sound<\/li>\n  <li>south<\/li>\n  <li>space<\/li>\n  <li>speak<\/li>\n  <li><span class='match'>special<\/span><\/li>\n  <li><span class='match'>specifi<\/span>c<\/li>\n  <li>speed<\/li>\n  <li>spell<\/li>\n  <li>spend<\/li>\n  <li>square<\/li>\n  <li>staff<\/li>\n  <li>stage<\/li>\n  <li>stairs<\/li>\n  <li>stand<\/li>\n  <li><span class='match'>standar<\/span>d<\/li>\n  <li>start<\/li>\n  <li>state<\/li>\n  <li><span class='match'>station<\/span><\/li>\n  <li>stay<\/li>\n  <li>step<\/li>\n  <li>stick<\/li>\n  <li>still<\/li>\n  <li>stop<\/li>\n  <li>story<\/li>\n  <li><span class='match'>straigh<\/span>t<\/li>\n  <li><span class='match'>strateg<\/span>y<\/li>\n  <li>street<\/li>\n  <li>strike<\/li>\n  <li>strong<\/li>\n  <li><span class='match'>structu<\/span>re<\/li>\n  <li><span class='match'>student<\/span><\/li>\n  <li>study<\/li>\n  <li>stuff<\/li>\n  <li>stupid<\/li>\n  <li><span class='match'>subject<\/span><\/li>\n  <li><span class='match'>succeed<\/span><\/li>\n  <li>such<\/li>\n  <li>sudden<\/li>\n  <li><span class='match'>suggest<\/span><\/li>\n  <li>suit<\/li>\n  <li>summer<\/li>\n  <li>sun<\/li>\n  <li>sunday<\/li>\n  <li>supply<\/li>\n  <li><span class='match'>support<\/span><\/li>\n  <li><span class='match'>suppose<\/span><\/li>\n  <li>sure<\/li>\n  <li><span class='match'>surpris<\/span>e<\/li>\n  <li>switch<\/li>\n  <li>system<\/li>\n  <li>table<\/li>\n  <li>take<\/li>\n  <li>talk<\/li>\n  <li>tape<\/li>\n  <li>tax<\/li>\n  <li>tea<\/li>\n  <li>teach<\/li>\n  <li>team<\/li>\n  <li><span class='match'>telepho<\/span>ne<\/li>\n  <li><span class='match'>televis<\/span>ion<\/li>\n  <li>tell<\/li>\n  <li>ten<\/li>\n  <li>tend<\/li>\n  <li>term<\/li>\n  <li><span class='match'>terribl<\/span>e<\/li>\n  <li>test<\/li>\n  <li>than<\/li>\n  <li>thank<\/li>\n  <li>the<\/li>\n  <li>then<\/li>\n  <li>there<\/li>\n  <li><span class='match'>therefo<\/span>re<\/li>\n  <li>they<\/li>\n  <li>thing<\/li>\n  <li>think<\/li>\n  <li><span class='match'>thirtee<\/span>n<\/li>\n  <li>thirty<\/li>\n  <li>this<\/li>\n  <li>thou<\/li>\n  <li>though<\/li>\n  <li><span class='match'>thousan<\/span>d<\/li>\n  <li>three<\/li>\n  <li><span class='match'>through<\/span><\/li>\n  <li>throw<\/li>\n  <li><span class='match'>thursda<\/span>y<\/li>\n  <li>tie<\/li>\n  <li>time<\/li>\n  <li>to<\/li>\n  <li>today<\/li>\n  <li><span class='match'>togethe<\/span>r<\/li>\n  <li><span class='match'>tomorro<\/span>w<\/li>\n  <li><span class='match'>tonight<\/span><\/li>\n  <li>too<\/li>\n  <li>top<\/li>\n  <li>total<\/li>\n  <li>touch<\/li>\n  <li>toward<\/li>\n  <li>town<\/li>\n  <li>trade<\/li>\n  <li><span class='match'>traffic<\/span><\/li>\n  <li>train<\/li>\n  <li><span class='match'>transpo<\/span>rt<\/li>\n  <li>travel<\/li>\n  <li>treat<\/li>\n  <li>tree<\/li>\n  <li><span class='match'>trouble<\/span><\/li>\n  <li>true<\/li>\n  <li>trust<\/li>\n  <li>try<\/li>\n  <li><span class='match'>tuesday<\/span><\/li>\n  <li>turn<\/li>\n  <li>twelve<\/li>\n  <li>twenty<\/li>\n  <li>two<\/li>\n  <li>type<\/li>\n  <li>under<\/li>\n  <li><span class='match'>underst<\/span>and<\/li>\n  <li>union<\/li>\n  <li>unit<\/li>\n  <li>unite<\/li>\n  <li><span class='match'>univers<\/span>ity<\/li>\n  <li>unless<\/li>\n  <li>until<\/li>\n  <li>up<\/li>\n  <li>upon<\/li>\n  <li>use<\/li>\n  <li>usual<\/li>\n  <li>value<\/li>\n  <li><span class='match'>various<\/span><\/li>\n  <li>very<\/li>\n  <li>video<\/li>\n  <li>view<\/li>\n  <li><span class='match'>village<\/span><\/li>\n  <li>visit<\/li>\n  <li>vote<\/li>\n  <li>wage<\/li>\n  <li>wait<\/li>\n  <li>walk<\/li>\n  <li>wall<\/li>\n  <li>want<\/li>\n  <li>war<\/li>\n  <li>warm<\/li>\n  <li>wash<\/li>\n  <li>waste<\/li>\n  <li>watch<\/li>\n  <li>water<\/li>\n  <li>way<\/li>\n  <li>we<\/li>\n  <li>wear<\/li>\n  <li><span class='match'>wednesd<\/span>ay<\/li>\n  <li>wee<\/li>\n  <li>week<\/li>\n  <li>weigh<\/li>\n  <li><span class='match'>welcome<\/span><\/li>\n  <li>well<\/li>\n  <li>west<\/li>\n  <li>what<\/li>\n  <li>when<\/li>\n  <li>where<\/li>\n  <li><span class='match'>whether<\/span><\/li>\n  <li>which<\/li>\n  <li>while<\/li>\n  <li>white<\/li>\n  <li>who<\/li>\n  <li>whole<\/li>\n  <li>why<\/li>\n  <li>wide<\/li>\n  <li>wife<\/li>\n  <li>will<\/li>\n  <li>win<\/li>\n  <li>wind<\/li>\n  <li>window<\/li>\n  <li>wish<\/li>\n  <li>with<\/li>\n  <li>within<\/li>\n  <li><span class='match'>without<\/span><\/li>\n  <li>woman<\/li>\n  <li>wonder<\/li>\n  <li>wood<\/li>\n  <li>word<\/li>\n  <li>work<\/li>\n  <li>world<\/li>\n  <li>worry<\/li>\n  <li>worse<\/li>\n  <li>worth<\/li>\n  <li>would<\/li>\n  <li>write<\/li>\n  <li>wrong<\/li>\n  <li>year<\/li>\n  <li>yes<\/li>\n  <li><span class='match'>yesterd<\/span>ay<\/li>\n  <li>yet<\/li>\n  <li>you<\/li>\n  <li>young<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view(words, "^[aeiou]")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-1d2a99f97d7b1db1d6af">{"x":{"html":"<ul>\n  <li><span class='match'>a<\/span><\/li>\n  <li><span class='match'>a<\/span>ble<\/li>\n  <li><span class='match'>a<\/span>bout<\/li>\n  <li><span class='match'>a<\/span>bsolute<\/li>\n  <li><span class='match'>a<\/span>ccept<\/li>\n  <li><span class='match'>a<\/span>ccount<\/li>\n  <li><span class='match'>a<\/span>chieve<\/li>\n  <li><span class='match'>a<\/span>cross<\/li>\n  <li><span class='match'>a<\/span>ct<\/li>\n  <li><span class='match'>a<\/span>ctive<\/li>\n  <li><span class='match'>a<\/span>ctual<\/li>\n  <li><span class='match'>a<\/span>dd<\/li>\n  <li><span class='match'>a<\/span>ddress<\/li>\n  <li><span class='match'>a<\/span>dmit<\/li>\n  <li><span class='match'>a<\/span>dvertise<\/li>\n  <li><span class='match'>a<\/span>ffect<\/li>\n  <li><span class='match'>a<\/span>fford<\/li>\n  <li><span class='match'>a<\/span>fter<\/li>\n  <li><span class='match'>a<\/span>fternoon<\/li>\n  <li><span class='match'>a<\/span>gain<\/li>\n  <li><span class='match'>a<\/span>gainst<\/li>\n  <li><span class='match'>a<\/span>ge<\/li>\n  <li><span class='match'>a<\/span>gent<\/li>\n  <li><span class='match'>a<\/span>go<\/li>\n  <li><span class='match'>a<\/span>gree<\/li>\n  <li><span class='match'>a<\/span>ir<\/li>\n  <li><span class='match'>a<\/span>ll<\/li>\n  <li><span class='match'>a<\/span>llow<\/li>\n  <li><span class='match'>a<\/span>lmost<\/li>\n  <li><span class='match'>a<\/span>long<\/li>\n  <li><span class='match'>a<\/span>lready<\/li>\n  <li><span class='match'>a<\/span>lright<\/li>\n  <li><span class='match'>a<\/span>lso<\/li>\n  <li><span class='match'>a<\/span>lthough<\/li>\n  <li><span class='match'>a<\/span>lways<\/li>\n  <li><span class='match'>a<\/span>merica<\/li>\n  <li><span class='match'>a<\/span>mount<\/li>\n  <li><span class='match'>a<\/span>nd<\/li>\n  <li><span class='match'>a<\/span>nother<\/li>\n  <li><span class='match'>a<\/span>nswer<\/li>\n  <li><span class='match'>a<\/span>ny<\/li>\n  <li><span class='match'>a<\/span>part<\/li>\n  <li><span class='match'>a<\/span>pparent<\/li>\n  <li><span class='match'>a<\/span>ppear<\/li>\n  <li><span class='match'>a<\/span>pply<\/li>\n  <li><span class='match'>a<\/span>ppoint<\/li>\n  <li><span class='match'>a<\/span>pproach<\/li>\n  <li><span class='match'>a<\/span>ppropriate<\/li>\n  <li><span class='match'>a<\/span>rea<\/li>\n  <li><span class='match'>a<\/span>rgue<\/li>\n  <li><span class='match'>a<\/span>rm<\/li>\n  <li><span class='match'>a<\/span>round<\/li>\n  <li><span class='match'>a<\/span>rrange<\/li>\n  <li><span class='match'>a<\/span>rt<\/li>\n  <li><span class='match'>a<\/span>s<\/li>\n  <li><span class='match'>a<\/span>sk<\/li>\n  <li><span class='match'>a<\/span>ssociate<\/li>\n  <li><span class='match'>a<\/span>ssume<\/li>\n  <li><span class='match'>a<\/span>t<\/li>\n  <li><span class='match'>a<\/span>ttend<\/li>\n  <li><span class='match'>a<\/span>uthority<\/li>\n  <li><span class='match'>a<\/span>vailable<\/li>\n  <li><span class='match'>a<\/span>ware<\/li>\n  <li><span class='match'>a<\/span>way<\/li>\n  <li><span class='match'>a<\/span>wful<\/li>\n  <li>baby<\/li>\n  <li>back<\/li>\n  <li>bad<\/li>\n  <li>bag<\/li>\n  <li>balance<\/li>\n  <li>ball<\/li>\n  <li>bank<\/li>\n  <li>bar<\/li>\n  <li>base<\/li>\n  <li>basis<\/li>\n  <li>be<\/li>\n  <li>bear<\/li>\n  <li>beat<\/li>\n  <li>beauty<\/li>\n  <li>because<\/li>\n  <li>become<\/li>\n  <li>bed<\/li>\n  <li>before<\/li>\n  <li>begin<\/li>\n  <li>behind<\/li>\n  <li>believe<\/li>\n  <li>benefit<\/li>\n  <li>best<\/li>\n  <li>bet<\/li>\n  <li>between<\/li>\n  <li>big<\/li>\n  <li>bill<\/li>\n  <li>birth<\/li>\n  <li>bit<\/li>\n  <li>black<\/li>\n  <li>bloke<\/li>\n  <li>blood<\/li>\n  <li>blow<\/li>\n  <li>blue<\/li>\n  <li>board<\/li>\n  <li>boat<\/li>\n  <li>body<\/li>\n  <li>book<\/li>\n  <li>both<\/li>\n  <li>bother<\/li>\n  <li>bottle<\/li>\n  <li>bottom<\/li>\n  <li>box<\/li>\n  <li>boy<\/li>\n  <li>break<\/li>\n  <li>brief<\/li>\n  <li>brilliant<\/li>\n  <li>bring<\/li>\n  <li>britain<\/li>\n  <li>brother<\/li>\n  <li>budget<\/li>\n  <li>build<\/li>\n  <li>bus<\/li>\n  <li>business<\/li>\n  <li>busy<\/li>\n  <li>but<\/li>\n  <li>buy<\/li>\n  <li>by<\/li>\n  <li>cake<\/li>\n  <li>call<\/li>\n  <li>can<\/li>\n  <li>car<\/li>\n  <li>card<\/li>\n  <li>care<\/li>\n  <li>carry<\/li>\n  <li>case<\/li>\n  <li>cat<\/li>\n  <li>catch<\/li>\n  <li>cause<\/li>\n  <li>cent<\/li>\n  <li>centre<\/li>\n  <li>certain<\/li>\n  <li>chair<\/li>\n  <li>chairman<\/li>\n  <li>chance<\/li>\n  <li>change<\/li>\n  <li>chap<\/li>\n  <li>character<\/li>\n  <li>charge<\/li>\n  <li>cheap<\/li>\n  <li>check<\/li>\n  <li>child<\/li>\n  <li>choice<\/li>\n  <li>choose<\/li>\n  <li>Christ<\/li>\n  <li>Christmas<\/li>\n  <li>church<\/li>\n  <li>city<\/li>\n  <li>claim<\/li>\n  <li>class<\/li>\n  <li>clean<\/li>\n  <li>clear<\/li>\n  <li>client<\/li>\n  <li>clock<\/li>\n  <li>close<\/li>\n  <li>closes<\/li>\n  <li>clothe<\/li>\n  <li>club<\/li>\n  <li>coffee<\/li>\n  <li>cold<\/li>\n  <li>colleague<\/li>\n  <li>collect<\/li>\n  <li>college<\/li>\n  <li>colour<\/li>\n  <li>come<\/li>\n  <li>comment<\/li>\n  <li>commit<\/li>\n  <li>committee<\/li>\n  <li>common<\/li>\n  <li>community<\/li>\n  <li>company<\/li>\n  <li>compare<\/li>\n  <li>complete<\/li>\n  <li>compute<\/li>\n  <li>concern<\/li>\n  <li>condition<\/li>\n  <li>confer<\/li>\n  <li>consider<\/li>\n  <li>consult<\/li>\n  <li>contact<\/li>\n  <li>continue<\/li>\n  <li>contract<\/li>\n  <li>control<\/li>\n  <li>converse<\/li>\n  <li>cook<\/li>\n  <li>copy<\/li>\n  <li>corner<\/li>\n  <li>correct<\/li>\n  <li>cost<\/li>\n  <li>could<\/li>\n  <li>council<\/li>\n  <li>count<\/li>\n  <li>country<\/li>\n  <li>county<\/li>\n  <li>couple<\/li>\n  <li>course<\/li>\n  <li>court<\/li>\n  <li>cover<\/li>\n  <li>create<\/li>\n  <li>cross<\/li>\n  <li>cup<\/li>\n  <li>current<\/li>\n  <li>cut<\/li>\n  <li>dad<\/li>\n  <li>danger<\/li>\n  <li>date<\/li>\n  <li>day<\/li>\n  <li>dead<\/li>\n  <li>deal<\/li>\n  <li>dear<\/li>\n  <li>debate<\/li>\n  <li>decide<\/li>\n  <li>decision<\/li>\n  <li>deep<\/li>\n  <li>definite<\/li>\n  <li>degree<\/li>\n  <li>department<\/li>\n  <li>depend<\/li>\n  <li>describe<\/li>\n  <li>design<\/li>\n  <li>detail<\/li>\n  <li>develop<\/li>\n  <li>die<\/li>\n  <li>difference<\/li>\n  <li>difficult<\/li>\n  <li>dinner<\/li>\n  <li>direct<\/li>\n  <li>discuss<\/li>\n  <li>district<\/li>\n  <li>divide<\/li>\n  <li>do<\/li>\n  <li>doctor<\/li>\n  <li>document<\/li>\n  <li>dog<\/li>\n  <li>door<\/li>\n  <li>double<\/li>\n  <li>doubt<\/li>\n  <li>down<\/li>\n  <li>draw<\/li>\n  <li>dress<\/li>\n  <li>drink<\/li>\n  <li>drive<\/li>\n  <li>drop<\/li>\n  <li>dry<\/li>\n  <li>due<\/li>\n  <li>during<\/li>\n  <li><span class='match'>e<\/span>ach<\/li>\n  <li><span class='match'>e<\/span>arly<\/li>\n  <li><span class='match'>e<\/span>ast<\/li>\n  <li><span class='match'>e<\/span>asy<\/li>\n  <li><span class='match'>e<\/span>at<\/li>\n  <li><span class='match'>e<\/span>conomy<\/li>\n  <li><span class='match'>e<\/span>ducate<\/li>\n  <li><span class='match'>e<\/span>ffect<\/li>\n  <li><span class='match'>e<\/span>gg<\/li>\n  <li><span class='match'>e<\/span>ight<\/li>\n  <li><span class='match'>e<\/span>ither<\/li>\n  <li><span class='match'>e<\/span>lect<\/li>\n  <li><span class='match'>e<\/span>lectric<\/li>\n  <li><span class='match'>e<\/span>leven<\/li>\n  <li><span class='match'>e<\/span>lse<\/li>\n  <li><span class='match'>e<\/span>mploy<\/li>\n  <li><span class='match'>e<\/span>ncourage<\/li>\n  <li><span class='match'>e<\/span>nd<\/li>\n  <li><span class='match'>e<\/span>ngine<\/li>\n  <li><span class='match'>e<\/span>nglish<\/li>\n  <li><span class='match'>e<\/span>njoy<\/li>\n  <li><span class='match'>e<\/span>nough<\/li>\n  <li><span class='match'>e<\/span>nter<\/li>\n  <li><span class='match'>e<\/span>nvironment<\/li>\n  <li><span class='match'>e<\/span>qual<\/li>\n  <li><span class='match'>e<\/span>special<\/li>\n  <li><span class='match'>e<\/span>urope<\/li>\n  <li><span class='match'>e<\/span>ven<\/li>\n  <li><span class='match'>e<\/span>vening<\/li>\n  <li><span class='match'>e<\/span>ver<\/li>\n  <li><span class='match'>e<\/span>very<\/li>\n  <li><span class='match'>e<\/span>vidence<\/li>\n  <li><span class='match'>e<\/span>xact<\/li>\n  <li><span class='match'>e<\/span>xample<\/li>\n  <li><span class='match'>e<\/span>xcept<\/li>\n  <li><span class='match'>e<\/span>xcuse<\/li>\n  <li><span class='match'>e<\/span>xercise<\/li>\n  <li><span class='match'>e<\/span>xist<\/li>\n  <li><span class='match'>e<\/span>xpect<\/li>\n  <li><span class='match'>e<\/span>xpense<\/li>\n  <li><span class='match'>e<\/span>xperience<\/li>\n  <li><span class='match'>e<\/span>xplain<\/li>\n  <li><span class='match'>e<\/span>xpress<\/li>\n  <li><span class='match'>e<\/span>xtra<\/li>\n  <li><span class='match'>e<\/span>ye<\/li>\n  <li>face<\/li>\n  <li>fact<\/li>\n  <li>fair<\/li>\n  <li>fall<\/li>\n  <li>family<\/li>\n  <li>far<\/li>\n  <li>farm<\/li>\n  <li>fast<\/li>\n  <li>father<\/li>\n  <li>favour<\/li>\n  <li>feed<\/li>\n  <li>feel<\/li>\n  <li>few<\/li>\n  <li>field<\/li>\n  <li>fight<\/li>\n  <li>figure<\/li>\n  <li>file<\/li>\n  <li>fill<\/li>\n  <li>film<\/li>\n  <li>final<\/li>\n  <li>finance<\/li>\n  <li>find<\/li>\n  <li>fine<\/li>\n  <li>finish<\/li>\n  <li>fire<\/li>\n  <li>first<\/li>\n  <li>fish<\/li>\n  <li>fit<\/li>\n  <li>five<\/li>\n  <li>flat<\/li>\n  <li>floor<\/li>\n  <li>fly<\/li>\n  <li>follow<\/li>\n  <li>food<\/li>\n  <li>foot<\/li>\n  <li>for<\/li>\n  <li>force<\/li>\n  <li>forget<\/li>\n  <li>form<\/li>\n  <li>fortune<\/li>\n  <li>forward<\/li>\n  <li>four<\/li>\n  <li>france<\/li>\n  <li>free<\/li>\n  <li>friday<\/li>\n  <li>friend<\/li>\n  <li>from<\/li>\n  <li>front<\/li>\n  <li>full<\/li>\n  <li>fun<\/li>\n  <li>function<\/li>\n  <li>fund<\/li>\n  <li>further<\/li>\n  <li>future<\/li>\n  <li>game<\/li>\n  <li>garden<\/li>\n  <li>gas<\/li>\n  <li>general<\/li>\n  <li>germany<\/li>\n  <li>get<\/li>\n  <li>girl<\/li>\n  <li>give<\/li>\n  <li>glass<\/li>\n  <li>go<\/li>\n  <li>god<\/li>\n  <li>good<\/li>\n  <li>goodbye<\/li>\n  <li>govern<\/li>\n  <li>grand<\/li>\n  <li>grant<\/li>\n  <li>great<\/li>\n  <li>green<\/li>\n  <li>ground<\/li>\n  <li>group<\/li>\n  <li>grow<\/li>\n  <li>guess<\/li>\n  <li>guy<\/li>\n  <li>hair<\/li>\n  <li>half<\/li>\n  <li>hall<\/li>\n  <li>hand<\/li>\n  <li>hang<\/li>\n  <li>happen<\/li>\n  <li>happy<\/li>\n  <li>hard<\/li>\n  <li>hate<\/li>\n  <li>have<\/li>\n  <li>he<\/li>\n  <li>head<\/li>\n  <li>health<\/li>\n  <li>hear<\/li>\n  <li>heart<\/li>\n  <li>heat<\/li>\n  <li>heavy<\/li>\n  <li>hell<\/li>\n  <li>help<\/li>\n  <li>here<\/li>\n  <li>high<\/li>\n  <li>history<\/li>\n  <li>hit<\/li>\n  <li>hold<\/li>\n  <li>holiday<\/li>\n  <li>home<\/li>\n  <li>honest<\/li>\n  <li>hope<\/li>\n  <li>horse<\/li>\n  <li>hospital<\/li>\n  <li>hot<\/li>\n  <li>hour<\/li>\n  <li>house<\/li>\n  <li>how<\/li>\n  <li>however<\/li>\n  <li>hullo<\/li>\n  <li>hundred<\/li>\n  <li>husband<\/li>\n  <li><span class='match'>i<\/span>dea<\/li>\n  <li><span class='match'>i<\/span>dentify<\/li>\n  <li><span class='match'>i<\/span>f<\/li>\n  <li><span class='match'>i<\/span>magine<\/li>\n  <li><span class='match'>i<\/span>mportant<\/li>\n  <li><span class='match'>i<\/span>mprove<\/li>\n  <li><span class='match'>i<\/span>n<\/li>\n  <li><span class='match'>i<\/span>nclude<\/li>\n  <li><span class='match'>i<\/span>ncome<\/li>\n  <li><span class='match'>i<\/span>ncrease<\/li>\n  <li><span class='match'>i<\/span>ndeed<\/li>\n  <li><span class='match'>i<\/span>ndividual<\/li>\n  <li><span class='match'>i<\/span>ndustry<\/li>\n  <li><span class='match'>i<\/span>nform<\/li>\n  <li><span class='match'>i<\/span>nside<\/li>\n  <li><span class='match'>i<\/span>nstead<\/li>\n  <li><span class='match'>i<\/span>nsure<\/li>\n  <li><span class='match'>i<\/span>nterest<\/li>\n  <li><span class='match'>i<\/span>nto<\/li>\n  <li><span class='match'>i<\/span>ntroduce<\/li>\n  <li><span class='match'>i<\/span>nvest<\/li>\n  <li><span class='match'>i<\/span>nvolve<\/li>\n  <li><span class='match'>i<\/span>ssue<\/li>\n  <li><span class='match'>i<\/span>t<\/li>\n  <li><span class='match'>i<\/span>tem<\/li>\n  <li>jesus<\/li>\n  <li>job<\/li>\n  <li>join<\/li>\n  <li>judge<\/li>\n  <li>jump<\/li>\n  <li>just<\/li>\n  <li>keep<\/li>\n  <li>key<\/li>\n  <li>kid<\/li>\n  <li>kill<\/li>\n  <li>kind<\/li>\n  <li>king<\/li>\n  <li>kitchen<\/li>\n  <li>knock<\/li>\n  <li>know<\/li>\n  <li>labour<\/li>\n  <li>lad<\/li>\n  <li>lady<\/li>\n  <li>land<\/li>\n  <li>language<\/li>\n  <li>large<\/li>\n  <li>last<\/li>\n  <li>late<\/li>\n  <li>laugh<\/li>\n  <li>law<\/li>\n  <li>lay<\/li>\n  <li>lead<\/li>\n  <li>learn<\/li>\n  <li>leave<\/li>\n  <li>left<\/li>\n  <li>leg<\/li>\n  <li>less<\/li>\n  <li>let<\/li>\n  <li>letter<\/li>\n  <li>level<\/li>\n  <li>lie<\/li>\n  <li>life<\/li>\n  <li>light<\/li>\n  <li>like<\/li>\n  <li>likely<\/li>\n  <li>limit<\/li>\n  <li>line<\/li>\n  <li>link<\/li>\n  <li>list<\/li>\n  <li>listen<\/li>\n  <li>little<\/li>\n  <li>live<\/li>\n  <li>load<\/li>\n  <li>local<\/li>\n  <li>lock<\/li>\n  <li>london<\/li>\n  <li>long<\/li>\n  <li>look<\/li>\n  <li>lord<\/li>\n  <li>lose<\/li>\n  <li>lot<\/li>\n  <li>love<\/li>\n  <li>low<\/li>\n  <li>luck<\/li>\n  <li>lunch<\/li>\n  <li>machine<\/li>\n  <li>main<\/li>\n  <li>major<\/li>\n  <li>make<\/li>\n  <li>man<\/li>\n  <li>manage<\/li>\n  <li>many<\/li>\n  <li>mark<\/li>\n  <li>market<\/li>\n  <li>marry<\/li>\n  <li>match<\/li>\n  <li>matter<\/li>\n  <li>may<\/li>\n  <li>maybe<\/li>\n  <li>mean<\/li>\n  <li>meaning<\/li>\n  <li>measure<\/li>\n  <li>meet<\/li>\n  <li>member<\/li>\n  <li>mention<\/li>\n  <li>middle<\/li>\n  <li>might<\/li>\n  <li>mile<\/li>\n  <li>milk<\/li>\n  <li>million<\/li>\n  <li>mind<\/li>\n  <li>minister<\/li>\n  <li>minus<\/li>\n  <li>minute<\/li>\n  <li>miss<\/li>\n  <li>mister<\/li>\n  <li>moment<\/li>\n  <li>monday<\/li>\n  <li>money<\/li>\n  <li>month<\/li>\n  <li>more<\/li>\n  <li>morning<\/li>\n  <li>most<\/li>\n  <li>mother<\/li>\n  <li>motion<\/li>\n  <li>move<\/li>\n  <li>mrs<\/li>\n  <li>much<\/li>\n  <li>music<\/li>\n  <li>must<\/li>\n  <li>name<\/li>\n  <li>nation<\/li>\n  <li>nature<\/li>\n  <li>near<\/li>\n  <li>necessary<\/li>\n  <li>need<\/li>\n  <li>never<\/li>\n  <li>new<\/li>\n  <li>news<\/li>\n  <li>next<\/li>\n  <li>nice<\/li>\n  <li>night<\/li>\n  <li>nine<\/li>\n  <li>no<\/li>\n  <li>non<\/li>\n  <li>none<\/li>\n  <li>normal<\/li>\n  <li>north<\/li>\n  <li>not<\/li>\n  <li>note<\/li>\n  <li>notice<\/li>\n  <li>now<\/li>\n  <li>number<\/li>\n  <li><span class='match'>o<\/span>bvious<\/li>\n  <li><span class='match'>o<\/span>ccasion<\/li>\n  <li><span class='match'>o<\/span>dd<\/li>\n  <li><span class='match'>o<\/span>f<\/li>\n  <li><span class='match'>o<\/span>ff<\/li>\n  <li><span class='match'>o<\/span>ffer<\/li>\n  <li><span class='match'>o<\/span>ffice<\/li>\n  <li><span class='match'>o<\/span>ften<\/li>\n  <li><span class='match'>o<\/span>kay<\/li>\n  <li><span class='match'>o<\/span>ld<\/li>\n  <li><span class='match'>o<\/span>n<\/li>\n  <li><span class='match'>o<\/span>nce<\/li>\n  <li><span class='match'>o<\/span>ne<\/li>\n  <li><span class='match'>o<\/span>nly<\/li>\n  <li><span class='match'>o<\/span>pen<\/li>\n  <li><span class='match'>o<\/span>perate<\/li>\n  <li><span class='match'>o<\/span>pportunity<\/li>\n  <li><span class='match'>o<\/span>ppose<\/li>\n  <li><span class='match'>o<\/span>r<\/li>\n  <li><span class='match'>o<\/span>rder<\/li>\n  <li><span class='match'>o<\/span>rganize<\/li>\n  <li><span class='match'>o<\/span>riginal<\/li>\n  <li><span class='match'>o<\/span>ther<\/li>\n  <li><span class='match'>o<\/span>therwise<\/li>\n  <li><span class='match'>o<\/span>ught<\/li>\n  <li><span class='match'>o<\/span>ut<\/li>\n  <li><span class='match'>o<\/span>ver<\/li>\n  <li><span class='match'>o<\/span>wn<\/li>\n  <li>pack<\/li>\n  <li>page<\/li>\n  <li>paint<\/li>\n  <li>pair<\/li>\n  <li>paper<\/li>\n  <li>paragraph<\/li>\n  <li>pardon<\/li>\n  <li>parent<\/li>\n  <li>park<\/li>\n  <li>part<\/li>\n  <li>particular<\/li>\n  <li>party<\/li>\n  <li>pass<\/li>\n  <li>past<\/li>\n  <li>pay<\/li>\n  <li>pence<\/li>\n  <li>pension<\/li>\n  <li>people<\/li>\n  <li>per<\/li>\n  <li>percent<\/li>\n  <li>perfect<\/li>\n  <li>perhaps<\/li>\n  <li>period<\/li>\n  <li>person<\/li>\n  <li>photograph<\/li>\n  <li>pick<\/li>\n  <li>picture<\/li>\n  <li>piece<\/li>\n  <li>place<\/li>\n  <li>plan<\/li>\n  <li>play<\/li>\n  <li>please<\/li>\n  <li>plus<\/li>\n  <li>point<\/li>\n  <li>police<\/li>\n  <li>policy<\/li>\n  <li>politic<\/li>\n  <li>poor<\/li>\n  <li>position<\/li>\n  <li>positive<\/li>\n  <li>possible<\/li>\n  <li>post<\/li>\n  <li>pound<\/li>\n  <li>power<\/li>\n  <li>practise<\/li>\n  <li>prepare<\/li>\n  <li>present<\/li>\n  <li>press<\/li>\n  <li>pressure<\/li>\n  <li>presume<\/li>\n  <li>pretty<\/li>\n  <li>previous<\/li>\n  <li>price<\/li>\n  <li>print<\/li>\n  <li>private<\/li>\n  <li>probable<\/li>\n  <li>problem<\/li>\n  <li>proceed<\/li>\n  <li>process<\/li>\n  <li>produce<\/li>\n  <li>product<\/li>\n  <li>programme<\/li>\n  <li>project<\/li>\n  <li>proper<\/li>\n  <li>propose<\/li>\n  <li>protect<\/li>\n  <li>provide<\/li>\n  <li>public<\/li>\n  <li>pull<\/li>\n  <li>purpose<\/li>\n  <li>push<\/li>\n  <li>put<\/li>\n  <li>quality<\/li>\n  <li>quarter<\/li>\n  <li>question<\/li>\n  <li>quick<\/li>\n  <li>quid<\/li>\n  <li>quiet<\/li>\n  <li>quite<\/li>\n  <li>radio<\/li>\n  <li>rail<\/li>\n  <li>raise<\/li>\n  <li>range<\/li>\n  <li>rate<\/li>\n  <li>rather<\/li>\n  <li>read<\/li>\n  <li>ready<\/li>\n  <li>real<\/li>\n  <li>realise<\/li>\n  <li>really<\/li>\n  <li>reason<\/li>\n  <li>receive<\/li>\n  <li>recent<\/li>\n  <li>reckon<\/li>\n  <li>recognize<\/li>\n  <li>recommend<\/li>\n  <li>record<\/li>\n  <li>red<\/li>\n  <li>reduce<\/li>\n  <li>refer<\/li>\n  <li>regard<\/li>\n  <li>region<\/li>\n  <li>relation<\/li>\n  <li>remember<\/li>\n  <li>report<\/li>\n  <li>represent<\/li>\n  <li>require<\/li>\n  <li>research<\/li>\n  <li>resource<\/li>\n  <li>respect<\/li>\n  <li>responsible<\/li>\n  <li>rest<\/li>\n  <li>result<\/li>\n  <li>return<\/li>\n  <li>rid<\/li>\n  <li>right<\/li>\n  <li>ring<\/li>\n  <li>rise<\/li>\n  <li>road<\/li>\n  <li>role<\/li>\n  <li>roll<\/li>\n  <li>room<\/li>\n  <li>round<\/li>\n  <li>rule<\/li>\n  <li>run<\/li>\n  <li>safe<\/li>\n  <li>sale<\/li>\n  <li>same<\/li>\n  <li>saturday<\/li>\n  <li>save<\/li>\n  <li>say<\/li>\n  <li>scheme<\/li>\n  <li>school<\/li>\n  <li>science<\/li>\n  <li>score<\/li>\n  <li>scotland<\/li>\n  <li>seat<\/li>\n  <li>second<\/li>\n  <li>secretary<\/li>\n  <li>section<\/li>\n  <li>secure<\/li>\n  <li>see<\/li>\n  <li>seem<\/li>\n  <li>self<\/li>\n  <li>sell<\/li>\n  <li>send<\/li>\n  <li>sense<\/li>\n  <li>separate<\/li>\n  <li>serious<\/li>\n  <li>serve<\/li>\n  <li>service<\/li>\n  <li>set<\/li>\n  <li>settle<\/li>\n  <li>seven<\/li>\n  <li>sex<\/li>\n  <li>shall<\/li>\n  <li>share<\/li>\n  <li>she<\/li>\n  <li>sheet<\/li>\n  <li>shoe<\/li>\n  <li>shoot<\/li>\n  <li>shop<\/li>\n  <li>short<\/li>\n  <li>should<\/li>\n  <li>show<\/li>\n  <li>shut<\/li>\n  <li>sick<\/li>\n  <li>side<\/li>\n  <li>sign<\/li>\n  <li>similar<\/li>\n  <li>simple<\/li>\n  <li>since<\/li>\n  <li>sing<\/li>\n  <li>single<\/li>\n  <li>sir<\/li>\n  <li>sister<\/li>\n  <li>sit<\/li>\n  <li>site<\/li>\n  <li>situate<\/li>\n  <li>six<\/li>\n  <li>size<\/li>\n  <li>sleep<\/li>\n  <li>slight<\/li>\n  <li>slow<\/li>\n  <li>small<\/li>\n  <li>smoke<\/li>\n  <li>so<\/li>\n  <li>social<\/li>\n  <li>society<\/li>\n  <li>some<\/li>\n  <li>son<\/li>\n  <li>soon<\/li>\n  <li>sorry<\/li>\n  <li>sort<\/li>\n  <li>sound<\/li>\n  <li>south<\/li>\n  <li>space<\/li>\n  <li>speak<\/li>\n  <li>special<\/li>\n  <li>specific<\/li>\n  <li>speed<\/li>\n  <li>spell<\/li>\n  <li>spend<\/li>\n  <li>square<\/li>\n  <li>staff<\/li>\n  <li>stage<\/li>\n  <li>stairs<\/li>\n  <li>stand<\/li>\n  <li>standard<\/li>\n  <li>start<\/li>\n  <li>state<\/li>\n  <li>station<\/li>\n  <li>stay<\/li>\n  <li>step<\/li>\n  <li>stick<\/li>\n  <li>still<\/li>\n  <li>stop<\/li>\n  <li>story<\/li>\n  <li>straight<\/li>\n  <li>strategy<\/li>\n  <li>street<\/li>\n  <li>strike<\/li>\n  <li>strong<\/li>\n  <li>structure<\/li>\n  <li>student<\/li>\n  <li>study<\/li>\n  <li>stuff<\/li>\n  <li>stupid<\/li>\n  <li>subject<\/li>\n  <li>succeed<\/li>\n  <li>such<\/li>\n  <li>sudden<\/li>\n  <li>suggest<\/li>\n  <li>suit<\/li>\n  <li>summer<\/li>\n  <li>sun<\/li>\n  <li>sunday<\/li>\n  <li>supply<\/li>\n  <li>support<\/li>\n  <li>suppose<\/li>\n  <li>sure<\/li>\n  <li>surprise<\/li>\n  <li>switch<\/li>\n  <li>system<\/li>\n  <li>table<\/li>\n  <li>take<\/li>\n  <li>talk<\/li>\n  <li>tape<\/li>\n  <li>tax<\/li>\n  <li>tea<\/li>\n  <li>teach<\/li>\n  <li>team<\/li>\n  <li>telephone<\/li>\n  <li>television<\/li>\n  <li>tell<\/li>\n  <li>ten<\/li>\n  <li>tend<\/li>\n  <li>term<\/li>\n  <li>terrible<\/li>\n  <li>test<\/li>\n  <li>than<\/li>\n  <li>thank<\/li>\n  <li>the<\/li>\n  <li>then<\/li>\n  <li>there<\/li>\n  <li>therefore<\/li>\n  <li>they<\/li>\n  <li>thing<\/li>\n  <li>think<\/li>\n  <li>thirteen<\/li>\n  <li>thirty<\/li>\n  <li>this<\/li>\n  <li>thou<\/li>\n  <li>though<\/li>\n  <li>thousand<\/li>\n  <li>three<\/li>\n  <li>through<\/li>\n  <li>throw<\/li>\n  <li>thursday<\/li>\n  <li>tie<\/li>\n  <li>time<\/li>\n  <li>to<\/li>\n  <li>today<\/li>\n  <li>together<\/li>\n  <li>tomorrow<\/li>\n  <li>tonight<\/li>\n  <li>too<\/li>\n  <li>top<\/li>\n  <li>total<\/li>\n  <li>touch<\/li>\n  <li>toward<\/li>\n  <li>town<\/li>\n  <li>trade<\/li>\n  <li>traffic<\/li>\n  <li>train<\/li>\n  <li>transport<\/li>\n  <li>travel<\/li>\n  <li>treat<\/li>\n  <li>tree<\/li>\n  <li>trouble<\/li>\n  <li>true<\/li>\n  <li>trust<\/li>\n  <li>try<\/li>\n  <li>tuesday<\/li>\n  <li>turn<\/li>\n  <li>twelve<\/li>\n  <li>twenty<\/li>\n  <li>two<\/li>\n  <li>type<\/li>\n  <li><span class='match'>u<\/span>nder<\/li>\n  <li><span class='match'>u<\/span>nderstand<\/li>\n  <li><span class='match'>u<\/span>nion<\/li>\n  <li><span class='match'>u<\/span>nit<\/li>\n  <li><span class='match'>u<\/span>nite<\/li>\n  <li><span class='match'>u<\/span>niversity<\/li>\n  <li><span class='match'>u<\/span>nless<\/li>\n  <li><span class='match'>u<\/span>ntil<\/li>\n  <li><span class='match'>u<\/span>p<\/li>\n  <li><span class='match'>u<\/span>pon<\/li>\n  <li><span class='match'>u<\/span>se<\/li>\n  <li><span class='match'>u<\/span>sual<\/li>\n  <li>value<\/li>\n  <li>various<\/li>\n  <li>very<\/li>\n  <li>video<\/li>\n  <li>view<\/li>\n  <li>village<\/li>\n  <li>visit<\/li>\n  <li>vote<\/li>\n  <li>wage<\/li>\n  <li>wait<\/li>\n  <li>walk<\/li>\n  <li>wall<\/li>\n  <li>want<\/li>\n  <li>war<\/li>\n  <li>warm<\/li>\n  <li>wash<\/li>\n  <li>waste<\/li>\n  <li>watch<\/li>\n  <li>water<\/li>\n  <li>way<\/li>\n  <li>we<\/li>\n  <li>wear<\/li>\n  <li>wednesday<\/li>\n  <li>wee<\/li>\n  <li>week<\/li>\n  <li>weigh<\/li>\n  <li>welcome<\/li>\n  <li>well<\/li>\n  <li>west<\/li>\n  <li>what<\/li>\n  <li>when<\/li>\n  <li>where<\/li>\n  <li>whether<\/li>\n  <li>which<\/li>\n  <li>while<\/li>\n  <li>white<\/li>\n  <li>who<\/li>\n  <li>whole<\/li>\n  <li>why<\/li>\n  <li>wide<\/li>\n  <li>wife<\/li>\n  <li>will<\/li>\n  <li>win<\/li>\n  <li>wind<\/li>\n  <li>window<\/li>\n  <li>wish<\/li>\n  <li>with<\/li>\n  <li>within<\/li>\n  <li>without<\/li>\n  <li>woman<\/li>\n  <li>wonder<\/li>\n  <li>wood<\/li>\n  <li>word<\/li>\n  <li>work<\/li>\n  <li>world<\/li>\n  <li>worry<\/li>\n  <li>worse<\/li>\n  <li>worth<\/li>\n  <li>would<\/li>\n  <li>write<\/li>\n  <li>wrong<\/li>\n  <li>year<\/li>\n  <li>yes<\/li>\n  <li>yesterday<\/li>\n  <li>yet<\/li>\n  <li>you<\/li>\n  <li>young<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# only contain consonants
str_view(words, "^[^aeiou]+$", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-414d4eeb32dea36c2a6f">{"x":{"html":"<ul>\n  <li><span class='match'>by<\/span><\/li>\n  <li><span class='match'>dry<\/span><\/li>\n  <li><span class='match'>fly<\/span><\/li>\n  <li><span class='match'>mrs<\/span><\/li>\n  <li><span class='match'>try<\/span><\/li>\n  <li><span class='match'>why<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# ends with ed, but not eed
str_view(words, "[^e]ed$", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-e20fdc4a56d970a63235">{"x":{"html":"<ul>\n  <li><span class='match'>bed<\/span><\/li>\n  <li>hund<span class='match'>red<\/span><\/li>\n  <li><span class='match'>red<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# ends with ing or ise
str_view(words, "ing$|ise$", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-8ecf4f4c063f0e94f34a">{"x":{"html":"<ul>\n  <li>advert<span class='match'>ise<\/span><\/li>\n  <li>br<span class='match'>ing<\/span><\/li>\n  <li>dur<span class='match'>ing<\/span><\/li>\n  <li>even<span class='match'>ing<\/span><\/li>\n  <li>exerc<span class='match'>ise<\/span><\/li>\n  <li>k<span class='match'>ing<\/span><\/li>\n  <li>mean<span class='match'>ing<\/span><\/li>\n  <li>morn<span class='match'>ing<\/span><\/li>\n  <li>otherw<span class='match'>ise<\/span><\/li>\n  <li>pract<span class='match'>ise<\/span><\/li>\n  <li>ra<span class='match'>ise<\/span><\/li>\n  <li>real<span class='match'>ise<\/span><\/li>\n  <li>r<span class='match'>ing<\/span><\/li>\n  <li>r<span class='match'>ise<\/span><\/li>\n  <li>s<span class='match'>ing<\/span><\/li>\n  <li>surpr<span class='match'>ise<\/span><\/li>\n  <li>th<span class='match'>ing<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### 2. Empirically verify the rule “i before e except after c”.

To examine whether any words in the dataset break this rule, we can search for words with the sequence of characters "cie", which contain an i before e even after a c. This yields the words "science" an "society". This tells us that the rule is not 100% correct! Searching for "cei", which will show words that adhere to the rule, yields "receive", which tells us that the rule might be correct for certain words. Is i always before e, even after something that is not c? Searching for words that do not follow this convention (i AFTER e, after anything other than a c) using the regex "\[^c\]ei" yields the word "weigh", which again disproves the rule. Searching for words that follow the rule (ie before e, after anything other than a c) using the regex "\[^c\]ie" yields many results, which shows us that the rule applies for most words, but not all of them.

``` r
# list words that contain i before e, after a c. if this yields matches, then the rule is false!
str_view(words, "cie", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-4c45076e640819027b9c">{"x":{"html":"<ul>\n  <li>s<span class='match'>cie<\/span>nce<\/li>\n  <li>so<span class='match'>cie<\/span>ty<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
str_view(words, "cei", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-0ef00e50821300b9648c">{"x":{"html":"<ul>\n  <li>re<span class='match'>cei<\/span>ve<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
str_view(words, "[^c]ei", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-cb2e58ef38bf4a7927cf">{"x":{"html":"<ul>\n  <li><span class='match'>wei<\/span>gh<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
str_view(words, "[^c]ie", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-b6dd4e124c1fb4f56c77">{"x":{"html":"<ul>\n  <li>ac<span class='match'>hie<\/span>ve<\/li>\n  <li>be<span class='match'>lie<\/span>ve<\/li>\n  <li>b<span class='match'>rie<\/span>f<\/li>\n  <li>c<span class='match'>lie<\/span>nt<\/li>\n  <li><span class='match'>die<\/span><\/li>\n  <li>expe<span class='match'>rie<\/span>nce<\/li>\n  <li><span class='match'>fie<\/span>ld<\/li>\n  <li>f<span class='match'>rie<\/span>nd<\/li>\n  <li><span class='match'>lie<\/span><\/li>\n  <li><span class='match'>pie<\/span>ce<\/li>\n  <li>q<span class='match'>uie<\/span>t<\/li>\n  <li><span class='match'>tie<\/span><\/li>\n  <li><span class='match'>vie<\/span>w<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### 3. Is “q” always followed by a “u”?

In the words dataset, we can identify words that contain q followed by any character using the regex "q.". All the results contain "q" followed by a "u". Alternatively, we can identify words that contain q followed by anything other than a "u", using "q\[^u\]". Because this does not yield any results, it does seem like "q" is followed by a "u" in this dataset.

``` r
# list words that contain q followed by any character
str_view(words, "q.", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-811a15bcdf1ed52d714c">{"x":{"html":"<ul>\n  <li>e<span class='match'>qu<\/span>al<\/li>\n  <li><span class='match'>qu<\/span>ality<\/li>\n  <li><span class='match'>qu<\/span>arter<\/li>\n  <li><span class='match'>qu<\/span>estion<\/li>\n  <li><span class='match'>qu<\/span>ick<\/li>\n  <li><span class='match'>qu<\/span>id<\/li>\n  <li><span class='match'>qu<\/span>iet<\/li>\n  <li><span class='match'>qu<\/span>ite<\/li>\n  <li>re<span class='match'>qu<\/span>ire<\/li>\n  <li>s<span class='match'>qu<\/span>are<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# list words that contain q followed by something other than u (no matches)
str_view(words, "q[^u]", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-48dc395645b3e6d34fbd">{"x":{"html":"<ul>\n  <li><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### 4. Write a regular expression that matches a word if it’s probably written in British English, not American English.

Some common differences between British and American English include colour instead of color, so we could look for the sequence "lou". Other differences include "summarize" vs "summarise", so we could also include a search for the sequence "ise". Of course, this will also fetch words such as "practice" that do not have two forms.

``` r
str_view(words, "lou|ise", match = T)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-92c1bd1eabc1dace5ef1">{"x":{"html":"<ul>\n  <li>advert<span class='match'>ise<\/span><\/li>\n  <li>co<span class='match'>lou<\/span>r<\/li>\n  <li>exerc<span class='match'>ise<\/span><\/li>\n  <li>otherw<span class='match'>ise<\/span><\/li>\n  <li>pract<span class='match'>ise<\/span><\/li>\n  <li>ra<span class='match'>ise<\/span><\/li>\n  <li>real<span class='match'>ise<\/span><\/li>\n  <li>r<span class='match'>ise<\/span><\/li>\n  <li>surpr<span class='match'>ise<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### 5. Create a regular expression that will match telephone numbers as commonly written in your country.

Phone numbers in the US can have the form (XXX) XXX - XXXX. Below is the regex that can be used to identify, very strictly, numbers that adhere to this exact form (including spacing and parentheses). Note the double backslash required to specify a digit. There is a more efficient way to write the regex below using brackets `{}`, but that is covered in the next section!

``` r
str_view ( c("(123) 456 - 7890 is a fake number,","and (555) 555 - 5555 is also a fake number"), "\\(\\d\\d\\d\\)\\s\\d\\d\\d\\s-\\s\\d\\d\\d\\d")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-72e7be358e2ac1854d5b">{"x":{"html":"<ul>\n  <li><span class='match'>(123) 456 - 7890<\/span> is a fake number,<\/li>\n  <li>and <span class='match'>(555) 555 - 5555<\/span> is also a fake number<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# same regex using brackets
str_view ( c("(123) 456 - 7890 is a fake number,","and (555) 555 - 5555 is also a fake number"), "\\(\\d{3}\\)\\s\\d{3}\\s-\\s\\d{4}")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-0b1a1cfbf92e49d97316">{"x":{"html":"<ul>\n  <li><span class='match'>(123) 456 - 7890<\/span> is a fake number,<\/li>\n  <li>and <span class='match'>(555) 555 - 5555<\/span> is also a fake number<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view(words, "^.*$")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-e47ce3db9d0b215abf5d">{"x":{"html":"<ul>\n  <li><span class='match'>a<\/span><\/li>\n  <li><span class='match'>able<\/span><\/li>\n  <li><span class='match'>about<\/span><\/li>\n  <li><span class='match'>absolute<\/span><\/li>\n  <li><span class='match'>accept<\/span><\/li>\n  <li><span class='match'>account<\/span><\/li>\n  <li><span class='match'>achieve<\/span><\/li>\n  <li><span class='match'>across<\/span><\/li>\n  <li><span class='match'>act<\/span><\/li>\n  <li><span class='match'>active<\/span><\/li>\n  <li><span class='match'>actual<\/span><\/li>\n  <li><span class='match'>add<\/span><\/li>\n  <li><span class='match'>address<\/span><\/li>\n  <li><span class='match'>admit<\/span><\/li>\n  <li><span class='match'>advertise<\/span><\/li>\n  <li><span class='match'>affect<\/span><\/li>\n  <li><span class='match'>afford<\/span><\/li>\n  <li><span class='match'>after<\/span><\/li>\n  <li><span class='match'>afternoon<\/span><\/li>\n  <li><span class='match'>again<\/span><\/li>\n  <li><span class='match'>against<\/span><\/li>\n  <li><span class='match'>age<\/span><\/li>\n  <li><span class='match'>agent<\/span><\/li>\n  <li><span class='match'>ago<\/span><\/li>\n  <li><span class='match'>agree<\/span><\/li>\n  <li><span class='match'>air<\/span><\/li>\n  <li><span class='match'>all<\/span><\/li>\n  <li><span class='match'>allow<\/span><\/li>\n  <li><span class='match'>almost<\/span><\/li>\n  <li><span class='match'>along<\/span><\/li>\n  <li><span class='match'>already<\/span><\/li>\n  <li><span class='match'>alright<\/span><\/li>\n  <li><span class='match'>also<\/span><\/li>\n  <li><span class='match'>although<\/span><\/li>\n  <li><span class='match'>always<\/span><\/li>\n  <li><span class='match'>america<\/span><\/li>\n  <li><span class='match'>amount<\/span><\/li>\n  <li><span class='match'>and<\/span><\/li>\n  <li><span class='match'>another<\/span><\/li>\n  <li><span class='match'>answer<\/span><\/li>\n  <li><span class='match'>any<\/span><\/li>\n  <li><span class='match'>apart<\/span><\/li>\n  <li><span class='match'>apparent<\/span><\/li>\n  <li><span class='match'>appear<\/span><\/li>\n  <li><span class='match'>apply<\/span><\/li>\n  <li><span class='match'>appoint<\/span><\/li>\n  <li><span class='match'>approach<\/span><\/li>\n  <li><span class='match'>appropriate<\/span><\/li>\n  <li><span class='match'>area<\/span><\/li>\n  <li><span class='match'>argue<\/span><\/li>\n  <li><span class='match'>arm<\/span><\/li>\n  <li><span class='match'>around<\/span><\/li>\n  <li><span class='match'>arrange<\/span><\/li>\n  <li><span class='match'>art<\/span><\/li>\n  <li><span class='match'>as<\/span><\/li>\n  <li><span class='match'>ask<\/span><\/li>\n  <li><span class='match'>associate<\/span><\/li>\n  <li><span class='match'>assume<\/span><\/li>\n  <li><span class='match'>at<\/span><\/li>\n  <li><span class='match'>attend<\/span><\/li>\n  <li><span class='match'>authority<\/span><\/li>\n  <li><span class='match'>available<\/span><\/li>\n  <li><span class='match'>aware<\/span><\/li>\n  <li><span class='match'>away<\/span><\/li>\n  <li><span class='match'>awful<\/span><\/li>\n  <li><span class='match'>baby<\/span><\/li>\n  <li><span class='match'>back<\/span><\/li>\n  <li><span class='match'>bad<\/span><\/li>\n  <li><span class='match'>bag<\/span><\/li>\n  <li><span class='match'>balance<\/span><\/li>\n  <li><span class='match'>ball<\/span><\/li>\n  <li><span class='match'>bank<\/span><\/li>\n  <li><span class='match'>bar<\/span><\/li>\n  <li><span class='match'>base<\/span><\/li>\n  <li><span class='match'>basis<\/span><\/li>\n  <li><span class='match'>be<\/span><\/li>\n  <li><span class='match'>bear<\/span><\/li>\n  <li><span class='match'>beat<\/span><\/li>\n  <li><span class='match'>beauty<\/span><\/li>\n  <li><span class='match'>because<\/span><\/li>\n  <li><span class='match'>become<\/span><\/li>\n  <li><span class='match'>bed<\/span><\/li>\n  <li><span class='match'>before<\/span><\/li>\n  <li><span class='match'>begin<\/span><\/li>\n  <li><span class='match'>behind<\/span><\/li>\n  <li><span class='match'>believe<\/span><\/li>\n  <li><span class='match'>benefit<\/span><\/li>\n  <li><span class='match'>best<\/span><\/li>\n  <li><span class='match'>bet<\/span><\/li>\n  <li><span class='match'>between<\/span><\/li>\n  <li><span class='match'>big<\/span><\/li>\n  <li><span class='match'>bill<\/span><\/li>\n  <li><span class='match'>birth<\/span><\/li>\n  <li><span class='match'>bit<\/span><\/li>\n  <li><span class='match'>black<\/span><\/li>\n  <li><span class='match'>bloke<\/span><\/li>\n  <li><span class='match'>blood<\/span><\/li>\n  <li><span class='match'>blow<\/span><\/li>\n  <li><span class='match'>blue<\/span><\/li>\n  <li><span class='match'>board<\/span><\/li>\n  <li><span class='match'>boat<\/span><\/li>\n  <li><span class='match'>body<\/span><\/li>\n  <li><span class='match'>book<\/span><\/li>\n  <li><span class='match'>both<\/span><\/li>\n  <li><span class='match'>bother<\/span><\/li>\n  <li><span class='match'>bottle<\/span><\/li>\n  <li><span class='match'>bottom<\/span><\/li>\n  <li><span class='match'>box<\/span><\/li>\n  <li><span class='match'>boy<\/span><\/li>\n  <li><span class='match'>break<\/span><\/li>\n  <li><span class='match'>brief<\/span><\/li>\n  <li><span class='match'>brilliant<\/span><\/li>\n  <li><span class='match'>bring<\/span><\/li>\n  <li><span class='match'>britain<\/span><\/li>\n  <li><span class='match'>brother<\/span><\/li>\n  <li><span class='match'>budget<\/span><\/li>\n  <li><span class='match'>build<\/span><\/li>\n  <li><span class='match'>bus<\/span><\/li>\n  <li><span class='match'>business<\/span><\/li>\n  <li><span class='match'>busy<\/span><\/li>\n  <li><span class='match'>but<\/span><\/li>\n  <li><span class='match'>buy<\/span><\/li>\n  <li><span class='match'>by<\/span><\/li>\n  <li><span class='match'>cake<\/span><\/li>\n  <li><span class='match'>call<\/span><\/li>\n  <li><span class='match'>can<\/span><\/li>\n  <li><span class='match'>car<\/span><\/li>\n  <li><span class='match'>card<\/span><\/li>\n  <li><span class='match'>care<\/span><\/li>\n  <li><span class='match'>carry<\/span><\/li>\n  <li><span class='match'>case<\/span><\/li>\n  <li><span class='match'>cat<\/span><\/li>\n  <li><span class='match'>catch<\/span><\/li>\n  <li><span class='match'>cause<\/span><\/li>\n  <li><span class='match'>cent<\/span><\/li>\n  <li><span class='match'>centre<\/span><\/li>\n  <li><span class='match'>certain<\/span><\/li>\n  <li><span class='match'>chair<\/span><\/li>\n  <li><span class='match'>chairman<\/span><\/li>\n  <li><span class='match'>chance<\/span><\/li>\n  <li><span class='match'>change<\/span><\/li>\n  <li><span class='match'>chap<\/span><\/li>\n  <li><span class='match'>character<\/span><\/li>\n  <li><span class='match'>charge<\/span><\/li>\n  <li><span class='match'>cheap<\/span><\/li>\n  <li><span class='match'>check<\/span><\/li>\n  <li><span class='match'>child<\/span><\/li>\n  <li><span class='match'>choice<\/span><\/li>\n  <li><span class='match'>choose<\/span><\/li>\n  <li><span class='match'>Christ<\/span><\/li>\n  <li><span class='match'>Christmas<\/span><\/li>\n  <li><span class='match'>church<\/span><\/li>\n  <li><span class='match'>city<\/span><\/li>\n  <li><span class='match'>claim<\/span><\/li>\n  <li><span class='match'>class<\/span><\/li>\n  <li><span class='match'>clean<\/span><\/li>\n  <li><span class='match'>clear<\/span><\/li>\n  <li><span class='match'>client<\/span><\/li>\n  <li><span class='match'>clock<\/span><\/li>\n  <li><span class='match'>close<\/span><\/li>\n  <li><span class='match'>closes<\/span><\/li>\n  <li><span class='match'>clothe<\/span><\/li>\n  <li><span class='match'>club<\/span><\/li>\n  <li><span class='match'>coffee<\/span><\/li>\n  <li><span class='match'>cold<\/span><\/li>\n  <li><span class='match'>colleague<\/span><\/li>\n  <li><span class='match'>collect<\/span><\/li>\n  <li><span class='match'>college<\/span><\/li>\n  <li><span class='match'>colour<\/span><\/li>\n  <li><span class='match'>come<\/span><\/li>\n  <li><span class='match'>comment<\/span><\/li>\n  <li><span class='match'>commit<\/span><\/li>\n  <li><span class='match'>committee<\/span><\/li>\n  <li><span class='match'>common<\/span><\/li>\n  <li><span class='match'>community<\/span><\/li>\n  <li><span class='match'>company<\/span><\/li>\n  <li><span class='match'>compare<\/span><\/li>\n  <li><span class='match'>complete<\/span><\/li>\n  <li><span class='match'>compute<\/span><\/li>\n  <li><span class='match'>concern<\/span><\/li>\n  <li><span class='match'>condition<\/span><\/li>\n  <li><span class='match'>confer<\/span><\/li>\n  <li><span class='match'>consider<\/span><\/li>\n  <li><span class='match'>consult<\/span><\/li>\n  <li><span class='match'>contact<\/span><\/li>\n  <li><span class='match'>continue<\/span><\/li>\n  <li><span class='match'>contract<\/span><\/li>\n  <li><span class='match'>control<\/span><\/li>\n  <li><span class='match'>converse<\/span><\/li>\n  <li><span class='match'>cook<\/span><\/li>\n  <li><span class='match'>copy<\/span><\/li>\n  <li><span class='match'>corner<\/span><\/li>\n  <li><span class='match'>correct<\/span><\/li>\n  <li><span class='match'>cost<\/span><\/li>\n  <li><span class='match'>could<\/span><\/li>\n  <li><span class='match'>council<\/span><\/li>\n  <li><span class='match'>count<\/span><\/li>\n  <li><span class='match'>country<\/span><\/li>\n  <li><span class='match'>county<\/span><\/li>\n  <li><span class='match'>couple<\/span><\/li>\n  <li><span class='match'>course<\/span><\/li>\n  <li><span class='match'>court<\/span><\/li>\n  <li><span class='match'>cover<\/span><\/li>\n  <li><span class='match'>create<\/span><\/li>\n  <li><span class='match'>cross<\/span><\/li>\n  <li><span class='match'>cup<\/span><\/li>\n  <li><span class='match'>current<\/span><\/li>\n  <li><span class='match'>cut<\/span><\/li>\n  <li><span class='match'>dad<\/span><\/li>\n  <li><span class='match'>danger<\/span><\/li>\n  <li><span class='match'>date<\/span><\/li>\n  <li><span class='match'>day<\/span><\/li>\n  <li><span class='match'>dead<\/span><\/li>\n  <li><span class='match'>deal<\/span><\/li>\n  <li><span class='match'>dear<\/span><\/li>\n  <li><span class='match'>debate<\/span><\/li>\n  <li><span class='match'>decide<\/span><\/li>\n  <li><span class='match'>decision<\/span><\/li>\n  <li><span class='match'>deep<\/span><\/li>\n  <li><span class='match'>definite<\/span><\/li>\n  <li><span class='match'>degree<\/span><\/li>\n  <li><span class='match'>department<\/span><\/li>\n  <li><span class='match'>depend<\/span><\/li>\n  <li><span class='match'>describe<\/span><\/li>\n  <li><span class='match'>design<\/span><\/li>\n  <li><span class='match'>detail<\/span><\/li>\n  <li><span class='match'>develop<\/span><\/li>\n  <li><span class='match'>die<\/span><\/li>\n  <li><span class='match'>difference<\/span><\/li>\n  <li><span class='match'>difficult<\/span><\/li>\n  <li><span class='match'>dinner<\/span><\/li>\n  <li><span class='match'>direct<\/span><\/li>\n  <li><span class='match'>discuss<\/span><\/li>\n  <li><span class='match'>district<\/span><\/li>\n  <li><span class='match'>divide<\/span><\/li>\n  <li><span class='match'>do<\/span><\/li>\n  <li><span class='match'>doctor<\/span><\/li>\n  <li><span class='match'>document<\/span><\/li>\n  <li><span class='match'>dog<\/span><\/li>\n  <li><span class='match'>door<\/span><\/li>\n  <li><span class='match'>double<\/span><\/li>\n  <li><span class='match'>doubt<\/span><\/li>\n  <li><span class='match'>down<\/span><\/li>\n  <li><span class='match'>draw<\/span><\/li>\n  <li><span class='match'>dress<\/span><\/li>\n  <li><span class='match'>drink<\/span><\/li>\n  <li><span class='match'>drive<\/span><\/li>\n  <li><span class='match'>drop<\/span><\/li>\n  <li><span class='match'>dry<\/span><\/li>\n  <li><span class='match'>due<\/span><\/li>\n  <li><span class='match'>during<\/span><\/li>\n  <li><span class='match'>each<\/span><\/li>\n  <li><span class='match'>early<\/span><\/li>\n  <li><span class='match'>east<\/span><\/li>\n  <li><span class='match'>easy<\/span><\/li>\n  <li><span class='match'>eat<\/span><\/li>\n  <li><span class='match'>economy<\/span><\/li>\n  <li><span class='match'>educate<\/span><\/li>\n  <li><span class='match'>effect<\/span><\/li>\n  <li><span class='match'>egg<\/span><\/li>\n  <li><span class='match'>eight<\/span><\/li>\n  <li><span class='match'>either<\/span><\/li>\n  <li><span class='match'>elect<\/span><\/li>\n  <li><span class='match'>electric<\/span><\/li>\n  <li><span class='match'>eleven<\/span><\/li>\n  <li><span class='match'>else<\/span><\/li>\n  <li><span class='match'>employ<\/span><\/li>\n  <li><span class='match'>encourage<\/span><\/li>\n  <li><span class='match'>end<\/span><\/li>\n  <li><span class='match'>engine<\/span><\/li>\n  <li><span class='match'>english<\/span><\/li>\n  <li><span class='match'>enjoy<\/span><\/li>\n  <li><span class='match'>enough<\/span><\/li>\n  <li><span class='match'>enter<\/span><\/li>\n  <li><span class='match'>environment<\/span><\/li>\n  <li><span class='match'>equal<\/span><\/li>\n  <li><span class='match'>especial<\/span><\/li>\n  <li><span class='match'>europe<\/span><\/li>\n  <li><span class='match'>even<\/span><\/li>\n  <li><span class='match'>evening<\/span><\/li>\n  <li><span class='match'>ever<\/span><\/li>\n  <li><span class='match'>every<\/span><\/li>\n  <li><span class='match'>evidence<\/span><\/li>\n  <li><span class='match'>exact<\/span><\/li>\n  <li><span class='match'>example<\/span><\/li>\n  <li><span class='match'>except<\/span><\/li>\n  <li><span class='match'>excuse<\/span><\/li>\n  <li><span class='match'>exercise<\/span><\/li>\n  <li><span class='match'>exist<\/span><\/li>\n  <li><span class='match'>expect<\/span><\/li>\n  <li><span class='match'>expense<\/span><\/li>\n  <li><span class='match'>experience<\/span><\/li>\n  <li><span class='match'>explain<\/span><\/li>\n  <li><span class='match'>express<\/span><\/li>\n  <li><span class='match'>extra<\/span><\/li>\n  <li><span class='match'>eye<\/span><\/li>\n  <li><span class='match'>face<\/span><\/li>\n  <li><span class='match'>fact<\/span><\/li>\n  <li><span class='match'>fair<\/span><\/li>\n  <li><span class='match'>fall<\/span><\/li>\n  <li><span class='match'>family<\/span><\/li>\n  <li><span class='match'>far<\/span><\/li>\n  <li><span class='match'>farm<\/span><\/li>\n  <li><span class='match'>fast<\/span><\/li>\n  <li><span class='match'>father<\/span><\/li>\n  <li><span class='match'>favour<\/span><\/li>\n  <li><span class='match'>feed<\/span><\/li>\n  <li><span class='match'>feel<\/span><\/li>\n  <li><span class='match'>few<\/span><\/li>\n  <li><span class='match'>field<\/span><\/li>\n  <li><span class='match'>fight<\/span><\/li>\n  <li><span class='match'>figure<\/span><\/li>\n  <li><span class='match'>file<\/span><\/li>\n  <li><span class='match'>fill<\/span><\/li>\n  <li><span class='match'>film<\/span><\/li>\n  <li><span class='match'>final<\/span><\/li>\n  <li><span class='match'>finance<\/span><\/li>\n  <li><span class='match'>find<\/span><\/li>\n  <li><span class='match'>fine<\/span><\/li>\n  <li><span class='match'>finish<\/span><\/li>\n  <li><span class='match'>fire<\/span><\/li>\n  <li><span class='match'>first<\/span><\/li>\n  <li><span class='match'>fish<\/span><\/li>\n  <li><span class='match'>fit<\/span><\/li>\n  <li><span class='match'>five<\/span><\/li>\n  <li><span class='match'>flat<\/span><\/li>\n  <li><span class='match'>floor<\/span><\/li>\n  <li><span class='match'>fly<\/span><\/li>\n  <li><span class='match'>follow<\/span><\/li>\n  <li><span class='match'>food<\/span><\/li>\n  <li><span class='match'>foot<\/span><\/li>\n  <li><span class='match'>for<\/span><\/li>\n  <li><span class='match'>force<\/span><\/li>\n  <li><span class='match'>forget<\/span><\/li>\n  <li><span class='match'>form<\/span><\/li>\n  <li><span class='match'>fortune<\/span><\/li>\n  <li><span class='match'>forward<\/span><\/li>\n  <li><span class='match'>four<\/span><\/li>\n  <li><span class='match'>france<\/span><\/li>\n  <li><span class='match'>free<\/span><\/li>\n  <li><span class='match'>friday<\/span><\/li>\n  <li><span class='match'>friend<\/span><\/li>\n  <li><span class='match'>from<\/span><\/li>\n  <li><span class='match'>front<\/span><\/li>\n  <li><span class='match'>full<\/span><\/li>\n  <li><span class='match'>fun<\/span><\/li>\n  <li><span class='match'>function<\/span><\/li>\n  <li><span class='match'>fund<\/span><\/li>\n  <li><span class='match'>further<\/span><\/li>\n  <li><span class='match'>future<\/span><\/li>\n  <li><span class='match'>game<\/span><\/li>\n  <li><span class='match'>garden<\/span><\/li>\n  <li><span class='match'>gas<\/span><\/li>\n  <li><span class='match'>general<\/span><\/li>\n  <li><span class='match'>germany<\/span><\/li>\n  <li><span class='match'>get<\/span><\/li>\n  <li><span class='match'>girl<\/span><\/li>\n  <li><span class='match'>give<\/span><\/li>\n  <li><span class='match'>glass<\/span><\/li>\n  <li><span class='match'>go<\/span><\/li>\n  <li><span class='match'>god<\/span><\/li>\n  <li><span class='match'>good<\/span><\/li>\n  <li><span class='match'>goodbye<\/span><\/li>\n  <li><span class='match'>govern<\/span><\/li>\n  <li><span class='match'>grand<\/span><\/li>\n  <li><span class='match'>grant<\/span><\/li>\n  <li><span class='match'>great<\/span><\/li>\n  <li><span class='match'>green<\/span><\/li>\n  <li><span class='match'>ground<\/span><\/li>\n  <li><span class='match'>group<\/span><\/li>\n  <li><span class='match'>grow<\/span><\/li>\n  <li><span class='match'>guess<\/span><\/li>\n  <li><span class='match'>guy<\/span><\/li>\n  <li><span class='match'>hair<\/span><\/li>\n  <li><span class='match'>half<\/span><\/li>\n  <li><span class='match'>hall<\/span><\/li>\n  <li><span class='match'>hand<\/span><\/li>\n  <li><span class='match'>hang<\/span><\/li>\n  <li><span class='match'>happen<\/span><\/li>\n  <li><span class='match'>happy<\/span><\/li>\n  <li><span class='match'>hard<\/span><\/li>\n  <li><span class='match'>hate<\/span><\/li>\n  <li><span class='match'>have<\/span><\/li>\n  <li><span class='match'>he<\/span><\/li>\n  <li><span class='match'>head<\/span><\/li>\n  <li><span class='match'>health<\/span><\/li>\n  <li><span class='match'>hear<\/span><\/li>\n  <li><span class='match'>heart<\/span><\/li>\n  <li><span class='match'>heat<\/span><\/li>\n  <li><span class='match'>heavy<\/span><\/li>\n  <li><span class='match'>hell<\/span><\/li>\n  <li><span class='match'>help<\/span><\/li>\n  <li><span class='match'>here<\/span><\/li>\n  <li><span class='match'>high<\/span><\/li>\n  <li><span class='match'>history<\/span><\/li>\n  <li><span class='match'>hit<\/span><\/li>\n  <li><span class='match'>hold<\/span><\/li>\n  <li><span class='match'>holiday<\/span><\/li>\n  <li><span class='match'>home<\/span><\/li>\n  <li><span class='match'>honest<\/span><\/li>\n  <li><span class='match'>hope<\/span><\/li>\n  <li><span class='match'>horse<\/span><\/li>\n  <li><span class='match'>hospital<\/span><\/li>\n  <li><span class='match'>hot<\/span><\/li>\n  <li><span class='match'>hour<\/span><\/li>\n  <li><span class='match'>house<\/span><\/li>\n  <li><span class='match'>how<\/span><\/li>\n  <li><span class='match'>however<\/span><\/li>\n  <li><span class='match'>hullo<\/span><\/li>\n  <li><span class='match'>hundred<\/span><\/li>\n  <li><span class='match'>husband<\/span><\/li>\n  <li><span class='match'>idea<\/span><\/li>\n  <li><span class='match'>identify<\/span><\/li>\n  <li><span class='match'>if<\/span><\/li>\n  <li><span class='match'>imagine<\/span><\/li>\n  <li><span class='match'>important<\/span><\/li>\n  <li><span class='match'>improve<\/span><\/li>\n  <li><span class='match'>in<\/span><\/li>\n  <li><span class='match'>include<\/span><\/li>\n  <li><span class='match'>income<\/span><\/li>\n  <li><span class='match'>increase<\/span><\/li>\n  <li><span class='match'>indeed<\/span><\/li>\n  <li><span class='match'>individual<\/span><\/li>\n  <li><span class='match'>industry<\/span><\/li>\n  <li><span class='match'>inform<\/span><\/li>\n  <li><span class='match'>inside<\/span><\/li>\n  <li><span class='match'>instead<\/span><\/li>\n  <li><span class='match'>insure<\/span><\/li>\n  <li><span class='match'>interest<\/span><\/li>\n  <li><span class='match'>into<\/span><\/li>\n  <li><span class='match'>introduce<\/span><\/li>\n  <li><span class='match'>invest<\/span><\/li>\n  <li><span class='match'>involve<\/span><\/li>\n  <li><span class='match'>issue<\/span><\/li>\n  <li><span class='match'>it<\/span><\/li>\n  <li><span class='match'>item<\/span><\/li>\n  <li><span class='match'>jesus<\/span><\/li>\n  <li><span class='match'>job<\/span><\/li>\n  <li><span class='match'>join<\/span><\/li>\n  <li><span class='match'>judge<\/span><\/li>\n  <li><span class='match'>jump<\/span><\/li>\n  <li><span class='match'>just<\/span><\/li>\n  <li><span class='match'>keep<\/span><\/li>\n  <li><span class='match'>key<\/span><\/li>\n  <li><span class='match'>kid<\/span><\/li>\n  <li><span class='match'>kill<\/span><\/li>\n  <li><span class='match'>kind<\/span><\/li>\n  <li><span class='match'>king<\/span><\/li>\n  <li><span class='match'>kitchen<\/span><\/li>\n  <li><span class='match'>knock<\/span><\/li>\n  <li><span class='match'>know<\/span><\/li>\n  <li><span class='match'>labour<\/span><\/li>\n  <li><span class='match'>lad<\/span><\/li>\n  <li><span class='match'>lady<\/span><\/li>\n  <li><span class='match'>land<\/span><\/li>\n  <li><span class='match'>language<\/span><\/li>\n  <li><span class='match'>large<\/span><\/li>\n  <li><span class='match'>last<\/span><\/li>\n  <li><span class='match'>late<\/span><\/li>\n  <li><span class='match'>laugh<\/span><\/li>\n  <li><span class='match'>law<\/span><\/li>\n  <li><span class='match'>lay<\/span><\/li>\n  <li><span class='match'>lead<\/span><\/li>\n  <li><span class='match'>learn<\/span><\/li>\n  <li><span class='match'>leave<\/span><\/li>\n  <li><span class='match'>left<\/span><\/li>\n  <li><span class='match'>leg<\/span><\/li>\n  <li><span class='match'>less<\/span><\/li>\n  <li><span class='match'>let<\/span><\/li>\n  <li><span class='match'>letter<\/span><\/li>\n  <li><span class='match'>level<\/span><\/li>\n  <li><span class='match'>lie<\/span><\/li>\n  <li><span class='match'>life<\/span><\/li>\n  <li><span class='match'>light<\/span><\/li>\n  <li><span class='match'>like<\/span><\/li>\n  <li><span class='match'>likely<\/span><\/li>\n  <li><span class='match'>limit<\/span><\/li>\n  <li><span class='match'>line<\/span><\/li>\n  <li><span class='match'>link<\/span><\/li>\n  <li><span class='match'>list<\/span><\/li>\n  <li><span class='match'>listen<\/span><\/li>\n  <li><span class='match'>little<\/span><\/li>\n  <li><span class='match'>live<\/span><\/li>\n  <li><span class='match'>load<\/span><\/li>\n  <li><span class='match'>local<\/span><\/li>\n  <li><span class='match'>lock<\/span><\/li>\n  <li><span class='match'>london<\/span><\/li>\n  <li><span class='match'>long<\/span><\/li>\n  <li><span class='match'>look<\/span><\/li>\n  <li><span class='match'>lord<\/span><\/li>\n  <li><span class='match'>lose<\/span><\/li>\n  <li><span class='match'>lot<\/span><\/li>\n  <li><span class='match'>love<\/span><\/li>\n  <li><span class='match'>low<\/span><\/li>\n  <li><span class='match'>luck<\/span><\/li>\n  <li><span class='match'>lunch<\/span><\/li>\n  <li><span class='match'>machine<\/span><\/li>\n  <li><span class='match'>main<\/span><\/li>\n  <li><span class='match'>major<\/span><\/li>\n  <li><span class='match'>make<\/span><\/li>\n  <li><span class='match'>man<\/span><\/li>\n  <li><span class='match'>manage<\/span><\/li>\n  <li><span class='match'>many<\/span><\/li>\n  <li><span class='match'>mark<\/span><\/li>\n  <li><span class='match'>market<\/span><\/li>\n  <li><span class='match'>marry<\/span><\/li>\n  <li><span class='match'>match<\/span><\/li>\n  <li><span class='match'>matter<\/span><\/li>\n  <li><span class='match'>may<\/span><\/li>\n  <li><span class='match'>maybe<\/span><\/li>\n  <li><span class='match'>mean<\/span><\/li>\n  <li><span class='match'>meaning<\/span><\/li>\n  <li><span class='match'>measure<\/span><\/li>\n  <li><span class='match'>meet<\/span><\/li>\n  <li><span class='match'>member<\/span><\/li>\n  <li><span class='match'>mention<\/span><\/li>\n  <li><span class='match'>middle<\/span><\/li>\n  <li><span class='match'>might<\/span><\/li>\n  <li><span class='match'>mile<\/span><\/li>\n  <li><span class='match'>milk<\/span><\/li>\n  <li><span class='match'>million<\/span><\/li>\n  <li><span class='match'>mind<\/span><\/li>\n  <li><span class='match'>minister<\/span><\/li>\n  <li><span class='match'>minus<\/span><\/li>\n  <li><span class='match'>minute<\/span><\/li>\n  <li><span class='match'>miss<\/span><\/li>\n  <li><span class='match'>mister<\/span><\/li>\n  <li><span class='match'>moment<\/span><\/li>\n  <li><span class='match'>monday<\/span><\/li>\n  <li><span class='match'>money<\/span><\/li>\n  <li><span class='match'>month<\/span><\/li>\n  <li><span class='match'>more<\/span><\/li>\n  <li><span class='match'>morning<\/span><\/li>\n  <li><span class='match'>most<\/span><\/li>\n  <li><span class='match'>mother<\/span><\/li>\n  <li><span class='match'>motion<\/span><\/li>\n  <li><span class='match'>move<\/span><\/li>\n  <li><span class='match'>mrs<\/span><\/li>\n  <li><span class='match'>much<\/span><\/li>\n  <li><span class='match'>music<\/span><\/li>\n  <li><span class='match'>must<\/span><\/li>\n  <li><span class='match'>name<\/span><\/li>\n  <li><span class='match'>nation<\/span><\/li>\n  <li><span class='match'>nature<\/span><\/li>\n  <li><span class='match'>near<\/span><\/li>\n  <li><span class='match'>necessary<\/span><\/li>\n  <li><span class='match'>need<\/span><\/li>\n  <li><span class='match'>never<\/span><\/li>\n  <li><span class='match'>new<\/span><\/li>\n  <li><span class='match'>news<\/span><\/li>\n  <li><span class='match'>next<\/span><\/li>\n  <li><span class='match'>nice<\/span><\/li>\n  <li><span class='match'>night<\/span><\/li>\n  <li><span class='match'>nine<\/span><\/li>\n  <li><span class='match'>no<\/span><\/li>\n  <li><span class='match'>non<\/span><\/li>\n  <li><span class='match'>none<\/span><\/li>\n  <li><span class='match'>normal<\/span><\/li>\n  <li><span class='match'>north<\/span><\/li>\n  <li><span class='match'>not<\/span><\/li>\n  <li><span class='match'>note<\/span><\/li>\n  <li><span class='match'>notice<\/span><\/li>\n  <li><span class='match'>now<\/span><\/li>\n  <li><span class='match'>number<\/span><\/li>\n  <li><span class='match'>obvious<\/span><\/li>\n  <li><span class='match'>occasion<\/span><\/li>\n  <li><span class='match'>odd<\/span><\/li>\n  <li><span class='match'>of<\/span><\/li>\n  <li><span class='match'>off<\/span><\/li>\n  <li><span class='match'>offer<\/span><\/li>\n  <li><span class='match'>office<\/span><\/li>\n  <li><span class='match'>often<\/span><\/li>\n  <li><span class='match'>okay<\/span><\/li>\n  <li><span class='match'>old<\/span><\/li>\n  <li><span class='match'>on<\/span><\/li>\n  <li><span class='match'>once<\/span><\/li>\n  <li><span class='match'>one<\/span><\/li>\n  <li><span class='match'>only<\/span><\/li>\n  <li><span class='match'>open<\/span><\/li>\n  <li><span class='match'>operate<\/span><\/li>\n  <li><span class='match'>opportunity<\/span><\/li>\n  <li><span class='match'>oppose<\/span><\/li>\n  <li><span class='match'>or<\/span><\/li>\n  <li><span class='match'>order<\/span><\/li>\n  <li><span class='match'>organize<\/span><\/li>\n  <li><span class='match'>original<\/span><\/li>\n  <li><span class='match'>other<\/span><\/li>\n  <li><span class='match'>otherwise<\/span><\/li>\n  <li><span class='match'>ought<\/span><\/li>\n  <li><span class='match'>out<\/span><\/li>\n  <li><span class='match'>over<\/span><\/li>\n  <li><span class='match'>own<\/span><\/li>\n  <li><span class='match'>pack<\/span><\/li>\n  <li><span class='match'>page<\/span><\/li>\n  <li><span class='match'>paint<\/span><\/li>\n  <li><span class='match'>pair<\/span><\/li>\n  <li><span class='match'>paper<\/span><\/li>\n  <li><span class='match'>paragraph<\/span><\/li>\n  <li><span class='match'>pardon<\/span><\/li>\n  <li><span class='match'>parent<\/span><\/li>\n  <li><span class='match'>park<\/span><\/li>\n  <li><span class='match'>part<\/span><\/li>\n  <li><span class='match'>particular<\/span><\/li>\n  <li><span class='match'>party<\/span><\/li>\n  <li><span class='match'>pass<\/span><\/li>\n  <li><span class='match'>past<\/span><\/li>\n  <li><span class='match'>pay<\/span><\/li>\n  <li><span class='match'>pence<\/span><\/li>\n  <li><span class='match'>pension<\/span><\/li>\n  <li><span class='match'>people<\/span><\/li>\n  <li><span class='match'>per<\/span><\/li>\n  <li><span class='match'>percent<\/span><\/li>\n  <li><span class='match'>perfect<\/span><\/li>\n  <li><span class='match'>perhaps<\/span><\/li>\n  <li><span class='match'>period<\/span><\/li>\n  <li><span class='match'>person<\/span><\/li>\n  <li><span class='match'>photograph<\/span><\/li>\n  <li><span class='match'>pick<\/span><\/li>\n  <li><span class='match'>picture<\/span><\/li>\n  <li><span class='match'>piece<\/span><\/li>\n  <li><span class='match'>place<\/span><\/li>\n  <li><span class='match'>plan<\/span><\/li>\n  <li><span class='match'>play<\/span><\/li>\n  <li><span class='match'>please<\/span><\/li>\n  <li><span class='match'>plus<\/span><\/li>\n  <li><span class='match'>point<\/span><\/li>\n  <li><span class='match'>police<\/span><\/li>\n  <li><span class='match'>policy<\/span><\/li>\n  <li><span class='match'>politic<\/span><\/li>\n  <li><span class='match'>poor<\/span><\/li>\n  <li><span class='match'>position<\/span><\/li>\n  <li><span class='match'>positive<\/span><\/li>\n  <li><span class='match'>possible<\/span><\/li>\n  <li><span class='match'>post<\/span><\/li>\n  <li><span class='match'>pound<\/span><\/li>\n  <li><span class='match'>power<\/span><\/li>\n  <li><span class='match'>practise<\/span><\/li>\n  <li><span class='match'>prepare<\/span><\/li>\n  <li><span class='match'>present<\/span><\/li>\n  <li><span class='match'>press<\/span><\/li>\n  <li><span class='match'>pressure<\/span><\/li>\n  <li><span class='match'>presume<\/span><\/li>\n  <li><span class='match'>pretty<\/span><\/li>\n  <li><span class='match'>previous<\/span><\/li>\n  <li><span class='match'>price<\/span><\/li>\n  <li><span class='match'>print<\/span><\/li>\n  <li><span class='match'>private<\/span><\/li>\n  <li><span class='match'>probable<\/span><\/li>\n  <li><span class='match'>problem<\/span><\/li>\n  <li><span class='match'>proceed<\/span><\/li>\n  <li><span class='match'>process<\/span><\/li>\n  <li><span class='match'>produce<\/span><\/li>\n  <li><span class='match'>product<\/span><\/li>\n  <li><span class='match'>programme<\/span><\/li>\n  <li><span class='match'>project<\/span><\/li>\n  <li><span class='match'>proper<\/span><\/li>\n  <li><span class='match'>propose<\/span><\/li>\n  <li><span class='match'>protect<\/span><\/li>\n  <li><span class='match'>provide<\/span><\/li>\n  <li><span class='match'>public<\/span><\/li>\n  <li><span class='match'>pull<\/span><\/li>\n  <li><span class='match'>purpose<\/span><\/li>\n  <li><span class='match'>push<\/span><\/li>\n  <li><span class='match'>put<\/span><\/li>\n  <li><span class='match'>quality<\/span><\/li>\n  <li><span class='match'>quarter<\/span><\/li>\n  <li><span class='match'>question<\/span><\/li>\n  <li><span class='match'>quick<\/span><\/li>\n  <li><span class='match'>quid<\/span><\/li>\n  <li><span class='match'>quiet<\/span><\/li>\n  <li><span class='match'>quite<\/span><\/li>\n  <li><span class='match'>radio<\/span><\/li>\n  <li><span class='match'>rail<\/span><\/li>\n  <li><span class='match'>raise<\/span><\/li>\n  <li><span class='match'>range<\/span><\/li>\n  <li><span class='match'>rate<\/span><\/li>\n  <li><span class='match'>rather<\/span><\/li>\n  <li><span class='match'>read<\/span><\/li>\n  <li><span class='match'>ready<\/span><\/li>\n  <li><span class='match'>real<\/span><\/li>\n  <li><span class='match'>realise<\/span><\/li>\n  <li><span class='match'>really<\/span><\/li>\n  <li><span class='match'>reason<\/span><\/li>\n  <li><span class='match'>receive<\/span><\/li>\n  <li><span class='match'>recent<\/span><\/li>\n  <li><span class='match'>reckon<\/span><\/li>\n  <li><span class='match'>recognize<\/span><\/li>\n  <li><span class='match'>recommend<\/span><\/li>\n  <li><span class='match'>record<\/span><\/li>\n  <li><span class='match'>red<\/span><\/li>\n  <li><span class='match'>reduce<\/span><\/li>\n  <li><span class='match'>refer<\/span><\/li>\n  <li><span class='match'>regard<\/span><\/li>\n  <li><span class='match'>region<\/span><\/li>\n  <li><span class='match'>relation<\/span><\/li>\n  <li><span class='match'>remember<\/span><\/li>\n  <li><span class='match'>report<\/span><\/li>\n  <li><span class='match'>represent<\/span><\/li>\n  <li><span class='match'>require<\/span><\/li>\n  <li><span class='match'>research<\/span><\/li>\n  <li><span class='match'>resource<\/span><\/li>\n  <li><span class='match'>respect<\/span><\/li>\n  <li><span class='match'>responsible<\/span><\/li>\n  <li><span class='match'>rest<\/span><\/li>\n  <li><span class='match'>result<\/span><\/li>\n  <li><span class='match'>return<\/span><\/li>\n  <li><span class='match'>rid<\/span><\/li>\n  <li><span class='match'>right<\/span><\/li>\n  <li><span class='match'>ring<\/span><\/li>\n  <li><span class='match'>rise<\/span><\/li>\n  <li><span class='match'>road<\/span><\/li>\n  <li><span class='match'>role<\/span><\/li>\n  <li><span class='match'>roll<\/span><\/li>\n  <li><span class='match'>room<\/span><\/li>\n  <li><span class='match'>round<\/span><\/li>\n  <li><span class='match'>rule<\/span><\/li>\n  <li><span class='match'>run<\/span><\/li>\n  <li><span class='match'>safe<\/span><\/li>\n  <li><span class='match'>sale<\/span><\/li>\n  <li><span class='match'>same<\/span><\/li>\n  <li><span class='match'>saturday<\/span><\/li>\n  <li><span class='match'>save<\/span><\/li>\n  <li><span class='match'>say<\/span><\/li>\n  <li><span class='match'>scheme<\/span><\/li>\n  <li><span class='match'>school<\/span><\/li>\n  <li><span class='match'>science<\/span><\/li>\n  <li><span class='match'>score<\/span><\/li>\n  <li><span class='match'>scotland<\/span><\/li>\n  <li><span class='match'>seat<\/span><\/li>\n  <li><span class='match'>second<\/span><\/li>\n  <li><span class='match'>secretary<\/span><\/li>\n  <li><span class='match'>section<\/span><\/li>\n  <li><span class='match'>secure<\/span><\/li>\n  <li><span class='match'>see<\/span><\/li>\n  <li><span class='match'>seem<\/span><\/li>\n  <li><span class='match'>self<\/span><\/li>\n  <li><span class='match'>sell<\/span><\/li>\n  <li><span class='match'>send<\/span><\/li>\n  <li><span class='match'>sense<\/span><\/li>\n  <li><span class='match'>separate<\/span><\/li>\n  <li><span class='match'>serious<\/span><\/li>\n  <li><span class='match'>serve<\/span><\/li>\n  <li><span class='match'>service<\/span><\/li>\n  <li><span class='match'>set<\/span><\/li>\n  <li><span class='match'>settle<\/span><\/li>\n  <li><span class='match'>seven<\/span><\/li>\n  <li><span class='match'>sex<\/span><\/li>\n  <li><span class='match'>shall<\/span><\/li>\n  <li><span class='match'>share<\/span><\/li>\n  <li><span class='match'>she<\/span><\/li>\n  <li><span class='match'>sheet<\/span><\/li>\n  <li><span class='match'>shoe<\/span><\/li>\n  <li><span class='match'>shoot<\/span><\/li>\n  <li><span class='match'>shop<\/span><\/li>\n  <li><span class='match'>short<\/span><\/li>\n  <li><span class='match'>should<\/span><\/li>\n  <li><span class='match'>show<\/span><\/li>\n  <li><span class='match'>shut<\/span><\/li>\n  <li><span class='match'>sick<\/span><\/li>\n  <li><span class='match'>side<\/span><\/li>\n  <li><span class='match'>sign<\/span><\/li>\n  <li><span class='match'>similar<\/span><\/li>\n  <li><span class='match'>simple<\/span><\/li>\n  <li><span class='match'>since<\/span><\/li>\n  <li><span class='match'>sing<\/span><\/li>\n  <li><span class='match'>single<\/span><\/li>\n  <li><span class='match'>sir<\/span><\/li>\n  <li><span class='match'>sister<\/span><\/li>\n  <li><span class='match'>sit<\/span><\/li>\n  <li><span class='match'>site<\/span><\/li>\n  <li><span class='match'>situate<\/span><\/li>\n  <li><span class='match'>six<\/span><\/li>\n  <li><span class='match'>size<\/span><\/li>\n  <li><span class='match'>sleep<\/span><\/li>\n  <li><span class='match'>slight<\/span><\/li>\n  <li><span class='match'>slow<\/span><\/li>\n  <li><span class='match'>small<\/span><\/li>\n  <li><span class='match'>smoke<\/span><\/li>\n  <li><span class='match'>so<\/span><\/li>\n  <li><span class='match'>social<\/span><\/li>\n  <li><span class='match'>society<\/span><\/li>\n  <li><span class='match'>some<\/span><\/li>\n  <li><span class='match'>son<\/span><\/li>\n  <li><span class='match'>soon<\/span><\/li>\n  <li><span class='match'>sorry<\/span><\/li>\n  <li><span class='match'>sort<\/span><\/li>\n  <li><span class='match'>sound<\/span><\/li>\n  <li><span class='match'>south<\/span><\/li>\n  <li><span class='match'>space<\/span><\/li>\n  <li><span class='match'>speak<\/span><\/li>\n  <li><span class='match'>special<\/span><\/li>\n  <li><span class='match'>specific<\/span><\/li>\n  <li><span class='match'>speed<\/span><\/li>\n  <li><span class='match'>spell<\/span><\/li>\n  <li><span class='match'>spend<\/span><\/li>\n  <li><span class='match'>square<\/span><\/li>\n  <li><span class='match'>staff<\/span><\/li>\n  <li><span class='match'>stage<\/span><\/li>\n  <li><span class='match'>stairs<\/span><\/li>\n  <li><span class='match'>stand<\/span><\/li>\n  <li><span class='match'>standard<\/span><\/li>\n  <li><span class='match'>start<\/span><\/li>\n  <li><span class='match'>state<\/span><\/li>\n  <li><span class='match'>station<\/span><\/li>\n  <li><span class='match'>stay<\/span><\/li>\n  <li><span class='match'>step<\/span><\/li>\n  <li><span class='match'>stick<\/span><\/li>\n  <li><span class='match'>still<\/span><\/li>\n  <li><span class='match'>stop<\/span><\/li>\n  <li><span class='match'>story<\/span><\/li>\n  <li><span class='match'>straight<\/span><\/li>\n  <li><span class='match'>strategy<\/span><\/li>\n  <li><span class='match'>street<\/span><\/li>\n  <li><span class='match'>strike<\/span><\/li>\n  <li><span class='match'>strong<\/span><\/li>\n  <li><span class='match'>structure<\/span><\/li>\n  <li><span class='match'>student<\/span><\/li>\n  <li><span class='match'>study<\/span><\/li>\n  <li><span class='match'>stuff<\/span><\/li>\n  <li><span class='match'>stupid<\/span><\/li>\n  <li><span class='match'>subject<\/span><\/li>\n  <li><span class='match'>succeed<\/span><\/li>\n  <li><span class='match'>such<\/span><\/li>\n  <li><span class='match'>sudden<\/span><\/li>\n  <li><span class='match'>suggest<\/span><\/li>\n  <li><span class='match'>suit<\/span><\/li>\n  <li><span class='match'>summer<\/span><\/li>\n  <li><span class='match'>sun<\/span><\/li>\n  <li><span class='match'>sunday<\/span><\/li>\n  <li><span class='match'>supply<\/span><\/li>\n  <li><span class='match'>support<\/span><\/li>\n  <li><span class='match'>suppose<\/span><\/li>\n  <li><span class='match'>sure<\/span><\/li>\n  <li><span class='match'>surprise<\/span><\/li>\n  <li><span class='match'>switch<\/span><\/li>\n  <li><span class='match'>system<\/span><\/li>\n  <li><span class='match'>table<\/span><\/li>\n  <li><span class='match'>take<\/span><\/li>\n  <li><span class='match'>talk<\/span><\/li>\n  <li><span class='match'>tape<\/span><\/li>\n  <li><span class='match'>tax<\/span><\/li>\n  <li><span class='match'>tea<\/span><\/li>\n  <li><span class='match'>teach<\/span><\/li>\n  <li><span class='match'>team<\/span><\/li>\n  <li><span class='match'>telephone<\/span><\/li>\n  <li><span class='match'>television<\/span><\/li>\n  <li><span class='match'>tell<\/span><\/li>\n  <li><span class='match'>ten<\/span><\/li>\n  <li><span class='match'>tend<\/span><\/li>\n  <li><span class='match'>term<\/span><\/li>\n  <li><span class='match'>terrible<\/span><\/li>\n  <li><span class='match'>test<\/span><\/li>\n  <li><span class='match'>than<\/span><\/li>\n  <li><span class='match'>thank<\/span><\/li>\n  <li><span class='match'>the<\/span><\/li>\n  <li><span class='match'>then<\/span><\/li>\n  <li><span class='match'>there<\/span><\/li>\n  <li><span class='match'>therefore<\/span><\/li>\n  <li><span class='match'>they<\/span><\/li>\n  <li><span class='match'>thing<\/span><\/li>\n  <li><span class='match'>think<\/span><\/li>\n  <li><span class='match'>thirteen<\/span><\/li>\n  <li><span class='match'>thirty<\/span><\/li>\n  <li><span class='match'>this<\/span><\/li>\n  <li><span class='match'>thou<\/span><\/li>\n  <li><span class='match'>though<\/span><\/li>\n  <li><span class='match'>thousand<\/span><\/li>\n  <li><span class='match'>three<\/span><\/li>\n  <li><span class='match'>through<\/span><\/li>\n  <li><span class='match'>throw<\/span><\/li>\n  <li><span class='match'>thursday<\/span><\/li>\n  <li><span class='match'>tie<\/span><\/li>\n  <li><span class='match'>time<\/span><\/li>\n  <li><span class='match'>to<\/span><\/li>\n  <li><span class='match'>today<\/span><\/li>\n  <li><span class='match'>together<\/span><\/li>\n  <li><span class='match'>tomorrow<\/span><\/li>\n  <li><span class='match'>tonight<\/span><\/li>\n  <li><span class='match'>too<\/span><\/li>\n  <li><span class='match'>top<\/span><\/li>\n  <li><span class='match'>total<\/span><\/li>\n  <li><span class='match'>touch<\/span><\/li>\n  <li><span class='match'>toward<\/span><\/li>\n  <li><span class='match'>town<\/span><\/li>\n  <li><span class='match'>trade<\/span><\/li>\n  <li><span class='match'>traffic<\/span><\/li>\n  <li><span class='match'>train<\/span><\/li>\n  <li><span class='match'>transport<\/span><\/li>\n  <li><span class='match'>travel<\/span><\/li>\n  <li><span class='match'>treat<\/span><\/li>\n  <li><span class='match'>tree<\/span><\/li>\n  <li><span class='match'>trouble<\/span><\/li>\n  <li><span class='match'>true<\/span><\/li>\n  <li><span class='match'>trust<\/span><\/li>\n  <li><span class='match'>try<\/span><\/li>\n  <li><span class='match'>tuesday<\/span><\/li>\n  <li><span class='match'>turn<\/span><\/li>\n  <li><span class='match'>twelve<\/span><\/li>\n  <li><span class='match'>twenty<\/span><\/li>\n  <li><span class='match'>two<\/span><\/li>\n  <li><span class='match'>type<\/span><\/li>\n  <li><span class='match'>under<\/span><\/li>\n  <li><span class='match'>understand<\/span><\/li>\n  <li><span class='match'>union<\/span><\/li>\n  <li><span class='match'>unit<\/span><\/li>\n  <li><span class='match'>unite<\/span><\/li>\n  <li><span class='match'>university<\/span><\/li>\n  <li><span class='match'>unless<\/span><\/li>\n  <li><span class='match'>until<\/span><\/li>\n  <li><span class='match'>up<\/span><\/li>\n  <li><span class='match'>upon<\/span><\/li>\n  <li><span class='match'>use<\/span><\/li>\n  <li><span class='match'>usual<\/span><\/li>\n  <li><span class='match'>value<\/span><\/li>\n  <li><span class='match'>various<\/span><\/li>\n  <li><span class='match'>very<\/span><\/li>\n  <li><span class='match'>video<\/span><\/li>\n  <li><span class='match'>view<\/span><\/li>\n  <li><span class='match'>village<\/span><\/li>\n  <li><span class='match'>visit<\/span><\/li>\n  <li><span class='match'>vote<\/span><\/li>\n  <li><span class='match'>wage<\/span><\/li>\n  <li><span class='match'>wait<\/span><\/li>\n  <li><span class='match'>walk<\/span><\/li>\n  <li><span class='match'>wall<\/span><\/li>\n  <li><span class='match'>want<\/span><\/li>\n  <li><span class='match'>war<\/span><\/li>\n  <li><span class='match'>warm<\/span><\/li>\n  <li><span class='match'>wash<\/span><\/li>\n  <li><span class='match'>waste<\/span><\/li>\n  <li><span class='match'>watch<\/span><\/li>\n  <li><span class='match'>water<\/span><\/li>\n  <li><span class='match'>way<\/span><\/li>\n  <li><span class='match'>we<\/span><\/li>\n  <li><span class='match'>wear<\/span><\/li>\n  <li><span class='match'>wednesday<\/span><\/li>\n  <li><span class='match'>wee<\/span><\/li>\n  <li><span class='match'>week<\/span><\/li>\n  <li><span class='match'>weigh<\/span><\/li>\n  <li><span class='match'>welcome<\/span><\/li>\n  <li><span class='match'>well<\/span><\/li>\n  <li><span class='match'>west<\/span><\/li>\n  <li><span class='match'>what<\/span><\/li>\n  <li><span class='match'>when<\/span><\/li>\n  <li><span class='match'>where<\/span><\/li>\n  <li><span class='match'>whether<\/span><\/li>\n  <li><span class='match'>which<\/span><\/li>\n  <li><span class='match'>while<\/span><\/li>\n  <li><span class='match'>white<\/span><\/li>\n  <li><span class='match'>who<\/span><\/li>\n  <li><span class='match'>whole<\/span><\/li>\n  <li><span class='match'>why<\/span><\/li>\n  <li><span class='match'>wide<\/span><\/li>\n  <li><span class='match'>wife<\/span><\/li>\n  <li><span class='match'>will<\/span><\/li>\n  <li><span class='match'>win<\/span><\/li>\n  <li><span class='match'>wind<\/span><\/li>\n  <li><span class='match'>window<\/span><\/li>\n  <li><span class='match'>wish<\/span><\/li>\n  <li><span class='match'>with<\/span><\/li>\n  <li><span class='match'>within<\/span><\/li>\n  <li><span class='match'>without<\/span><\/li>\n  <li><span class='match'>woman<\/span><\/li>\n  <li><span class='match'>wonder<\/span><\/li>\n  <li><span class='match'>wood<\/span><\/li>\n  <li><span class='match'>word<\/span><\/li>\n  <li><span class='match'>work<\/span><\/li>\n  <li><span class='match'>world<\/span><\/li>\n  <li><span class='match'>worry<\/span><\/li>\n  <li><span class='match'>worse<\/span><\/li>\n  <li><span class='match'>worth<\/span><\/li>\n  <li><span class='match'>would<\/span><\/li>\n  <li><span class='match'>write<\/span><\/li>\n  <li><span class='match'>wrong<\/span><\/li>\n  <li><span class='match'>year<\/span><\/li>\n  <li><span class='match'>yes<\/span><\/li>\n  <li><span class='match'>yesterday<\/span><\/li>\n  <li><span class='match'>yet<\/span><\/li>\n  <li><span class='match'>you<\/span><\/li>\n  <li><span class='match'>young<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
str_view("hello world, {hello world} hello world", "\\{.+\\}")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-4d89bfe3ab4148009ac2">{"x":{"html":"<ul>\n  <li>hello world, <span class='match'>{hello world}<\/span> hello world<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
str_view("5555-55-55asdf", "\\d{4}-\\d{2}-\\d{2}")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-867f444d0be65a8309d2">{"x":{"html":"<ul>\n  <li><span class='match'>5555-55-55<\/span>asdf<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
str_view("\\\\\\\\asdf", "\\\\{4}")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-1858dd2e6df01a354e1f">{"x":{"html":"<ul>\n  <li><span class='match'>\\\\\\\\<\/span>asdf<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### 3. Create regular expressions to find all words that:

-   Start with three consonants.
-   Have three or more vowels in a row.
-   Have two or more vowel-consonant pairs in a row.

``` r
# three consonants
str_view("street", '[^aeiou]{3}')
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-571130effefe1933f727">{"x":{"html":"<ul>\n  <li><span class='match'>str<\/span>eet<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
#three or more vowels in a row:
str_view("streeet", '[aeiou]{3,}')
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-f2c8d3592115ed0b4add">{"x":{"html":"<ul>\n  <li>str<span class='match'>eee<\/span>t<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# two or more vowel-consonant pairs in a row:
str_view("streettttt", '([aeiou][^aeiou]|[^aeiou][aeiou]){2,}')
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-e3a18b3c98358724b78a">{"x":{"html":"<ul>\n  <li>st<span class='match'>reet<\/span>tttt<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view("woohooo!", "(.)\\1\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-4a1e7313b754812e11a1">{"x":{"html":"<ul>\n  <li>wooh<span class='match'>ooo<\/span>!<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
-   `"(.)(.)\\2\\1"`

This will match a pair of characters followed by the reverse of the pair. For example, "elle" in "belle".

``` r
str_view("belle", "(.)(.)\\2\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-21bdc6c5f45e1f560534">{"x":{"html":"<ul>\n  <li>b<span class='match'>elle<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
-   `(..)\1`

This will match any two characters repeated twice. For example, "caca" in "cacao beans".

``` r
str_view("cacao beans", "(..)\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-1d5de2e13595166fe21b">{"x":{"html":"<ul>\n  <li><span class='match'>caca<\/span>o beans<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
-   `"(.).\\1.\\1"`

This matches any character repeated every other character, such as "lulul" in "lululemon".

``` r
str_view("lululemon", "(.).\\1.\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-bd60e28cb7c2b5043c00">{"x":{"html":"<ul>\n  <li><span class='match'>lulul<\/span>emon<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
-   `"(.)(.)(.).*\\3\\2\\1"`

This matches 3 characters followed by any number of characters followed by the first 3 characters in reverse, such as "but the tub" in "but the tub is full".

``` r
str_view("but the tub is full", "(.)(.)(.).*\\3\\2\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-2b93f36160742ee0d4b5">{"x":{"html":"<ul>\n  <li><span class='match'>but the tub<\/span> is full<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
### 2. Construct regular expressions to match words that:

-   Start and end with the same character.

`"^(.).*\\1$"` performs this function. The ^ anchor specifies that any character (.) must also be present at the end of the string using the `\$` anchor after the backreference. Below, it will match "regular" but not "expression".

``` r
str_view(c("regular","expressions", "a", "aa"), "^(.).*\\1$")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-9cb026da64b1ed27c39f">{"x":{"html":"<ul>\n  <li><span class='match'>regular<\/span><\/li>\n  <li>expressions<\/li>\n  <li>a<\/li>\n  <li><span class='match'>aa<\/span><\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
-   Contain a repeated pair of letters (e.g. “church” contains “ch” repeated twice.)

`"(..).*\\1"` will work by specifying that any two characters (..) can be separated by any amount of characters `.*` followed by the same two characters using a backreference.

``` r
str_view(c("church", "no repeats", "papaya"), "(..).*\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-e26df960a27c39aa9403">{"x":{"html":"<ul>\n  <li><span class='match'>church<\/span><\/li>\n  <li>no repeats<\/li>\n  <li><span class='match'>papa<\/span>ya<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
-   Contain one letter repeated in at least three places (e.g. “eleven” contains three “e”s.)

`"(.).*\\1.*\\1"` would work for single word strings. If there is a sentence with multiple words, this regex would match the first occurance of 3 characters, including whitespace, even if they were in different words. If we don't want this to happen, we would use a "anything except whitespace" instead of `.*`. This would be a regexp for that purpose: `"([^\\s])[^\\s]*\\1[^\\s]*\\1"`. We could also use the boundary regexp `\\b`.

``` r
# this only works for individual words
str_view(c("eleven", "apply", "papaya", "bananas", "will this match spaces?","letters next"), "(.).*\\1.*\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-3fd1f00b371473776d91">{"x":{"html":"<ul>\n  <li><span class='match'>eleve<\/span>n<\/li>\n  <li>apply<\/li>\n  <li>p<span class='match'>apaya<\/span><\/li>\n  <li>b<span class='match'>anana<\/span>s<\/li>\n  <li>will<span class='match'> this match <\/span>spaces?<\/li>\n  <li>l<span class='match'>etters ne<\/span>xt<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
``` r
# this will exclude whitespace and only match if a word within a sentence has a letter repeated 3 times.
str_view(c("eleven", "apply", "papaya", "bananas", "will this match spaces?", "letters next"), "([^\\s])[^\\s]*\\1[^\\s]*\\1")
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-8f117dc42aa52c308d66">{"x":{"html":"<ul>\n  <li><span class='match'>eleve<\/span>n<\/li>\n  <li>apply<\/li>\n  <li>p<span class='match'>apaya<\/span><\/li>\n  <li>b<span class='match'>anana<\/span>s<\/li>\n  <li>will this match spaces?<\/li>\n  <li>letters next<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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
str_view_all(more, colour_match)
```

<!--html_preserve-->

<script type="application/json" data-for="htmlwidget-519310013e077623d762">{"x":{"html":"<ul>\n  <li>It is hard to erase<span class='match'> blue <\/span>or<span class='match'> red <\/span>ink.<\/li>\n<\/ul>"},"evals":[],"jsHooks":[]}</script>
<!--/html_preserve-->
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

    ## [1] "UpmkjLV2Bx" "OKD2p561Ta" "vBUHRFggIx" "2baErU5Gb2" "43qIwBmqi5"

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
