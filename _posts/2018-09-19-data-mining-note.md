---
layout:     postMathJax
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

# data preprocessing
qualitative: norminal, ordinal <br>
quantitative: interval, ratio

valid transformations
- nominal: any one-to-one mapping, like permutation
- ordinal: any monotonic order-preserving function
- interval: new = a \* b + c
- ratio: new = a \* b

assymetric attribute: only interested in non-zero value <br>
lossy process: A data reduction method which results in the inalibity to re-create the original data<br>
domain specific knowledge: expertise in a given field which enables informed, knowledge-preserving decisions

## dataset characteristic
- dimentionality: the number of object attirbutes
- resolution: granularity of the data. often in terms of time or physical dimensions
- sparsity: the frequency of non-zero values.

## data quality

precision: the closeness of repeated measurement to one another <br>
bias: systematic variation of measurements from the quantity <br>
accuracy: the closeness of measurements to the true value of the quantity <br>
noise: random component of measurement error <br>
outlier: unusuall data objects or unusual values of an attribute <br>

## data missing
(1) remove the object or attritbute
(2) evaluate
(3) ignore the missing value during analysis

## selection
goal: reduce the resources requirement
- aggregation. combine two or more objects into a single object.
- sampleing. select a subset of data. represantative: sample has approximately the same property (interested) as the original data set.
- dimensionality reduction. create new attributes that are a combination of old attributes.
- feature subset selection. select new attributes that are a subset of old attributes.

## transformation
goal: improve the utility
- extraction.
- mapping. e.g., fourier transformation
- construction. e.g., density

## normalization vs weighting
normalization: fit attribute values to a desirable range.
weighting: more important attribute is assigned a higher weight

## discretization
equal width, equal frequency, K-means, visual inspection<br>
split point: threshold values which determine ranges of continuous values to map to a new discrete value.

proximity vs similarity <br>
proximity: a relative measure of distance <br>
similarity: a numeric measure of the degree to which two objects are like

## proximity
Hamming, Euclidean (positivity, symetry, triangle inequality), Chebycheve, Minkowski

## similarity
SMC: simple matching coefficient. count both presences and absences.
Jacard: only count presences. Handle objects that have assymetric binary attributes.
Consine similarity
Correlation: represent positive and negative relationships between attributes.

# classification
classification goal: predict class membership. develop a population description

category is a nominal attribute which references a group of objects which hold a particular relationship.

confusion matrix: classifier performance

## kmeans

