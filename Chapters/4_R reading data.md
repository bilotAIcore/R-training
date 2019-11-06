<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (4) Reading data into R

Usually, the user wants to work on their own data instead of simulated or 
self-contained data in **R**. While the most common types likely 
are `.csv`, `.txt`, and `.xls`, there is no need to stop here. This yet 
another srtrength of **R**; one can bring practically any data to **R**, 
no matter the format. Moreover, you don't have to settle with local 
files; **R** can also get data directly from ftp and SQL servers, 
API's, and web pages. Here we will concentrate on mastering the 
reading and writing of local files.

---

<br>

# Reading and writing files

## *.csv files*
Let's start by wiping the working memory clean:

```{r}
# Clear working memory:
rm(list = ls())

# List working memory content:
ls()
```

Now that the we have a clean "desktop", we can continue to create an 
example data that will be stored to disk:

```{r}
# Write an example file to disk:
tmp = data.frame(class = rep(c('a','b'),each=5),
                 value1 = rep(1:5,2),
                 value2 = runif(10))
head(tmp,3)
```

Now let's write the `tmp` data.frame to disk: 

```{r}
# Write as .csv:
write.csv(tmp,file='test.csv', row.names = FALSE)

# remove data from memory:
rm(tmp)
```

Great, we have successfully saved the data and wiped it from the working 
memory (use `ls()` to check it is really gone), we can get it back by 
reading it from disk using `read.csv()`: 

```{r}
tmp = read.csv('test.csv',sep=';',header = T)
head(tmp)
```

Darn, something went wrong! Can you figure out what's the problem?

- **Do it yourself!** There is a generic function for reading in local 
text files `read.table()`. How would you use this function to 
succesfully load the `test.scv` file?

```{r}
# your code comes here:
```

<br>

## *Alternative approach*
There is an alternative to the **R** functions used above.

```{r}
# Load new tools:
require(readr)

# Read the test file:
tmp = read_csv('test.csv')
head(tmp)
```

This seems to work great out of the box. There is also a generic 
reading function in `readr`, called `read_delim`.

---

<br>

# Reading data from URL

It is also possible to read data directly from the web. 
This can be done e.g. by giving the URL location of a data file 
to any function reading `csv`-files (or any format that the target 
file happens to be in)

For example, fetching the classic `iris` data from an online repository:

```{r}
url = 'https://www.openml.org/data/get_csv/61/dataset_61_iris.arff'
df = read.csv(url)
head(df)
```

---

<br>

# Getting data from API 

Many instances offer their data via APIs (*Application Programming Interface*), 
either openly or behind authentication. When accessing an API one typically 
gets a `JSON` file as a response, which needs to be parsed. Luckily, there 
are ready solutions available for automatically getting the `JSON` and 
parsing that to a `list` or `data.frame`.

As an example, let's get some real-time data about the status of 
New York CityBike stations: 

```{r, message=F}
library(jsonlite)

# Get JSON from API:
citibike = fromJSON("http://citibikenyc.com/stations/json")

# Extract station data from response:
stations = citibike$stationBeanList

colnames(stations)
head(stations)
```

---

<br>

# Exporting data
While exporting `data.frames` is conveniently made by e.g. the 
`write.csv()` function, this does not allow for exporting other 
object types. For example, one might want to save a custom 
function or a model object to a file. To do this, there are 
several alternatives

Let's first make something to save:

```{r}
# Calculate summary:
s = summary(tmp)
s
```

<br>

## *.RData format*
One way to store the object `s` to use `.RData` format, which retains 
the object name, using the `save()` function: 

```{r}
# Save to .RData format:
save(s,file = 'summary.rda')

# Remove object:
rm(s)
```

Then we can get the object back by using the `load()` function:

```{r}
load('summary.rda')
s
```

Note that the loading was not assigned to an object.

<br>

## *.RDS format*
Another option for storing objects is the `.RDS` format, standing for 
*serialized* data. In contrast to '.RData', the object itself is not 
retained, only the content. The function to save to `.RDS` is `saveRDS()`:

```{r}
# Save to .RData format:
saveRDS(s,file = 'summary.rds')

# Remove object:
rm(s)
```

And then to read the object back to **R**, one needs the `readRDS()` function:

```{r}
ss = readRDS('summary.rds')
ss
```

Note that the loading *was* assigned to an object.
