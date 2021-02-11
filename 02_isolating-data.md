Isolating Data
================

[\<\<\< Previous](data-wrangling.md) | [Next \>\>\>](03_piping.md)

-----

This type of task occurs often in Data Science: you need to extract data
from a table before you can use it. You can do this task quickly with
two functions that come in the `dplyr` package:

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

A typical `select()` call looks like this:  
`select(<data>, <variables>)`

If you want to select the `loudness` variable, you run this:

``` r
select(spotify, loudness)
```

    ## # A tibble: 787 x 1
    ##    loudness
    ##       <dbl>
    ##  1    -9.66
    ##  2    -5.63
    ##  3    -6.36
    ##  4    -7.43
    ##  5    -9.02
    ##  6    -8.90
    ##  7    -5.98
    ##  8    -7.65
    ##  9    -7.74
    ## 10   -10.8 
    ## # … with 777 more rows

Notice how it returned a data frame of the `loudness` variable, rather
than a list or vector\!

If you want to extract more than one variable you can include them,
separated by commas, as arguments to the function:  
`select(spotify, loudness, instrumentalness, key)`

Or, you can provide the variables as a vector\! They will return the
same thing.

``` r
select(spotify, c(loudness, instrumentalness, key))
```

    ## # A tibble: 787 x 3
    ##    loudness instrumentalness   key
    ##       <dbl>            <dbl> <dbl>
    ##  1    -9.66       0.386          2
    ##  2    -5.63       0              1
    ##  3    -6.36       0              7
    ##  4    -7.43       0              7
    ##  5    -9.02       0.0136         2
    ##  6    -8.90       0.0000257      4
    ##  7    -5.98       0.00046        0
    ##  8    -7.65       0              0
    ##  9    -7.74       0.00000431     1
    ## 10   -10.8        0.0000393      4
    ## # … with 777 more rows

A third option, in case you have many variables or aren’t sure of the
variable’s exact names, is to use `select helpers`.

-----

### select() helpers

You can also use a series of helpers with `select()`. For example, if
you place a minus sign before a column name, `select()` will return
every column but that column. The code below will return every column
except lyrics.

``` r
select(spotify, -lyrics)
```

The table below summarizes most of the other `select()` helpers that are
available in `dplyr`. This is here for your reference and to demonstrate
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

**Exercise 1**

Use the example below to get a feel for `select()`. Can you extract just
the `artist` column? How about the `artist` and `energy` columns?

``` r
select(spotify, loudness)
```

    ## # A tibble: 787 x 1
    ##    loudness
    ##       <dbl>
    ##  1    -9.66
    ##  2    -5.63
    ##  3    -6.36
    ##  4    -7.43
    ##  5    -9.02
    ##  6    -8.90
    ##  7    -5.98
    ##  8    -7.65
    ##  9    -7.74
    ## 10   -10.8 
    ## # … with 777 more rows

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

For example, the code chunk below returns every track with a 5/4 time
signature in `spotify`. **7** has been on the charts for a while\! Also,
I’m happy to see my man John Prine switching up his time signatures too.

``` r
filter(spotify, time_signature == 5)
```

    ## # A tibble: 4 x 17
    ##    rank track artist time_period danceability energy   key loudness  mode
    ##   <dbl> <chr> <chr>  <chr>              <dbl>  <dbl> <dbl>    <dbl> <dbl>
    ## 1    57 7 (E… Lil N… early              0.697  0.472     8    -12.0     1
    ## 2    83 7 (E… Lil N… first              0.697  0.472     8    -12.0     1
    ## 3   114 The … John … first              0.584  0.317     2    -13.5     1
    ## 4    45 7 (E… Lil N… current            0.697  0.472     8    -12.0     1
    ## # … with 8 more variables: speechiness <dbl>, acousticness <dbl>,
    ## #   instrumentalness <dbl>, liveness <dbl>, valence <dbl>, tempo <dbl>,
    ## #   duration_ms <dbl>, time_signature <dbl>

