R Graphics Using the ggplot2 Package
========================================================
author: Brandon Gavett
date: 2022-04-07
autosize: true

Workshop Goals
========================================================

We will explore the `ggplot2` package and how it can be used to generate graphics to accompany research output.

Code and data are available at https://github.com/begavett/Rgraphics


Required Packages
========================================================


```r
# if necessary: install.packages("pacman")
library(pacman)
p_load(tidyverse, haven, ggplot2, ggthemes, cowplot, GGally, patchwork, psych)
```

- `tidyverse`: a suite of data tools
- `haven`: imports data
- `ggplot2`: the focus of today's workshop
- `ggthemes`: a ggplot2 add-on that provides pre-made graphical styles
- `cowplot`: a good supplementary graphics package
- `GGally`: another companion to ggplot
- `patchwork`: package for combining plots together
- `psych`: statistical tools relevant to psychological research



Import Data
========================================================

Source: https://www.afltables.com


```r
afl_data <- read_csv("afl_team_data_2000_2021.csv")
```


```r
describe(afl_data)
```

```
               vars   n    mean     sd median trimmed    mad     min    max
Team*             1 357    9.98   5.77   10.0    9.98   7.41     1.0   19.0
P                 2 357   21.74   1.10   22.0   22.00   0.00    17.0   22.0
W                 3 357   10.78   4.34   11.0   10.86   4.45     1.0   21.0
D                 4  64    1.06   0.24    1.0    1.00   0.00     1.0    2.0
L                 5 357   10.78   4.31   10.0   10.64   4.45     1.0   21.0
GF_BF*            6 357  176.99 101.36  177.0  177.11 128.99     1.0  352.0
For               7 357 1957.81 342.08 1990.0 1982.18 272.80   826.0 2816.0
GA_BA*            8 357  177.13 102.29  177.0  177.04 130.47     1.0  354.0
Agn               9 357 1957.81 358.74 1961.0 1968.17 314.31   869.0 2990.0
Pct              10 357  102.63  22.50  101.6  102.26  23.57    46.2  167.7
Pts              11 356   43.46  17.28   44.0   43.82  17.79     4.0   84.0
Year             12 357 2010.86   6.49 2012.0 2010.93   8.90  2000.0 2021.0
Home_W           13 357    6.14   2.50    6.0    6.20   2.97     0.0   11.0
Home_D           14 357    0.10   0.29    0.0    0.00   0.00     0.0    1.0
Home_L           15 357    4.64   2.46    4.0    4.58   2.97     0.0   11.0
Away_W           16 357    6.14   2.50    6.0    6.20   2.97     0.0   11.0
Away_D           17 357    0.10   0.29    0.0    0.00   0.00     0.0    1.0
Away_L           18 357    4.64   2.46    4.0    4.58   2.97     0.0   11.0
WA_Team          19 357    0.12   0.32    0.0    0.02   0.00     0.0    1.0
Freo             20 357    0.06   0.24    0.0    0.00   0.00     0.0    1.0
Eagles           21 357    0.06   0.24    0.0    0.00   0.00     0.0    1.0
primary_col*     22 357    2.82   0.51    3.0    2.98   0.00     1.0    3.0
secondary_col*   23 357    2.82   0.51    3.0    2.98   0.00     1.0    3.0
Pt_diff          24 357    0.00 446.31   35.0   17.46 418.09 -1481.0 1046.0
                range  skew kurtosis    se
Team*            18.0  0.01    -1.37  0.31
P                 5.0 -4.06    14.62  0.06
W                20.0 -0.16    -0.77  0.23
D                 1.0  3.53    10.63  0.03
L                20.0  0.24    -0.72  0.23
GF_BF*          351.0 -0.01    -1.20  5.36
For            1990.0 -0.81     1.21 18.10
GA_BA*          353.0  0.01    -1.21  5.41
Agn            2121.0 -0.39     1.00 18.99
Pct             121.5  0.17    -0.31  1.19
Pts              80.0 -0.17    -0.77  0.92
Year             21.0 -0.09    -1.28  0.34
Home_W           11.0 -0.20    -0.71  0.13
Home_D            1.0  2.75     5.56  0.02
Home_L           11.0  0.25    -0.63  0.13
Away_W           11.0 -0.20    -0.71  0.13
Away_D            1.0  2.75     5.56  0.02
Away_L           11.0  0.25    -0.63  0.13
WA_Team           1.0  2.36     3.60  0.02
Freo              1.0  3.73    11.98  0.01
Eagles            1.0  3.73    11.98  0.01
primary_col*      2.0 -2.84     6.75  0.03
secondary_col*    2.0 -2.84     6.75  0.03
Pt_diff        2527.0 -0.40     0.12 23.62
```


