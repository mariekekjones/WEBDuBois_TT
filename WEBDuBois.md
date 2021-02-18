---
title: "WEB DuBois Challenge"
author: "Your name here"
date: "2/16/2021"
output: 
  html_document:
    keep_md: true
---

# Load the data


```r
library(tidyverse)
```

Try to re-create WEB Du Bois's [original income figure](https://github.com/ajstarks/dubois-data-portraits/blob/master/challenge/challenge05/original-plate-31.jpg)

Read in the income data

```r
income <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-02-16/income.csv')
```

```
## 
## ── Column specification ────────────────────────────────────────────────────────
## cols(
##   Class = col_character(),
##   `Actual Average` = col_double(),
##   Rent = col_double(),
##   Food = col_double(),
##   Clothes = col_double(),
##   Tax = col_double(),
##   Other = col_double()
## )
```

```
## Warning: 1 parsing failure.
## row col  expected    actual                                                                                                   file
##   1  -- 7 columns 6 columns 'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-02-16/income.csv'
```

```r
income
```

```
## # A tibble: 7 x 7
##   Class      `Actual Average`  Rent  Food Clothes   Tax Other
##   <chr>                 <dbl> <dbl> <dbl>   <dbl> <dbl> <dbl>
## 1 $100-200               139.    19    43      28   9.9  NA  
## 2 $200-300               249.    22    47      23   4     4  
## 3 $300-400               336.    23    43      18   4.5  11.5
## 4 $400-500               434.    18    37      15   5.5  24.5
## 5 $500-750               547     13    31      17   5    34  
## 6 $750-1000              880      0    37      19   8    36  
## 7 Over $1000            1125      0    29      16   4.5  50.5
```

Looks like the original plots the types of expenses so we should `pivot_longer()`


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income")
```

```
## # A tibble: 35 x 4
##    Class    `Actual Average` Expense Percent_of_Income
##    <chr>               <dbl> <chr>               <dbl>
##  1 $100-200             139. Rent                 19  
##  2 $100-200             139. Food                 43  
##  3 $100-200             139. Clothes              28  
##  4 $100-200             139. Tax                   9.9
##  5 $100-200             139. Other                NA  
##  6 $200-300             249. Rent                 22  
##  7 $200-300             249. Food                 47  
##  8 $200-300             249. Clothes              23  
##  9 $200-300             249. Tax                   4  
## 10 $200-300             249. Other                 4  
## # … with 25 more rows
```

Yes, that is better

Now try a basic plot


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  ggplot(aes(Percent_of_Income, Class)) + 
  geom_col(aes(fill = Expense))
```

```
## Warning: Removed 1 rows containing missing values (position_stack).
```

![](WEBDuBois_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

Already close to the original, but the y-axis is upside down, so reverse that


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  ggplot(aes(Percent_of_Income, Class)) + 
  geom_col(aes(fill = Expense)) +
  scale_y_discrete(limits = rev)
```

```
## Warning: Removed 1 rows containing missing values (position_stack).
```

![](WEBDuBois_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Change the order of the expenses and the class to match those in the original


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense))
```

```
## # A tibble: 35 x 4
##    Class    `Actual Average` Expense Percent_of_Income
##    <fct>               <dbl> <fct>               <dbl>
##  1 $100-200             139. Rent                 19  
##  2 $100-200             139. Food                 43  
##  3 $100-200             139. Clothes              28  
##  4 $100-200             139. Tax                   9.9
##  5 $100-200             139. Other                NA  
##  6 $200-300             249. Rent                 22  
##  7 $200-300             249. Food                 47  
##  8 $200-300             249. Clothes              23  
##  9 $200-300             249. Tax                   4  
## 10 $200-300             249. Other                 4  
## # … with 25 more rows
```

```r
# now plot
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense)) %>%
  ggplot(aes(Percent_of_Income, Class)) + 
  geom_col(aes(fill = Expense)) +
  scale_y_discrete(limits = rev)
```

```
## Warning: Removed 1 rows containing missing values (position_stack).
```

![](WEBDuBois_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

Change the colors to match the original


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense)) %>%
  ggplot(aes(Percent_of_Income, Class)) + 
  geom_col(aes(fill = Expense)) +
  scale_y_discrete(limits = rev) +
  scale_fill_manual(values = c("azure2", "steelblue3", "lightpink", "mediumpurple4", "black"))
```

```
## Warning: Removed 1 rows containing missing values (position_stack).
```

![](WEBDuBois_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

Change the spacing between the bars and remove axes labels and change theme


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense)) %>%
  ggplot(aes(Percent_of_Income, Class)) + 
  geom_col(aes(fill = Expense), width = .4) +
  scale_y_discrete(limits = rev) +
  scale_fill_manual(values = c("azure2", "steelblue3", "lightpink", "mediumpurple4", "black")) +
  theme_classic() +
  labs(x = "", y = "")
```

```
## Warning: Removed 1 rows containing missing values (position_stack).
```

