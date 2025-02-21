---
title: "Evaluating Classification Models, Part 2: The Sufficiency of Precision and Recall"
author: "Greg Gandenberger"
date: "2019-11-20"
categories: [ml, evals]
image: "district.webp"
---

![With high recall, most dresses appear in the dress feed. With high precision, most products in the dress feed are dresses. Are those two numbers all we need?](district.webp)

This post is part of a series on evaluating classification models:

* [**Part 1: Weighing False Positives Against False Negatives**](/posts/2019-11-14_evaluating-classifier-pt1/evaluating_classifiers_pt_1.html) explains why we need systematic ways to evaluate classification models.
* [**Part 2: The Sufficiency of Precision and Recall**](/posts/2019-11-20_evaluating-classifiers-pt2/evaluating_classifiers_pt_2.html) explains why *precision* and *recall* are sufficient for evaluating classification models in typical cases.
* [**Part 3: $F_\beta$ and Other Weighted Pythagorean Means of Precision and Recall**](/posts/2019-11-26_evaluating-classifiers-pt3/evaluating_classifiers_pt_3.html) explains what patterns of preferences are encoded by the *Pythagorean means* of precision and recall. This class of metrics includes the popular $F_\beta$ family, among others.
* [**Part 4: Weighted Power Means of Precision and Recall**](/posts/2019-12-02_evaluating-classifiers-pt4/evaluating_classifiers_pt4.html) generalizes beyond the Pythagorean means to the broader class of *weighted power means* of precision and recall.

This series differs from other discussions of evaluation metrics for classification models in that it aims to provide a **systematic perspective**. Rather than providing a laundry list of individual metrics, it situates those metrics within a fairly comprehensive family and explains how you can choose a member of that family that is appropriate for your use case.

[Part 1](/posts/2019-11-14_evaluating-classifier-pt1/evaluating_classifiers_pt_1.html) of this series explains why we need metrics that allow us to evaluate binary classification models systematically. For instance, if we are developing a model that takes images of a product in ShopRunner's retailer network and returns a probability that the product is a dress, we need a metric that weighs false positives (products that are predicted to be dresses but are not) against false negatives (products that are dresses but are predicted not to be) in a way that approximately matches what our preferences over models would be upon sufficient reflection.

That post put forward *precision* and *recall* as ways to measure the ability of a model to avoid false positives and false negatives, respectively, using the example of a model that classifies a product as either a dress or not. Precision is the accuracy of a model's positive predictions. For instance, when it says that a product is a dress, how often is it actually a dress? Recall is the accuracy of the model's predictions for actual instances of the positive class. For instance, when a product is a dress, how often does the model classify it as a dress?

Precision and recall are useful metrics, but in order to use them to rank models we need some way to combine them into a single measure of overall performance. Subsequent posts in this series will go into detail about how to think about such composite metrics. This post addresses a prior question: **why use precision and recall as our starting points for model evaluation?** In particular, how can we be sure that those numbers do not leave out important information?

## Summarizing Model Performance with Precision and Recall

The results of applying a binary classifier to a dataset can be displayed in a confusion matrix:

|           | Predicted No | Predicted Yes |
|-----------|-------------|---------------|
| Actual No | \|True Negative\| | \|False Positive\| |
| Actual Yes| \|False Negative\| | \|True Positive\| |

where e.g. "|True Positive|" refers to the number of true positive predictions that the model generates (i.e., where the model correctly predicted that the item in question belongs to the positive class).

For instance, a model that classifies a product as "dress" or "not a dress" might have the following confusion matrix:

|           | Predicted No | Predicted Yes |
|-----------|-------------|---------------|
| Actual No | 92          | 8             |
| Actual Yes| 10          | 90            |

**We often act as if an observed confusion matrix contains everything we can learn about the value of a model from its performance on a test dataset.** This approach is not always justified: for instance, the confusion matrix discards information about the presence or absence of problematic patterns of error such as racial biases. However, if those kinds of problems are being addressed by other means, then the assumption that a confusion matrix is a sufficient basis for preferences over models may be justified.

We can calculate precision and recall from a confusion matrix but not vice versa. However, when we evaluate a model against a validation dataset, we typically have access to the total number of positive and negative examples (the row sums in the confusion matrix). Given that information, we *can* reconstruct a confusion matrix from precision and recall (See the Appendix to this post.) Thus, in a typical scenario, **precision and recall contain the same information as a confusion matrix**, so they are sufficient for evaluating a model if the confusion matrix is.

Now, there are other pairs of numbers that also contain the same information as a confusion matrix given the numbers of actual positive and actual negative cases, such as sensitivity and specificity. The choice among such pairs of numbers is a matter of taste. I use precision and recall because they are popular in the machine learning community. I suspect that they are also easier for people to understand than some of the alternatives because they have actual positive or predicted positive cases in the denominator, so that thinking about them requires processing fewer negations.

In any case, **precision and recall are sufficient for model evaluation when a confusion matrix is sufficient for model evaluation and the numbers of actual positive and actual negative examples in the dataset are known.**

## Appendix: Constructing a Confusion Matrix from Precision, Recall, and its Row Sums

Here is one way to reconstruct a confusion matrix from precision, recall, and the number of "Actual No" and "Actual Yes" cases.

### |True Positive|

$$
\text{Recall} = \frac{|\text{True Positive}|}{|\text{Actual Yes}|}
$$

so

$$
|\text{True Positive}| = \text{Recall} \times |\text{Actual Yes}|
$$

### |False Negative|

$$
|\text{False Negative}| = |\text{Actual Yes}| - |\text{True Positive}|
$$

### |False Positive|

$$
\text{Precision} = \frac{|\text{True Positive}|}{|\text{True Positive}| + |\text{False Positive}|}
$$

so a bit of algebra shows

$$
|\text{False Positive}| = |\text{True Positive}|\frac{1-\text{Precision}}{\text{Precision}}
$$

### |True Negative|

$$
|\text{True Negative}| = |\text{Actual No}| - |\text{False Positive}|
$$

## Acknowledgements

Thanks to Nicole Carlson, Ali Vanderveld, and the rest of the ShopRunner data science team for comments on earlier versions of this material.

*Originally published at [medium.com](https://medium.com/@gsganden/https-medium-com-gsganden-evaluating-classification-models-2-64e9e21f9038)* 