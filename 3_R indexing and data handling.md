<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (3) Indexing and data handling in R

In this part you will become familiar with various ways one can handle 
and manipulate objects and data in **R**. 

---

# Indexing
So far we have considered objects as a whole, but this is (of course!) not 
the end of the line. One can conveniently access, select, remove, and 
manipulate only parts of a given object and even append them selectively. 
All this is done via indexing.

<br>

## *Vectors*
The first thing to learnis to know how different types of brakets differ 
in their meaning. We already know that **R** functions use regular `()` 
brackets (these have to be used even if there are no input arguments). 
If you want to index an object, however, you need to use square brackets `[]`.

This is how it works:

```{r}
# Create numeric vector:
x = c(2,6,1,5,12,9)

# Access the 3rd element (or cell) in the vector:
x[3]
```

That is, an object (either a vector, matrix, or data.frame) has kind of an 
internal address system that tells where exactly each element can be found. 
The analogy with addresses is actually quite good, as the most important 
characteristic of indices is that they need to be integers (such as street 
addresses). Thus, one might think that the following will not work:

```{r}
x[1.3]
```

But it does! How is this possible (in fact, it actually was not possible 
in the past)? What happens is that **R** is being clever. It recognises that 
you are trying to use an illegal value as index and tries to correct for the 
mistake by *rounding* the intended index. Notice the the rounding is actually 
done to the next integer, because there is nothing at the index position 0 
(**try and see!**). Note that there is also nothing at other index positions 
that exceed the size of an object (**try and see!**). This is important 
to keep in mind.

As with the single index, we can select several elements at once, 
by giving a vector of integers as index to vector `x`:

```{r}
# Selectin a range:
x[1:3]
```
```{r}
# Making a selection:
x[c(4,1,3)]
```

Notice that the indexing does not need to be given in any specific order. 
This means that you can conveniently reorder an object with indexing. For example:

```{r}
# Reverse vector:
x[length(x):1]
```
OK, now you know the basics. All you need to remember here is that the index needs 
to be an integer (or a value that can be converted to an integer) and that this 
integer needs to be within the limits of the dimensions of an object. 

But wait, what about negative values! Negative addresses do not excist, right? Well...

```{r}
# Negative indexing:
x[-3]
```
If you compare the result with vector `x`, you might notice that the third element 
is missing. Thus, negative indexing is used to *exclude* elements from an object.

A powerfull approach is to use logical operations to index objects. One can use 
the logicals as such, or convert them into numerical indices. This can be done 
with function `which()`:

```{r}
which(x == 1)
```

- **Do it yourself!** How would you print out only those values of vector `x` 
exceeding 5? What are their indices? What is the index of the maximum value of `x`?

```{r}
# your code comes here:
```

<br>

## *Matrices*
After getting familiar with 1-dimensional indexing it's time to switch gears to 
meet 2D indexing (and it does not even stop there!). Naturally, if you think of a 
matrix that has both rows and columns, one should be able to index both ways. 
The only new concept needed here is: the comma `,`!

```{r}
# Create an example matrix:
set.seed(12) # fix random seed for reproducibility
X = matrix(data = rpois(15,10),nrow = 5,ncol = 3)
X
```

The `dimnames` argument was left out on purpose here, so that you can see the 
logic of matrix indices; row and column indices are separated by `,`. Importantly, 
if one only wants to index rows or coloumns, the index for the other dimension 
in left *empty*.

This is how it works:

```{r}
# Index rows:
X[2:4,]

# Index columns:
X[,c(1,3)]

# Index rows and columns simultaneously:
X[c(2,4),c(1,3)]
```

- **Do it yourself!** Find the row in `X` with the highest total count?

```{r}
# your code comes here:
```

<br>

## *Data.frames*
Already knowing how to index 2D objects, there isn't that much more to 
learn considering data.frames. Still, there are a few usefull tricks to know.

Let's use a simple in-built example data:

```{r}
# Load a inbuilt data into memory:
data(chickwts)
head(chickwts,3)
```
One can index this data.frame as any matrix:

```{r}
chickwts[c(nrow(chickwts),1,6),]
```

It is also possible to concentrate on individual variables in a data.frame by appending 
the data.frame with `$variablename`:

```{r}
chickwts$weight[1:5]
```

Alternatively, the variable name can be given as an index:

```{r}
chickwts[1:5,'weight']
```

This approach also works with matrices, given that column/row names have been specified:

```{r}
# Example matrix:
data = strsplit(head(help("sum")),'/')[[1]]
A = matrix(data,3,3,
           dimnames = list(paste0('row',1:3),paste0('col',1:3)))

# Indexing:
A[c('row1','row3'),'col3']
```

Finally, each variable in a data.frame can also be accessed using bouble brackets `[[]]`:

```{r}
chickwts[[1]][1:10]

levels(chickwts[['feed']])
```

- **Do it yourself!** What is the maximum weight for chicks fed with casein?

```{r}
# your code comes here:
```

---

<br>

# Data handling & manipulation

Next we will see how one can make changes to data tables by manipulating, removing, 
appending, and combining.

<br>

## *Manipulate*
As data.frame is probably the most commonly used data structure in **R**, we will 
here use that as an example. However, the same principles can be used to handle any 
other data type.

For the sake of excersice, let's generate our own data.frame (note the use of indexing here):

```{r}
df = data.frame(count = X[c(1:5,3),1], 
                word = c('cat','dog','horse','python','pig','pidgeon'),
                stringsAsFactors = F)
df
```

This data.frame could for example contain the number of times each word appears in a text.

Now, let's say that we notice that there is an error: the imaginary text actually 
has only 3 instances of `'sum'`. Luckily, we can easily fix this by locating the 
erraneous entry in the data.frame and replacing the value:

```{r}
df[df$word=='pidgeon',1] = 21
tail(df,1)
```

What if we would want to change the appearance of the words? Let's say we want to 
make `dog` to upper-case `DOG`? Again, we just need to locate the correct 
element and change it:

```{r}
df[df$word=='dog','word'] = 'DOG'
df[df$word=='DOG',]
```

We can also operate along one dimension of an object, e.g., by modifying an 
entire variable at one go:

```{r}
# Replace capital letters in the 'word' variable with lower case:
df$word = tolower(df$word)
df$word
```

Sweet!

Another example: let's say that the counts are off by one order of magnitude. 
It is tedious to change all values manually, but luckily we don't have to:

```{r}
df$count = df$count * 10
df
```

Great! 

- **Do it yourself!** How would you convert the count values in `df` 
to proportions of total word count?

```{r}
# your code comes here:
```

<br>

## *Append*
We can also append the `data.frame` by adding new columns/rows. 
For example, let's add a new column that contains the square root 
of word counts:

```{r}
df$sqrt_count = sqrt(df$count)
head(df,3)
```

Adding a row is slightly more complicated in this case (it's easier with matrices), 
as the data.frame contains several different data types (integer and character):

```{r}
df[nrow(df)+1,] = data.frame(count=153,word='and', sqrt_count=sqrt(153))
tail(df,1)
```

unless the assignment is done using the `data.frame()` function, `df` will be 
converted into a character matrix! 

- **Do it yourself!** Add a new variable to `df` that gives the number of characters in each word.

```{r}
# your code comes here:
```

<br>

## *Remove*
We might also want to remove something from our data. Let's say that we decide to 
exclude the column with sqrt counts from our `data.frame`:

```{r}
# Find the correct column and remove it:
df = df[,-which(names(df)=='sqrt_count')]
head(df,3)
```

- **Do it yourself!** The newly added row has to go. How would you do this?

```{r}
# your code comes here:
```

<br>

## *Combine*
There are also convenient ways for combining data from different tables:

```{r}
# Combine column-wise:
df2 = cbind(df,chickwts[1:nrow(df),])
head(df2)

# Combine row-wise:
df3 = rbind(df[1:3,],df[2:6,])
df3
```

- **Do it yourself!** Add two new columns to matrix `X`, assuming the same input type (integers).

```{r}
# your code comes here:
```
