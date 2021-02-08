---
layout: post
title: Inferential Statistics
date: 2018-02-23 19:20:23 +0900
category: datascience
---

**Problem 1**: Data for old Faithful geyser (Variance and Standard Deviation)
You can get data from the google spreadsheet here <https://docs.google.com/spreadsheets/d/10gvXKJJhN_KIuRNTPKrtWwHkdckJVD5-FsL-s2a0Xgg/edit?usp=sharing>

Some questions which can be answered from the dataset:

- What is the variance of the population of all the eruption durations?

_1.302728333_

- What is the variance of the sample of all the eruption durations?

_1.540217344_

- What is the standard deviation of the population of all the eruption durations?

_1.141371251_

- What is the standard deviation of the sample of all eruption durations?

_1.241054932_

- For normal distribution, what proportion of values lie within

1 Stadard Deviation:

_0.68 or 68%_

2 Standard Deviation

_0.95 or 95%_

---

**Problem 2**: Heights in U.S (Z-Score)
Suppose heights of adult males in the U.S are normally distributed with a mean of 70 inches(5′ 10”) and a standard deviation of 4 inches. The heights of adult females are also normally distributed, with a mean of 65 inches (5’5”) and a standard deviation of 3.5 inches

Pam is an adult female and her height is 68 inches (5’8”). Jesse is an adult male and his height is 72 inches (6′)

- Convert Pam’s height to a z-score:

`Standard Error (S.E) = σ/√n`

`z-score = xmean – μ/(S.E) = 68-65/(3.5) = 0.857`

- Convert Jesse’s height to a z-score

`z-score = 72-70/4 = 0.5`

- What proportion of adult males in the U.S are taller than Jesse?

`This can be found by calculating the area under the curve for z- score of 0.5 which is 0.6915`

`So the proportion of adult males taller than Jesse = 1- 0.6915 = 0.3085`

- What proportion of adult females in the U.S are taller than Pam?

`This can be found by calculating the area under the curve for z- score of 0.0.857 which is 0.8051`

`So the proportion of adult males taller than Jesse = 1- 0.8051 = 0.1949`

- Who is taller after standardizing based on gender?

`As the z-score for Pam is greater than Jesse, so Pam is taller`

---

**Problem 3**: SAT Score (Sampling Distribution)
The average SAT score in the U.S (including the writing section) is 1497, and the standard deviation is 322. Suppose you take a random sample of 25 people who have taken the SAT in the U.S. and look at their scores.

- What do you expect the mean score of this sample to be?

`As the dataset for the mean sample is not given the best guess we have is that it must be equal to the population mean which is 1497`

- How much do you expect the sample mean to differ from the population mean? That is, what is the standard error?

`Standard Error (S.E) = σ/√n = 322/√25 = 64.4`

- What is the mean of the sampling distribution (for samples of size 25)?

`The mean of the sampling distribution should be 1497`

- What is the standard deviation of the sampling distribution (for samples of size 25)?

`The standard deviation of the sampling distribution should be 64.4`

- Suppose you take a sample of 25 people from a certain school, and the mean SAT score is 1609. What is the probability that a randomly selected sample from the U.S would have had a mean at least this high?

`Here let us calculate the z-score = 1609-1497/(322/√25 ) = 1.739`

`The probability of having at least this z-score should be 1 minus area under the bell curve = 1- 0.9591 = 0.0409`

- Suppose you take a random sample of 25 people and give a them a new type of test preparation and their mean score is 1609. Calculate a 95% confidence interval for the mean SAT score if everyone was given this test preparation?

`Here we have a two tailed distribution so Confidence Interval (C.I) =`

`xmean ± z-score for 2.5% * S.E = 1609 ± 1.96 * 64.4 = 1482.776 to 1735.224`

- Suppose you want to use the test prep if it increases the scores, but you dont care about the difference between a decrease and no effect. What is the null and alternate hypothesis?

`Null Hypothesis (H0) = xmean = μ`

`Null hypothesis states that there is no effect of the test preparation on the sample`

`Alternate Hypothesis (Ha) = xmean > μ`

`Alternate hypothesis states that there is positive effect of the test preparation on the sample`

- At an alpha level of 0.05 what is z-critical value for the hypothesis test?

`z-critical for the hypothesis should be towards the right of the mean so to get an area of 0.95 (assuming two tailed test) we have z-critical = 1.645`

- Would you accept or reject null?

`We would reject the null as the z-score for 1609(mean) is 1.739 which is greater than 1.645 (z-critical)`

---