Input: k (# clusters),<br>
       D (the sample set)<br>
Method:<br>
Arbritrarily choose k objects as k cluster centroids<br>
**Repeat:**<br>
1. for each object, calculate the distance to all centroids. Assign the object to the cluster whose centroid provides shortes distance.
2. for each cluster, update its centroid by calculating the mean value of objects in that cluster.

**Until:** No changes

## decision tree

decision tree pseudo code
1. All training instances are initially put into the root node.
2. Select an attribute which provides maximal information gain. Split based on that attribute.
3. continue to recursively expand the internal node until some stopping criterias are satisfied:
(1) all samples in a node have same label
(2) no remaining attribute
(3) number of samples in a node *falls below* threshold.
(4) or, other criteras.

overfitting: test error begins to increase even though training error continue to decrease.<br>
cause: noise, lack of representative samples.<br>
pre-pruning: early halt before generating a fully grown tree when some criterias are satified, e.g. information gaim, entroy and number of samples in a node <= threshold or tree depth and number of used attributes >= threshod.
Pro: cheap computation Drawback: suffer from premature termination due to the difficulty of choosing a right threshold. Too high, result in the unfitted model; Too low, not sufficient to overcome the overfitting problem.<br>
post-pruning: generate a fully grown tree then trim it in a bottom-up fashion. The trim can be done by (1) subtree replacement, that is, to replace subtree with a new leaf node using majority vote (2) subtree raise, that is, to replac subtree with most used branches (3) eliminate sub-tree where E < threshold or gain < threshold. Processing penaly compared to pre-pruning Pro: better result Con: expensive computation

hold out: partitioned into two disjoint sets, called training set and validation set. Model is induced from training and evaluated on the test set. Well-known limitations: (1) reduce data available for training. (2) validation set should be large enough (3) training and validation is not independent.

Random sampling: doens't utilize the data as much as possible. lose the control over the number of times each instance is used.

cross validation: split original data into k segment. each segment is used once as a validation set. leave one out is a special case where k=N. Pro: utilize as much as possbile. test sets are mutually exclusive. Con: expensive computation to repeat the process N times. the variance of error rate tends to be high.

Boostrap: select n instances using sampling with replacement. Unselected data used for validation. On average, the new training set would contain about 63.2% data in the original data. A record may occur more than once.
$acc_{bootstrap} = \frac{1}{b} \sum_{i=1}^{b} 0.632 acc_{i}+ 0.368acc_{s}$, where $acc_{s}$ is called resubstituion acc testing on all labeled data.

At XX% confidence level, the following confidence interval for $d^t$ is obtained ... If the interval doens't span the value, the observed differance is statistically significant at confidence level XX%. At XX or lower, reject the null hypothosize.

Height of a decition tree represents the maximum number of decisions required during classification.

Pro: classification is O(max_height). nonparametric. intuitive.

Con: subject to overfitting. NP-complete. 

## rule-based classifier

rule-based classifier is a collection of conditional statements where each antecedent is a conjunction of restrictions (attribute-op-value) on attributes and each consequent is class label. 

multually exclusive: no two rules cover the same record.<br>
exhausitive: cover entire attribute space.

ordered rule: rules are ordered by decreasing order of their pririty. A test record is classified by the highest-rank rule that covers the record. Pro: avoid the problem of conflicting classes predicited by multiple rules. <nr>
unordered rule: Allows a record to trigger multiple rules and consider the consequent of each rule as a vote to a particular class.  Pro: model build is cheap due to no rule sorting. less susceptible to erros caused by wrong rules. Con: prediction is expenssive. A test record must be compared against the antecedent of each rule.

rule-based ordering: Individual rule is ordered by some rule quality measure. Pro: Each record is classified by the best rule covering it. Con: low-rank rules are less expressive.<br>
class-based ordering: Rules that belong to the same class apprears together. Then they are collectively ordered by class information. Pro: Easier interpretation. Con: a high-quality rule may be overlooked in favor of an inferior rule that happens to predict a higher-rank class.

Direct rule extraction: directly from data<br>
Indirect rule extraction: from other models, such as decision tree.

### direct rule extraction
Sequential covering algorithm <br>
1. order class
2. for each class in order:
    until stopping criteria:
        greedily select a desirable rule for current class. (covers many positive examples and no or less negatives).<br>
        remove records covered by rule
3. insert default rule

### Rule Grow Strategy
general-to-specific: Start with a completely general rule (empty set on the left-side, target class on the right-side) and add conjuncts until no improvement in the rule quality. <br>
specific-to-general: Randomly pick up a positive exampale as the seed, the initial rule contains the same conjuncts as the attributes of seed. Remove conjuncts until stopping criteria is met.

### RIPPER
1. sort class by non-decreasing order of frequency.
2. for each class in order:
        follow general-to-specific strategey and use the FOIL's information gain measure to add best conjuncts. It stops when the rule starts covering negatives. <br>
        post-prune new rule. (simplified rule is retained provided that the metric p-n/p+n increases. The simplified version may cover negatives whereas the original one contains only positives.) <br>
        remove records covered by the new rule. <br>
3. insert default rule.

### Indirect Rule Extraction
Each path from the root to a certain leaf can be expressed as a rule. Test conditions encountered along the path form conjuncts of the rule antecedent, while the class label at the leaf node is assigned to the rule consequent.

Rule-based classifier VS decision tree
·at least as expressive as decision trees<br>
·like decision trees, may create partitions of the attribute space. unordered rules may create more complex decision boundaries<br>
·can be used to produce descriptive models which are clear but cost no more that a decision tree to construct<br>
·class-based ordering well suited for imbalanced class distributions<br>

## k-nearest neighborhood
for each test example z do
    compute distance individually between z and training examples.
    choose k closest training examples.
    Classify z by majority voting based on the choosen k neighborhoods.
end for

## naive bayes classifier
Pro<br>
·robust to isolated noise <br>
·robust to occasional missing values <br>
·robust to irrelevant attributes <br>

Con<br>
·susceptible to correlated attributes <br>

# Association

## terminology
- itemset. A collection of 0 or more co-occurring items.
- $$\sigma(X)$$ itemset support account. \# transactions containing itemset X.
- $$s(X)$$ itemset support. percentage of transactions containing itemset X.
- transaction is an itemset

## Apriori alogrithm
repeat until no new k-itemsets found
1. use *minsup* to find all frequent k-itemsets
2. combine frequent k-itemsets to produce candidate (k+1)-itemsets

construct candidate association rules

apply minconf to pick up assocation rules

## maximal frequent itemsets
A frequent itemset, none of its immeidate supersets are frequent.
Pro: provide a compact representation of frequent itemsets. All frequent items can be derived from them.
Con: lose support information of subsets.

## closed itemsets
None of its immediate supersets has exactly the same support count as itself.
Pro: contain support information

## lift
The ratio of the observed co-occurrence to that expected if X and Y were independent.

$$\dfrac{S(X, Y)}{S(X) \times S(Y)}$$
- lift(X, Y)=1, independent
- lift(X, Y)>1, positive correlated
- lift(X, Y)<1, negative correlated

## conviction
Measure increased co-occurence likelihood over random. Overcome limitations of lift.
$$Conv(X \rightarrow Y) = \dfrac{1-S(Y)}{1-S(X,Y)}$$

## simpson paradox
Lesson: Need stratification to avoid spurious pattern.

## min-Apriori
$$\sigma_{m-a}(x) = \sum\min\limits_{y \in x}(\sigma_{m-a}(y))$$

## sequence
ordered list of elments. Each element is an itemset, collection of one or more events.

Support of a sequence *s* is the fraction of all sequences containing *s*.

k-sequence is a sequence that contains k events (instead of k elements).

order matters in sequence, but not in itemset.

\\(t(t_1, t_2, \ldots, t_m) \in s(s_1, s_2, s_m)\\), if there exits integers<br>
1 \\(\le j_1 < j_2 < \ldots < j_m \le n such that t_1 \in s_{j_1}, t_2 \in s_{j_2}, \ldots, t_m \in s_{j_m}\\)









