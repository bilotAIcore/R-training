<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (2) Data structures in R

To make the best of the **R** language, you'll need a strong understanding of the 
basic data types and data structures and how to operate on those. This is 
important to understand, as these are the objects that are used and manipulate on 
a day-to-day basis in **R**. Dealing with object conversions is one of the most 
common sources of frustration for beginners. There are in fact many different 
types of data in **R**, but we will cover only a part of the full range here.

---

<br>

# Vectors

## *Numeric*
In the first part we generated a simple numerical vector by concatenating four values. 
For the sake of excercise, let's try this again:

```{r}
# Create numeric vector:
x = c(2,6,1,5,12,9)
x
```
```{r}
# Make summary:
summary(x)
```
You have also learnt to genrate a vector of random numbers. 
This time we'll make a vector of uniformly distributed random values:
```{r}
y = runif(n = 20)
```
We know that `y` is a numeric vector with 20 values. Still, and 
especially if we did not know these facts, we can use in-built **R** 
functions to check this:

```{r}
# class of y:
class(y)
```
```{r}
# length of a vector:
length(y)
```
As with numeric scalars (single values), vectors can be used in mathematical 
operations. For example:

```{r}
x + 2*x
```

- **Do it yourself!** sort the values in `x` and use these values to multiply the square root of `x`.
```{r}
# your code comes here:
```

<br>

## *Character*
So far we have considered numeric data, but **R** is not restricted to numeric objects. 
The same as above can be done also with non-numeric objects.

```{r}
# Create a character vector:
a = c('no','yes','what?','no','no','sure')
a
```
```{r}
# Query the class:
class(a)
```
```{r}
# Query length:
length(a)
```
```{r}
# Summarize character vector:
summary(a)
```

```{r}
# Get unique values:
unique(a)
```

While numeric objects can be used in operations, this is not the case with character o
bjects (unlike in e.g. Python):

```{r,eval=FALSE}
a + a
```
However, all operations that can be applied to data structures will work, e.g.:
```{r}
# Reverse vector:
rev(a)

# Sort vector:
sort(a)
```
also, we can use `paste()` to combine character strings:
```{r}
paste(a,rev(a),sep='-')
```

- **Do it yourself!** Calculate the number of different instances in `a`. 
Can you figure out how to make a character vector where all instances in `a` 
occur as many times as "no"?

```{r}
# your code comes here:
```

<br>

## *Logical*
A very usefull type of data results from logical operations made with either 
numerical or character vectors (the utility of logical vectors will become 
clear later). For example:


```{r}
# Logical operation:
test = x > 2
test
```
```{r}
# Query the class of "test":
class(test)
```
The logical object `test` can be used in a similar way as either numeric or character objects. 

```{r}
# Tabulate logical object:
table(test)
```
Conveniently, a logical object can also be interpretted as numerical, i.e., `FALSE = 0` 
and `TRUE = 1`. Thus, one can also make mathematical operations with logical objects:
```{r}
sum(test)
test * 2
```

- **Do it yourself!** Print out how many times either "yes" or "no" occur in `a`.

```{r}
# your code comes here:
```

<br>

## *Factor*
Factors are a special type of character objects that are convenient especially 
in grouping data. A factor has information about its unique entities, called levels.

```{r}
# Make a simple factor:
fact = factor(rep(c('type a','type b'),each=5))
fact
class(fact)
```
We can acces separately the levels as well the number of levels:
```{r}
levels(fact)
nlevels(fact)
```
The utility of factors will be more evident later.

---

<br>

# Matrices
Above we considered *vectors*, which actually are just one-dimensional special 
cases of matrices; two-dimensional data tables. A matrix can contain either 
numeric, character, or logical information, but not a mix of these different 
classes. Here we will cover numeric and logical matrices, as character matrices 
are less common. 

<br>

## *Numeric*
```{r}
# Numeric matrix:
set.seed(7)
X = matrix(data = rpois(30,10),nrow = 10,ncol = 3,
           dimnames = list(paste0('row',1:10),paste0('col',1:3)))
X
```

As you might remember, the length of a vector can be queried with `length()` 
function. Using this function with a matrix gives the total number of cells:

```{r}
length(X)
```

While this might be usefull in some cases, there are more usefull functions 
for getting info of the dimensionality of a matrix:

```{r}
# Get matrix dimensions:
dim(X)
```

This print-out contains the number of rows and columns in the matrix, respectively. 
These can be queried independently as well:

```{r}
# Number of rows:
nrow(X)
# Number of columns:
ncol(X)
```

Moreover, it is possible to view only a part of a matrix, which is handy especially 
when the matrix is large:

```{r}
# Print the top n rows:
head(X,n = 3)
```
```{r}
# Print the bottom n rows:
tail(X,n = 3)
```

A vector could be manipulated by either sorting the values or reversing the ordering 
of elements. These operations can also be done with matrices, but additionally a 
matrix can be transposed (meaning that the rows are exchanged with columns and vice versa):

```{r}
# Matrix transpose:
t(X)
```

- **Do it yourself!** As with vectors, (numeric) matrices can be subjected to 
mathematical operations. Devide matrix `X` with the square root of the total 
sum of `X` and round the result to the closest integer.

```{r}
# your code comes here:
```

<br><br>

## *Logical*
As before with logical vectors, a logical matrix usually arises from a logical operation. 
For example:

```{r}
L = X < 10
L
```

One could then, e.g., calculkate the number of matches either per column or per row:
```{r}
colSums(L)

rowSums(L)
```

- **Do it yourself!** How many times does 11 occur in matrix `X`? Which row 
contains the most instances of number 9?

```{r}
# your code comes here:
```

---

<br>

# Data frames
A data frame is a list of variables of the same number of rows with unique row names, 
given class "data.frame". The most obvious difference between a data.frames and a 
matrix is that data.frames can contain data of different type. While one can create 
a data.frame by combining different variables, this type of data structure usually 
arises from loading data into **R** (this will be covered later). The base 
installation of **R**, as well as each separate package, contains some data sets 
that can be easily accessed using the `data()` function:

```{r,eval=FALSE}
data()
```

This will open a listy of available data sets. 
Let's pick a simple example data (Note: each data set also has a help file):

```{r}
# Load a inbuilt data into memory:
data(chickwts)
```

```{r}
# Query the structure of the data:
str(chickwts)
```

The `str()` function tells us that "chickwts" is a data.frame with 71 rows and two 
variables (columns). It also lists the names of the vartiables, their classes and a 
glimpse of their values. 
We can also look at, e.g., the header of the data.frame:

```{r}
head(chickwts)
```

or calculate the summary:
```{r}
summary(chickwts)
```

Notice that the summary for "weight" is different from that for "feed", as the first 
is numeric and the latter is categorical.

- **Do it yourself!** Make your own data.frame by combining variables `x` and `a` 
and calculate the summary.

```{r}
# your code comes here:
```

---

<br>

# Lists
Dataframes are actually special cases of a more general data structure, a `list`. 
Whereas is a `data.frame` all variables are of same length, this is not a general 
requirement in a `list`, where any objects can be stored.

A list can be generated using the `list` function:

```{r}
my_list = list(num_vector = x,
               rnd_vector = y,
               chr_vector = a,
               factor = fact,
               num_matrix = X,
               bool_matrix = L,
               dataframe = chickwts)
```

As with a `vector`, we can examine the length of a `list`, which corresponds to the  
number of objects in the list:

```{r}
length(my_list)
```

As with a `data.frame`, we can examine the structure of a `list`:

```{r}
str(my_list)
```