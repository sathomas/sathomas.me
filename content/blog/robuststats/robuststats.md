---
title: Robust Statistics
description: Describing data with outliers
date: 2025-01-27
draft: true
tags: [statistics, python]
---

Most folks reading this blog probably understand something about _mean_ and _standard deviation._ Both are statistical measures that summarize a collection of numerical data. The mean tells us the “average” value of the data, and the standard deviation measures how much the data values vary from that average. But despite their widespread use, mean and standard deviation can sometimes mislead. Data sets with outliers or extreme values, for example, result in statistical measurements that may not reflect the true characteristics of the population. In those cases, alternative measures based on robust statistics provide more helpful summary statistics.

## The Problem with Mean and Standard Deviation

To undestand how mean and standard deviation can fall short, we can consider a simple example. Imagine a population of people whose net worth follows a normal distribution with a mean of \$200,000 and a standard deviation of \$25,000<label for="sn-1" class="sidenote-toggle sidenote-number"></label>
<input type="checkbox" id="sn-1" class="sidenote-toggle" />
<span class="sidenote">Although the distribution of net worth in the United States is far from a normal distribution, its median value in 2023 was about \$200,000 according to the Federal Reserve's <a src="https://www.federalreserve.gov/econres/scfindex.htm">Survey of Consumer Finance</a>. So this imaginary population at least bears a superficial resemblence to the <abbr>US</abbr> population.</span>. Let’s pick fifty random inviduals from this population and describe our sample using statistics:

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

When we describe data with robust statistics, we might keep in mind that robust measures may not be as familiar as mean and standard deviation. Sometimes we can fix that by adjusting the robust measures. For example, we may be able to scale the median absolute deviation so that a given  <abbr>MAD</abbr> value represents the same variation as a standard deviation. That makes <abbr>MAD</abbr> directly comparable to the standard deviation. To make these adjustments we need to know the relationship between expected values of robust and traditional measures. In an entire population, what is the relationship between mean and median and between standard deviation and <abbr>MAD</abbr>?

In many cases the mean/median relationship is trivial: they’re often the same. This is true whenever the distribution is symmetric about the mean, including for the normal distribution we’ve been using in our example. You can see that graphically in figure 1, which plots the probability density for our example population. Half of the density lies below the mean and half lies above it, so the mean must be exactly in the middle. Of course, the “middle” is, by definition, the median. So in this case we can consider the median to be the “robust mean.”

<figure>

![PDF of normal distribution shows that the mean lies exactly in the middle and, therefore, is equivalent to the median](medianlight.png)

![PDF of normal distribution shows that the mean lies exactly in the middle and, therefore, is equivalent to the median](mediandark.png)

<figcaption>The probability density function (<abbr>PDF</abbr>) of the normal distribution shows that the mean lies exactly in the middle; that is exactly the definition of the <em>median,</em> and for this distribution, the two measures have the same expected value.</figcaption></figure>

The situation is more complicated for the <abbr>MAD</abbr> and the standard deviation. That’s clear in our example, as the population standard deviation was \$25,000 while the sample <abbr>MAD</abbr> of the original group was only \$14,845. You can’t directly compare those numbers. It turns out, however, that all we need is a constant scaling factor to make the two values comparable. Let’s find that scaling factor. Remember that the <abbr>MAD</abbr> is defined as the median of absolute deviations. We’ve already seen that the median equals the mean, $\mu$.:
$$
\small{\text{MAD}}(X) = \text{median}(\left| X_i - \text{median}(X) \right|) = \text{median}(\left| X_i - \mu) \right|)
$$
The symmetry of the normal distribution helps here as well. Notice that the absolute deviations can be split into two groups: (1) the deviation for values greater than the mean, $X_i - \mu$, and (2) the deviation for values less than the mean, $\mu - X_i$. But, as figure 2 shows, those two groups have the same absolute deviations; for every point that’s greater than the mean, there is an equivalent point less than the mean with the same absolute deviation. And since each group has the same absolute deviations, each group will also have the same median value for those absolute deviations. Deriving the median for one of the groups is enough to tell us the <abbr>MAD</abbr> of the entire distribution.

