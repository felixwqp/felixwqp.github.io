---
layout: post
title: "Something about Information Retrieval"
description: "Naive Understanding to IR."
comments: true
keywords: "updating, statistic, matrix, covariance"
---

# IR

### What's Search? 

###### Search doc model

doc -  web page
90% don't look beyond the 1st page
#1 result -> 33% click


Already have dwonload the page. 
goal: pick the most relevent


challenges: 
1. result relevance
2. processing speed
3. scaling to many docs



###### Boolean retrieval

1. whether a doc have a specific word. 
Term-doc incidence: table:
    terms aka words,
    document aka, entire web. 

simply include or exclude a doc from results


###### Vector Space Model. 

each doc -> vector of values, one component for each term. 
a doc is a point in the space.  -> this is a SPARSE space.
position i represent the term i. 

docs that closes together are close in meaning.
measures the distance between two docs. 


1. treat query as a short doc.
2. sort docs by increasing distance to the query docs.
3. easy to compute, both query and doc are vectors. 


###### tf-idf

1. Term frequency: times mentioned in a doc. 
2. Document freq: how often a word appears in doc collection. (whether it's a rare word)

High value for rare word: IDF = N / n_k
N = total # docs in collection C
n_k = # docs in C that contain T_k

use log, -> still monotonically increasing, order preserved. 



TF * IDF
1. TF: high for common word in one document. 
2. IDF: high for rare word in collection. 

$$
W_{i k}=t f_{i k} * \log \left(N / n_{k}\right)
$$


tf-idf normalization: avoid giving long doc more weight. to normalize. 

$$
w_{i k}=\frac{t f_{i k} \log \left(N / n_{k}\right)}{\sqrt{\sum_{k=1}^{l}\left(t f_{i k}\right)^{2}\left[\log \left(N / n_{k}\right)\right]^{2}}}
$$


similarity: cosine

### Assessing Quality

1. precision/recal curve(return or not)
2. kendall's Tau(total order right or not )
3. Mean reciprocal Rank (top hit)


True/false:  relevent or irrelevent. 

posisitive/negative:  returned or not. 


$$
\begin{array}{|l|l|l|}\hline & {\text { Relevant }} & {\text { Not Relevant }} \\ \hline \text { Retrieved } & {\text { TP }} & {F P} \\ \hline \text { Not retrieved } & {F N} & {T N} \\ \hline\end{array}
$$

Precision and recall: whether its there or not. (tradeoff)


###### Kendall's Tau
1. order of all results. 
whether its in right order. 

1 - perfect agree
-1 perfect not agree. 

0 irrelevent. 


###### mean reciprocal rank
