---
title: Robust Statistics
description: Describing data with outliers
date: 2025-01-29
draft: true
tags: [statistics, python]
---

Most folks reading this blog probably understand something about _mean_ and _standard deviation._ Both are statistical measures that summarize a collection of numerical data. The mean tells us the “average” value of the data, and the standard deviation measures how much the data values vary from that average. But despite their widespread use, mean and standard deviation can sometimes mislead. Data sets with outliers or extreme values, for example, result in statistical measurements that may not reflect the true characteristics of the population. In those cases, alternative measures based on robust statistics provide more helpful summary statistics.

## The Problem with Mean and Standard Deviation

To undestand how mean and standard deviation can fall short, we can consider a simple example. Imagine a population of people whose net worth follows a normal distribution with a mean of \$200,000 and a standard deviation of \$25,000<label for="sn-1" class="sidenote-toggle sidenote-number"></label>
<input type="checkbox" id="sn-1" class="sidenote-toggle" />
<span class="sidenote">Although the distribution of net worth in the United States is far from a Normal distribution, its median value in 2023 was about \$200,000 according to the Federal Reserve's <a src="https://www.federalreserve.gov/econres/scfindex.htm">Survey of Consumer Finance</a>. So this imaginary population at least bears a superficial resemblence to the <abbr>US</abbr> population.</span>. Let’s pick fifty random inviduals from this population and describe our sample using statistics:

```python
import numpy as np
np.random.seed(42)

population_mean = 200000
population_stdev = 25000

group = np.random.normal(loc=population_mean, scale=25000, size=50)

mean = np.mean(group)
stdev = np.std(group)
```

The mean net worth of the sample is about \$194,000 and its standard deviation is about \$23,000; both values are consistent with the population as a whole. If these measures were the only information you knew about a group of fifty people, you would have no reason to doubt that the group’s members were typical folks from the population.

Now imagine that a single billionaire joins the group.

```python
newgroup = np.append(group, 1000000000)
mean = np.mean(newgroup)
stdev = np.std(newgroup)
```

The group’s mean net worth has now grown to nearly \$20 million with a standard deviation of over \$138 million. Now the group looks like a gathering of multi-millionaires; hardly typical folks from the population. But we know that 50 out of the 51 people in the group really are regular, unremarkable representatives of that population. In this case the common descriptive statistics could lead us to infer something about the population that is not actually the case.

## Accommodating Outliers with Robust Measures

As this example shows, extreme values or outliers can distort the common descriptive statistical measurements. If we want to avoid that distortion, we can use different measurements, ones that fall under the general category of “robust statistics.” Statisticians have defined several different robust statistical measures; we’ll focus on two common ones: the _median_ and the _median absolute deviation_ (<abbr>MAD</abbr>).

Like the mean, the median is a relatively familiar statistical concept. To calculate it, we arrange our data in order and pick the value exactly in the middle. Once we have the median, we calculate the <abbr>MAD</abbr> by first calculating the absolute value of the difference between each data value and that median, and then we find the median of the result. Here’s a simple example:

1. Data set: `[3, 1, 10, 5, 7]`
2. Data set in order:  `[1, 3, 5, 7, 10]`
3. Median is central (third) value: `5`
4. Deviations from median in original data set: `[-2, -4, 5, 0, 2]`
5. Absolute deviations: `[2, 4, 5, 0, 2]`
6. Absolute deviations in order: `[0, 2, 2, 4, 5]`
7. Median absolute deviation is central (third) value: `2`

Let’s see what these new measures say about our group of people.

```python
from scipy import stats

median = np.median(group)
mad = stats.median_abs_deviation(group)
```

The median net worth of our original group is \$194,146 and its <abbr>MAD</abbr> is \$14,845, both values are representative of the whole population. What happens when we include the billionaire?

```python
median = np.median(newgroup)
mad = stats.median_abs_deviation(newgroup)
```

The median net worth increases to \$194,147 and the <abbr>MAD</abbr> to $15,246; neither changes much from its original value. With only those measures to go on, we would still conclude that our group mostly consists of typical members of the population. And this time our inference is correct.

## Relating Robust Measures to Traditional Measures

When we describe data with robust statistics, we should keep in mind that readers may not be as familiar with those measures as they are with mean and standard deviation. One way to address that problem is directly relating robust measures to the more traditional ones. The relationship is often trivial in the case of the median. For any distribution that is symmetric about its mean, the median is a direct analog of the mean. More formally, the expected values of the mean and the median are identical.  You can see that graphically in figure 1. Since half of the density lies below the mean and half lies above it, the mean must be exactly in the middle, which is the very definition of the median. When this condition is true, the median can be considered the “robust mean.”

<figure>

![PDF of Normal distribution shows that the mean lies exactly in the middle and, therefore, is equivalent to the median](medianlight.png)

![PDF of Normal distribution shows that the mean lies exactly in the middle and, therefore, is equivalent to the median](mediandark.png)

<figcaption>The probability density function (<abbr>PDF</abbr>) of the Normal distribution shows that the mean lies exactly in the middle; that is exactly the definition of the <em>median,</em> and for this distribution, the two measures have the same expected value.</figcaption></figure>

The situation is more complicated for the <abbr>MAD</abbr> and the standard deviation. That’s clear in our example, as the population standard deviation was \$25,000 while the sample <abbr>MAD</abbr> of the original group was only \$14,845. You can’t directly compare those numbers.