<figure>

![PDF of normal distribution shows each point below the mean has a corresponding point above the mean and both have the same deviation from the mean](deviationlight.png)

![PDF of normal distribution shows each point below the mean has a corresponding point above the mean and both have the same deviation from the mean](deviationdark.png)

<figcaption>For the normal distribution, every point below the mean/median has a corresponding point above the mean/median with the same deviation.</figcaption>

</figure>

Just like the median is the point exactly in the middle of the probability density, the median deviation from that median is the point exactly in the middle of the upper half of the probability density. It is point at which half of the probability lies to its left and half lies to its right. Figure 3 highlights these divisions. As the figure shows, we first divide the probability density in half to find the median, and then we divide one of those halves in half again to find the median deviation.

<figure>

![PDF of normal distribution which shows that the median deviation from the median for values above the median occurs at the 75th percentile](percentilelight.png)

![PDF of normal distribution which shows that the median deviation from the median for values above the median occurs at the 75th percentile](percentiledark.png)

<figcaption>The area of the <abbr>PDF</abbr> above the mean/median represents half of the total probability. Dividing that region in half again locates the median deviation from the mean/median.</figcaption>

</figure>

One we see where the median deviation is located, it’s easy to calculate. The value, in this case net worth, is the 75th percentile value for our distribution. And the median absolute deviation is the difference between that value and the median value or the population mean. In our example the expected value for the <abbr>MAD</abbr> is $16,862,

```python
percentile75 = population.ppf(0.75)
mad = percentile75 - population_mean
```

The same approach works for any normal distribution. The expected value of the median is the population mean, and the expected value of the <abbr>MAD</abbr> is the difference between the 75th percentile and the mean.
$$
\begin{aligned}
\text{median}\left[X \sim N(\mu,\sigma)\right] &= \mu \\
\small{\text{MAD}}\left[X \sim N(\mu,\sigma)\right] &= \text{P}_{0.75}(X) - \mu
\end{aligned}
$$
To make the relationship between <abbr>MAD</abbr> and $\sigma$ more concrete, use the formula for percentile values of the normal distribution. It depends on both $\mu$ and $\sigma$ as well as values of the probit function $\Phi^{-1}$. Equation 3 shows the results: the expected value of <abbr>MAD</abbr> is linearly proportional to the standard deviation, and the scaling constant is $\Phi^{-1}(0.75)$.
$$
\begin{aligned}
\small{\text{MAD}}\left[X \sim N(\mu,\sigma)\right] &= \text{P}_{0.75}(X) - \mu \\
&= \left[\mu + \sigma \ {\Phi}^{-1}(0.75)\right] - \mu \\
&= \sigma \ \Phi^{-1}(0.75) \\
\text{or} \\
\sigma &= \dfrac{\small{\text{MAD}}}{\Phi^{-1}(0.75)}
\end{aligned}
$$
No simple expression exists for the probit funtion $\Phi^{-1}(p)$ in general, but its value at 0.75 can be computed analytically and is approximately 0.67449. If we measure the <abbr>MAD</abbr> of a sample from a normally distributed population, we can expect the population’s standard deviation to equal that measurement divided by 0.67449. This value is sometimes called the _normalized median absolute deviation,_ and it serves as a robust version of the standard deviation for normal distributions. Some programming libraries (e.g. [R stats](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/mad)) even employ this scaling by default when computing the median absolute deviation.

Now we have a way to provide robust descriptive statistics that are directly comparable to to traditional measures, at least for normally distributed populations:
$$
\begin{aligned}
\mu &\sim \text{median} \\
\sigma &\sim 1.4826 \cdot \small{\text{MAD}}
\end{aligned}
$$

## Evaluating the Results

Now that we have a way to calculate robust versions of the mean and standard deviation, we can see how well those measures resist outliers. Recall that our original group was sampled from a normal distribution with $\mu = 200{,}000$ and $\sigma = 25{,}000$, and we added one outlier to that group. The mean and standard deviation of the new group were nearly 20,000,000 and 138,000,000, respectively. Those measures aren’t even close to the distribution parameters. The robust measures, however, reflect the population quite nicely: 194,000 and 22,600 for the “robust mean” and “robust standard deviation”. At least for our initial example, robust statistics provide a better description of the underlying population when the sample includes outliers.

