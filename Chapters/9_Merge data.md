<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (9) Merge and aggregate data

<br>

# Merging data

Sometimes a data is not only in one data.frame. It may be in multiple files and 
formats. To make the analysis we need the data to be in one tidy data.frame and 
we get it by merging data.frames. For example SQL databases have many tables which 
usually can be joined by some key variable. A key variable shows which rows are 
measured from the same object.

<br>

## *Unique keys*
Let's check a simple example about data merge (same as join). Let's create 
two data: one that has the name of the customer with the age and other one 
with the name and the gender. The key variable is the customer name.

```{r}
# Example data on people age:
agedata = data.frame(name = letters[1:5], 
                      age = c(27, 65, 46, 34, 10))

# Examp,e data on people gender:
genderdata = data.frame(name = letters[c(3, 5, 4, 6, 1)], 
                          gender = c("Male", "Female", 
                                     "Female", "Female", "Male"))

# names are in different order and 'f' is missing from 'agedata' and 'b' from 'genderdata' 

agedata

genderdata
```

Let's inspect different joins: inner join, left join and outer join. 
See what happens with the key values that aren't in both data.

```{r}
# Inner join: all = FALSE
# Result: only the key values which are in both data 
merge(agedata, genderdata, by = "name", all = FALSE)
# Left join: all.x = TRUE
# Result: only the key values which are in the left (x) data 
merge(agedata, genderdata, by = "name", all.x = TRUE)
# Outer join: all = TRUE
# Result: all the key values
merge(agedata, genderdata, by = "name", all = TRUE)
```

- **Do it yourself!** How would you do the right join and what would be the result? Think about it first and then write the code. Were you correct or was the result something unexpected?

```{r}
# your code comes here:
```

<br>

## *Replicate keys*
Sometimes data have multiple times the same id, for example the customer 
buys several products. Let's simulate a sales data which shows the customer 
(buyer), product group, the price of one item and the amount of items bought:

```{r}
set.seed(20) # to keep the simulated data the same

buyer = sample(letters[1:6], size = 30, replace = TRUE)
product_group = sample(LETTERS[1:3], size = 30, replace = TRUE) 
price = sample(1:100, size = 30, replace = TRUE)
amount = sample(1:5, size = 30, replace = TRUE)
salesdata = data.frame(buyer, product_group, price, amount)
head(salesdata)
```

Let's merge this with the previously joined data with all customers. 
Now, pay attention to the fact that the key variable names differ in these data.

```{r}
# previous merge:
customer = merge(agedata, genderdata, by = "name", all = TRUE)

# merging customers and sales
merge(customer, salesdata, by.x = "name", by.y = "buyer", all = TRUE)
```

Great! But how to get one row per customer?

---

<br>

# Aggregating data

Now we have a nice and tidy data about customers and their purchase activity. 
But what if we want to for example know which product group is the most popular? 
For this we have to calculate the sum of the amount of products bought in each 
prouct group. This is aggregating the sum of the amount by the product group:

```{r}
aggregate(x = salesdata[,"amount"], 
          by = list(salesdata[,"product_group"]), 
          FUN = sum)
```

The B group seems to be the most popular.

- **Do it yourself!** Calculate the total price for each row of the 'salesdata'. 
Aggregate the sum of the total price for each customer and join the aggregated 
data with the customer data.

```{r}
# your code comes here:
```