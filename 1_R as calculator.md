<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (1) R as a calculator

<p style='text-align: justify;'><b>R</b> is a free software environment for statistical computing and graphics. One of <b>R</b>'s strengths is the ease with which well-designed publication-quality plots can be produced, including mathematical symbols and formulae where needed. Great care has been taken over the defaults for the minor design choices in graphics, but the user retains full control. <br />
  
Many users think of R as a statistics system, while it is more of an environment within which statistical techniques are implemented. <b>R</b> can be extended (easily) via packages. There are about eight packages supplied with the <b>R</b> distribution and many more are available through the CRAN (https://cran.r-project.org/mirrors.html) family of Internet sites covering a very wide range of modern statistics.
We will begin by explorting the use of <b>R</b> as a basic calculator.
</p>

---

<br>

# Basic operations
<b>R</b> can be used as a calculator in order to perform mathematical operations, for example:

```{r}
26 + 17
598 - 43
2*12
25/5
```

- **Do it yourself!** Calculate the product between the sum of 3.5 and 4.12 and the division of 5 with 1.3:
```{r}
# your code comes here:
```

---

<br>

# Assignment
<p style='text-align: justify;'>Unless an operation is assigned to an <i>object</i>, the results will only be printed to the *console*. Conveniently, the result from any operation can be stored in an object, which means the results can be used elsewhere. </p>
  
```{r}
# note the different ways you can make an assignment:
a = 26 + 17
b <- 598 - 43
2*12 -> c
d = 25/5
print(a)
```

Moreover, such objects can be used in operations as well:
```{r}
a + b
c / d
```

- **Do it yourself!** Save the product of `b` and `c` to a new object and substract this object from `a` and add your age in months:
```{r}
# your code comes here:
```

---

<br>

# Basic functions
<p style='text-align: justify;'>Of course, <b>R</b> is much much more than just a calculator. The power of <b>R</b> is in the availability of a vast number of different functions. There are even hundreds of functions included in the base installation of <b>R</b>. Let's look at some very basic functions: </p>

```{r}
# list the content of the working memory:
ls()
```
```{r}
# concatenate:
x = c(a,b,c,d)
x
```
```{r}
# sum:
sum(x)
```
```{r}
# summary:
summary(x)
```

- **Do it yourself!** Find out how to use the `rnorm` function and generate a series of 100 random numbers with mean of 25.7 and standard deviation of 12.3. Next, figure out how to check the mean and SD of the generated series.

```{r}
# your code comes here:
```

---

<br>

# Libraries
<p style='text-align: justify;'>The base installation of <b>R</b> contains many functions, but often one needs to do something that is not implemented. Luckily, there are countless additional libraries that can be called into <b>R</b> to expand its functionality. You can list all installed packages as:</p>

```{r,eval=FALSE}
installed.packages()
```
If the package you need is not already installed, you can get it via:
```{r,eval=FALSE}
install.packages()
```
And finally, to use a package, you need to load it:
```{r,eval=FALSE}
# Use either:
library()

# or 

require()
```

- **Do it yourself!** First install the `dplyr` package and then load it.
```{r}
# your code comes here:
```
