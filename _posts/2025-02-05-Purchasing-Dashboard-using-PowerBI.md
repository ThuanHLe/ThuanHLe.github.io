---
layout: post
title: Purchasing Dashboard Using PowerBI
image: "/posts/purchasing-cover-img.png"
tags: [PowerBI, Data Viz]
---

In this project we create a PowerBI dashboard to ensure smooth operation for supply chain department.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Business Question](#overview-businessquestion)
- [01. Design Thinking](#design-thinking)
- [02. Data Visualization](#data-visualization)
- [03. Insights & Detail Analysis](#detail-analysis)
- [04. Analysing The Results](#chi-square-results)
- [05. Discussion](#discussion)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

The Purchasing team will be responsible for procuring goods, raw materials, and semi-finished products to support production.
The leadership expects that goods are ordered in sufficient quantities for sales, on time, and at optimized costs.

### Business Question <a name="overview-businessquestion"></a>

Create an overview dashboard for stakeholders that displays the fulfillment rate & COGS (cost of goods sold) of products and helps identify which product or category needs further review.

Develop solutions to improve the situation and optimize costs.

___

# Design Thinking  <a name="design-thinking "></a>

There are 5 steps of design thinking:
<br>
#### Step 1: Empathize

![alt text](/img/posts/empathize-img.png "PowerBI – Empathize Step")

<br>
#### Step 2: Define

![alt text](/img/posts/define-img.png "PowerBI – Define Step")

<br>
#### Step 3: Ideate

![alt text](/img/posts/ideate-img.png "PowerBI – Ideate Step")

<br>
#### Step 4 & 5: Prototype & Review

![alt text](/img/posts/pro-review-img.png "PowerBI – Prototype & Review Step")

___

# Data Visualization  <a name="data-visualization"></a>
<br>
#### 1. Fulfillment Overview

![alt text](/img/posts/powerbi-viz-1.png "PowerBI – Fulfillment overview")

<br>
#### 2. Fulfillment Drillthrough

![alt text](/img/posts/powerbi-viz-2.png "PowerBI – Fulfillment drillthrough")

<br>
#### 3. COGS Overview

![alt text](/img/posts/powerbi-viz-3.png "PowerBI – COGS overview")

<br>
#### 4. COGS Drillthrough

![alt text](/img/posts/powerbi-viz-4.png "PowerBI – COGS drillthrough")

___

<br>
# Insights & Detail Analysis <a name="detail-analysis"></a>

<br>

<iframe src="https://1drv.ms/p/c/4fa8ee64d7ee142a/IQRvjypHb7kXTatWvKtF1iFRAYCxfzhd5XDKwzhfyo12vW0?em=2&amp;wdAr=1.7777777777777777" width="476px" height="288px" frameborder="0">This is an embedded <a target="_blank" href="https://office.com">Microsoft Office</a> presentation, powered by <a target="_blank" href="https://office.com/webapps">Office</a>.</iframe>

___

<br>
# Analysing The Results <a name="chi-square-results"></a>

At this point we have everything we need to understand the results of our Chi-Square test - and just from the results above we can see that, since our resulting p-value of **0.16** is *greater* than our Acceptance Criteria of 0.05 then we will _retain_ the Null Hypothesis and conclude that there is no significant difference between the signup rates of Mailer 1 and Mailer 2.

We can make the same conclusion based upon our resulting Chi-Square statistic of **1.94** being _lower_ than our Critical Value of **3.84**

To make this script more dynamic, we can create code to automatically interpret the results and explain the outcome to us...

```python

# print the results (based upon p-value)
if p_value <= acceptance_criteria:
    print(f"As our p-value of {p_value} is lower than our acceptance_criteria of {acceptance_criteria} - we reject the null hypothesis, and conclude that: {alternate_hypothesis}")
else:
    print(f"As our p-value of {p_value} is higher than our acceptance_criteria of {acceptance_criteria} - we retain the null hypothesis, and conclude that: {null_hypothesis}")

>> As our p-value of 0.16351 is higher than our acceptance_criteria of 0.05 - we retain the null hypothesis, and conclude that: There is no relationship between mailer type and signup rate.  They are independent


# print the results (based upon Chi Square Statistic)
if chi2_statistic >= critical_value:
    print(f"As our chi-square statistic of {chi2_statistic} is higher than our critical value of {critical_value} - we reject the null hypothesis, and conclude that: {alternate_hypothesis}")
else:
    print(f"As our chi-square statistic of {chi2_statistic} is lower than our critical value of {critical_value} - we retain the null hypothesis, and conclude that: {null_hypothesis}")
    
>> As our chi-square statistic of 1.9414 is lower than our critical value of 3.841458820694124 - we retain the null hypothesis, and conclude that: There is no relationship between mailer type and signup rate.  They are independent

```
<br>
As we can see from the outputs of these print statements, we do indeed retain the null hypothesis.  We could not find enough evidence that the signup rates for Mailer 1 and Mailer 2 were different - and thus conclude that there was no significant difference.

___

<br>
# Discussion <a name="discussion"></a>

While we saw that the higher cost Mailer 2 had a higher signup rate (37.8%) than the lower cost Mailer 1 (32.8%) it appears that this difference is not significant, at least at our Acceptance Criteria of 0.05.

Without running this Hypothesis Test, the client may have concluded that they should always look to go with higher cost mailers - and from what we've seen in this test, that may not be a great decision.  It would result in them spending more, but not *necessarily* gaining any extra revenue as a result

Our results here also do not say that there *definitely isn't a difference between the two mailers* - we are only advising that we should not make any rigid conclusions *at this point*.  

Running more A/B Tests like this, gathering more data, and then re-running this test may provide us, and the client more insight!
