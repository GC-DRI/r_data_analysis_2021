Piping
================

[&lt;&lt;&lt; Previous](02_isolating-data.md) \| [Next
&gt;&gt;&gt;](04_summarizing-data.md)

------------------------------------------------------------------------

%&gt;%
------

### Steps

Notice how each dplyr function takes a data frame as input and returns a
data frame as output. This makes the functions easy to use in a step by
step fashion. For example, you could:

1.  Filter `spotify` to just songs from before the COVID pandemic, then
2.  Select the `loudness` and `energy` columns from the result

<!-- -->

    pre_covid <- filter(spotify, covid_period == "pre_covid")
    pre_covid <- select(pre_covid, loudness, energy)
    pre_covid

    ## # A tibble: 4,900 x 2
    ##    loudness energy
    ##       <dbl>  <dbl>
    ##  1   -10.7   0.317
    ##  2    -2.75  0.792
    ##  3    -7.05  0.488
    ##  4    -5.57  0.479
    ##  5    -3.71  0.73 
    ##  6    -2.73  0.904
    ##  7    -5.63  0.653
    ##  8   -11.7   0.364
    ##  9    -7.36  0.539
    ## 10    -7.05  0.462
    ## # … with 4,890 more rows

### Redundancy

Do you notice how we re-create `pre_covid` at each step so we will have
something to pass to the next step? This is an inefficient way to write
R code.

You could avoid creating `pre_covid` by nesting your functions inside of
each other, but this creates code that is hard to read:

    select(filter(spotify, covid_period == "pre_covid"), loudness, energy)

The dplyr package provides a third way to write sequences of functions:
the pipe.

### %&gt;%

![Source: dplyr cheatsheet](images/pipes.png)

The pipe operator `%>%` performs an extremely simple task: it passes the
result on its left into the first argument of the function on its right.
Or put another way, `x %>% f(y)` is the same as `f(x, y)`. This piece of
code punctuation makes it easy to write and read series of functions
that are applied in a step by step way. For example, we can use the pipe
to rewrite our code above:

    spotify %>% 
      filter(covid_period == "pre_covid") %>% 
      select(loudness, energy)

    ## # A tibble: 4,900 x 2
    ##    loudness energy
    ##       <dbl>  <dbl>
    ##  1   -10.7   0.317
    ##  2    -2.75  0.792
    ##  3    -7.05  0.488
    ##  4    -5.57  0.479
    ##  5    -3.71  0.73 
    ##  6    -2.73  0.904
    ##  7    -5.63  0.653
    ##  8   -11.7   0.364
    ##  9    -7.36  0.539
    ## 10    -7.05  0.462
    ## # … with 4,890 more rows

As you read the code, pronounce `%>%` as “then”. You’ll notice that
`dplyr` makes it easy to read pipes. Each function name is a verb, so
our code resembles the statement, “Take spotify, *then* filter it by
mode, *then* select the loudness and energy.”

`dplyr` also makes it easy to write pipes. Each `dplyr` function returns
a data frame that can be piped into another `dplyr` function, which will
accept the data frame as its first argument. In fact, `dplyr` functions
are written with pipes in mind: each function does one simple task.
`dplyr` expects you to use pipes to combine these simple tasks to
produce sophisticated results.

**Exercise 1**

We’ll use pipes for the remainder of the tutorial. Let’s practice a
little by writing a new pipe. The pipe should:

1.  Filter `spotify` to just the songs that are above 0.50
    `danceability`
2.  Select the `tempo` and `energy` columns

------------------------------------------------------------------------

Using Color
-----------