### Logical tests

To get the most from filter, you will need to know how to use R’s
logical test operators, which are summarized below.

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

Let’s look at another example before moving on to exercises. Tracks with
an `instrumentalness` value greater than 0.5 are considered to likely be
instrumental, according to the [metadata](data/meta.html). Let’s see
which tracks are instrumental\!

``` r
filter(spotify, instrumentalness > 0.5)
```

    ## # A tibble: 55 x 17
    ##     rank track artist time_period danceability energy   key loudness  mode
    ##    <dbl> <chr> <chr>  <chr>              <dbl>  <dbl> <dbl>    <dbl> <dbl>
    ##  1    13 ASTR… Travi… pre                0.784 0.539      6   -10.0      0
    ##  2    25 Baby… DaBaby pre                0.324 0.532      1   -34.8      1
    ##  3    34 Whil… Kehla… pre                0.622 0.347      9   -15.9      0
    ##  4    39 Hami… Origi… pre                0.667 0.322      4   -19.3      0
    ##  5    66 Cult… Migos  pre                0.613 0.647      0    -6.68     0
    ##  6    67 This… Gary … pre                0.553 0.0252     0   -26.3      1
    ##  7    93 More… Drake  pre                0.707 0.258     11   -10.7      0
    ##  8    99 Perc… NF     pre                0.243 0.893      3    -4.82     0
    ##  9   116 Nigh… Imagi… pre                0.808 0.441      7   -13.4      0
    ## 10   125 BALL… Joji   pre                0.404 0.183      7   -19.0      1
    ## # … with 45 more rows, and 8 more variables: speechiness <dbl>,
    ## #   acousticness <dbl>, instrumentalness <dbl>, liveness <dbl>, valence <dbl>,
    ## #   tempo <dbl>, duration_ms <dbl>, time_signature <dbl>

Huh, it seems like this isn’t a strict enough filter. There are quite a
few tracks that definitely have words in them here. Let’s increase the
filter to 0.8 and see where that gets us.

``` r
filter(spotify, instrumentalness > 0.8)
```

    ## # A tibble: 33 x 17
    ##     rank track artist time_period danceability  energy   key loudness  mode
    ##    <dbl> <chr> <chr>  <chr>              <dbl>   <dbl> <dbl>    <dbl> <dbl>
    ##  1    25 Baby… DaBaby pre               0.324  0.532       1    -34.8     1
    ##  2    34 Whil… Kehla… pre               0.622  0.347       9    -15.9     0
    ##  3    39 Hami… Origi… pre               0.667  0.322       4    -19.3     0
    ##  4    67 This… Gary … pre               0.553  0.0252      0    -26.3     1
    ##  5   116 Nigh… Imagi… pre               0.808  0.441       7    -13.4     0
    ##  6   125 BALL… Joji   pre               0.404  0.183       7    -19.0     1
    ##  7   165 Delta Mumfo… pre               0.0757 0.00429     2    -38.5     1
    ##  8   176 NOW … Vario… pre               0.466  0.531       6    -10.1     0
    ##  9   182 Simo… Simon… pre               0.839  0.392       0    -12.1     1
    ## 10   197 Illu… Shawn… pre               0      0.00983    10    -28.3     0
    ## # … with 23 more rows, and 8 more variables: speechiness <dbl>,
    ## #   acousticness <dbl>, instrumentalness <dbl>, liveness <dbl>, valence <dbl>,
    ## #   tempo <dbl>, duration_ms <dbl>, time_signature <dbl>

Jeez Spotify, I don’t think `instrumentalness` is a very accurate
metric\! Or something may have gone wrong when I was mining the data…
This goes to show- exploring your data beforehand will help you catch
errors early so they don’t make it into your analyses\!

**Exercise 3**

