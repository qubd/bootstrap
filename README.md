# Bootstrap Confidence Intervals for the Mean

Let's compare the standard parametric method for making a confidence interval for the mean of an unknown population (using the T-distribution) with the non-parametric bootstrap.

### Why do parametric intervals work?

The [CLT]((http://en.wikipedia.org/wiki/Central_limit_theorem)) tells us the sampling distribution of the mean X̄ is asymptotically normal as the sample size n grows larger, with standard deviation σ / sqrt(n), where σ is the standard deviation of the distribution our sample was drawn from. With this information, we can calculate the distance between the 5th and 95th quantiles of an asymptotic (normal) approximation to our sampling distribution, which approximates the width of a 90% confidence interval well when our sample is sufficiently large.

There's a problem though. The value of σ is, in practice, unknown, so we need to estimate it from our sample. We can use the sample standard deviation, s, as an (unbiased) estimator for σ. 

Consider the Z-score for our sample, Z = (X̄ - μ) / (σ / sqrt(n)). The distribution of the sample mean X̄ is asymptotically normal by the CLT, so the distribution of Z tends asymptotically to the standard normal distribution, since everything except X̄ in there is a constant. But remember that we don't know σ. 

We can instead define the T-score by T = (X̄ - μ) / (s / sqrt(n)) using the value of s from our sample. The question is how this quantity is distributed now that there's a new source of uncertainty, s, in the denominator. 

*If we know our sample was drawn from a normal distribution*, then the distribution of T is called [Student's T-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution), which looks like a normal distribution, but with fatter tails. After deriving the distribution / density function, it's not too hard to see that it's asymptotically normal, i.e., as the sample size (and hence the degree of freedom) grows, the T distribution approaches the standard normal distribution.

*If our sample was not drawn from a normal distribution*, then the distribution of T-scores is not Student's T-distribution. However, for many well-behaved population distributions, Student's T-distribution is a fairly good approximation to the distribution of T-scores for all but very small sample sizes. Confidence intervals based on the T distribution are said to be robust to non-normality, though I've yet to find a precise statement of what this means. It also turns out that the distribution of T is still asymptotically normal as long as the CLT applies, for *much* more complicated reasons. In fact, this was only [proven in 1997](https://projecteuclid.org/download/pdf_1/euclid.aop/1024404523)!

### Why does bootstrapping work?

The logic of the non-parametric bootstrap method is very simple. We want to know the sampling distribution of X̄, so we approximate it by explicitly sampling from the best approximation of the unknown population we have access to, which is our sample.

In this context, the distribution of values in our sample is sometimes called the empirical distribution, and the sampling distribution created by sampling from it is called the resampling distribution. The endpoints of our 90% confidence interval are approximated by the 5th and 95th quantiles of the resampling distribution.

For this to work, the resampling distribution has to accurately approximate the sampling distribution. As the sample size gets larger and larger, the empirical distribution (our sample) will approach the unknown population distribution because we're taking a random sample. To prove this requires only the [LLN](http://en.wikipedia.org/wiki/Law_of_large_numbers). We expect the resampling distribution to then approach the sampling distribution since the *sampling procedure used to construct each is identical* (note that the bootstrap method of takes resamples of the *same size* as the original sample). The r code in the four notebooks in this repo generates histograms of sampling and resampling distributions of the mean so you can compare them!

### Experimental Results

The r code in this repo was used to draw 10000 samples of a fixed size (either 5, 10, 20, 50, or 100) from one of three distributions (Normal, Exponential, and Pareto). For each sample, a 90% confidence interval was constructed. Ideally, 9000 of the intervals would contain the population mean. The results are tabulated below.

T-distribution Intervals

| 	    |5    |10   |20	  |50   |100  |
|-------|-----|-----|-----|-----|-----|
|Norm   |9026 |8969 |9000 |8990 |9019 |
|Exp    |8257 |8583 |8718 |8865 |8915 |
|Unif   |8888 |8935 |9015 |9003	|8995 |
|Pareto	|6672 |6982 |7280 |7615	|7989 |

Note that precisely 90% of T-distribution confidence intervals will contain the population mean when sampling from a normal distribution, for any sample size, so the first row gives an idea of how much variation to expect simply because our simulation runs only 10000 times.

Bootstrap Intervals

|       |5    |10   |20	  |50   |100  |
|-------|-----|-----|-----|-----|-----|
|Norm   |7812 |8422 |8745 |8883 |8924 |
|Exp    |7301 |8171 |8494 |8775 |8863 |
|Unif   |8023 |8511 |8814 |8909 |8958 |
|Pareto	|5887 |6685 |7205 |7629	|8022 |

It seems like the bootstrap intervals are notably less accurate for smaller samples, and of course, when sampling from a normal distribution, since the T-distribution intervals are a priori dead on 90% in that case. With a non-normal distribution and a sample size of 50 or larger though, there's not much between the two. Both methods perform dismally when sampling from the heavy-tailed Pareto distribution, which is not surprising.

In my mind, the results vindicate the parametric method a bit, because despite the dubious assumptions and complicated justification, it performs significantly better with small samples. On the other hand, it's nice to see the bootstrap intervals almost completely catch up by the time our sample size reaches 100. Can you think of a population distribution where the bootstrap method might outperform the parametric one (and do you know how to write r to sample from it)? Let me know!