To verify that this result isn’t a fluke, we can generate a series of samples from normal distributions with a range of parameters, introduce outliers into those samples, and compare traditional and robust measures.

```python
np.random.seed(42)
sample_stats = []
for mu in [1, 10, 100]:
    for sigma in [x * mu for x in [0.1, 0.2, 0.5, 1, 2, 5, 10]]:
        sample = np.random.normal(loc=mu, scale=sigma, size=500000)
        sample[0:500] = [mu + 5 * sigma] * 500 # replace some samples with outliers
        sample_stats.append({
            'mu': mu,
            'mean': np.mean(sample),
            'median': np.median(sample),
            'sigma': sigma,
            'std': np.std(sample),
            'mad': stats.median_abs_deviation(sample, scale="normal")
        })
```

Table 1 lists the results. As it makes clear, the robust measures consistently provide substantially more accurate estimates of the true population parameters. The results are pretty impressive, but they do come with an important caveat: we’re considering a population represented by the normal distribution. The next post will examine the complications that different distributions introduce.

<table class="col-1-r col-2-r col-3-r col-4-r col-5-r col-6-r">
  <caption>Comparison of traditional and robust measures for multiple samples from various normal distributions. Each row summarizes 499,500 values randomly sampled from the distribution, combined with 500 values set to <em>μ</em> + 5<em>σ</em>. Note that <abbr>MAD</abbr> is the <em>normalized</em> median absolute deviation.</caption>
  <thead>
    <tr>
      <th><em>μ</em></th>
      <th>mean</th>
      <th>median</th>
      <th><em>σ</em></th>
      <th>std dev</th>
      <th><abbr>MAD</abbr></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>1.000383</td>
      <td>1.000028</td>
      <td>0.1</td>
      <td>0.101241</td>
      <td>0.100256</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1.000605</td>
      <td>1.000065</td>
      <td>0.2</td>
      <td>0.202366</td>
      <td>0.200352</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1.001843</td>
      <td>0.999959</td>
      <td>0.5</td>
      <td>0.507097</td>
      <td>0.501442</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1.005748</td>
      <td>1.003024</td>
      <td>1.0</td>
      <td>1.012169</td>
      <td>0.999266</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1.012409</td>
      <td>1.006309</td>
      <td>2.0</td>
      <td>2.025853</td>
      <td>2.004507</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1.020550</td>
      <td>1.006610</td>
      <td>5.0</td>
      <td>5.052604</td>
      <td>5.003562</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1.053924</td>
      <td>1.006006</td>
      <td>10.0</td>
      <td>10.110405</td>
      <td>9.997652</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.005786</td>
      <td>9.999597</td>
      <td>1.0</td>
      <td>1.011163</td>
      <td>1.001487</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.010695</td>
      <td>10.007263</td>
      <td>2.0</td>
      <td>2.025043</td>
      <td>2.004667</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.024332</td>
      <td>10.006425</td>
      <td>5.0</td>
      <td>5.058050</td>
      <td>5.010623</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.050436</td>
      <td>9.998484</td>
      <td>10.0</td>
      <td>10.124124</td>
      <td>10.002446</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.054985</td>
      <td>10.004193</td>
      <td>20.0</td>
      <td>20.244190</td>
      <td>20.037551</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.335298</td>
      <td>10.117630</td>
      <td>50.0</td>
      <td>50.564300</td>
      <td>50.047076</td>
    </tr>
    <tr>
      <td>10</td>
      <td>10.662132</td>
      <td>10.121115</td>
      <td>100.0</td>
      <td>101.178462</td>
      <td>100.086371</td>
    </tr>
    <tr>
      <td>100</td>
      <td>100.048039</td>
      <td>100.012035</td>
      <td>10.0</td>
      <td>10.122126</td>
      <td>10.040442</td>
    </tr>
    <tr>
      <td>100</td>
      <td>100.117190</td>
      <td>100.018728</td>
      <td>20.0</td>
      <td>20.228212</td>
      <td>20.006114</td>
    </tr>
    <tr>
      <td>100</td>
      <td>100.171905</td>
      <td>99.988163</td>
      <td>50.0</td>
      <td>50.653803</td>
      <td>50.146970</td>
    </tr>
    <tr>
      <td>100</td>
      <td>100.610046</td>
      <td>100.389951</td>
      <td>100.0</td>
      <td>101.192734</td>
      <td>100.270438</td>
    </tr>
    <tr>
      <td>100</td>
      <td>100.709345</td>
      <td>100.164250</td>
      <td>200.0</td>
      <td>202.234231</td>
      <td>200.081156</td>
    </tr>
    <tr>
      <td>100</td>
      <td>102.726667</td>
      <td>101.292416</td>
      <td>500.0</td>
      <td>505.393698</td>
      <td>499.635618</td>
    </tr>
    <tr>
      <td>100</td>
      <td>102.912577</td>
      <td>99.961542</td>
      <td>1000.0</td>
      <td>1012.838466</td>
      <td>1001.865549</td>
    </tr>
  </tbody>