```r
table(afl_data$P)
```

```

 17  21  22 
 18   2 337 
```

Filter out teams who did not play 22 games


```r
afl_data <- afl_data %>%
  filter(P == 22)
```

Scatterplots
========================================================

We will begin learning about many of the primary ggplot2 features using scatterplots.

For scatterplots we need, at a minimum, 2 continuous variables. We could also add

- Grouping variable(s)
- Trend lines
    - Confidence bands
    - Various methods for smoothing/fitting
- Chart aesthetics
- And more

Grammar of Graphics
========================================================

The style for writing ggplot2 syntax is a bit non-intuitive at first, but it is very systematic. Once you learn the basic structure of ggplot2 syntax, you can do almost anything.

Step 1. Specify data and aesthetics


```r
ggplot(data = x, mapping = aes(x = PREDICTOR, y = OUTCOME))
```

These are global aesthetics that apply to the entire plot (you'll see what I mean later).

Running this by itself would produce an empty plot.

Grammar of Graphics
========================================================

Step 2. Add the main plotting layer 


```r
ggplot(data = x, mapping = aes(x = PREDICTOR, y = OUTCOME)) +
  geom_point()
```

This takes the values stored in `PREDICTOR` and `OUTCOME` (which, here, must be columns in a data frame called `x`) and plots them as points using `geom_point()`

Grammar of Graphics
========================================================

Step 3. Adding additional customizations


```r
ggplot(data = x, mapping = aes(x = PREDICTOR, y = OUTCOME)) +
  geom_point() +
  xlab("Predictor Variable") +
  ylab("Outcome Variable")
```

This provides custom labels for the x-axis (`xlab`) and y-axis (`ylab`), overriding the default (which would be to label the x-axis as `PREDICTOR` and the y-axis as `OUTCOME`)

Note that each new layer gets added to the previous layer with a `+` sign.

Scatterplots
========================================================

We'll begin by plotting the pairwise association between two continuous variables in our data set: Points For (`For`) and Wins (`W`).


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins")
```


Additional Customizations: Trend Line
========================================================

The default is to use either a generalized additive model (GAM) or loess for smoothing (the decision is automatically made by ggplot depending on the sample size)

`geom_smooth()`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth()
```


Additional Customizations: Trend Line
========================================================

However, for many purposes, we may want to use a linear trend line (e.g., if we are planning to use linear regression).

`geom_smooth(method = "lm")`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm")
```


Additional Customizations: Trend Line without Confidence Band
========================================================

Notice that ggplot automatically added a +/- 1SE confidence band around the trend line.

To remove this:

`geom_smooth(method = "lm", se = FALSE)`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm", se = FALSE)
```


Additional Customizations: Grouping
========================================================

What if we wanted our plot to differentiate between teams?

We should first understand how teams are coded in our data set.


```r
str(afl_data$Team)
```

```
 chr [1:337] "Essendon" "Carlton" "Melbourne" "Kangaroos" "Geelong" ...
```

The `Team` variable is a character.

Because we want to treat Team as a categorical variable for plotting purposes, we should first create a factor variable.


```r
afl_data$Team_f <- factor(afl_data$Team)
```


```r
str(afl_data$Team_f)
```

```
 Factor w/ 19 levels "Adelaide","Brisbane Lions",..: 5 3 12 11 7 2 19 10 15 17 ...
```

Additional Customizations: Grouping
========================================================

One way to group by a categorical variable is to use different shapes for the points.

`shape = Team_f`


```r
ggplot(afl_data, aes(x = For, y = W, shape = Team_f)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins")
```


Additional Customizations: Grouping
========================================================

Another way to group by a categorical variable is to use different colors for the points.

`colour = Team_f`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_f)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins")
```


Additional Customizations: Grouping & Trend Line
========================================================

What if we wanted to include both the linear trend line and the grouping by team?

`colour = Team_f`

`geom_smooth(method = "lm")`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_f)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm")
```