Color and shapes do more than make your plot look nicer. They add a
third dimension to your plot that can highlight groups, emphasize higher
or lower continuous values, and more. Used wisely, your data
visualization can become much more impactful. Used unwisely, you may end
up with an ugly plot or, even worse, mislead the viewer. We’ll learn the
mechanics of how to add color in `ggplot` here, but make sure to check
out [this chapter](https://clauswilke.com/dataviz/color-basics.html) in
Claus Wilke’s Fundamentals of Data Visualization for a more in-depth
discussion of using color in your data viz. 

Remember this histogram of valence for pre-COVID songs from the last
section?  
![](03_piping_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

To answer the question “Are songs from before the pandemic more positive
than those after the pandemic?” I think it’s more useful to visualize
the two distributions side-by-side than make individual plots for both.
One way to do this is to use color.

To change the color of a plot, we supply either the `color` argument or
the `fill` argument. The `color` argument refers to the color of points,
lines, and the edges of shapes, while the `fill` argument refers to the
color of the inside of shapes (e.g. the inside of a polygon).  
When you use the `color` or `fill` arguments inside of the `aes()`
function, you supply the variable to map the colors to. For instance, to
split a histogram into two colors, one for the pre-COVID time period,
and one for the post-COVID time period, we specify the `covid_period`
variable for the `fill` argument.

    spotify %>%
      ggplot(aes(x = valence, fill = covid_period)) +
      geom_histogram(binwidth = 0.1)

![](03_piping_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

However, this plot isn’t super helpful if we’re interested in
differences in the shape of the distributions. The histogram uses
absolute counts, while we want to see a relative comparison.
Fortunately, the `geom_density` argument allows us to visualize the
relative shapes of two distributions. To visualize the differences
between the pre-COVID and post-COVID time periods, we specify the
`color` variable.

    spotify %>%
      ggplot(aes(x = valence, color = covid_period)) +
      geom_density()

![](03_piping_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Man, it looks like the American public consistently likes to listen to
moderately positive music! There is a bit of a jump at the lower range
of valence values for the post\_COVID time frame, but I’m hesitant to
over-interpret that result.

Let’s spruce the plot up a bit by adding a plot title, capitalizing the
axis title names, and customizing the legend title. To do this, we use
the `labs()` function! The arguments are mostly self-explanatory, except
for the legend. To change the title of a legend, you specify the
aesthetic the legend is referring to, in this case the `color`
aesthetic.

    spotify %>%
      ggplot(aes(x = valence, color = covid_period)) +
      geom_density() +
      labs(
        title = "No trend in valence before and after the COVID pandemic",
        x = "Valence",
        y = "Density",
        color = "COVID period"
      )

![](03_piping_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

For fun, let’s update the color to something more visually appealing.
`ggplot2` is notorious for its ugly default color scheme. In our case,
let’s use the *viridis* color scheme. It’s functional and visually
appealing, and fortunately comes with its own function! We will use the
`scale_color_viridis_d()` function to specify that we want to *scale*
the *color* aesthetic using the *viridis* color palette, and the
variable we are mapping to is *d*iscrete.

    spotify %>%
      ggplot(aes(x = valence, color = covid_period)) +
      geom_density() +
      labs(
        title = "No trend in valence before and after the COVID pandemic",
        x = "Valence",
        y = "Density",
        color = "COVID period"
      ) +
      scale_color_viridis_d()

![](03_piping_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

The `scale_*` family of functions maps colors, shapes, transparency, and
more to your aesthetics (arguments you provide in the `aes()` function).
We used `scale_color_viridis_d()` to map the viridis color palette to
the color aesthetic. If we wanted to map the viridis color palette to
the `fill` aesthetic, we would use the `scale_fill_viridis_d()` for a
discrete variable, or `scale_fill_viridis_c()` for a continuous
variable. There are a TON of `scale_*` functions, and fortunately a kind
soul recently provided a comprehensive and clear [guide to all of
them](https://ggplot2tor.com/scales/). They even made it easy to choose
which function you need, given what you want to change!

**Exercise 2**  
Make a density plot of the `loudness` variable and color it according to
the `covid_period` variable. Change the x-axis and y-axis labels so
their first letters are upper case and change the legend to read “COVID
period”. Finally, use one of the `scale_*_viridis_*()` functions to use
the viridis color palette instead of `ggplot2`’s default. Add a title
describing the plot if you want! What trend (if any) do you see?  
*Extension:* if you’re feeling ambitious, try out one of the other
`scale_*` functions to color your plot.

------------------------------------------------------------------------

### Go further

There are some fun color palettes out there! Check out these palettes to
up your plotting game:

-   [ggpomological](https://www.garrickadenbuie.com/project/ggpomological/)
    for fruit themed colors  
-   [wesanderson](https://github.com/karthik/wesanderson) for colors
    derived from Wes Anderson movies  
-   [ggsci](https://cran.r-project.org/web/packages/ggsci/vignettes/ggsci.html)
    for color palettes inspired by colors used in scientific journals,
    data visualization libraries, science fiction movies, and TV shows

In addition, it’s important to make your color choices accessible to
those with vision impairments. To see how your color palette is viewed
under a range of color blindness syndromes, try out your plots with the
[colorblindr](https://github.com/clauswilke/colorblindr) package!

------------------------------------------------------------------------

Answers
-------

**Exercise 1**

    spotify %>% 
      filter(danceability > 0.50) %>% 
      select(tempo, energy) 

    ## # A tibble: 9,475 x 2
    ##    tempo energy
    ##    <dbl>  <dbl>
    ##  1 140.   0.317
    ##  2 100.   0.792
    ##  3 136.   0.488
    ##  4  89.9  0.479
    ##  5 155.   0.73 
    ##  6  82.0  0.904
    ##  7 107.   0.653
    ##  8 124.   0.364
    ##  9 100.   0.539
    ## 10  59.6  0.462
    ## # … with 9,465 more rows

**Exercise 2**

    spotify %>%
      ggplot(aes(x = loudness, color = covid_period)) +
      geom_density() +
      labs(
        title = "Slight decrease in loudness after the pandemic began",
        x = "Loudness",
        y = "Density",
        color = "COVID period"
      ) +
      scale_color_viridis_d()

![](03_piping_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Now I’ll try it with one of the `scale_color_brewer()` palettes!

    spotify %>%
      ggplot(aes(x = loudness, color = covid_period)) +
      geom_density() +
      labs(
        title = "Slight decrease in loudness after the pandemic began",
        x = "Loudness",
        y = "Density",
        color = "COVID period"
      ) +
      scale_color_brewer(palette = "Dark2")

![](03_piping_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

------------------------------------------------------------------------

[&lt;&lt;&lt; Previous](02_isolating-data.md) \| [Next
&gt;&gt;&gt;](04_summarizing-data.md)
