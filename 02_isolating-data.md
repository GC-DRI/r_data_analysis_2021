Isolating Data
================

[\<\<\< Previous](data-wrangling.md) | [Next \>\>\>](03_piping.md)

-----

This type of task occurs often in Data Science: you need to extract data
from a table before you can use it. You can do this task quickly with
three functions that come in the dplyr package:

1.  **select()** - which extracts columns from a data frame
2.  **filter()** - which extracts rows from a data frame

Each function takes a data frame or tibble as it’s first argument and
returns a new data frame or tibble as its output.

## select()

[![Source: dplyr
cheatsheet](images/select.png)](https://rstudio.com/resources/cheatsheets/)

`select()` extracts columns of a data frame and returns the columns as a
new data frame. To use `select()`, pass it the name of a data frame to
extract columns from, and then the names of the columns to extract. The
column names do not need to appear in quotation marks or be prefixed
with a `$`; `select()` knows to find them in the data frame that you
supply.

**Exercise 1**

Use the example below to get a feel for `select()`. Can you extract just
the `genre` column? How about the `genre` and `energy` columns?

``` r
select(spotify, loudness)
```

    ## # A tibble: 26,000 x 1
    ##    loudness
    ##       <dbl>
    ##  1    -8.51
    ##  2    -4.75
    ##  3    -6.09
    ##  4    -5.06
    ##  5    -5.10
    ##  6    -3.64
    ##  7    -5.94
    ##  8    -4.43
    ##  9    -4.47
    ## 10    -5.74
    ## # … with 25,990 more rows

-----

### select() helpers

You can also use a series of helpers with `select()`. For example, if
you place a minus sign before a column name, `select()` will return
every column but that column. The code below will return every column
except lyrics.

``` r
select(spotify, -lyrics)
```

The table below summarizes the other `select()` helpers that are
available in dplyr. This is here for your reference and to demonstrate
the flexibility of `select()`

| Helper Function    | Use                                          | Example                                         |
| ------------------ | -------------------------------------------- | ----------------------------------------------- |
| **-**              | Columns except                               | `select(spotify, -loudness)`                    |
| **:**              | Columns between (inclusive)                  | `select(spotify, loudness:danceability)`        |
| **contains()**     | Columns that contains a string               | `select(spotify, contains("d"))`                |
| **ends\_with()**   | Columns that ends with a string              | `select(spotify, ends_with("y"))`               |
| **matches()**      | Columns that matches a regex                 | `select(spotify, matches("n"))`                 |
| **num\_range()**   | Columns with a numerical suffix in the range | Not applicable with `spotify`                   |
| **one\_of()**      | Columns whose name appear in the given set   | `select(spotify, one_of(c("energy", "tempo")))` |
| **starts\_with()** | Columns that starts with a string            | `select(spotify, starts_with("e"))`             |

**Exercise 2**  
Use the `contains()` helper function to select the columns that contain
the letter “n”. The structure should look like this: `select(spotify,
contains("d"))`. What variables are returned?

-----

### Go further

The `tidyr` package allows you to reshape your entire data frame, rather
than perform simple operations with columns. It’s super helpful if you
need to, for instance, convert your *wide* data frame to a *long*
(i.e. *tidy*) data frame, or convert your nested JSON data to a tidy
data frame. For a short introduction, [check this
out](https://tidyr.tidyverse.org/)

-----

## filter()

[![Source: dplyr
cheatsheet](images/filter.png)](https://rstudio.com/resources/cheatsheets/)

`filter()` extracts rows from a data frame and returns them as a new
data frame. As with `select()`, the first argument of `filter()` should
be a data frame to extract rows from. The arguments that follow should
be logical tests; `filter()` will return every row for which the tests
return `TRUE`.

### Filter in action

For example, the code chunk below returns every row with the genre
“Alternative” in `spotify`.

``` r
filter(spotify, genre == "Alternative")
```

    ## # A tibble: 1,000 x 6
    ##    genre    energy loudness tempo danceability lyrics                           
    ##    <chr>     <dbl>    <dbl> <dbl>        <dbl> <chr>                            
    ##  1 Alterna…  0.647    -8.51  79.8        0.709 into into into of him by economi…
    ##  2 Alterna…  0.735    -4.75 163.         0.436 paying fishing realizes Johnson …
    ##  3 Alterna…  0.917    -6.09 141.         0.544 man has happened then to his by …
    ##  4 Alterna…  0.606    -5.06  93.1        0.603 realizes him Johnson I flees his…
    ##  5 Alterna…  0.641    -5.10 142.         0.487 she he his by before then depict…
    ##  6 Alterna…  0.973    -3.64 126.         0.524 she mainland Mark Mr. depicts ec…
    ##  7 Alterna…  0.919    -5.94 108.         0.657 by charter forced into who is aw…
    ##  8 Alterna…  0.889    -4.43 132.         0.611 by forced any Mark slipping main…
    ##  9 Alterna…  0.761    -4.47 119.         0.675 Mark she mainland without Johnso…
    ## 10 Alterna…  0.611    -5.74  79.6        0.584 away airplane I his his she man …
    ## # … with 990 more rows

### Logical tests

To get the most from filter, you will need to know how to use R’s
logical test operators, which are summarised below.

| Logical operator | tests                                | Example     |
| ---------------- | ------------------------------------ | ----------- |
| **\>**           | Is `x` greater than `y`?             | `x > y`     |
| **\>=**          | Is `x` greater than or equal to `y`? | `x >= y`    |
| **\<**           | Is `x` less than `y`?                | `x < y`     |
| **\<=**          | Is `x` less than or equal to `y`?    | `x <= y`    |
| **==**           | Is `x` equal to `y`?                 | `x == y`    |
| **\!=**          | Is `x` not equal to `y`?             | `x != y`    |
| **is.na()**      | Is `x` an `NA`?                      | `is.na(x)`  |
| **\!is.na()**    | Is `x` not an `NA`?                  | `!is.na(x)` |

-----

**Exercise 3**

See if you can use the logical operators to manipulate our code below to
show:

  - All of the songs in the “Rap” genre
  - All of the songs where tempo is less than or equal to 80
  - *Stretch challenge*. All songs that have more than 40 characters in
    their lyrics. Hint: you will need to use the `nchar()` function.

<!-- end list -->

``` r
filter(spotify, genre == "Ska")
```

-----

### Two common mistakes

When you use logical tests, be sure to look out for two common mistakes.
One appears in each code chunk below. Can you find them? When you spot a
mistake, fix it and then run the chunk to confirm that it works.

``` r
filter(spotify, genre = "Ska")
```

``` r
filter(spotify, genre == Ska)
```

**Answer**

``` r
filter(spotify, genre == "Ska")
```

    ## # A tibble: 1,000 x 6
    ##    genre energy loudness tempo danceability lyrics                              
    ##    <chr>  <dbl>    <dbl> <dbl>        <dbl> <chr>                               
    ##  1 Ska    0.97     -4.80 118.         0.419 tricks airplane man tricks I the Ha…
    ##  2 Ska    0.935    -4.16  95.5        0.436 into essentially he initially fishi…
    ##  3 Ska    0.988    -5.63 168.         0.452 Brownstone he what Brownstone who h…
    ##  4 Ska    0.95     -4.49 111.         0.435 Brownstone depicts is mainland depi…
    ##  5 Ska    0.994    -6.18  98.7        0.48  what tricks what money beyond novel…
    ##  6 Ska    0.949    -4.04 167.         0.529 him Brownstone slipping slipping an…
    ##  7 Ska    0.973    -3.22 105.         0.27  fishing depicts happened I by him c…
    ##  8 Ska    0.966    -4.66  98.8        0.473 fishing economic blackmarket depict…
    ##  9 Ska    0.588    -8.43  87.7        0.841 initially by charter forces man goo…
    ## 10 Ska    0.703   -11.4  136.         0.679 Mark slipping is any mainland by wh…
    ## # … with 990 more rows

### Two mistakes - Recap

When you use logical tests, be sure to look out for these two common
mistakes:

1.  using `=` instead of `==` to test for equality.
2.  forgetting to use quotation marks when comparing strings,
    e.g. `genre == Ska`, instead of `genre == "Ska"`

### Boolean operators

You can find a complete list of base R’s boolean operators in the table
below. This is here for your reference.

| Boolean operator | represents                                 | Example             |
| ---------------- | ------------------------------------------ | ------------------- |
| **&**            | Are *both* `A` and `B` true?               | `A & B`             |
| `\|`             | Are *one or both* of `A` and `B` true?     | `A \| B`            |
| **\!**           | Is `A` *not* true?                         | `!A`                |
| **xor()**        | Is *one and only one* of `A` and `B` true? | `xor(A, B)`         |
| **%in%**         | Is `x` in the set of `a`, `b`, and `c`?    | `x %in% c(a, b, c)` |
| **any()**        | Are *any* of `A`, `B`, or `C` true?        | `any(A, B, C)`      |
| **all()**        | Are *all* of `A`, `B`, or `C` true?        | `all(A, B, C)`      |

These allow you to customize filtered searches according to a variety of
criteria. We’re not going to go into depth about all of these here. The
basic Boolean operators we will focus on today are **&** and `|` (or).

If you provide more than one test to `filter()`, `filter()` will combine
the tests with an **and** statement (`&`): it will only return the rows
that satisfy all of the tests.

For instance,

``` r
filter(spotify, genre == "Alternative", tempo > 80)
```

will only return songs that are classified as “Alternative” **and** are
faster than 80 beats per minute. Run this code and see how many
observations are returned.

Now, try this.

``` r
filter(spotify, genre == "Alternative" | tempo > 80)
```

Many more observations are returned because the function is filtering
for songs that are classified as “Alternative” **or** have a tempo
greater than 80 beats per minute. A song only has to satisfy one of the
criteria to pass the filter.

### Go further

The `stringr` package is very helpful for manipulating strings in R.
It’s part of the core tidyverse set of packages, so it is easy to
integrate into your data wrangling pipeline. It takes some of the pain
away from forming your own `regex` expressions, but is flexible enough
to accomodate complex cases. I use `stringr` regularly within `filter()`
and other data manipulation functions. If you’re interested, check out
[this light introduction](https://stringr.tidyverse.org/).

-----

## Answers

**Exercise 1**

``` r
select(spotify, genre)
select(spotify, genre, energy)
```

**Exercise 2**

``` r
spotify %>% select(contains("n"))
```

    ## # A tibble: 26,000 x 4
    ##    genre       energy loudness danceability
    ##    <chr>        <dbl>    <dbl>        <dbl>
    ##  1 Alternative  0.647    -8.51        0.709
    ##  2 Alternative  0.735    -4.75        0.436
    ##  3 Alternative  0.917    -6.09        0.544
    ##  4 Alternative  0.606    -5.06        0.603
    ##  5 Alternative  0.641    -5.10        0.487
    ##  6 Alternative  0.973    -3.64        0.524
    ##  7 Alternative  0.919    -5.94        0.657
    ##  8 Alternative  0.889    -4.43        0.611
    ##  9 Alternative  0.761    -4.47        0.675
    ## 10 Alternative  0.611    -5.74        0.584
    ## # … with 25,990 more rows

**Exercise 3**

``` r
filter(spotify, genre == "Rap")
filter(spotify, tempo <= 80)
filter(spotify, nchar(lyrics) > 40)
```

-----

[\<\<\< Previous](data-wrangling.md) | [Next \>\>\>](03_piping.md)
