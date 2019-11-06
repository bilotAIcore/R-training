<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (10) Introduction to `dplyr` 

<br>

[`dplyr`](https://dplyr.tidyverse.org/) is one of the most powerful tools for 
data engineering. It is a grammar of data manipulation, providing a consistent 
set of verbs that help you solve the most common data manipulation challenges. 
In essence, `dplyr` is kind of doing SQL on `data.frames`, but in a much easier 
way. At the core of working with `dplyr` is the `%>%` pipe operator, which 
allows one to chain several consecutive operations.

---

<br>

# SELECT from data.frame
In SQL, the most common syntax is `SELECT * FROM table`, which return all 
content of `table` in a database. The same operation in `dplyr` is rather 
silly, but this is how it works:

```{r}
library(dplyr)
data(mpg,package = 'ggplot2')

mpg %>% 
  select(everything())
```

Usually, one is interested in selecting only certain variables, which can be 
optionally rename them on the fly:

```{r}
mpg %>% 
  select(manufacturer,
         fuel_type = fl,
         city_mpg = cty)
```

To *exclude* rather than select variables, use `-` sign in front of the variable name:

```{r}
mpg %>%
  select(-trans,-model,-class)
```

To select only the top rows by variable type, one can use `select_if()` selector 
function and pipe the result to `head()` function:

```{r}
mpg %>% 
  select_if(is.character) %>% 
#  select_if(is.integer) %>% 
  head(10)
```

Conveniently, `dplyr` also allows selection by partial matching:

```{r}
mpg %>% 
  # Match to string in different ways:
  select(contains('manu'), 
         starts_with('y'),
         ends_with('y')) %>%
  head()
```

---

<br>

# FILTER data.frame rows
In addition to selecting columns, one often also wants to subset rows in 
a `data.frame` based on some conditions. This is akin to the `WHERE` 
clause in SQL. Different filters can be combined in a single `filter()`-function call:

```{r}
mpg %>%
  filter(
    # Filter with regexp:
    manufacturer %in% grep('^h',manufacturer,value = T),
    # Numeric filter:
    displ > 1.6,
    # Character filter:
    class == 'midsize')
```

Alternatively, you can also `slice()` rows by index:

```{r}
set.seed(78)
mpg %>% 
  # take a random sample of 10 cars:
  slice(sample(n(),10))
```

---

<br>

# CALCULATE variables
A central part of data engineering is to generate new variables to the data. 
To this end, `dplyr` has a function `mutate()`:

```{r}
mpg %>% 
  mutate(avg_mpg = (cty+hwy)/2) %>%
  select(model,displ,drv,fl,avg_mpg) %>%
  head()
```

While `mutate` adds new variables on top of the existing variables, it is 
also possible to extract a new variable. This is done using the `transmute()` function:

```{r}
mpg %>% 
  transmute(avg_mpg = (cty+hwy)/2) %>%
  head()
```

`mutate` can also be used to modify existing variables:

```{r}
mpg %>%
  # Convert all characters to factor:
  mutate_if(is.character,as.factor) %>%
  # Scale numeric features:
  mutate_if(is.numeric,scale) %>%
  select(manufacturer, displ) %>%
  summary()
```

- **Do it yourself**: Calculate the average and SD of city fuel consumption, 
but only for cars with automatic transmission.

```{r}
# your code comes here:
```

---

<br>

# CASE WHEN
In SQL, CASE WHEN is a handy for either categorising or re-categorising 
variables. In `dplyr`, there is a function `case_when` that does exactly the same thing:

```{r}
mpg %>%
  mutate(avg_mpg = (cty+hwy)/2) %>%
  mutate(mpg_class = case_when(avg_mpg<18 ~ 'high',
                               avg_mpg>=18 & avg_mpg<26 ~ 'medium',
                               avg_mpg>=26 ~ 'low')) %>%
  select(manufacturer,model,mpg_class) %>%
  slice(sample(nrow(mpg),10))
```

---

<br>

# GROUP BY
Typically, data contains some structure and one is interested in making 
calculations within those structures or performing aggregations across them. 
In SQL there is a `GROUP BY` statement and the same thing exists also in `dplyr`.

For example, to broadcast aggregated variable within groups:

```{r}
mpg %>%
  group_by(manufacturer) %>%
  mutate(avg_cty = round(mean(cty),1)) %>%
  select(ends_with('y')) %>%
  head()
```

Aggregate data at grouping level:
```{r}
mpg %>%
  group_by(manufacturer, class) %>%
  summarize(avg_hwy = round(mean(hwy),1),
            num_cars = n()) %>%
  head()
```


- **Do it yourself**: Calculate the average anomaly of highway fuel consumption 
per model, with respect to average highway fuel consumption across the entire data.

```{r}
# your code comes here:
```

---

<br>

# JOINING data

Joins are an important tool when working with multi-faceted data, typically in SQL. 
These you can do without a problem in `dplyr`.

**LEFT JOIN**, which returns all rows in the left table:

```{r}
left_join(band_instruments,band_members,by='name')
```

**INNER JOIN**, which returns all rows that have a matching key in both tables:

```{r}
inner_join(band_instruments,band_members,by='name')
```

**FULL JOIN** returns all rows:

```{r}
full_join(band_instruments,band_members,by='name')
```

**SEMI JOIN**, return all rows from left table where there are matching values 
in right table, keeping just columns from left table:

```{r}
semi_join(band_instruments,band_members,by='name')
```

A semi join differs from an inner join because an inner join will return one 
row of left table for each matching row of right table, where a semi join will 
never duplicate rows of left table.

**ANTI JOIN**, which returns only those rows in the left table that do not 
occur in the right table:

```{r}
anti_join(band_instruments,band_members,by='name')
```

---

<br>

# ADVANCED manipulation

The `PimaIndiansDiabetes` contains many 0-values that should actually be treated 
as missing information. Such values are usually not allowed in statistical models 
and thus we need to do something about them.

First, we need to do some preparations:

```{r}
# Get data:
data(PimaIndiansDiabetes, package='mlbench')

# Define custom function to replaxce 0's with NA:
repl0 = function(x) ifelse(x==0,NA,x)
# Define custom function to replaxce NA's with mean:
replNA = function(x) ifelse(is.na(x),mean(x,na.rm = T),x)

# Define variables to modify:
nam = setdiff(names(PimaIndiansDiabetes),c('pregnant','diabetes'))
```

And then we can proceed to manipulating the data:

```{r}
PimaIndiansDiabetes %>%
  # Apply function repl0 to selected features:
  mutate_at(vars(nam), repl0) %>%
  # Apply function replNA to selected features:
  mutate_at(vars(nam), replNA) %>%
  # Scale numeric features:
  mutate_if(is.numeric,scale) -> df

head(df)
```

Convert the resulting data into `tidy`-format for effective visualisation:

```{r, warning=FALSE}
library(tidyr)
df %>% 
  gather(key = 'feature', value = 'value', -diabetes) %>%
  ggplot(aes(diabetes,value,fill=diabetes))+
    scale_fill_manual(values=c('skyblue','salmon'))+
    geom_violin()+
    facet_wrap(~feature)+
    theme_bw()+
    theme(legend.position = 'n')
```