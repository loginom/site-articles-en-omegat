---
publish: 2020-01-14
---

# Clustering of Categorical Data: the Scalable CLOPE Algorithm

**Grouping according to the similar features of categorical and transactional arrays of data in large DB is the most important task of Data Mining. In the majority of cases, traditional clustering algorithms are not effective while large databases processing. The scalable heuristical CLOPE algorithm is discussed in the article. It allows for high quality and performance of clustering.**

## Introduction and the Main Concepts

The issue of [clustering](https://wiki.loginom.ru/articles/clustering.html) of large arrays of [categorical data](https://wiki.loginom.ru/articles/categorical-data.html) is highly topical for data analysis systems. The categorical data occur in all spheres: industry, trade, marketing, medicine, etc. The categorical data include so-called transactional data: market basket data, tracking data of web surfing. This includes analysis and classification of text documents ([Text Mining](https://wiki.loginom.ru/articles/text-mining.html)).

Hereafter the categirical data refer to qualitative characteristics of objects measured according to the nominal [scale](https://wiki.loginom.ru/articles/scale-type.html). It is to be recalled that when using the nominal scale, it is required to specify only whether the objects are similar as far as the measured feature is concerned.

It is not effective and sometimes even impossible to use traditional algorithms for clustering of objects with categorical features (more detailed information is available in the mateiral called "[Clustering Algorithms for Data Mining"](https://loginom.ru/blog/data-mining-clustering)). The main complications are connected with high dimensionality and huge volume typical of such databases.

The algorithms based on the pair-wise distance calculation ([k-means](https://wiki.loginom.ru/articles/k-means.html) and the similar ones) are mostly effective for numerical data. When using arrays of records with large amount of non-numerical factors, their performance is unsatisfactory. And the importance lies not so much in the complexity of metrics definition to calculate distances between the categorical [attributes](https://wiki.loginom.ru/articles/attribute.html), as in the fact that it is required to provide pair-wise comparison of objects with each other for each algorithm iteration, and there can be too many iterations. It is not applicable for the tables with millions of records and thousands of fields.

That's why development of [scalable algorithms](https://wiki.loginom.ru/articles/scalable-algorithm.html) for clustering of the categorical and transactional data is actively performed nowadays. The following special requirements are imposed on them:

* minimum possible number of the database table "scans";
* operation under condition of the limited computer RAM capacity;
* possibility to interrupt the algorithm operation providing saving of intermediate results to continue calculations later;
* operability of the algorithm when objects can be extracted from database only in the forward-only cursor mode (namely, in the record navigation mode).

Nowadays there are more than a dozen methods of work with categorical data, for example, a family of hierarchical clustering algorithms. But they do not always meet the requirements specified above. The LargeItem algorithm is considered to be one of the most effective algorithms. It is based on improvement of some global criterion. This global criterion is based on the support parameter (as far as terminology is concerned, there is much in common with algorithms for detection of [association rules](https://wiki.loginom.ru/articles/association-rules.html)).

In fact, calculation of the global criterion makes the clustering algorithm many times faster as compared with the local criterion in the case of the pair-wise comparison of objects. That's why "globalisation" of the valuation function is one of the ways to get scalable algorithms.

The CLOPE algorithm analyzed in this article is similar to LargeItem but it is faster and easier as far as software implementation is concerned. CLOPE was proposed in 2002 by a team of the Chinese scientists. It provides higher performance and clustering quality as compared with the LargeItem algorithm and many hierarchical algorithms.

First, let's formalize the clustering task under consideration for categorical data. The case will be described as though we had a database of transactional data, and in the end it will be explained how to use CLOPE to [cluster](https://wiki.loginom.ru/articles/cluster.html) any categorical arrays working with them as with the transactional ones.

In this case, the [transaction](https://wiki.loginom.ru/articles/transaction.html) means a random set of objects, for example, a list of key words in an article, goods bought in the supermarket, a set of patient symptoms, typical image elements, etc. The task of the transactional data clustering is to provide clustering of the whole set of transactions to place all similar transactions into one cluster and the different ones - into different clusters.

The CLOPE clustering algorithm is based on the concept of maximisation of the global cost function that increases proximity of transactions in clusters by increase of the cluster [histogram](https://wiki.loginom.ru/articles/histogram.html) parameter. Let's consider a simple example that consists of 5 transactions: { {% math %}(a,b){% endmath %}, {% math %}(a,b,c){% endmath %}, {% math %}(a,c,d){% endmath %}, {% math %}(d,e){% endmath %}, {% math %}(d,e,f){% endmath %} }. Imagine that it is required to compare two cases of clustering with each other:

{  { {% math %}ab, abc, acd{% endmath %} }, { {% math %}de, def{% endmath %} } } , (1)

{ { {% math %}ab, abc{% endmath %} }, { {% math %}acd, de, def{% endmath %} } }, (2)

For the first and second cases of clustering it is required to calculate the number of occurrences of each transaction item in each cluster, and then it is required to calculate the height ({% math %}H{% endmath %}) and width ({% math %}W{% endmath %}) of the cluster. For example, { {% math %}ab, abc, acd{% endmath %} } cluster has {% math %}a:3{% endmath %}, {% math %}b:2{% endmath %}, {% math %}c:2{% endmath %} с {% math %}H=2{% endmath %} and {% math %}W=4{% endmath %} occurrences. To facilitate understanding, Figure. 1 shows these results geometrically in the form of histograms.

![Figure 1. Histograms of two clustering cases](split.svg)

Let's estimate the quality of two clustering cases according to analysis of their {% math %}H{% endmath %} height and {% math %}W{% endmath %} width. { {% math %}de, def{% endmath %} } and { {% math %}ab, abc{% endmath %} } clusters have the similar histograms, consequently, they are equivalent. The histogram for { {% math %}ab, abc, acd{% endmath %} } cluster contains 4 different items, and its area is equal to 8 blocks ({% math %}H=2.0, H/W=0.5{% endmath %}), and { {% math %}acd, de, def{% endmath %} } cluster contains 5 different items with the same area ({% math %}H=1.6, H/W=0.32{% endmath %}). Clearly, clustering (1) is better since we prefer more overlapping among
transactions (consequently, {% math %}H{% endmath %} parameter is higher).

The CLOPE algorithm (Clustering with sLOPE) is used according to such obvious and simple concept of geometric histograms. Let's analyze it in detail in its more formal description.

## The CLOPE Algorithm

Let's take a database of transactions {% math %}D{% endmath %} taht consists of a set of transactions { {% math %}t_1,t_2,…,t_n{% endmath %} }. Each transaction has s set of objects { {% math %}i_1,…,i_m{% endmath %} }. Set of clusters { {% math %}C_1,…,C_k{% endmath %} } is clustering of a set { {% math %}t_1,…,t_n{% endmath %} } that allows for {% math %}C_1 … C_k={% endmath %} { {% math %}t_1,…,t_n{% endmath %} } and {% math %}C_i\neq \varnothing  \wedge C_i \bigcap C_j = \varnothing{% endmath %}, for {% math %}1<=i{% endmath %}, {% math %}j<=k{% endmath %}. Each {% math %}C_i{% endmath %} item is called a cluster, {% math %}n{% endmath %}, {% math %}m{% endmath %}, {% math %}k{% endmath %} are used
respectively for the number of transactions, the number of objects in the base of transactions, and the number of clusters.

Each {% math %}C{% endmath %} cluster has the following features:

* {% math %}D(C){% endmath %} – a set of unique objects;
* {% math %}Occ(i,C){% endmath %} – number of occurrences (frequency) of the object {% math %}i{% endmath %} in {% math %}C{% endmath %} cluster;
* {% math %}S(C)=\sum_{i\in\ D(C)}\ Occ\ (i, C)=\sum_{t_i\in C}\mid  t_i \mid{% endmath %}
* {% math %}W(C) = |D(C)|{% endmath %};
* {% math %}H(C) = S(C)/W(C){% endmath %}.

The histogram of {% math %}C{% endmath %} cluster is called the graphic presentation of its estimated characteristics: with cluster objects along the {% math %}OX{% endmath %}-axis decreasingly ordered by {% math %}Occ(i,C){% endmath %} value, and  {% math %}Occ(i,C){% endmath %} value – along the {% math %}OY{% endmath %}-axis (refer to Figure 2).

![Figure 2. Representation of the histogram of cluster](histogram.svg)

Figure 2 shows that {% math %}S(C)=8{% endmath %}, it complies with the area of the rectangle restricted by coordinate axes and dashed line. It's straightforward that the larger {% math %}H{% endmath %} value is, the more similar the two transactions are. That's why the algorithm must select the clusterings that maximize {% math %}H{% endmath %}.

However, {% math %}H{% endmath %} height value alone is not enough. Let's take a base that contains 2 transactions: { {% math %}abc, def{% endmath %} }. They do not contain shared objects but { { {% math %}abc, def{% endmath %} } } clustering and { { {% math %}abc{% endmath %} }, { {% math %}def{% endmath %} } } clustering have the same {% math %}H=1{% endmath %} height. Thus, both clustering cases are equivalent. But if we use gradient {% math %}G(C)=H(C)/W(C)=S(C)/W(C)^2{% endmath %} insteda of {% math %}H(C){% endmath %} as the quality measure, then { { {% math %}abc{% endmath %} },{ {% math %}def{% endmath %} } } clustering will be better (the gradient of each cluster is equal to 1/3 as compared with 1/6 of clustering { { {% math %}abc, def{% endmath %} } }).

Having generalized the information above, let's take down the formula for calculation of the global criterion – cost function {% math %}Profit(C){% endmath %}:

{% math %}Profit (C) = \frac {\sum_{i=1}^k G(C_i)\times\mid C_i\mid}{\sum_{i=1}^k \mid C_i\mid}=\frac {\sum_{i=1}^k \frac{S(C_i)}{W(C_i)^r}\times\mid C_i\mid}{\sum_{i=1}^k \mid C_i\mid}{% endmath %}

where:

* {% math %}|Ci|{% endmath %} – the number of transactions in {% math %}i{% endmath %} cluster
* {% math %}k{% endmath %} – the number of clusters
* {% math %}r{% endmath %} – positive real number exceeding 1.

{% math %}r{% endmath %} parameter called a repulsion coefficient by the CLOPE authors enables to control the level of intra-cluster similarity, and the final number of clusters, as a consequence. This coefficient is selected by a user. The higher is {% math %}r{% endmath %}, the lower the similarity level is and the more clusters will be generated.

The formal clustering task using the CLOPE algorithm is as follows: it is required to find clustering {% math %}C: Profit(C,r) -> max{% endmath %} for the set {% math %}D{% endmath %} and {% math %}r{% endmath %} .

## The Algorithm Implementation

Let's assume that transactions are kept in the database table. We approximate the best solution by sequential iterative scanning of the database records. As the optimization criterion is defined globally based only on calculation of {% math %}H{% endmath %} and {% math %}W{% endmath %}, the algorithm performance and speed will be
much higher as compared with the pair-wise comparison of transactions.

The algorithm implementation requires the first scan of the transaction table to build
the initial clustering, driven by function {% math %}Profit(C,r){% endmath %}. After that, insignificant (1-3) number of additional table scans are required to increase the clustering quality and optimize the cost function. If no changes are made in the current scan in the table, the algorithm will stop. The algorithm pseudocode is as follows:

1. // Phase 1 – initialization
2. &nbsp;&nbsp; not the end yet
3. &nbsp;&nbsp;&nbsp;&nbsp; read the following transaction from the table [t, -];
4. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;place t into the existing or new cluster C<sub>i</sub>, that provides maximum Profit(C,r);
5. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;write [t,i] to the table (cluster number);
6. // Phase 2 – Iteration
7. &nbsp;&nbsp;Repeat
8. &nbsp;&nbsp;&nbsp;&nbsp;go to the table start;
9. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;moved := false;
10. &nbsp;&nbsp;&nbsp;&nbsp;not the table end yet
11. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;read [t,i];
12. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;place t into the existing or new cluster C<sub>j</sub>, that provides maximum Profit(C,r);
13. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if C<sub>i</sub>&lt;&gt;C<sub>j</sub> then
14. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;write [t,i];
15. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;moved := true;
16. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;yet (not moved).
17. delete all blank clusters;

It is evident that the CLOPE algorithm is scalable because it can work in the limited RAM space. Only the current transaction and a small amount of information for each
cluster is kept in the RAM space during the work. It includes the number of transactions {% math %}N{% endmath %}, the number of unique objects (or the cluster width) {% math %}W{% endmath %}, simple hash table for calculation {% math %}Occ(i,C){% endmath %}, and values {% math %}S{% endmath %} of the cluster area. They are called cluster features. For simplicity, let's denote them as cluster features {% math %}C{% endmath %}, for example, {% math %}C.Occ[i]{% endmath %} means the number of the object occurrences {% math %}i{% endmath %} in {% math %}C{% endmath %} cluster, etc. It is easy to calculate that frequency of occurrences equal to 10 k objects with a clustering of 1k clusters can be fit into a 40M RAM.

To finish the algorithm implementation, we need two more functions that enable to calculate the profit {% math %}Profit(C,r){% endmath %} when adding or removing transactions from the cluster. It is easy to do if you know the values {% math %}S{% endmath %}, {% math %}W{% endmath %} and {% math %}N{% endmath %} of each cluster:

1. function DeltaAdd(C,t,r): double;
2. begin
3. &nbsp;&nbsp;S_new := C.S + t.ItemCount;
4. &nbsp;&nbsp;W_new := C.W;
5. &nbsp;&nbsp;for i:=0 to t.ItemCount–1 do
6. &nbsp;&nbsp;&nbsp;&nbsp; if (C.Occ[t.items[i]]=0) then W_new := W_new + 1;
7. &nbsp;&nbsp;result := S_new &#042; (C.N+1)/(W_new)<sup>r</sup> – C.S &#042; C.N/(C.W)<sup>r</sup>
8. end;

In this case, {% math %}t.Items[i]{% endmath %} – value {% math %}i{% endmath %}- transaction object {% math %}t{% endmath %}. Note that {% math %}DeltaAdd(C,t,r){% endmath %} when adding {% math %}t{% endmath %} to the new cluster is equal to {% math %}S/W^r{% endmath %}, where {% math %}S{% endmath %} and {% math %}W{% endmath %} – area and width of the cluster that consists of the added transaction {% math %}t{% endmath %}.

Implentation of the profit function {% math %}Profit(C,r){% endmath %} when deleting a transaction is similar to {% math %}DeltaAdd(C,t,r){% endmath %}, that's why let's omit its detailed code.

The following theorema guarantees the correctness of function {% math %}DeltaAdd{% endmath %} usage.

**Theorema.** If {% math %}DeltaAdd(Ci,t){% endmath %} is the maximum, then moving {% math %}t{% endmath %} to {% math %}C_i{% endmath %} cluster will maximize {% math %}Profit(C,r){% endmath %}.

Now it is possible to estimate the computational complexity of the CLOPE algorithm. Suppose the average length of a transaction is {% math %}A{% endmath %}, the total number of transactions is {% math %}N{% endmath %}, and the maximum possible number of clusters is {% math %}K{% endmath %}. The time complexity for one iteration is {% math %}O(N*K*A){% endmath %},  indicating that the algorithm execution speed is affected linearly by the number
of clusters and table size. It makes the algorithm fast and effective for large volumes.

Having described the algorithm implementation, we said nothing about the type of the transactions table to make usage of the CLOPE algorithm possible. CLOPE enables to complete the clustering tasks not only for transactional data but also for any categorical data. The main thing is to provide measurement of all features of objects in the nominal scale.

However, before we run CLOPE, it is required to provide the normalized data kind. It can be in the form of binary matrix of images in the association rules, and it can also be in the form of bijective mapping between a set of unique objects {% math %}{u_1,…u_q}{% endmath %} of the table and a set of integer numbers {% math %}{0,1,2,…,q-1}{% endmath %}.

## The Mushroom Dataset

The mushroom dataset is a popular test that is used for assessment of clustering algorithms used for categorical data sets (it is available in [UCI machine learning repository](http://archive.ics.uci.edu/ml/index.php)). The test set contains 8,124 records with description of 22 characteristics of two types of mushrooms: 4,208 edible mushrooms ({% math %}e{% endmath %}) and 3,916 poisonous mushrooms ({% math %}p{% endmath %}). The set file is as follows:

{% math %}p,x,s,n,t,p,f,c,n,k,e,e,s,s,w,w,p,w,o,p,k,s,u{% endmath %}

{% math %}e,x,s,y,t,a,f,c,b,k,e,c,s,s,w,w,p,w,o,p,n,n,g{% endmath %}

{% math %}e,b,s,w,t,l,f,c,b,n,e,c,s,s,w,w,p,w,o,p,n,n,m{% endmath %}

{% math %}p,x,y,w,t,p,f,c,n,n,e,e,s,s,w,w,p,w,o,p,k,s,u{% endmath %}

{% math %}e,x,s,g,f,n,f,w,b,k,t,e,s,s,w,w,p,w,o,e,n,a,g{% endmath %}

{% math %}..., ..., ...{% endmath %}

There are 116 unique characteristics of the objects in total. 2,480 records have missing values in one attribute. Description of the data set — [https://archive.ics.uci.edu/ml/datasets/mushroom](https://archive.ics.uci.edu/ml/datasets/mushroom).

If such data set is represented in the normalized form described above, there will be 8124 transactions, 2408 transactions will have the length equal to 21, the other transactions – 22 items (missing values are ignored). Now we can use the CLOPE algorithm. Result of the CLOPE operation when {% math %}r=2.6{% endmath %} for the Mushroom Dataset after the first iteration (the initialization phase) is shown in Table 1.

We can estimate the algorithm quality according to the number of the "dirty" clusters, namely, the clusters that include both eadible ({% math %}e{% endmath %}), and poisonouse ({% math %}p{% endmath %}) mushrooms. The number of such clusters should be as few as possible. Table 1 shows that there is only one "dirty" cluster #18 already after the first iteration. Two or three database scans are required to achieve the final clustering. It is clear that cluster 12 disappears.

The detailed analysis of the CLOPE algorithm performed by its authors proved the high clustering quality as compared with other algorithms, namely, the hierarchical ones. Its operation speed and performance are several times higher.

Table 1: Result of the CLOPE operation after the first iteration.

| CLUSTER | e | p |
|----------:|----------:|----------:|
| 1 |           | 256 |
| 2 | 512 |           |
| 3 | 768 |           |
| 4 | 96 |           |
| 5 | 96 |           |
| 6 | 192 |           |
| 7 | 1296 |           |
| 8 | 432 |           |
| 9 |           | 149 |
| 10 |           | 192 |
| 11 |           | 1146 |
| 12 |           | 1 |
| 13 |           | 288 |
| 14 | 192 |           |
| 15 |           | 223 |
| 16 | 48 |           |
| 17 |           | 72 |
| 18 | 48 | 32 |
| 19 |           | 8 |
| 20 |           | 8 |
| 21 |           | 1497 |
| 22 | 192 |           |
| 23 | 288 |           |
| 24 | 32 |           |
| 25 |           | 36 |
| 26 |           | 8 |
| 27 | 16 |           |
| Total | 4208 | 3916 |

## Spheres of CLOPE Usage

The CLOPE algorithm is designated for transactional data but it has been shown that there are many data sets with categorical attributes that represent the transactional data, or they are reduced to them. The respondent's answers in the questionary, a list of the document key words, a set of tracking data of web surfing, patinet's symptoms, mushroom characteristics represent a transaction. Therefore, the CLOPE spheres of usage include all arrays of the categorical databases.

In fact, the transactional data clustering has much in common with analysis of associations. Both [Data Mining](https://wiki.loginom.ru/articles/data-mining.html) methods enable to detect concealed dependences in data sets. But there are differences as well. On the one hand, clustering provides a general vision of a data set, whereas association analysis enables to detect particular dependences between attributes. On the other hand, association rules can be used at once, whereas clustering is frequently used as the first analysis stage.

To draw a conclusion, let's emphasize the CLOPE algorithm advantages:

1. High scalability level and operation speed, clustering quality that is achieved by using the global optimization criterion based on maximisation of height gradient of the cluster histogram. It is easily computed and interpreted. During the operation process the algorithm keep a small amount of information for each cluster in RAM, and it requires the minimum number of data set scans. It enables to use it for clustering of the large categorical data sets.
2. CLOPE automatically selects the number of clusters, and it is controlled using the only parameter, namely, the repulsion coefficient.

## References

1. Yang, Y., Guan, H., You. J. CLOPE: A fast and Effective Clustering Algorithm for Transactional Data In Proc. of SIGKDD’02, July 23-26, 2002, Edmonton, Alberta, Canada.
2. Wang, K., Xu, C.. Liu, B. Clustering transactions using large items. In Proc. CIKM’99, Kansas, Missouri, 1999.
