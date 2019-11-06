---
title: (1) R as a calculator
author: "Lasse Ruokolainen, Bilot Consulting Oy"
---

<img src="redstudio.png" 
style="position:absolute;top:5px;right:30px;" />

---

<br><br>

<p style='text-align: justify;'>**R** is a free software environment for statistical computing and graphics. One of **R**'s strengths is the ease with which well-designed publication-quality plots can be produced, including mathematical symbols and formulae where needed. Great care has been taken over the defaults for the minor design choices in graphics, but the user retains full control.
Many users think of R as a statistics system, while it is more of an environment within which statistical techniques are implemented. **R** can be extended (easily) via packages. There are about eight packages supplied with the **R** distribution and many more are available through the CRAN (https://cran.r-project.org/mirrors.html) family of Internet sites covering a very wide range of modern statistics.
We will begin by explorting the use of **R** as a basic calculator.
<p>

---

<br><br>

# Basic operations
**R** can be used as a calculator in order to perform mathematical operations, for example:
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

<br><br>

# Assignment
<p style='text-align: justify;'>Unless an operation is assigned to an *object*, the results will only be printed to the *console*. Conveniently, the result from any operation can be stored in an object, which means the results can be used elsewhere.
<p>
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

<br><br>

# Basic functions
<p style='text-align: justify;'>Of course, **R** is much much more than just a calculator. The power of **R** is in the availability of a vast number of different functions. There are even hundreds of functions included in the base installation of **R**. Let's look at some very basic functions:
<p>
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

- <p style='text-align: justify;'>**Do it yourself!** Find out how to use the `rnorm` function and generate a series of 100 random numbers with mean of 25.7 and standard deviation of 12.3. Next, figure out how to check the mean and SD of the generated series.
<p>
```{r}
# your code comes here:
```

---

<br><br>

# Libraries
<p style='text-align: justify;'>The base installation of **R** contains many functions, but often one needs to do something that is not implemented. Luckily, there are countless additional libraries that can be called into **R** to expand its functionality. You can list all installed packages as:
<p>
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