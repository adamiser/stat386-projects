---
layout: post
title:  "Scraping Population Data"
date:   2022-10-18
author: Adam Simpson
description: Gathering population data using a public API.
image: https://raw.githubusercontent.com/adamiser/stat386-projects/main/assets/images/un.jpg
---

I'm sure you have heard about how "adults don't have as many kids as they used to". You've heard about countries who experience "zero population growth", whose population will decline by 20% over the next 100 years. And your grandparents have probably confronted you before, saying something like, "back in my day, a family with 6 kids was small. Now you have 1 kid and you're done!"

No, this isn't an argument for having more or less kids, don't worry. Rather, these statements pose the question, is the world population really declining? What do things like fertility rate, life expectancy, and population density look like throughout the world? We should be careful not to jump to conclusions before looking at the data. And there is plenty of data involving population growth.

> Is the world population really declining? How do you know?

This post examines gathering population data from [the United Nations Data Portal](https://population.un.org/dataportal/home) using their public API. *I wish to emphasize that this API is public, and the United Nations encourages its use*. The UN has data for dozens of variables involving population growth in countries around the world. This data, as I mentioned before, is public and quite easy to access. Many insights can be gleaned from examining these variables. Perhaps we can prove through analysis if zero population growth really is where we're heading.

### The API

The API is easily accessed. The UN website even includes a [helpful walkthrough](https://population.un.org/dataportal/about/dataapi) on how to use the API. My work will use and expound on examples found in this walkthrough.

> Remember this API is public and the United Nations encourages its use.

We start by exploring the base URL for the API:

```
import requests as re
base_url = "https://population.un.org/dataportalapi/api/v1"
r = re.get(url)
r.status_code
```

*If we did it right, we ought to receive a '200' from the line 'r.status_code', indicating a successful link to the URL*

The walkthrough then discusses *indicators* and *locations*. These are how we access the variables from the API. The UN provides information, as stated above, on dozens of variables for many countries. We access these different things using indicator and location codes. For example, the indicator code for *fertility rate* is 19, and the location code for *Australia* is 36. We can then add this relative_path to the base_url:

```
relative_path = "/data/indicators/19/locations/36"
url = base_url + relative_path

import pandas as pd
j = r.json()
df = pd.json_normalize(j['data'])
```

The API divides the data into pages. Thus, the code above will only return the first page of data. We can gather all data for Australia by doing this:

```
while j['nextPage'] != None:
    response = re.get(j['nextPage'])
    j = response.json()
    df_temp = pd.json_normalize(j['data'])
    df = pd.concat([df, df_temp])
```

Now *df* will contain all information about *fertility rate* for Australia.

Note: Identifying variables and locations and their respective codes isn't completely straightforward. The [walkthrough](https://population.un.org/dataportal/about/dataapi) contains information about *topics*, helpful divisions of indicators. You can use these topics to find variables and indicator codes. To find location codes, I used the code below, substituting whichever country I want:

```
url = "https://population.un.org/dataportalapi/api/v1/locations"
df_locations[df_locations['name'] == "Australia"]
```

### Creating a dataset

Now we can create a dataset. We first construct a function as follows:

```
def callAPI(relative_path) -> pd.DataFrame:

    base_url = "https://population.un.org/dataportalapi/api/v1"
    url = base_url + relative_path
    r = re.get(url)
    r.status_code

    j = r.json()
    
    df = pd.json_normalize(j['data'])
    while j['nextPage'] != None:
        response = re.get(j['nextPage'])
        j = response.json()
        df_temp = pd.json_normalize(j['data'])
        #df = df.append(df_temp)
        df = pd.concat([df, df_temp])
    
    return(df)
```

*Note that all we are doing is combining the steps above!*

We can now call this function and begin generating datasets for various indicators. I decided that an effective and simple dataset for analysis ought to include 3-5 variables for 5-8 countries. I have decided to examine 5 variables:

1. Fertility Rate
2. Infant Morality Rate
3. Life Expectancy at Birth
4. Population Density
5. Crude Birth Rate

for 6 countries:

1. USA
2. Australia
3. New Zealand
4. Samoa
5. Fiji
6. Tonga

We can then examine population growth and other factors for our country and countries in Oceania. 

First, we must identify the indicator codes and location codes for these variables and countries. Once that is done, we can gather data frames for each variable separately. I show the example of creating a dataset for the variable *fertility rate*.

```
fert = callAPI("/data/indicators/19/locations/840,36,554,882,776,242/start/1980/end/2020")
fert = fert[fert['variantLabel'] == 'Median']
fert = fert.rename(columns={'value':'Fertility_rate'})
fert = fert.drop(fert.columns[[2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18,
                       19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]], axis = 1)
```

Note a few things:
1. We use our *callAPI* function, passing our *relative_path* which will then combine with our base_url to create a successful call to the API.
2. The *relative_path* contains a starting year and an ending year.
3. We clean the dataset after examination (*note the 'variantLabel' == 'Median' line).
4. We remove almost all columns, leaving only the merging variables and the variable value itself.

Having these separate datasets, we can merge on common characteristics. I show an example of merging with the *inf_mort* dataframe:

```
df = pd.merge(fert, inf_mort, on=["locationId", "location", "timeLabel"])
```

You can check out my [GitHub Repo](https://github.com/adamiser/WebScraping) to see my full code and how I created the final dataset.

In my next post, we will explore this dataset. We will use Bayesian regression techniques to predict and analyze the trends of population growth. Do you think we will find evidence of zero population growth? How will America compare to the island nations of Oceania? What countries and variables would *you* analyze to search for evidence of zero population growth? 

