# Homework #2 - A/B Testing in the Wild

1. The Law of Large Numbers (LLN) says that sample mean will converge to expectation as sample size grows. Assuming that this is true, prove that sample variance will converge to variance as sample size grows. 

2. What is p-value? (Choose one or more)

* [x] Assuming that the null hypothesis is true, what is the probability of observing the current data.

* [ ] Based on the observed data, what is the probability of the null hypothesis being true.

* [ ] Based on the observed data, what is the probability of the null hypothesis being false.

* [ ] Assuming that our hypothesis is true, what is the chance that we reject the null hypothesis.

3. If we conduct a frequentist statistical test at 5% significance level repeatedly for 4,000 times, how many times can we expect to have statistically significant results even if group A and B are exactly the same?

--------------------------------------------------------------------------------------------------------------------

**Solution 3**

Since the x% significance level means if we do the test repeatedly 100 times, We will wrongly reject Null Hypothesis x times. This means that for this scenario, we will have statistically significant even if group A and B are exactly the same about (5/100)x4000 = 200 times.

**End Solution 3**

--------------------------------------------------------------------------------------------------------------------

4. Hamster Inc. once again wants to test the conversion rates between package colors of its sunflower seeds; this time it is Red Package vs Gold Package. The Red Package is the existing group with average conversion rate of 11%. If they think the minimum detectable effect is 1% and want to make a 80/20 control/test split, how many unique users should see each package color before we decide which one performs better? Assume that they are testing at significance level of 15%. Show your work.

--------------------------------------------------------------------------------------------------------------------

**Solution 4**

We use MDE here to find the number samples of each group. Since they test at significant level of 15% and the test is one-tail. (The rejection area is on the left tail.)

By using scipy.stats.norm in python, we found the z value which gives the cdf of 0.15 by z_alpha = norm.ppf(0.15) = -1.0364333894937898. 

Given that the red package (existing group) has average rate of 11% but we know that each conversion rate RV is Bernoulli, with mean p, variance is p(1-p). Hence, We have the pooled sample variance of 0.11*(1-0.11) = 0.0979. (here we use the existing variance to be the pooled one.)

The notation used in the equation to find number of sample is n be the number of samples of red package (control) and mn be the number of samples of gold package (test) where m is the numtiplier, in this case 0.25. mu is zero in our case since null hypothesis is that the mean of two groups are equal.

Now we apply the equation n = ((m+1)/m) * (z_alpha * pooled_variance/MDE )^2 substitute with the corresponding value, 
n = ((0.25+1)/0.25)x0.0979x(-1.0364333894937898/0.01)^2 = 5258.18. 
Then 0.25n = 1314.54. 

Thus, we will use about 5259 unique users to see red package and 1315 unique users to see gold package.

**End Solution 4**

--------------------------------------------------------------------------------------------------------------------

5. Let us say Hamster Inc. ran the experiment and got the following results. 

| campaign_id | clicks | conv_cnt | conv_per |
|------------:|-------:|---------:|---------:|
|         Red |  59504 |     5901 | 0.099170 |
|        Gold |  58944 |     6012 | 0.101995 |

5.1 At significance level of 7%, which variation should be chosen to run at 100% traffic? Show your work.

**Remark: Each click is Bernoulli trial. Hence, conv_per which is average conversion rate is Normal distributed due to CLT with mean of p.
Now that we knew conv_per of both Red and Gold are Normal distributed, We can use two-sample Z-test. State as follow.**

**H0 (Null hypothesis) : conv_per_red - conv_per_gold = 0
Ha (Alternative hypothesis) :  conv_per_red - conv_per_gold < 0
Test statistic Z = (conv_per_red - conv_per_gold - 0)/sqrt((pooled_variance^2)x( 1/59504 + 1/58944 )). 
pooled_variance can be found by p(1-p) where p is total conversions of both red and gold devided by their click. Hence, p = (5901+6012)/(59504+58944) = 0.10057. 
Now compute Z = (0.099170-0.101995)/sqrt(0.10057(1-0.10057)(1/59504 + 1/58944)) = -1.616328. 
Finally, compute p-value in this case, p-value = P(Z<-1.616328) = 0.05301.**

**We can see that our p-value is less than significance level of 0.07 so we reject Null hypothesis in favor of Ha. This result in choosing gold package to run at 100% traffic.**

**Note that : It's good to leave small portion of control group to verify so in practice may not run 100%.**

5.2 What are the confidence intervals at 7% significance of conversion rates for Red and Gold? Show your work.

--------------------------------------------------------------------------------------------------------------------

**Solution 5.2**

Finding confidence intervals at a% significance is finding the left and right border which give the area under pdf of the region = 1-0.01a\
We knew conversion rate of bith Red and Green is normally distributed. Let X be RV of standard normal distribution.

First, we compute Z which make P(-Z < X < Z) = 1-0.01(7) = 0.93. This is equivalent to finding Z which make P(X < -Z) = 0.01(7)/2 resulted in Z = 1.8119.\

Hence, we will find r1, r2 as left and right bound of interval of Red and g1, g2 as left and right bound of interval of Gold.\
We compute these by backward of doing standardize Normal dist.\
We know that conv_per is mean of each bernoulli. Along with LLN, this conv_per is close to real mean of bernoullis. Let p_red be probability of conversion successof that click in Red campaign. p_gold is same but for Gold campaign. Thus, p_red = 0.099170, p_gold = 0.101995. Follow that bernoulli RV with mean p has variance p(1-p) and with CLT,

variance of conv_per_red is p_red(1-p_red)/59504, mean of conv_per_red is 0.099170\
variance of conv_per_gold is p_gold(1-p_gold)/58944, mean of conv_per_gold is 0.101995

Note that in standardize normal RV, Z = (X-mu)/sqrt(variance) -> X = mu + Z(sqrt(variance)). Now we really compute r1,r2,g1,g2.\
r1 = 0.099170 + -1.8119(sqrt(0.099170(1-0.099170)/59504)) = 0.096949899.\
r2 = 0.099170 + 1.8119(sqrt(0.099170(1-0.099170)/59504)) = 0.1013901.\
g1 = 0.101995 + -1.8119(sqrt(0.101995(1-0.101995)/58944)) = 0.099736379.\
g2 = 0.101995 + -1.8119(sqrt(0.101995(1-0.101995)/58944)) = 0.104253620.

To conclude, confidence intervals at 7% significance of conversion rates for Red is [0.096949899,0.1013901]\
confidence intervals at 7% significance of conversion rates for Gold is [0.099736379,0.104253620]

**End Solution 5.2**

--------------------------------------------------------------------------------------------------------------------

6. Which of the following are true about frequentist A/B tests? (True/False)

* [**True**] It does not tell us the magnitude of the difference between control and test groups.

* [**False**] We can never know when to stop the experiments.

* [**True**] We can never determine if the null hypothesis being true.

* [**False**] We can run one or as many experiments as we want using the same significance level.

* [**True**] If we have too many samples in each group, the validity of the test can be jeopardized.

* [**False**] If you have set up the experiment based on desired minimum detectable effect and significance level, statististical significance is the only factor in determining which group is the better one.

* [**True**] We can only test difference between two proportions.

* [**False**] More samples in control and test groups are always better.
