<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (7) Write your own function

<br>

Functions really are the salt of **R**. While there are countless 
functions already awailable, (a) you might not be aware of their 
existence, (b) or you might have very specific needs. In these 
cases it is good to know how to write a function of your own. 

---

<br>

# Example function
To get started, let's make a simple function to mimic coin 
tossing. Assuming a fair coin (i.e., heads and tails have equal probability):

```{r,eval=FALSE}
cointoss = function(n){
  # Input arguments: n = number of coin tosses
  
  # Generate n random numbers: 
  x = runif(n)
  
  # Generate output:
  # How should this line be updated?
  result = 
    
  return(result)
}
```

**Question**: How could we modify this function to introduce bias to the coin toss? 

---

<br>

# Your own function
Feeling comfortable? It's time to use your imagination and try to
 convert that into form of a usefull **R** function:
 
```{r}
# your code comes here:
```