See if you can use the logical operators to manipulate our code below to
show:

  - All of the songs in the “current” `time_period`
  - All of the songs where `tempo` is less than or equal to 80
  - *Stretch challenge*. All songs that have more than 20 characters in
    their title. Hint: you will need to use the `nchar()` function.

<!-- end list -->

``` r
filter(spotify, time_period == "pre")
```

-----

### Two common mistakes

When you use logical tests, be sure to look out for two common mistakes.
One appears in each code chunk below. Can you find them? When you spot a
mistake, fix it and then run the chunk to confirm that it works.

``` r
filter(spotify, artist = "DaBaby")
```

``` r
filter(spotify, artist == DaBaby)
```

**Answer**

``` r
filter(spotify, artist == "DaBaby")
```

    ## # A tibble: 7 x 17
    ##    rank track artist time_period danceability energy   key loudness  mode
    ##   <dbl> <chr> <chr>  <chr>              <dbl>  <dbl> <dbl>    <dbl> <dbl>
    ## 1    25 Baby… DaBaby pre                0.324  0.532     1   -34.8      1
    ## 2    20 KIRK  DaBaby early              0.451  0.632     8    -7.98     0
    ## 3    66 Baby… DaBaby early              0.324  0.532     1   -34.8      1
    ## 4    32 KIRK  DaBaby first              0.451  0.632     8    -7.98     0
    ## 5   115 Baby… DaBaby first              0.324  0.532     1   -34.8      1
    ## 6     8 KIRK  DaBaby current            0.451  0.632     8    -7.98     0
    ## 7    51 Baby… DaBaby current            0.324  0.532     1   -34.8      1
    ## # … with 8 more variables: speechiness <dbl>, acousticness <dbl>,
    ## #   instrumentalness <dbl>, liveness <dbl>, valence <dbl>, tempo <dbl>,
    ## #   duration_ms <dbl>, time_signature <dbl>

### Two mistakes - Recap

When you use logical tests, be sure to look out for these two common
mistakes:

1.  using `=` instead of `==` to test for equality.
2.  forgetting to use quotation marks when comparing strings,
    e.g. `artist == DaBaby`, instead of `genre == "DaBaby"`