**Problem 4**: Facebook friends (One sample t test)
The mean number of facebook friends is 338.  Diana wants to know whether her co workers have lesser or greater facebook friends than the mean, so she randomly selects 5 of her co workers and looks their number of friends. The value she finds are 356, 536, 1032, 209 and 285.

- Can you define the null and alternate hypothesis?

`The null hypothesis is that the mean of Diana’s coworkers facebook friends is equal to 338`

`The alternate hypothesis is that the mean of Diana’s coworkers facebook friends is no equal to 338.`

- What is the t- statistic of the sample?

`The t-statistic is normally used when we do not have a population standard deviation.`

`So the t-value is given by (xmean-μ)/(σ/√n)`

`Here mean value for 356, 536, 1032, 209 and 285 is 483.6`

`Also σ is given with the Stdev of sample/degree of freedom`

`= √(stdev sample / n-1) `

` =√(108654.3/4) = 329.63`

`t-value = (483.6-338)/(329.63/√5) = 0.9877`

- What is the p value of the sample?

`p value is the area under the curve at t-value 0.9877`

`From the link` <https://www.graphpad.com/quickcalcs/pValue1/>`, we can calculate the p value and df = 0.3792`

- Construct a 95% confidence interval for the mean number of Facebook friends for Diana’s coworkers`

`t-critical for 95% C.I is give from t-table 2.776`

`So with this the CI is given as xmean ± tcritical * S.E sample`

`= 483.6 ± 2.776 * (329.63/√5)`

`= 74.38 and 892.82`

- At alpha level of 0.05 would you accept or reject null?

`t-critical at 0.05 = 2.132, so as the t-value is 0.9877 lies in between -2.132 and 2.132 so we fail to reject the null hypothesis`

---

**Problem 5**: NASDAQ stocks (Dependant sample t- test)
A random sample of 10 stocks was drawn from all companies that were traded on NASDAQ on both Mar 24 and Jun 24, 2015. The opening prices of these stocks on both dates are in the given google spreadsheet here <https://docs.google.com/spreadsheets/d/1HKRFwERqel1q65gikgZQYvhgzx4NbxJJKK6GuQ0DMec/edit?usp=sharing>

The null hypothesis is that the mean of the stocks between the two days are equal and the alternate hypothesis is that the mean of the stocks between the two days are not equal

- What is the t- statistic of the sample?

`t-value = x1 sample mean – x2 sample mean / S.E`

`= 24.667 – 24.312 / 0.9544 = 0.3719`

- What is the p value of the sample?

`From the link `<https://www.graphpad.com/quickcalcs/pValue1/>`, we can calculate the p value and df = 0.7186`

- At alpha level of 0.1 what is the t-critical?

`If alpha level is 0.1, it means a CI of 90% so the t-critical is 1.833`

- Construct 90% CI for the difference

`CI is given as x difference mean ± tcritical * S.E sample`

`= 0.355 ± 1.833 * 0.9544`

`= -1.3945 and 2.1044`

`As the the t-value is positive, so the average of June stock prices is greater than  May, but not significantly higher. (as the t-crtitical lies between -1.833 to 1.833 `

---

**Problem 6**: Guinea pig treatment (In dependant sample t- test)
The google spreadsheet here <https://docs.google.com/spreadsheets/d/1ni-2TeLpl2RH8tDzt2tXNvyJ4KkaIJ8FHRL17KoKMh8/edit?usp=sharing> contains the tooth lengths of 37 guinea pigs who were given either 1 mg or 2 mg of Vitamin C. The goal is to see whether changing the Vitamin C dosage causes a difference (in either direction) in the guinea pigs’ tooth growth.

- How many is the degree of freedom?

`The sample nx = 20 and ny = 17; so df = nx + ny – 2 = 20 + 17 – 2 = 35`

- What is the t statistic?

`Pooled variance Sp^2 = (SSx + SSy)/(dfx + dfy)`

`Standard Error = Sxy = √(Sp^2/nx + Sp^2/ny)`

`t-value = Mean X – Mean Y / S.E = 19.735 – 26.105 / 1.407 = -4.527`

- What is the p value

`From the link `<https://www.graphpad.com/quickcalcs/pValue1/>`, we can calculate the p value and df = 0.0001`

- At alpha of 0.01 what is t-critical?

`If alpha level is 0.01, it means a CI of 99% so the t-critical is 2.75`

- Construct 99% C.I for the difference between two groups

`CI is given as  difference mean of a and y ± t-critical * S.E `

`= -6.37 ± 2.75 * 1.407`

`= 2.52603 and 10.216`

`As the the t-value is negative and not between the t-critical there is a significant difference between the two doses and the average tooth length of pigs with 1 mg dose is significantly lower than pigs with 2mg dose.`