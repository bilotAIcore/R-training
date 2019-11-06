<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (8) Meet the apply family

<br>

The apply family pertains to the R base package, and is populated with 
functions to manipulate slices of data from matrices, arrays, lists and 
dataframes in a repetitive way. These functions allow crossing the data 
in a number of ways and avoid explicit use of loop constructs (the loops 
are actually built in the functions). They act on an input list, matrix 
or array, and apply a named function with one or several optional arguments.

---

<br>

# Apply
Apply is a function that lets you to apply any function to an object, 
say a matrix, along either the rows or columns. Let's try a simpel example:

```{r}
# Make example data:
set.seed(123)
X = matrix(data = c(rpois(10,10),rpois(10,5),rpois(10,20)),nrow = 10,ncol = 3,
           dimnames = list(paste0('row',1:10),paste0('col',1:3)))
X
```
```{r}
# Apply an inbuilt function to each column in X:
apply(X,MARGIN=2,FUN='mean')
```

This is just a slightly complicated way of doing `colMeans()`. There is, 
however, no handy way of calculating the standard deviation for each 
variable in a data table and thus `apply` comes very handy:

```{r}
# Apply an inbuilt function to each column in X:
apply(X,MARGIN=2,FUN='sd')
```

The great thing about apply is that you can, literally, use any function you please.

```{r}
# Apply a custom function to each column in X:
apply(X,MARGIN=2,FUN=function(x) x/max(x))
```
In this example we have normalised `X` by deviding each column by the 
column maxima. Remember that the function is sequentually applied to 
each element along the specified dimension.

---

<br>

# Tapply
Okey, it is great that one can apply a function across an entire table. 
However, it is often the case that one would need to perform operations 
across a table, such that the operation is perform within levels of a 
grouping factor.

A simple solution is to use `tapply`, which applyies a function to each 
cell in a vector, within each level of a given factor:

```{r}
# Define an arbitrary grouping factor:
group = factor(rep(c('a','b'),each=5))

# Apply 'mean' along the group-factor:
tapply(X[,1],group,'mean')
```

This is of course not quite what we would want, as the operation 
can be performed only to one vector at a time. The options here is to 
either use a `for()` loop or, use `apply` to perform `tapply`:

```{r}
apply(X,2,function(x) tapply(x,group,mean))
```

What do you know! Exactly what we wanted, illustrating the power of `apply`.

- **Do it yourself!** Standardise the weight of chicks within each 
feed type in the `chickwts` data.

```{r}
# your code comes here:
```

---

<br>

# Aggregate
Above we used a combination of `apply` and `tapply` to summarise a data 
table, according to a grouping factor. While this is a perfectly valid 
approach, **R** has, again, alternative approaches. It so happens that 
there is a function called `aggregate`, which does this combination of 
apply-function for you:

```{r,echo=TRUE}
aggregate(X,by=list(group),'mean')
```

Here the `by` argument can take a list of several grouping fators, 
which is often quite convenient.

---

# Sapply
Finally, we'll get familiar with `sapply` (thre are still more...). 
This function is a version of `apply` specificaslly ment to be used 
with lists and data.frames. Due to characteristics of these data structures, 
the `MARGIN` argument sopecifying the applied dimension is not needed. 
This function comes handy especially when you need to know, e.g., the 
properties of each variable in a data.frame. For example:

```{r}
data("airquality")
sapply(airquality,class)
```

- **Do it yourself!** Calculate the average airquality values per month, 
but using only integer variables.

```{r}
# your code comes here:
```