### Go further: Boolean operators

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
filter(spotify, mode == 0, tempo > 80)
```

will only return songs that are in a minor key **and** are faster than
80 beats per minute. Run this code and see how many observations are
returned.

Now, try this.

``` r
filter(spotify, mode == 0 | tempo > 80)
```

Many more observations are returned because the function is filtering
for songs that are in a minor key **or** have a tempo greater than 80
beats per minute. A song only has to satisfy one of the criteria to pass
the filter.

-----

### Go further: stringr

The `stringr` package is very helpful for manipulating strings in R.
It’s part of the core tidyverse set of packages, so it is easy to
integrate into your data wrangling pipeline. It takes some of the pain
away from forming your own `regex` expressions, but is flexible enough
to accomodate complex cases. I use `stringr` regularly within `filter()`
and other data manipulation functions. If you’re interested, check out
[this light introduction](https://stringr.tidyverse.org/).

-----

## Visualizing Distributions

We’re going to kick off the data visualization segment with an
introduction to the `ggplot2` package and a simple, but important task-
visualizing distributions.

### Plotting with ggplot2

`ggplot2` is a plotting package that makes it simple to create complex
plots from data in a data frame. It stands for “grammar of graphics
plot” and the 2 comes from its total redesign after the creator
[Hadley Wickham](http://hadley.nz/) scrapped the original package. The
[grammar of
graphics](https://vita.had.co.nz/papers/layered-grammar.html) is a
useful framework to describe the components of a graphic. It provides a
more programmatic interface for specifying what variables to plot, how
they are displayed, and general visual properties. Therefore, we only
need minimal changes if the underlying data change or if we decide to
change from a bar plot to a scatter plot. This gives us a lot of
customization flexibility with reasonable defaults.

In practical terms,

  - `ggplot2` functions like tidy data. Well-structured data will save
    you lots of time when making figures.

  - `ggplot2` graphics are built step by step by adding new elements.
    Adding layers in this fashion allows for extensive flexibility and
    customization of plots.

### The basics

To build a ggplot, we will use the following basic template that can be
used for different types of plots:

`ggplot(data = < DATA > , aes( < MAPPINGS > )) + geom_FUNCTION()`

*data* Bind the plot to a specific data frame using the data argument

*aes()* Use the aes() (aesthetics) function to select the variables to
be plotted and specify how to present them in the graph, e.g. as x/y
positions or characteristics such as size, shape, color, etc.

*geom* Add ‘geoms’ – graphical representations of the data in the plot
(points, lines, bars). Here are some examples:

`geom_point()` for scatter plots, dot plots, etc. `geom_smooth()` for
trendlines `geom_boxplot()` for boxplots `geom_histogram()` for
histograms

Plot components are tied together using the `+` operator. You can add
`geom_*`s, for instance plotting both a scatterplot and a trendline:

`ggplot() + geom_point() + geom_smooth()`,

or add components that customize the plot- we will get to this part
later.

Let’s build a plot, piece by piece\!

### Building a plot

Visualizing the distribution of your data is one of the most fundamental
methods of data exploration. We’ll learn the fundamentals of `ggplot2`
while making a fundamental plot\!

Every plot begins with a call to `ggplot()` (no **2**\! That’s only in
the package name). First, we have to specify the data with the `data =`
argument. In addition, we need to specify what variables we’re plotting
and which axes we’re plotting them on. These are considered plot
aesthetics and are specified within the `aes()` function.

Let’s visualize the distribution of `valence`\! It is Spotify’s derived
metric that describes the “happiness” and “positivity” of a song. Since
we’re visualizing a single variable, we only provide an aesthetic for
the x axis.

``` r
ggplot(data = spotify, aes(x = valence))
```

![](02_isolating-data_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

As you can see, we get a mostly blank plot when we run the `ggplot`
function\! That’s because we haven’t told `ggplot` what type of `geom_`
we want. `ggplot` sets the framework that the rest of the components
inherit. Every plot component that follows will use `spotify` as the
data set and `valence` as the x-axis data, unless otherwise specified.

Let’s make something classic- a histogram. It’s easy- just specify the
geom\!

``` r
ggplot(data = spotify, aes(x = valence)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](02_isolating-data_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

Huh, you got a funny message-  
“`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.”

`ggplot` defaults to using 30 bins, but that may not always be the best
choice. Different values can highlight aspects about your distribution
that may be useful or obscure important patterns. We can play with the
`binwidth` argument to adjust how wide each bin is in the units of our
data. Let’s make them extra narrow. Notice how we specify the `binwidth`
argument within the call to `geom_histogram`- that’s because it is an
argument specific to this geom\!

``` r
ggplot(data = spotify, aes(x = valence)) +
  geom_histogram(binwidth = 0.01)
```

![](02_isolating-data_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

I don’t think this uncovers patterns and just adds noise to the
distribution. Let’s widen those binwidths.

``` r
ggplot(data = spotify, aes(x = valence)) +
  geom_histogram(binwidth = 0.1)
```

![](02_isolating-data_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

I like this a bit better- the distribution looks to be mostly normal
with a bit of a right-skew. Most songs appear to be middle of the road
in their positivity.

I’m curious- minor keys tend to create a sad mood. Does this show up in
the valence of the song? Let’s use our data isolation skillset to
`filter` our data set for songs with a minor key and then visualize the
valence of those songs. The variable that contains this information is
the `mode` variable, where **0** indicates a minor key and **1**
indicates a major key.

``` r
# first filter for minor key songs
minor_songs <- filter(spotify, mode == 0)

# now, visualize a histogram of the valence for these songs
ggplot(data = minor_songs, aes(x = valence)) +
  geom_histogram(binwidth = 0.1)
```

![](02_isolating-data_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

Interesting\! There seems to be a lump at the lower range of valence
values. I think it would be cool to visualize the minor key distribution
alongside the major key distribution. We’ll learn this in the next data
visualization segment\!

While these plots aren’t too pretty, they get the message across. We
will learn how to spruce up the plots in a bit\!

**Exercise 4**  
Try to make your own ggplot\! Visualize a histogram of the `loudness`
variable. Play around with the binwidths to get a feel of how this
variable is distributed\! If you’re feeling a bit ambitious, see if you
can filter the data by another variable (like `time_period` perhaps?)
before you create the visualization to see if there is an interesting
pattern not obvious in the full dataset.

-----

### Go further

Although this isn’t an R-specific book, Claus Wilke’s (free)
[Fundamentals of Data Visualization](https://serialmentor.com/dataviz/)
is a must-read for those trying to up their data-viz game. He’s a
contributor to the ggplot2 package, and even made a couple of packages
that complement ggplot2, like
[cowplot](https://github.com/wilkelab/cowplot) for stitching together
plots and annotating them,
[ggridges](https://github.com/wilkelab/ggridges) for creating partially
overlapping line plots that look like mountain ranges (or that one Joy
Division album cover), and [ggtext](https://github.com/wilkelab/ggtext),
which allows you to add Markdown and HTML formatted text to your
ggplots. Super cool stuff\! The book doesn’t contain R code and isn’t a
“this is how you make this plot in R” book, but that type of info is
available in many other places, like [From data to
viz](https://www.data-to-viz.com/).

-----

## Answers

**Exercise 1**

``` r
select(spotify, artist)
select(spotify, artist, energy)
```

**Exercise 2**

``` r
spotify %>% select(contains("n"))
```

    ## # A tibble: 787 x 11
    ##     rank danceability energy loudness speechiness acousticness instrumentalness
    ##    <dbl>        <dbl>  <dbl>    <dbl>       <dbl>        <dbl>            <dbl>
    ##  1     1        0.427  0.407    -9.66      0.0382       0.612        0.386     
    ##  2     2        0.717  0.653    -5.63      0.0658       0.229        0         
    ##  3     3        0.572  0.385    -6.36      0.0308       0.371        0         
    ##  4     4        0.793  0.399    -7.43      0.0951       0.12         0         
    ##  5     5        0.826  0.513    -9.02      0.0349       0.327        0.0136    
    ##  6     6        0.807  0.414    -8.90      0.0352       0.105        0.0000257 
    ##  7     7        0.245  0.773    -5.98      0.0525       0.0665       0.00046   
    ##  8     8        0.916  0.756    -7.65      0.188        0.0942       0         
    ##  9     9        0.736  0.515    -7.74      0.163        0.384        0.00000431
    ## 10    10        0.775  0.465   -10.8       0.0605       0.615        0.0000393 
    ## # … with 777 more rows, and 4 more variables: liveness <dbl>, valence <dbl>,
    ## #   duration_ms <dbl>, time_signature <dbl>

**Exercise 3**

``` r
filter(spotify, time_period == "current")
filter(spotify, tempo <= 80)
filter(spotify, nchar(track) > 20)
```

**Exercise 4**

``` r
ggplot(data = spotify, aes(x = loudness)) +
  geom_histogram(binwidth = 1)
```

![](02_isolating-data_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

I thought it would be cool to see if top 40 songs are louder\! I
filtered for all songs with a rank equal to or below 40 before
visualizing loudness. It looks to be the case\! This filter at least got
rid of some of the quieter outliers. I took a look at the extra quiet
song, and it looks like DaBaby’s song Baby on Baby wanted to keep it
quiet for the babies.

``` r
top40_songs <- filter(spotify, rank <= 40)

ggplot(data = top40_songs, aes(x = loudness)) +
  geom_histogram(binwidth = 1)
```

![](02_isolating-data_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

-----

[\<\<\< Previous](data-wrangling.md) | [Next \>\>\>](03_piping.md)
