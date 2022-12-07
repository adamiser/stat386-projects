---
layout: post
title:  "Exploring Population Growth in Highly-Populated Nations"
date:   2022-12-05
author: Adam Simpson
description: A final story detailing the relationships between fertility rate and life expectancy in some of the world's most populous and high-growth nations.
image: https://raw.githubusercontent.com/adamiser/stat386-projects/main/assets/images/population.jpeg
---

### Motivation and Project Summary

Did you know that the world has reached 8 billion people?! Shocking! It's hard to imagine that many distinct human beings living on this planet. How did we reach this many people? Are we still growing? Where will we be in 20 years? 50 years? According to an article from Forbes about [world population trends](https://www.forbes.com/sites/roberthart/2022/11/14/worlds-population-reaches-8-billion-this-week-heres-what-you-need-to-know/), it seems that growth isn't stopping anytime soon. Despite what we hear about many first world nations reaching "zero population growth", the global population won't be declining anytime soon.

These thoughts inspired me to examine these trends firsthand. I gathered data from a United Nations public API. The UN provides data on a vast array of variables for all of its members, including fertility rate and life expectancy. The API contained plenty of documentation on how to access certain variables for whichever nation you desired. There were over 80 variables. For my analysis, I chose 5 to examine from 9 nations, though in the future I would love to look at more. I summarize my data collection [here](https://adamiser.github.io/stat386-projects/2022/10/18/webScraping.html), and my exploration of the data can be found [here](https://adamiser.github.io/stat386-projects/2022/11/18/eda.html). Of the variables I chose, fertility rate and life expectancy were the most interesting and had the greatest link to population growth in nations mentioned by the Forbes article. I found that I could summarize most of my findings in one graphic:

![Data Story](https://raw.githubusercontent.com/adamiser/stat386-projects/main/assets/images/datastory.png)

### The Story

I believe the message of this plot is clear: the 3 nations expected to surpass the United States in population by 2100 (Nigeria, Pakistan, and the Congo), despite experiencing a decline in fertility rate, still have the life expectancy and reproduction levels to grow in astronomical levels. No nations in the history of the world have experienced a combination of high life expectancy and fertility rates quite like these nations. First-world, highly populated nations such as Japan, China, and the United States only maintain population through migration: the sub-necessary reproduction levels of these nations leave them at high risk of zero population growth and even decline. This makes way for third-world and former third-world nations to easily rise in the world population rankings. While it is clearly true that as a whole people are not having as many children, we are still having enough to keep the population growing. Combined with longer life expectancies, the Earth is truly filling up! 

### Gratitude

Thank you for following my data story! It has been incredibly exciting to explore population data. I look forward to further exploration and even predictive modeling based on data from the public United Nations API. Check out my code in [my GitHub repo](https://github.com/adamiser/WebScraping) to see what I did. I would love to hear from you with feedback or suggestions! 

