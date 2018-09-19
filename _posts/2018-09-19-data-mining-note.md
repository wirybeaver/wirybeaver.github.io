---
layout:     post
title:      Data Mining Note
excerpt:    Details about my intern project
date:       2018-09-19 10:00:00
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Note
    - Data Mining
---

## kmeans pseudo code

Input: k (# clusters),<br>
       D (the sample set)<br>
Method:<br>
Arbritrarily choose k objects as k cluster centroids<br>
**Repeat:**<br>
1. for each object, calculate the ditance to all centroids. Assign that objects to the cluster whose centroid provides shortes distance.
2. for each cluster, update its centroid by calculating the mean value of objects in that cluster.at

**Until:** No changes

## decision tree pseudo code
1. All training instances are initially put into the root node.
2. Select an attribute which provides maximal information gain. Split based on that attribute.
3. continue to recursively expand the internal node until some stopping criterias are satisfied:
(1) all samples in a node have same label
(2) no remaining attribute
(3) number of samples in a node falls below threshold.
(4) or, other criteras.

overfitting: test error begins to increase even though training error continue to decrease.<br>
cause: noise, lack of representative samples.<br>
pre-pruning: early halt before generating a fully grown tree when some criterias are satified, e.g. information gaim, entroy and number of samples in a node <= threshold or tree depth and number of used attributes >= threshod.
Pro: cheap computation Drawback: suffer from premature termination due to the difficulty of choosing a right threshold. Too high, result in the unfitted model; Too low, not sufficient to overcome the overfitting problem.<br>
post-pruning: generate a fully grown tree then trim it in a bottom-up fashion. The trim can be done by (1) subtree replacement, that is, to replace subtree with a new leaf node using majority vote (2) subtree raise, that is, to replac subtree with most used branches (3) eliminate sub-tree where E < threshold or gain < threshold. Processing penaly compared to pre-pruning Pro: better result Con: expensive computation

hold out: partitioned into two disjoint sets, called training set and validation set. Model is induced from training and evaluated on the test set. Well-known limitations: (1) reduce data available for training. (2) validation set should be large enough (2) training and validation is not independent.

Random sampling: doens't utilize the data as much as possible. lose the control over the number of times each instance is used.

cross validation: split original data into k segment. each segment is used once as a validation set. leave one out is a special case where k=N. Pro: utilize as much as possbile. test sets are mutually exclusive. Con: expensive computation to repeat the process N times. the variance of error rate tends to be high.

Boostrap: select n instances using sampling with replacement. Unselected data used for validation. On average, the new training set would contain about 63.2% data in the original data. A record may occur more than once.
$acc_{bootstrap} = \frac{1}{b} \sum_{i=1}^{b} 0.632 acc_{i}+ 0.368acc_{s}$, where $acc_{s}$ is called resubstituion acc testing on all labeled data.

At XX% confidence level, the following confidence interval for $d^t$ is obtained ... If the interval doens't span the value, the observed differance is statistically significant at confidence level XX%. At XX or lower, reject the null hypothosize.