Additional Customizations: Grouping & Trend Line
========================================================

Was that plot too messy? One way to clean it up is to make a single trend line for all of the data, ignoring Team.


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_point(aes(colour = Team_f)) +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm")
```


We moved `colour = Team_f` out of the main aesthetics and into the aesthetics for `geom_point()`. This means that the `geom_smooth()` function is ignorant of the colour aesthetics passed to `geom_point()`; as such, it doesn't create a separate trend line for each team.

Additional Customizations: Grouping & Trend Line
========================================================

Another way we could make our visualizations cleaner would be to focus on WA teams.


```r
afl_data <- afl_data %>%
  mutate(Team_wa = case_when(!Team %in% c("West Coast", "Fremantle") ~ "Other",
                             TRUE ~ Team) %>% factor())
```

`colour = Team_wa`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm")
```


Additional Customizations: Custom Colors
========================================================

https://colorbrewer2.org/

![colorbrewer2.org](Brewer.png)


Additional Customizations: Custom Colors
========================================================

https://colorbrewer2.org/


```r
?scale_colour_brewer
```


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_brewer(palette = "Dark2")
```



Additional Customizations: Custom Colors 
========================================================

What if we wanted the confidence band to be color-coded as well? 

`fill = Team_wa`

`scale_fill_brewer(palette = "Dark2")`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_brewer(palette = "Dark2") +
  scale_fill_brewer(palette = "Dark2")
```


Additional Customizations: Custom Colors 
========================================================

Note:

- Colour

`colour = Team_wa` sets `Team_wa` as the variable used to do the color-coding of the points.

`scale_colour_brewer` defines the color palette that is mapped to the `Team_wa` variable.

- Fill

`fill = Team_wa` sets `Team_wa` as the variable used to fill the confidence bands with color

`scale_fill_brewer` defines the fill palette that is mapped to the `Team_wa` variable.

Additional Customizations: Custom Colors 
========================================================

If you want to use your own custom colors, you can set them manually.

Check the Addins dropdown menu above for a comprehensive list of all colors in R.


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_point(colour = "burlywood4") +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm", colour = "peachpuff") 
```


Note that `colour =` is NOT included inside `aes()` This makes customization a bit more challenging.

Additional Customizations: Custom Colors 
========================================================

One alternative is to specify colours in their own objects.


```r
afl_primary_colours <- c("West Coast" = "#003087",
                         "Fremantle" = "#2a0d54",
                         "Other" = "peachpuff")

afl_secondary_colours <- c("West Coast" = "#f2a900",
                         "Fremantle" = "#a2acb4",
                         "Other" = "burlywood4")
```

Additional Customizations: Custom Colors 
========================================================

`scale_colour_manual(values = afl_primary_colours)`

`scale_fill_manual(values = afl_secondary_colours)`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours) +
  scale_fill_manual(values = afl_secondary_colours)
```


Additional Customizations: Custom Colors 
========================================================

You can also color the points according to a continuous variable if desired.

`shape = Team_wa`

`scale_colour_distiller(palette = "Reds")` Note: distiller instead of brewer!


```r
ggplot(afl_data, aes(x = For, y = W, fill = Team_wa)) +
  geom_point(aes(shape = Team_wa, colour = Pt_diff), size = 3) +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_distiller(palette = "Reds", direction = 1) +
  scale_fill_manual(values = afl_secondary_colours) +
  scale_shape_discrete()
```


Additional Customizations: Legends 
========================================================

If you're picky, you may not like the label that's been applied to the legend. We can fix that.

