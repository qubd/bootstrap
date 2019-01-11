# Why bootstrap?

Let's compare the standard method for making a confidence interval for the mean of an unknown population (using the T-distribution) with the non-parametric bootstrap.

### Why do parametric intervals work?

The [CLT]((http://en.wikipedia.org/wiki/Central_limit_theorem)) tells us the sampling distribution of the mean X̄ is asymptotically normal as the sample size n grows larger, with standard deviation σ / sqrt(n), where σ is the standard deviation of the distribution our sample was drawn from. With this information, we can calculate the distance between the 5th and 95th quantiles of an asymptotic (normal) approximation to our sampling distribution, which approximates the width of a 90% confidence interval well when our sample is sufficiently large.

There's a problem though. The value of σ is, in practice, unknown, so we need to estimate it from our sample. We can use the sample standard deviation, s, as an (unbiased) estimator for σ. 

Consider the Z-score for our sample, Z = (X̄ - μ) / (σ / sqrt(n)). The distribution of the sample mean X̄ is asymptotically normal by the CLT, so the distribution of Z tends asymptotically to the standard normal distribution, since everything except X̄ in there is a constant. But remember that we don't know σ. 

We can define instead the T-score by T = (X̄ - μ) / (s / sqrt(n)) using the value of s from our sample. The question is how this quantity behaves now that there's a new source of uncertainty, s, in the denominator. 

*If we know our sample was drawn from a normal distribution*, then the distribution of T is called [Student's T-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution), and it's not too hard to see it's asymptotically normal after deriving the distribution / density function (which takes some work). 

*If our sample was not drawn from a normal distribution*, then the distribution of T-scores is usually still well approximated by Student's T-distribution, for all but very small sample sizes. Confidence intervals based on the T distribution are said to be robust to deviations from normality, though I've yet to find a precise statement of what this means. It also turns out that the distribution of T is asymptotically normal as long as the CLT applies, for *much* more complicated reasons. In fact, this was only [proven in 1997](https://projecteuclid.org/download/pdf_1/euclid.aop/1024404523)!


### Why does bootstrapping work?

The logic of the bootstrap method is very simple. We want to know the sampling distribution of X̄, so we approximate it by explicitly sampling from the best approximation of the unknown population we have access to, which is our sample.

In this context, the distribution of values in our sample is sometimes called the empirical distribution, and the sampling distribution created by sampling from it is called the resampling distribution. The endpoints of our 90% confidence interval are approximated by the 5th and 95th quantiles of the resampling distribution.

For this to work, we need the resampling distribution has to accurately approximate the sampling distribution. As the sample size gets larger and larger, the empirical distribution (our sample) will approach the unknown population distribution because we're taking a random sample. To prove this requires only the [LLN](http://en.wikipedia.org/wiki/Law_of_large_numbers). The resampling distribution will thus approach the sampling distribution since the sampling procedure used to construct each is identical, and their 5th and 95th quantiles will eventually match.

### Why do I like bootstrapping?

When I teach statistics, I would like my students to understand what they're doing and to think about why and when it does / does not work. Bootstrapping allows this, and is a simple idea that generalizes well. I don't have to sweep discussions of robustness and asymptotic correctness under the rug.

### Experimental Results

The r code in this repo was used to draw 10000 samples of a fixed size (either 5, 10, 20, 50, or 100) from one of three distributions (Normal, Exponential, and Pareto). For each sample, a 90% confidence interval was constructed. Ideally, 9000 of the intervals would contain the population mean. The results are tabulated below.

T-distribution Intervals

| 	    |5    |10   |20	  |50   |100  |
|-------|-----|-----|-----|-----|-----|
|Norm   |9026 |8969 |9000 |8990 |9019 |
|Exp    |8257 |8583 |8718 |8865 |8915 |
|Pareto	|6672 |6982 |7280 |7615	|7989 |

Bootstrap intervals

|       |5    |10   |20	  |50   |100  |
|-------|-----|-----|-----|-----|-----|
|Norm   |7812 |8422 |8745 |8883 |8924 |
|Exp    |7301 |8171 |8494 |8775 |8863 |
|Pareto	|5887 |6685 |7205 |7629	|8022 |
