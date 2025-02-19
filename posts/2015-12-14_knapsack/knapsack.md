---
title: "Hypothesis Test Construction as a Knapsack Problem"
author: "Greg Gandenberger"
date: "2015-12-14"
categories: [stats, philosophy]
image: "knapsack.jpg"
---

![Photo Credit: [M J M](https://www.flickr.com/photos/98908208@N00/168997122/) via [Compfight](https://www.vecteezy.com/free-photos?utm_source=compfight&utm_medium=referral&utm_campaign=redirect) [cc](https://creativecommons.org/licenses/by-nc-nd/2.0/)](knapsack.jpg)

Neyman and Pearson (e.g. [1933](https://www.jstor.org/stable/91247?seq=1#page_scan_tab_contents)) treat the problem of choosing the best rejection region for a simple-vs.-simple hypothesis test as what computer scientists call a 0/1 knapsack problem. **Standard examples of 0/1 knapsack problems are easier to grasp than hypothesis testing problems, so thinking about Neyman-Pearson test construction on analogy with those examples is helpful for developing intuitions. It is also illuminating to think about points of disanalogy between those scenarios and hypothesis testing scenarios, which give rise to possible objections to the Neyman-Pearson approach.**

## The 0/1 Knapsack Problem

In a knapsack problem, one seeks to **maximize some quantity subject to a constraint.** A standard example is that of a thief who wants to maximize the value of the objects she steals from a particular home, subject to the constraint that the total weight of those objects cannot be greater than the maximum weight that she can carry. For instance, suppose the thief has the following items to choose from.[^1]

|                      | Clock | Painting | Radio | Vase | Book | Computer |
|---------------------|-------|-----------|--------|------|------|----------|
| Value (USD)         | 175   | 90        | 20     | 50   | 10   | 200      |
| Weight (lb.)        | 10    | 9         | 4      | 2    | 1    | 20       |
| Value/Weight (USD/lb.)| 17.5 | 10        | 5      | 25   | 10   | 10       |

One possible approach to this problem is to **choose objects in order of descending value/weight ratio** until adding the next object would cause the total weight to exceed the limit. In this example, given enough space, that approach would lead one to choose first the vase, then the clock, then either the computer, the book, or the painting, and so on. **This approach has the following virtue: it yields a set of objects that has at least as much value as any other set with the same or less total weight.** However, there may be a set of objects that has greater value within the maximum weight limit. For instance, if the maximum weight is 10 lb., then this approach would lead one to take the vase only, because the next object, the clock, would put one over that weight limit. This choice provides more value than any other choice with the same total weight (2lb.). However, there are other choices with total weight less than 10 lbs. and greater value: for instance, one could take just the clock, or the vase, radio, and book.

In the 0/1 knapsack problem, each item is either in the knapsack or not. An easier problem is the **continuous knapsack program**, in which objects can be arbitrarily broken up into smaller objects, preserving the ratios of their basic attributes. For instance, if the objects were things like gold bullion and crude oil, the thief might be able to take arbitrary quantities of those items at a fixed value/weight ratio. The optimal solution to the thief's problem in this case would be to fill up on each item as much as possible in order of descending value/weight ratio, stopping precisely when the maximum weight is reached.

## Hypothesis Testing

Suppose one wanted to test a null hypothesis $H_0$ against an alternative hypothesis $H_a$. In the simplest case, $H_0$ and $H_a$ are both "simple statistical hypotheses" relative to some proposed experiment, meaning that they each specify a particular chance distribution over the sample space $S$ of possible outcomes of that experiment. **Our task is to decide which elements of $S$ to place in the "rejection region" $R$**, that is, the precise set of results on which will reject $H_0$ for $H_a$.

Neyman and Pearson propose to choose a test on the basis of *power* and *Type I error rate*, where a test's power is the probability that it correctly rejects $H_0$ if $H_0$ is false, and its Type I error rate is the probability that it incorrectly rejects $H_0$ if $H_0$ is true. Specifically, they propose to choose a test that maximizes power subject to the constraint that the Type I error rate cannot exceed some maximum value $\alpha$. Thus, **they treat the problem of constructing a hypothesis test as a 0/1 knapsack problem**, completely analogous to the thief's problem described above, as shown in this table.

| Literal Knapsack Problem | Hypothesis Test Construction |
|-------------------------|----------------------------|
| Putting item into knapsack | Putting element of $S$ into rejection region $R$ |
| Total value | Power (sum of $\Pr(s;H_a)$ over elements of $S$ in $R$) |
| Total weight | Type I error rate (sum of $\Pr(s;H_0)$ over elements of $S$ in $R$) |
| Maximizing total value subject to maximum total weight | Maximizing power subject to maximum Type I error rate |

Consider the example shown in the table below. $s_1$, $s_2$, and $s_3$ are elements of a sample space $S$. They could be, for instance, the event that a three-sided die produces a 1, 2, or 3, respectively. $H_0$ and $H_a$ would then be hypotheses about the biases of the die.

|                           | $s_1$ | $s_2$ | $s_3$ |
|--------------------------|-------|-------|-------|
| $\Pr(s;H_a)$            | 0.04  | 0.05  | 0.91  |
| $\Pr(s;H_0)$            | 0.01  | 0.05  | 0.94  |
| $\Pr(s;H_a)/\Pr(s;H_0)$ | 4     | 1     | 0.97  |

I said above that putting objects into the knapsack in descending order by value/weight ratio, stopping when the next item would cause the total weight to exceed the limit, yields a set of items that has the largest value among all sets with no more than its total weight. Analogously, **putting elements of the sample space into the rejection region in order by descending likelihood ratio, stopping when the next item would cause the Type I error rate to exceed $\alpha$, yields a rejection region that has the greatest power among all possible rejection regions with no more than its Type I error rate.** (This result is known as the Neyman-Pearson lemma.) Just as that approach in the thief's case may not yield the greatest possible value consistent with the cap on the total weight, so too in the hypothesis testing case **it may not yield the greatest possible power consistent with the Type I error rate being no greater than $\alpha$.** For instance, in the example shown above, it would lead one to perform a test that has power $.04$ when $0.05\leq \alpha < .06$ (with $R=\{s_1\}$) even though a test with power $.05$ and Type I error rate less than $\alpha$ is available (with $R=\{s_2\}$).

**There are two ways to turn the 0/1 knapsack problem of constructing a best Neyman-Pearson hypothesis test into a continuous knapsack problem.** First, one can consider cases with continuous, strictly positive probability distributions over continuous sample spaces. Here, the optimal solution is to add elements of the hypothesis space to the rejection region in descending order by likelihood ratio until the Type I error rate reaches $\alpha$. Second, one can allow *randomized* tests that reject the null hypothesis with some non-extremal probability on some elements of the sample space. Here, the optimal solution is to add elements of the sample space to the rejection region in descending order by likelihood ratio until we get to the first element that would cause the Type I error rate to exceed $\alpha$ if we were to add it to $R$. We then prescribe consulting some auxiliary randomizer to decide to reject the null hypothesis if that result is observed, in such a way that the Type I error rate of the test is exactly $\alpha$. This procedure is analogous to having the thief taking a portion of the item with the largest value/weight ratio that will not wholly fit in the bag, choosing the size of the portion so that the total weight is exactly the maximum weight.

Randomized tests are often discussed in presentations of the Neyman-Pearson framework because they make certain results easier to state. However, they are generally rejected in practice. They violate the plausible principle that the output of a hypothesis test should depend only on aspects of the data that are evidentially relevant to the hypotheses in question. One could take the hardline view suggested by Neyman and Pearson's own writings that this principle is false because only long-run error rates matter. However, few methodologists take this view so seriously that they are willing to countenance randomized tests.[^2]

## Is This Approach Sensible?

**The Neyman-Pearson approach of treating hypothesis test construction as a knapsack problem has some odd consequences.** For instance, in the example above, the optimal solution for $.05\leq \alpha < .06$ rejects $H_0$ if and only if $s_2$ is observed. But $s_2$ has the same probability ($.05$) under $H_0$ and $H_a$, whereas $s_1$ is four times more probable under $H_a$ than under $H_0$. If one accepts the Law of Likelihood, which says that $s$ favors $H_a$ over $H_0$ if and only if $\Pr(s;H_a)/\Pr(s;H_0)>1$, then it follows that $s_1$ favors $H_a$ over $H_0$ while $s_2$ is neutral between them. Even if the Law of Likelihood is not acceptable in full generality, it seems to give a sensible verdict in this case. One might think, then, that $s_2$ should not appear in $R$ without $s_1$.

**Pearson provides a solution to this problem in later papers.** In his ([1947](https://www.researchgate.net/publication/42305858_The_choice_of_statistical_tests_illustrated_on_the_interpretation_on_data_classes_in_a_2X2_table), 173), for instance, he prescribes a three-step process for specifying tests:

> Step 1. We must specify the [sample space].
>
> Step 2. We then divide this set by a system of ordered boundaries or contours such that as we pass across one boundary and proceed to the next, we come to a class of results which makes us more and more inclined, on the information available, to reject the hypothesis tested in favour of alternatives which differ from it by increasing amounts.
>
> Step 3. We then, if possible, associate with each contour level [a Type I error rate].

The key point here is that **Pearson prescribes ordering results according to the degree to which they would incline one to reject $H_0$ and only considering tests that reject $H_0$ on result $s$ but not result $s'$ if $s$ makes one more inclined to reject $H_0$ than $s'$.** If the ordering of one's inclinations to reject $H_0$ on the basis of possible observations conforms to the likelihood ratios of those observations, then on this approach one will not consider problematic tests like the one that rejects on $s_2$ but not $s_1$ in our example. This approach is analogous to the thief always taking items in descending order by value/weight ratio, stopping when the next item will not fit. **Here we see a point of disanalogy between hypothesis test construction and literal knapsack problems:** because the outcomes of hypothesis tests are (at least *de facto*) interpreted in evidential terms, it seems inappropriate to add elements of the sample space to $S$ "out of order" relative to their likelihood ratios, even if doing so allows one to get greater power while keep the Type I error rate below $\alpha$. By contrast, it is not problematic to add items to the thief's knapsack "out of order" relative to their value/weight ratios if doing so allows one to get a higher total value while keeping the maximum weight below the maximum.

**A second point of disanalogy between a literal knapsack problem and the problem of constructing a hypothesis test concerns the appropriate way to trade off value against weight, or power against Type I error rate.** We can arrange so that it is only a slight idealization to suppose that the thief does not care how heavy her bag is as long as she can carry it away. (We can assume that she has a strong back and a getaway vehicle nearby, does not have to worry about how much noise she makes, and so on.) We cannot arrange so that a scientist does not care about the Type I error rate of his or her test as long as it is below a particular threshold, at least if we impose the normative assumption that the scientist's goal is to advance knowledge and not just, say, to get his or her paper past journal referees.

**Rather than maximizing power subject to a maximum Type I error rate, it would seem to make more sense to minimize a weighted sum of the Type I and Type II error rates,** where the Type II error rate is the probability of failing to reject the null hypothesis if it is false (1-power) and the weights reflect the importance of avoiding Type I and Type II errors. Like Pearson's approach if one's inclinations to reject $H_0$ conform to likelihood ratios, this approach would lead one to reject $H_0$ for $H_a$ if and only if the likelihood ratio $\Pr(s;H_a)/\Pr(s;H_0)$ exceeds some threshold $k$. In this case, $k$ is simply the weight one associates with the Type I error rate divided by the weight one associates with the Type II error rate. **The only difference between this approach and Pearson's is that this approach involves fixing relative weights on the Type I error rate and power and letting the likelihood ratio cutoff for rejection and the Type I error rate fall where they may,** whereas Pearson's involves putting a cap on the Type I error rate and letting the likelihood ratio cutoff and (implied) relative weights on the Type I error rate and power fall where they may.

One might object that weights on the Type I and Type II error rates are too subjective or arbitrary for use in science. However, they do not seem to be any more subjective or arbitrary than the maximum tolerated Type I error rate $\alpha$. There is a typical convention of setting $\alpha=.05$, but that convention is itself rather arbitrary. Moreover, we could establish the analogous convention of setting $k=20$, which has the effect of guaranteeing that the Type I error rate is no greater than the standard .05 ([Royall 2000](http://www.tandfonline.com/doi/abs/10.1080/01621459.2000.10474264)).

**To my mind, this alternative to Pearson's approach seems more sensible.** Constructing a hypothesis test is more like filling a knapsack for a long journey than for a quick getaway: every increase in weight (Type I error rate) matters and needs to be compensated by a sufficient increase in value (power).

The main problem for this approach is that it does not generalize well to cases involving composite hypotheses (e.g., that a particular parameter is in a specified range or is not equal to a specific value), which are the usual cases in science. In those cases one or both of $\Pr(s;H_0)$ and $\Pr(s;H_a)$ lack definite values, and many methodologists are reluctant to appeal to the corresponding conditional probabilities $\Pr(s|H_0)$ and $\Pr(s|H_a)$ because they lack generally accepted objective values.

[^1]: This example comes from Lecture 8 of the edX course [MITx 6.00.2x](https://www.edx.org/course/introduction-computational-thinking-data-mitx-6-00-2x-2).

[^2]: Lehmann is perhaps the most prominent contemporary methodologists who takes randomized tests seriously; see [Lehmann and Romano 2005](https://books.google.com/books/about/Testing_Statistical_Hypotheses.html?id=K6t5qn-SEp8C), Ch. 15 