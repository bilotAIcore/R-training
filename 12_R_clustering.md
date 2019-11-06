<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (12) Clustering in R

[Cluster analysis](https://en.wikipedia.org/wiki/Cluster_analysis) or 
clustering is the task of grouping a set of objects in such a way that
 objects in the same group (called a cluster) are more similar (in some sense)
  to each other than to those in other groups (clusters). Clustering has many 
  practical applications. For example, it is used in marketing to assess the 
  demographics of consumers. By knowing more about different market segments, 
  consumers can be more accurately targeted with commercials.

---

<br>

# *K*-means partitioning
In *K*-means clustering the goal of is to find groups in the data, 
with the number of groups represented by the variable *K*. The 
algorithm works iteratively to assign each data point to one of *K* 
groups based on the features that are provided. Data points are 
clustered based on feature similarity.

<br>

## *Cluster assignment*
Let's use the `mtcars` data as an example. We first need to scale 
the features, as the variance in each feature influences their 
weight in the analysis. We'll also select only those features 
that are floats, for simplicity. 

```{r, message=FALSE}
library(dplyr)
mtcars %>% 
  select(mpg,disp,hp,drat,wt,qsec) %>%
  mutate_all(scale) -> x

head(x)
```

Next we can proceed to performing the clustering, 
using the `kmeans()` function:

```{r}
k = 2
clust = kmeans(x,centers = k,iter.max = 100)

# clusters sizes:
table(clust$cluster)

# Between group SS / total SS:
round(clust$betweenss/clust$totss,2)
```

After quick experimentation 2 cluster seems to be optimal in terms 
of cluster coherence (based on minimum $SS_{between-group}/SS_{total}$).

<br>

## *Cluster interpretation*
An important part of clustering analysis is to find an intertpretation 
for the clusters, otherwise the analysis is mostly useless. A good 
point to start is to examine the difference in cluster centroids 
(average feature values within groups):

```{r}
library(tidyr)
library(ggplot2)
data.frame(cluster = factor(1:k), 
              round(clust$centers,2)) %>%
  gather(key='feature',value = 'value',-cluster) %>%
  ggplot(aes(feature,value,
             col=cluster,group=cluster)) +
    geom_line() +
    geom_point() +
    theme_bw()
```

This figure tells us that cluster 2 contains cars whose fuel economy (*mpg*) 
is low, probably due to high horse powers (*hp*) in their large engines 
(*disp*) and large weight (*wt*). 

The feature distributions in each cluster can be visualized as follows:

```{r,warning=FALSE}
data.frame(cluster = factor(clust$cluster),x) %>%
  gather(key='feature',value='value',-cluster) %>%
  ggplot(aes(cluster,value,fill=cluster)) +
    geom_boxplot() +
    facet_wrap(~feature,scales='free_y',nrow = 1) +
    theme_bw() +
    theme(legend.position = 'n')
```

<br>

## *Cluster visualization*
We can also try to visualize the similarity of data points using Principal 
Components Analysis (**PCA**), which acts to find axes of maximum 
variation in the feature space: 

```{r}
pca = princomp(x)
df = data.frame(cluster = factor(clust$cluster),
                pca$scores[,1:2])
ggplot(df,aes(Comp.1,Comp.2,col=cluster,
              fill=cluster))+
  stat_ellipse(level = 0.9,geom='polygon',alpha=0.2)+
  geom_text(label=rownames(mtcars))+
  theme_bw()
```

---

<br>

# *K*-medoids
*K*-medoids is very similar to *K*-means, the main different being 
that in *K*-medoids the cluster centroids are represented by actual 
data points rather than feature averages as in *K*-means.

<br>

## *PAM*
*K*-medoids, or *partitioning around medoids*, can be done with the
 `pam()` function in the `cluster` package:
 
```{r}
library(cluster)

k = 2
clust2 = pam(x,k)

# clustering summary:
round(clust2$clusinfo,2)
```

This summary tells us the number of observations in each cluster, 
the maximum and average (*Euclidean*) distance between observations 
and the cluster medoid, the diameter of the cluster 
(maximal dissimilarity between two observations of the cluster), 
and the separation of the cluster (minimal dissimilarity between an 
observation of the cluster and an observation of another cluster).

The visualisation of the medoids resembles that we got from *K*-means, 
but the the values are not the same: 

```{r}
data.frame(cluster = factor(1:k), 
              round(clust2$medoids,2)) %>%
  gather(key='feature',value = 'value',-cluster) %>%
  ggplot(aes(feature,value,
             col=cluster,group=cluster)) +
    geom_line() +
    geom_point() +
    theme_bw()
```

<br>

## *Comparison with K-means*
Due to the different assignment of cluster centroids, *K*-means and 
*K*-medoids do not necessarily produce identical cluster assignments. 
We can easily compare these by doing a cross-tabulation:

```{r}
table(clust$cluster,clust2$clustering,
      dnn = c('K-means','K-medoids'))
```

As you can see, some observations have ben differently clustered 
between the two methods. 

---

<br>

# Fuzzy clustering
In fuzzy clustering (aka soft clustering), each data point can 
belong to more than one cluster. Each data point will get an 
estimated membership grades, which indicate the degree to which 
data points belong to each cluster. Thus, points on the edge of a 
cluster, with lower membership grades, may be in the cluster to a 
lesser degree than points in the center of cluster.

<br>

## *Finding fuzzy clusters*
Fuzzy clustering, or *C*-means, can be calculated e.g. with the 
`cmeans()` function in the `e1071` package:

```{r,message=FALSE}
library(e1071) 

k = 2
clust3 = cmeans(x,k)

# Cluster sizes:
clust3$size
```

In contrast to *K*-means and *K*-medoids, we get an even distribution 
of observations into the two clusters (which is of course not to be 
expected in general).

```{r}
data.frame(cluster = factor(1:k), 
              round(clust3$centers,2)) %>%
  gather(key='feature',value = 'value',-cluster) %>%
  ggplot(aes(feature,value,
             col=cluster,group=cluster)) +
    geom_line() +
    geom_point() +
    theme_bw()
```

<br>

## *Visualizing the clusters*
```{r}
pca = princomp(x)
df = data.frame(cluster = factor(clust3$cluster),
                pca$scores[,1:2],
                Membership = clust3$membership[,1])
ggplot(df,aes(Comp.1,Comp.2,col=cluster,
              fill=cluster))+
  stat_ellipse(level = 0.9,geom='polygon',alpha=0.2)+
  geom_point(aes(size=Membership)) +
  geom_text(label=rownames(mtcars),nudge_y = .3)+
  theme_bw()
```
Here the size of the point indicates the degree of membership to 
cluster 1. In comparison to *K*-meands, *Ferrari Dino* and *Valiant* 
are included in cluster 2, but clearly their membership to cluster 
1 is relatively high (close to 0.5).