`name = "Team"`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team")
```


Additional Customizations: Titles 
========================================================

As we get closer to perfecting our plot, let's add a title.

`ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams, 2000-2021",
  subtitle = "2000-2021")`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_point() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021")
```


Additional Customizations: Jitter 
========================================================


We're getting even more pedantic now. Wins is technically not a continuous variable (it's a count variable). As such, some of the points on the y-axis obscure other points. If we want to improve our ability to see each individual point, we can apply jitter by replacing

`geom_point()`

with

`geom_jitter()`

Additional Customizations: Jitter 
========================================================


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021")
```


Additional Customizations: Themes 
========================================================


Ok, this looks pretty good. But are you going to be submitting it for publication? There is some "chart junk" here. If you have ever heard of Edward Tufte, you probably know what that is. If we wanted to please Tufte and remove *all* "chart junk", we could use the `tufte` theme from the `ggthemes` package.

`theme_tufte()`

Additional Customizations: Themes 
========================================================


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021") +
  theme_tufte()
```


Additional Customizations: Themes 
========================================================


That may be a bit extreme! My favorite theme for publication-ready figures is the `cowplot` theme from the `cowplot` package. 

`theme_cowplot()`

Additional Customizations: Themes 
========================================================

`theme_cowplot()`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021") +
  theme_cowplot()
```


Additional Customizations: Themes 
========================================================

`theme_few()`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021") +
  theme_few()
```


Additional Customizations: Themes 
========================================================

`theme_minimal()`


```r
ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021") +
  theme_minimal()
```



Additional Customizations: Facets 
========================================================

Want to separate your data into multiple smaller plots? Use a facet plot.

`facet_wrap(~ Year)`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  ggtitle("Year-to-Year Association Between Points and Wins",
  subtitle = "2000-2021") +
  theme_cowplot() +
  facet_wrap(~ Year)
```


Additional Customizations: Facets 
========================================================

Use `facet_grid` if you have multiple dimensions of faceting.

`facet_grid(WA_Team ~ Year)`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  ggtitle("Year-to-Year Association Between Points and Wins",
  subtitle = "2000-2021") +
  theme_cowplot() +
  facet_grid(WA_Team ~ Year)
```


Additional Customizations: More Themes 
========================================================

The `ggthemes` package has themes that attempt to reproduce the styles used in professional publications, like *The Economist*

`theme_economist()`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  ggtitle("Year-to-Year Association Between Points and Wins",
  subtitle = "2000-2021") +
  theme_economist() +
  facet_wrap(~ Year)