![](WEBDuBois_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Add annotations in each segment. To do so, replace NA in Percent of Income variable with 0 first


```r
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense)) %>%
  mutate(Percent_of_Income = replace_na(Percent_of_Income, 0))
```

```
## # A tibble: 35 x 4
##    Class    `Actual Average` Expense Percent_of_Income
##    <fct>               <dbl> <fct>               <dbl>
##  1 $100-200             139. Rent                 19  
##  2 $100-200             139. Food                 43  
##  3 $100-200             139. Clothes              28  
##  4 $100-200             139. Tax                   9.9
##  5 $100-200             139. Other                 0  
##  6 $200-300             249. Rent                 22  
##  7 $200-300             249. Food                 47  
##  8 $200-300             249. Clothes              23  
##  9 $200-300             249. Tax                   4  
## 10 $200-300             249. Other                 4  
## # … with 25 more rows
```

```r
# now plot
income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense)) %>%
  mutate(Percent_of_Income = replace_na(Percent_of_Income, 0)) %>%
  ggplot(aes(Percent_of_Income, Class)) + 
  geom_col(aes(fill = Expense), width = .4) +
  scale_y_discrete(limits = rev) +
  scale_fill_manual(values = c("azure2", "steelblue3", "lightpink", "mediumpurple4", "black")) +
  theme_classic() +
  labs(x = "", y = "") +
  geom_text(aes(label = paste0(Percent_of_Income, " %")), size = 3, position = position_stack(vjust = .5), color = rep(c("white", rep("black", 4)), 7))
```

![](WEBDuBois_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Add the lines connecting each portion of the bar and finalize details (background color, title, etc.)


```r
plotdata <- income %>%
  pivot_longer(Rent:Other, names_to = "Expense", values_to = "Percent_of_Income") %>%
  mutate(Expense = factor(Expense, levels = c("Other", "Tax", "Clothes", "Food", "Rent"))) %>%
  mutate(Class = factor(Class, levels = c("$100-200", "$200-300", "$300-400", "$400-500", "$500-750", "$750-1000", "Over $1000"))) %>%
  arrange(Class, desc(Expense)) %>%
  mutate(Percent_of_Income = replace_na(Percent_of_Income, 0)) 
plotdata
```

```
## # A tibble: 35 x 4
##    Class    `Actual Average` Expense Percent_of_Income
##    <fct>               <dbl> <fct>               <dbl>
##  1 $100-200             139. Rent                 19  
##  2 $100-200             139. Food                 43  
##  3 $100-200             139. Clothes              28  
##  4 $100-200             139. Tax                   9.9
##  5 $100-200             139. Other                 0  
##  6 $200-300             249. Rent                 22  
##  7 $200-300             249. Food                 47  
##  8 $200-300             249. Clothes              23  
##  9 $200-300             249. Tax                   4  
## 10 $200-300             249. Other                 4  
## # … with 25 more rows
```

```r
# create cumsum column and make cumsum NA for Other
plotdata <- plotdata %>%
  group_by(Class) %>%
  mutate(Cumulative_perc = cumsum(Percent_of_Income)) %>% ungroup() %>%
  mutate(Cumulative_perc = ifelse(Expense == "Other", NA, Cumulative_perc))

# take a look
plotdata
```

```
## # A tibble: 35 x 5
##    Class    `Actual Average` Expense Percent_of_Income Cumulative_perc
##    <fct>               <dbl> <fct>               <dbl>           <dbl>
##  1 $100-200             139. Rent                 19              19  
##  2 $100-200             139. Food                 43              62  
##  3 $100-200             139. Clothes              28              90  
##  4 $100-200             139. Tax                   9.9            99.9
##  5 $100-200             139. Other                 0              NA  
##  6 $200-300             249. Rent                 22              22  
##  7 $200-300             249. Food                 47              69  
##  8 $200-300             249. Clothes              23              92  
##  9 $200-300             249. Tax                   4              96  
## 10 $200-300             249. Other                 4              NA  
## # … with 25 more rows
```

```r
# now plot
plotdata %>%
  ggplot() + 
  geom_col(aes(Percent_of_Income, Class, fill = Expense), width = .4) +
  scale_y_discrete(limits = rev) +
  geom_path(aes(Cumulative_perc, Class, group = Expense), lwd = .2) +
  scale_fill_manual(values = c("azure2", "steelblue3", "lightpink", "mediumpurple4", "black")) +
  theme_classic() +
  labs(x = "", y = "") +
  geom_text(aes(Percent_of_Income, Class, label = paste0(Percent_of_Income, " %")), size = 3, position = position_stack(vjust = .5), color = rep(c("white", rep("black", 4)), 7)) + 
  theme(
  plot.background=element_rect(fill = "antiquewhite"),
  panel.background = element_rect(fill = 'antiquewhite'),
  legend.background = element_rect(fill = "antiquewhite", color = NA),
  axis.line = element_blank()) +
  labs(title = "INCOME AND EXPENDITURE OF 150 NEGRO FAMILIES IN ATLANTA, GA., U.S.A.", caption = "reproducing WEB DuBois's original data graphic using ggplot2")
```

```
## Warning: Removed 7 row(s) containing missing values (geom_path).
```

![](WEBDuBois_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
# save the final plot
ggsave(filename = "dubois_challenge.png", height = 8, width = 12)
```

```
## Warning: Removed 7 row(s) containing missing values (geom_path).
```