</table>

## Colophon

Figures 1-3 were created by the following code:

```python
from matplotlib import font_manager, pyplot as plt

# Define the range for the x-axis
x = np.linspace(
    population_mean - 4 * population_stdev, population_mean + 4 * population_stdev, 1000
)

# Compute the PDF of the normal distribution
population = stats.norm(population_mean, population_stdev)
pdf = population.pdf(x)

# Plot styles
%config InlineBackend.figure_format = 'retina'
font_dir = ["../Fonts"]
for font in font_manager.findSystemFonts(font_dir):
    font_manager.fontManager.addfont(font)

darkmode = True
plottype = "topmedian" # median, deviation, topmedian

plt.style.use('../Styles/darkmode.mplstyle' if darkmode else '../Styles/lightmode.mplstyle')

color1 = plt.rcParams["axes.prop_cycle"].by_key()["color"][0]
color2 = plt.rcParams["axes.prop_cycle"].by_key()["color"][1]
color3 = plt.rcParams["axes.prop_cycle"].by_key()["color"][2]
color4 = plt.rcParams["axes.prop_cycle"].by_key()["color"][3]
color5 = plt.rcParams["axes.prop_cycle"].by_key()["color"][4]
color6 = plt.rcParams["axes.prop_cycle"].by_key()["color"][5]
textcolor = plt.rcParams["text.color"]

#--- Figure created in layers
fig, ax = plt.subplots(figsize=(7.88, 5), tight_layout=True)

#--- shading of region divided by mean
if plottype in ("median", "deviation"):
    if darkmode:
        ax.add_patch(plt.Rectangle(
            (0, 0), 0.5, 1, facecolor=textcolor, alpha=0.20, transform=ax.transAxes
        ))
    else:
        ax.add_patch(plt.Rectangle(
            (0.5, 0), 0.5, 1, facecolor=textcolor, alpha=0.05, transform=ax.transAxes
        ))

#--- PDF itself
ax.plot(x, pdf)
ax.set_xlabel("Net Worth ($1000s)")
ax.xaxis.set_major_formatter(lambda x, pos: f'{x/1000:.0f}')
ax.set_ylabel("Probability Density")

# disable y ticks and tick labels
ax.tick_params(
    axis="y",
    which="both",
    left=False,
    right=False,
    labelleft=False,
)

# remove the bottom margin but retain the top margin
ax.set_ymargin(0)
ax.autoscale_view()
lim = ax.get_ylim()
delta = np.diff(lim)
top = lim[1] + delta * 0.05
bottom = lim[0]
ax.set_ylim(bottom, top)

#--- fill the area beneath the curve
if plottype in ("median", "deviation"):
    plt.fill_between(x[: len(x) // 2], pdf[: len(pdf) // 2], color=color1, alpha=0.33)
    plt.fill_between(x[len(x) // 2 :], pdf[len(pdf) // 2 :], color=color1)
elif plottype == "topmedian":
    x0 = population_mean
    x1 = population.ppf(0.75)
    i50plus = [i for i in range(len(x)) if x0 <= x[i] < x1]
    i75plus = [i for i in range(len(x)) if x[i] >= x1]
    plt.fill_between(x[: len(x) // 2], pdf[: len(pdf) // 2], color=color1, alpha=0.33)
    plt.fill_between(x[i50plus], pdf[i50plus], color=color1, alpha=0.67)
    plt.fill_between(x[i75plus], pdf[i75plus], color=color1)

#--- label the regions

if plottype == "median":
    ax.text(
        0.525,
        0.98,
        "Half of density above mean",
        transform=ax.transAxes,
        verticalalignment="top",
        horizontalalignment="left",
    )
    ax.text(
        0.475,
        0.98,
        "Half of density below mean",
        transform=ax.transAxes,
        verticalalignment="top",
        horizontalalignment="right",
    )

#--- highlight the mean value
if plottype in ("median", "deviation"):
    ax.axvline(
        population_mean,
        linewidth=4,
        color=color2,
    )
if plottype == "median":
    ax.annotate(
        "mean",
        xy=(0.5, 0.4),
        xytext=(0.45, 0.4),
        arrowprops=dict(facecolor=textcolor, arrowstyle='-|>'),
        horizontalalignment="right",
        verticalalignment="center",
        xycoords=ax.transAxes
    )

#--- add example deviations
if plottype == "deviation":
    x0 = population_mean
    y0 = population.pdf(x0)
    x1 = population_mean - 1.75 * population_stdev
    y1 = population.pdf(x1)
    x2 = population_mean + 1.75 * population_stdev
    y2 = population.pdf(x2)
    plt.scatter(x1, y1, color=color4, zorder=2.5)
    plt.scatter(x2, y2, color=color4, zorder=2.5)
    ax.annotate(
        "deviation",
        xy=(x0,y1),
        xytext=(x1 + (x0-x1)/2, y1),
        horizontalalignment="center",
        verticalalignment="bottom"
    )
    ax.annotate(
        "",
        xy=(x0,y1),
        xytext=(x1,y1),
        arrowprops=dict(facecolor=textcolor, arrowstyle="<->")
    )
    ax.annotate(
        "deviation",
        xy=(x0,y2),
        xytext=(x2 - (x2-x0)/2, y2),
        horizontalalignment="center",
        verticalalignment="bottom"
    )
    ax.annotate(
        "",
        xy=(x0,y2),
        xytext=(x2,y2),
        arrowprops=dict(facecolor=textcolor, arrowstyle="<->")
    )

#--- label regions of probability
if plottype == "topmedian":
    ax.annotate(
        "50% of total probability",
        horizontalalignment="center",
        verticalalignment="center",
        xy=(0.44,0.4),
        xytext=(0.2,0.75),
        xycoords=ax.transAxes,
        arrowprops=dict(facecolor=textcolor, arrowstyle='-|>', relpos=(0.55,0.5))
    )
    ax.annotate(
        "25% of total probability",
        horizontalalignment="left",
        verticalalignment="center",
        xy=(0.53,0.75),
        xytext=(0.6,0.9),
        xycoords=ax.transAxes,
        arrowprops=dict(facecolor=textcolor, arrowstyle='-|>', relpos=(0.05,0.5))
    )
    ax.annotate(
        "25% of total probability",
        horizontalalignment="left",
        verticalalignment="center",
        xy=(0.62,0.3),
        xytext=(0.65,0.65),
        xycoords=ax.transAxes,
        arrowprops=dict(facecolor=textcolor, arrowstyle='-|>', relpos=(0.15,0.5))
    )
    x0 = population_mean
    y = 0.25 * population.pdf(x0)
    x1 = population.ppf(0.75)
    ax.annotate(
        "median deviation\nfrom median",
        xy=(x0,y),
        xytext=(x1 + (x0-x1)/2, y*0.9),
        horizontalalignment="center",
        verticalalignment="top"
    )
    ax.annotate(
        "",
        xy=(x0,y),
        xytext=(x1,y),
        arrowprops=dict(facecolor=textcolor, arrowstyle="<->")
    )

plt.show()
```

