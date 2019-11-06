<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (14) Predictive modeling

# DATA import
The first thing to do is to import data to R. The example data about 
the insidence of diabetes among the Pima indians. The data 
`diabetes.csv` is a comma (,) separated file, where the last column 
is the "label"; does a person have diabetes or not?. 

We can use the `read.csv` function to read in the data, 
which asummes by default that the data is `,`-separated.

```{r}
dataset = read.csv('diabetes.csv',header = T)
head(dataset)
```

---

<br>

# Inspect DATA properties
It is good practice to see how the data looks like. For example, 
how many observations and features there are:

```{r}
# Data dimesionality:
dim(dataset)
```

or what is the proportion of different outcomes in the variable of interest?

```{r}
# See how the label is distributed:
prop.table(table(dataset$Outcome))
```

It is also usefull to be aware of the correlation structure between 
explanatory variables, as this can be important when using certain 
machine learning models (or statistical models in general):

```{r}
# Inspect correlations between variables:
require(corrplot)
C = signif(cor(dataset[,-ncol(dataset)]),2)
par(mgp=c(2,.8,0),mar=c(0,3,1,1))
corrplot(C,type = 'lower',method = 'ellipse',
		 col = colorRampPalette(c('blue3','white','red3'))(21))
```

From this figure (or the actual values) it can be concluded that the features 
are not particularly strongly correlated.

---

<br>

# Split DATA
When doing predictive modeling, it is very important to be able 
to evaluate the predictive power of a model. The simplest way 
to do this is the so-called hold-out method. Here the data is 
split (usually with random sampling) to two parts, one used 
for model training and the other for testing. The splitting 
is often done such that 70-80% is used for training, but the 
optimal partitioning depends, e.g., on the amount of data.

```{r}
# Create a random partitioning of the data:
train = rbinom(nrow(dataset),1,p = 0.7) == 1
signif(prop.table(table(train)),2)
```

As one could have expected, the `train` object now contais 
about 70% of `TRUE` cases, which can be used to index the `dataset`. 
Now we can proceed to split the data to two parts:

```{r}
# training set:
df_tr = dataset[train,]

# testing set:
df_ts = dataset[!train,] # note the complement operator (!)
```

At this point it is good to check how representative the sample is.

```{r}
# Tabulate label:
prop.table(table(dataset$Outcome)) # Full data

prop.table(table(df_tr$Outcome)) # Training data

prop.table(table(df_ts$Outcome)) # Testing data
```

Looks like the random sampling *has not* introduced any significant bias.

---

<br>

# Train model
Let's first try a simple decision tree model: 

```{r,message=FALSE}
require(rpart)

# Train model:
dt.model = rpart(Outcome~.,data=df_tr)

# Output prediction:
pred_tr = ifelse(predict(dt.model,df_tr) > 0.5,1,0)
```

Now what? We need some means to evaluate the quality of the model. 
A simple way rto do this is to calculate the so-called confusion matrix:

```{r}
# Function fro deriving the confusion matrix:
conf = function(y,pred) table(y,pred,dnn=c('observed','predicted'))

conf(df_tr$Outcome,pred_tr)
```

In this matrix the correctly classified examples (patients in this case) 
can be found on the diagonal of the matrix. The most simple metric of 
model performance that can be derived from the confusion matrix is called 
model *accuracy*, where 1 indicates perfect classification and 0.5 is 
the boundary of any classification ability:

```{r}
M = conf(df_tr$Outcome,pred_tr)
accyracy_tr = sum(diag(M))/sum(M)
print(accyracy_tr)
```

Another commonly used metric is the area under the receiver-operator 
characteristic curve (ROC-curve), which indicates the ability of 
the model to correctly classify positive cases. The value of the AUC 
ranges between 0 amd 1 (as with accuracy)

```{r,message=FALSE,fig.height=4,fig.width=5}
require(ROCR)
tmp = prediction(predict(dt.model,df_tr),df_tr$Outcome)
perf = performance(tmp,"tpr","fpr")

par(pty='s',mgp=c(2,.8,0),mar=c(3,3,1,1))
plot(perf,type='s',lwd=2,col='red3')
  abline(0,1,lty=5)
  
auc = performance(tmp,"auc") 
unlist(auc@y.values)
```

It looks like the classifier performs quite well on the training data.

---

<br>

# Evaluate model performance
When one is happy with the model, evaluated egainst the training set, 
it is time to see how well the classifier is able to handle data that 
has not been used for training. Here is where the testing set comes into play.

```{r}
# Output prediction for testing data:
pred_ts = ifelse(predict(dt.model,df_ts) > 0.5,1,0)

M = conf(df_ts$Outcome,pred_ts)
accyracy_ts = sum(diag(M))/sum(M)

print(M)
accyracy_ts
```

So how much does this differ from that observed before?

```{r}
1-accyracy_ts/accyracy_tr
```

There is always some drop in accuracy with the testing set, as again 
this is new data for the classifier. The larger the drop in performance, 
the stronger is the case for over-fitting, meaning that the model does 
not generalise well. 

```{r,message=FALSE,fig.height=4,fig.width=5}
tmp = prediction(predict(dt.model,df_ts),df_ts$Outcome)
perf = performance(tmp,"tpr","fpr")

par(pty='s',mgp=c(2,.8,0),mar=c(3,3,1,1))
plot(perf,type='s',lwd=2,col='red3')
  abline(0,1,lty=5)
  
auc = performance(tmp,"auc") 
unlist(auc@y.values)
```

While the performance does not look too promising, it is fair to note that 
this data is actually rather hard to classify. Try and see for yourself, 
**can you do better?** 

---

<br>

# Model interpretation
An important asset of tree-based models is that the internal ranking of 
features, which is needed for the splitting of examples, can be used to 
infer how important each feature is for the classification.

Let's csee what the `dt.model` object holds inside: 

```{r}
names(dt.model)
```

We can plot the relative (scaled between 0-100) variable importance to 
see which features are likely to be the best indicators of the outcome:

```{r}
tmp = dt.model$variable.importance
names(tmp) = substr(names(tmp),1,5)
par(mgp=c(2,.8,0),mar=c(3,3,1,1))
barplot(sort(tmp,decreasing = T),las=2,col='skyblue2',ylab='Importance')
```

It is probably not that surprising that the blood glucose level and BMI are 
most important when trying to identify whether a patient has diabetes or not.  

Another thing we counld plot is the convergence of the model in terms of 
classification error: 

```{r}
par(mgp=c(2,.8,0),mar=c(3,3,1,1))
with(as.data.frame(dt.model$cptable),
     plot(nsplit,`rel error`,type='b',pch=16,
          col='skyblue2',ylim=c(0.5,1)))
```

Finally, it is also possible to plot the structure of the decision tree model, 
showing the different splits and the probability of a positive outcome at each 
node, as well as the percentage of examples:

```{r,message=FALSE}
require(rpart.plot)
par(mar=c(1,1,1,1))
rpart.plot(dt.model)
```

When thew data structure is relative simple (there aren't too many features), 
this figure can give a very good idea of what factors affec (potentially 
interactively) the probability of a postive outcome.