```



Additional Customizations: More Themes 
========================================================

And  *The Wall Street Journal*

`theme_wsj()`


```r
ggplot(afl_data, aes(x = For, y = W)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  ggtitle("Year-to-Year Association Between Points and Wins",
  subtitle = "2000-2021") +
  theme_wsj() +
  facet_wrap(~ Year)
```



Scatterplot Matrices
========================================================

Visualize correlation matrices (correlograms) in your data.


```r
names(afl_data)
```

```
 [1] "Team"          "P"             "W"             "D"            
 [5] "L"             "GF_BF"         "For"           "GA_BA"        
 [9] "Agn"           "Pct"           "Pts"           "Year"         
[13] "Home_W"        "Home_D"        "Home_L"        "Away_W"       
[17] "Away_D"        "Away_L"        "WA_Team"       "Freo"         
[21] "Eagles"        "primary_col"   "secondary_col" "Pt_diff"      
[25] "Team_f"        "Team_wa"      
```

Scatterplot Matrices
========================================================

`ggpairs()` comes from the `GGally` package


```r
ggpairs(afl_data, columns = c(3, 5, 7, 9, 11), ggplot2::aes(colour = Team_wa, fill = Team_wa)) +
  scale_colour_manual(values = afl_secondary_colours) +
  scale_fill_manual(values = afl_secondary_colours) +
  theme_cowplot()
```


Histograms
========================================================


```r
ggplot(afl_data, aes(x = Pt_diff)) + 
  geom_histogram(fill = "peachpuff", colour = "burlywood4") +
  theme_cowplot()
```


Histograms with Stratification
========================================================


```r
ggplot(afl_data, aes(x = Pt_diff, fill = Team_wa)) + 
  geom_histogram() +
  scale_fill_manual(values = afl_secondary_colours) +
  theme_cowplot()
```


Density Plots
========================================================


```r
ggplot(afl_data, aes(x = Pt_diff)) + 
  geom_density() +
  theme_cowplot()
```


Density Plots with Stratification
========================================================


```r
ggplot(afl_data, aes(x = Pt_diff, fill = Team_wa)) + 
  geom_density() +
  scale_fill_manual(values = afl_secondary_colours) +
  theme_cowplot()
```



Density Plots with Stratification and Transparency
========================================================


```r
ggplot(afl_data, aes(x = Pt_diff, fill = Team_wa)) + 
  geom_density(alpha = .5) +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  theme_cowplot()
```


Boxplots
========================================================


```r
afl_data$Year_f <- factor(afl_data$Year)
```


```r
ggplot(afl_data, aes(x = Year_f, y = For)) + 
  geom_boxplot() +
  xlab("Year") +
  ylab("Points For") +
  theme_cowplot()
```


Boxplots
========================================================

Axis labels too scrunched together?

`scale_x_discrete(guide = guide_axis(n.dodge = 2))`


```r
ggplot(afl_data, aes(x = Year_f, y = For)) + 
  geom_boxplot() +
  xlab("Year") +
  ylab("Points For") +
  theme_cowplot() +
  scale_x_discrete(guide = guide_axis(n.dodge = 2))
```



Violin Plots
========================================================


```r
ggplot(afl_data, aes(x = Year_f, y = For)) + 
  geom_violin() +
  xlab("Year") +
  ylab("Points For") +
  theme_cowplot() +
  scale_x_discrete(guide = guide_axis(n.dodge = 2))
```



Spaghetti Plots
========================================================

Spaghetti plots are useful for plotting longitudinal data, grouped by individual cases (in this instance, Teams)


```r
ggplot(afl_data, aes(x = Year, y = W, group = Team_f, colour = Team_wa)) +
  geom_line(size = 2) +
  scale_colour_manual(values = afl_secondary_colours, name = "Team") +
  ylab("Wins") +
  theme_cowplot()
```



Bar Charts
========================================================

For some data, like bar charts, you may want to plot *summaries* of data, rather than individual data points.

`stat_summary(fun = mean, geom = "bar")`


```r
ggplot(afl_data, aes(x = Team_f, y = W)) +
  stat_summary(fun = mean, geom = "bar") +
  theme_cowplot() +
  theme(axis.text.x = element_text(angle = 75, hjust = 1, vjust = 1))
```


Bar Charts
========================================================

With summaries of data, we often want confidence intervals as well.

`stat_summary(fun.data = mean_se, geom = "errorbar")`


```r
ggplot(afl_data, aes(x = Team_f, y = W)) +
  stat_summary(fun = mean, geom = "bar") +
  stat_summary(fun.data = mean_se, geom = "errorbar") +
  theme_cowplot() +
  theme(axis.text.x = element_text(angle = 75, hjust = 1, vjust = 1))
```



Bar Charts
========================================================

Sometimes you may want to flip the orientation of your plots.

`coord_flip()` (works on all plots, not just bar charts)


```r
ggplot(afl_data, aes(x = Team_f, y = W)) +
  stat_summary(fun = mean, geom = "bar") +
  stat_summary(fun.data = mean_se, geom = "errorbar") +
  theme_cowplot() +
  coord_flip()
```


Bar Charts
========================================================

Want the y-axis in alphabetical order?

`x = forcats::fct_rev(Team_f)`


```r
ggplot(afl_data, aes(x = forcats::fct_rev(Team_f), y = W)) +
  stat_summary(fun = mean, geom = "bar") +
  stat_summary(fun.data = mean_se, geom = "errorbar") +
  theme_cowplot() +
  coord_flip()
```


Bar Charts
========================================================

Want the y-axis in descending order?

`x = forcats::fct_reorder(.f = Team_f, .x = W, .fun = mean)`


```r
ggplot(afl_data, aes(x = forcats::fct_reorder(.f = Team_f, .x = W, .fun = mean), y = W)) +
  stat_summary(fun = mean, geom = "bar") +
  stat_summary(fun.data = mean_se, geom = "errorbar") +
  theme_cowplot() +
  coord_flip() +
  xlab("Team")
```


Bar Charts
========================================================

Or maybe we'd prefer to stratify by another variable (e.g., Year), rather than aggregate.

`position = "dodge"`


```r
ggplot(afl_data %>% filter(Team %in% c("West Coast", "Fremantle")), aes(x = Team_f, y = W, fill = Year_f)) +
  geom_col(position = "dodge", colour = "black") +
  theme_cowplot() +
  theme(axis.text.x = element_text(angle = 75, hjust = 1, vjust = 1)) +
  scale_fill_ordinal()
```


Annotations
========================================================

It is possible to add text as a new plot layer.

`annotate("text", x = 1, y = 2, label = "Fremantle"`


```r
ggplot(afl_data %>% filter(Team %in% c("West Coast", "Fremantle")), aes(x = Team_f, y = W, fill = Year_f)) +
  geom_col(position = "dodge", colour = "black") +
  theme_cowplot() +
  theme(axis.title.x = element_blank(), axis.text.x = element_blank()) +
  scale_fill_ordinal(name = "Year") +
  annotate("text", x = 1, y = 2, label = "Fremantle", colour = "#2a0d54", size = 12.5) +
  annotate("text", x = 2, y = 2, label = "West Coast", colour = "#f2a900", size = 12.5)
```


Patchwork
========================================================

What if you want to put multiple plots together in the same window? The `patchwork` package makes it easy.

Let's pick 4 plots that we want to combine, and assign each of them to a different object.


```r
plot1 <- ggplot(afl_data, aes(x = For, y = W, colour = Team_wa, fill = Team_wa)) +
  geom_jitter() +
  xlab("Points For") +
  ylab("Wins") +
  geom_smooth(method = "lm") +
  scale_colour_manual(values = afl_primary_colours, name = "Team") +
  scale_fill_manual(values = afl_secondary_colours, name = "Team") +
  ggtitle("Association Between Points and Wins in WA Teams and Other AFL Teams",
  subtitle = "2000-2021") +
  theme_cowplot()
```

Patchwork
========================================================

`plot2`


```r
plot2 <- ggplot(afl_data, aes(x = Year, y = W, group = Team_f, colour = Team_wa)) +
  geom_line(size = 2) +
  scale_colour_manual(values = afl_secondary_colours, name = "Team") +
  ylab("Wins") +
  theme_cowplot()
```


Patchwork
========================================================

`plot3`


```r
plot3 <- ggplot(afl_data, aes(x = forcats::fct_rev(Team_f), y = W)) +
  stat_summary(fun = mean, geom = "bar") +
  stat_summary(fun.data = mean_se, geom = "errorbar") +
  theme_cowplot() +
  coord_flip()
```

Patchwork
========================================================

`plot4`


```r
plot4 <- ggplot(afl_data %>% filter(Team %in% c("West Coast", "Fremantle")), aes(x = Team_f, y = W, fill = Year_f)) +
  geom_col(position = "dodge", colour = "black") +
  theme_cowplot() +
  theme(axis.title.x = element_blank(), axis.text.x = element_blank()) +
  scale_fill_ordinal(name = "Year") +
  annotate("text", x = 1, y = 2, label = "Fremantle", colour = "#2a0d54", size = 12.5) +
  annotate("text", x = 2, y = 2, label = "West Coast", colour = "#f2a900", size = 12.5)
```

Patchwork
========================================================

Put the four plots together in a 2x2 grid


```r
(plot1 | plot2) / (plot3 | plot4)
```


Export
========================================================

Saving plots

`ggsave()`


```r
ggsave("Figure 1.pdf", plot4, width = 210, height = 297, units = "mm")
```

Supported filetypes include:
  - eps
  - ps
  - tex
  - pdf
  - jpeg
  - tiff
  - png
  - bmp
  - svg
  - wmf
