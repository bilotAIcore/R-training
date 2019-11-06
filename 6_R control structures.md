<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (6) Control structures in R

As any programming language, **R** contains several control structures to, 
e.g., control the flow of execution of a script, iterate through different 
scenarios, or make logical checks.

---

<br>

# Logical control structures
The simplest control structure is the `ifelse()` function:

```{r}
# Example data:
x = runif(10)

# Generate new variable using ifelse:
y = ifelse(x>0.5, 1, 0)
y
```

The apparent simplicity of `ifelse()` hides quite some complexity. 
This is because the function is actually a *wrapper* af a more 
general if-else structure for performing a specific function.

The if-else structure works as follows:

```{r}
if(any(y==1)) print('yes')
```

So, within the `if()` function a logical test is performed. If this 
test returns `TRUE`, the part following the if-statement is executed and otherwise not.

Even more generally:

```{r}
input = TRUE
if(input){
  print('proceed')
}else{
  print('halt!')
}
```

Notice that there are curly brackets `{}` here. These are used in **R** to 
specify where a control structure strats and where it ends.

---

<br>

# Loops
Sometimes it would be usefull to iterate through different options and 
perform some operations. This and many other things can be achieved with loops. 

## *The while loop*
In most computer programming languages, a while loop is a control flow 
statement that allows code to be executed repeatedly based on a given 
Boolean condition. The while loop can be thought of as a repeating if statement.

For example:

```{r}
k = 0
while(k < 5){
  print(k)
  k = k+1
}
```

That is, the while-loop executes anything that is contained within 
the curly brackets (in this case increase the value of `k` by one), 
as long as the logical requirement within the normal brackets is met. 
This is particularly usefull if one needs to iterate a process untill 
some convergence critiria are met.

## *The for loop*
In computer science, a for-loop (or simply for loop) is a control 
flow statement for specifying iteration, which allows code to be 
executed repeatedly. This is an important work-horse of programming, 
which can be found at the core of most **R** functions.

As such, the for loop is even simpler than the while loop:

```{r}
for(index in 1:5){
  print(index)
}
```

A common application is to perform an operation to data, across the 
levels of a grouping variable:

```{r}
# Example data:
data(chickwts)
group = chickwts$feed
n = nlevels(group)

# Create an ampty vector for results:
mean_weights = numeric(n)

# Loop through chick feed types:
for(ii in 1:n){
  # find correct values: 
  w = which(group == levels(group)[ii]) 
  mean_weights[ii] = mean(chickwts$weight[w])
}
names(mean_weights) = levels(group)
mean_weights
```

Here the `for()` loop iterates trough values from one to `n`, using `ii` 
as an index within the loop. This index is used to select one level of the 
grouping variable at a time. Then, for each iteration, the mean of the chick 
weights corresponding to the given feed type is stored to the vector `mean_weights`.   

- **Do it yourself!** Execute a while loop until a random number exceeds your age 
(make sure that you select/tune a random number generator accordingly).

```{r}
# your code comes here:
```
