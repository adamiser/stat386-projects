---
layout: post
title:  "Scraping Population Data"
date:   2022-10-18
author: Adam Simpson
description: Gathering population data using a public API.
image: https://raw.githubusercontent.com/adamiser/stat386-projects/main/assets/images/Lock.jpeg
---

I'm sure you have heard about how "adults don't have as many kids as they used to". You've heard about countries who experience "zero population growth", whose population will decline by 20% over the next 100 years. And your grandparents have probably confronted you before, saying something like, "back in my day, a family with 6 kids was small. Now you have 1 kid and you're done!"

No, this isn't an argument for having more or less kids, don't worry. Rather, these statements pose the question, is the world population really declining? What do things like fertility rate, life expectancy, and population density look like throughout the world? We should be careful not to jump to conclusions before looking at the data. And there is plenty of data involving population growth.

This post examines gathering population data from [the United Nations Data Portal](https://population.un.org/dataportal/home) using their public API. The UN has data for dozens of variables involving population growth in countries around the world. This data, as I mentioned before, is public and quite easy to access. Many insights can be gleaned from examining these variables. Perhaps we can prove through analysis if zero population growth really is where we're heading.

### The API

The API is easily accessed. The UN website even includes a [helpful walkthrough](https://population.un.org/dataportal/about/dataapi) on how to use the API. This post will use and expound on examples found on this website.

We start by exploring the base URL for the API:

```
import requests as re
base_url = "https://population.un.org/dataportalapi/api/v1"
r = re.get(url)
r.status_code
```

*If we did it right, we ought to receive a '200' from the line 'r.status_code', indicating a successful link to the URL*

The walkthrough then discusses *indicators* and *locations*. These are how we access the variables from the API. The UN provides information, as stated above, on dozens of variables for many countries. We access these different things using indicator and location codes. We can add to the base_url something like this:

```
relative_path = "/data/indicators/19/locations/36"
url = base_url + relative_path

import pandas as pd
j = r.json()
df = pd.json_normalize(j['data'])
```

This will give us the variable *fertility_rate* (indicator code = 19) for the country of Australia (country code = 36).

The API divides the data into pages. Thus, the code above will only return part of the desired data - one page of it in fact. We can gather all fertility data for Australia by doing this:

```
while j['nextPage'] != None:
    response = re.get(j['nextPage'])
    j = response.json()
    df_temp = pd.json_normalize(j['data'])
    df = pd.concat([df, df_temp])
```

Now, *df* will contain all information about *fertility rate* for Australia.

Note: Identifying which variables are available and what their indicator codes isn't completely straightforward. The [walkthrough](https://population.un.org/dataportal/about/dataapi) contains information about *topics*, helpful divisions of indicators. You can use these topics to find variables and indicator codes.

### Creating a dataset

Now we can create a dataset. We first construct a function as follows:
```
def callAPI(relative_path, topic_list = False) -> pd.DataFrame:

    base_url = "https://population.un.org/dataportalapi/api/v1"
    url = base_url + relative_path
    r = re.get(url)
    r.status_code

    j = r.json()
    
    if topic_list:
        df = pd.json_normalize(j, 'indicators')
        return(df)
    
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

First, we must identify the indicator codes and location codes for these variables and countries. Once that is done, we can gather data frames for each variable separately as follows:
```
# Fertility rate
# Only want variantLabel = Median
fert = callAPI("/data/indicators/19/locations/840,36,554,882,776,242/start/1980/end/2020")
fert = fert[fert['variantLabel'] == 'Median']
fert = fert.rename(columns={'value':'Fertility_rate'})
fert = fert.drop(fert.columns[[2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18,
                       19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]], axis = 1)

# Infant mortality rate 
# Only want sex = Both sexes
inf_mort = callAPI("/data/indicators/22/locations/840,36,554,882,776,242/start/1980/end/2020")
inf_mort = inf_mort[inf_mort['sex'] == 'Both sexes']
inf_mort = inf_mort.rename(columns={'value':'Inf_Mortality_rate'})
inf_mort = inf_mort.drop(inf_mort.columns[[2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18,
                       19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]], axis = 1)

# Life expectancy at birth 
# Only want sex = Both sexes
life = callAPI("/data/indicators/61/locations/840,36,554,882,776,242/start/1980/end/2020")
life = life[life['sex'] == 'Both sexes']
life = life.rename(columns={'value':'Life_expectancy'})
life = life.drop(life.columns[[2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18,
                       19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]], axis = 1)

# Population density 
pop_dens = callAPI("/data/indicators/54/locations/840,36,554,882,776,242/start/1980/end/2020")
pop_dens = pop_dens.rename(columns={'value':'Pop_density'})
pop_dens = pop_dens.drop(pop_dens.columns[[2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18,
                       19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]], axis = 1)

# Crude birth rate 
birth_rate = callAPI("/data/indicators/55/locations/840,36,554,882,776,242/start/1980/end/2020")
birth_rate = birth_rate.rename(columns={'value':'Birth_rate'})
birth_rate = birth_rate.drop(birth_rate.columns[[2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18,
                       19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30]], axis = 1)
```

Note a few things:
1. We use our *callAPI* function, passing our *relative_path* which will then combine with our base_url to create a successful call to the API.
2. Each *relative_path* contains a starting year and an ending year.
3. We clean each dataset respectively after examination (we do this in the *fert*, *inf_mort*, and *life* data frames). This leaves each with the same number of rows.
4. We remove almost all columns, leaving only the merging variables and the variable value itself.

Having these separate datasets, we can merge on common characteristics as follows:
```
df = pd.merge(fert, inf_mort, on=["locationId", "location", "timeLabel"])
df = pd.merge(df, life, on=["locationId", "location", "timeLabel"])
df = pd.merge(df, pop_dens, on=["locationId", "location", "timeLabel"])
df = pd.merge(df, birth_rate, on=["locationId", "location", "timeLabel"])
```

And we're done! *df* now contains our 5 variables for 6 countries. We can write *df* to a csv and save it for future analysis.

