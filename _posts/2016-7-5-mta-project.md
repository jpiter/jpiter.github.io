---
layout: post
title: Mission Impossible. MTA turnstile data.
---
![turnstile2](https://farm8.staticflickr.com/7541/15217776574_846f1afdc4_b.jpg)

My first week at a data science bootcamp can be compared only with the first swimming experience. Racing against time, exhausting resources and patience, working on project Benson was not my first choice of spending the first week at Metis. I am used to face predictable difficulties  - the choice of prior distributin or MCMC sampler, amortization schedule for mortgage securities, or hedging strategies for credit derivatives. This time I simply lacked coding skills and did not have time to focus on modeling issues.
{: style="text-align: justify"}

The goal  - to help a prospective Women Tech Women Yes organization to raise money and awareness for their cause by analyzing MTA turnstile data and to make suggestion on where and when to place canvassers to hand out flyers for upcoming fundrasing event.
{: style="text-align: justify"}

The data was easy to find and download: http://web.mta.info/developers/turnstile.html. As a beginner student with quite modest knowledge of Python, I tried to train my methods on data collected for the week of 06/11/2016. Most of the time I focused on matching station names in MTA data to geographical coordinates, which turned out to be an ardious task.
{: style="text-align: justify"}

It turns out there are a lot of databases that match stations to coordinates, for example here is the data from [City of New York](https://data.cityofnewyork.us/Transportation/Subway-Entrances/drex-xx56). The question is how many of those can you match to station names in MTA turnstile data? 
{: style="text-align: justify"}

MTA exploited non-cosistent abbreviations for streets, avenues and parkways: WHITEHALL S-FRY, ATL AV-BARCLAY, 5 AV/59 ST, EASTN PKWY-MUSM. To add to my frustration some stations like ORCHARD BEACH, line 6, or PATH stations are not in any database.
{: style="text-align: justify"}

There were about 500 stations, and regex was something still to perfect, so I resorted to searching for a better dataset to match coordinates. The next [data]([http://web.mta.info/developers/sbwy_entrance.html) I found from MTA website itself was very promising. Unfortunately, it turns out MTA is not very consistent with its own dictionaries and I still could not match more than a hundred stations. Luckily, I stumbled upon a thread in a [google MTA developers group](https://groups.google.com/forum/#!topic/mtadeveloperresources/rUnkyRQDN3s) from 2010 which gave a link to an [open spreadsheet](https://docs.google.com/spreadsheets/d/10sz0xWODQ02Kemx6ovS0NLQ_gA0YV9YQtdD7uiCcyjI/edit?hl=en&authkey=CMTzrvwE#gid=4) that provided the closest possible match I could find. With help of regex I was able to get most of them. 
{: style="text-align: justify"}


| MTA Station |  GTFS Station  | Latitude | Longitude |
|:--------|:---------:|--------:|--------:|
| 5 AV/59      |  5TH AVENUE - 59 ST|  40.764811  |  -73.973347  |
| 57 ST-7 AV     |  57TH STREET / 7TH AVENUE (MIDTOWN)  |  40.764664  |  -73.980658   |
| TIMES SQ-42 ST |TIMES  SQ. SHUTTLE - 42 ST|  40.755983  |  -73.986229  |
| 23 ST     | 23RD STREET | 40.745906 |  -73.998041  |
| 14 ST-UNION SQ | 14TH STREET - LEXINGTON - UNION SQ |  40.734673  |  -73.989951 |
|=====
{: rules="groups"}


Still I had other 50 station names that needed to be matched.

```

    Could not match  KINGS HWY
    Could not match  BAY PKWY
    Could not match  182-183 STS
    ould not match  GUN HILL RD
    Could not match  FLATBUSH AV-B.C

```

Some of these I was able to match by introducing a dictionary of commonly used abreviations.


```python
common_words = {'RD':'ROAD', 'HWY' : 'HIGHWAY', 'ST':'STREET',
                'STS': ' STREET','PKWY':'PARKWAY','HTS':'HEIGHTS',
                'HW':'HIGHWAY', 'SQ':'SQUARE','AV':'AVENUE',
                'BLVD' : 'BOULEVARD', 'SNDVW':'SOUNDVIEW', 
                'B.C':'BROOKLYN COLLEGE'}

```

The rest, like PATH stations: Exchange Place, Twenty Third St, RIT Roosevelt stations or aforementioned ORCHARD BEACH, I had to work out manually.
{: style="text-align: justify"}

[Lauren Oldja](http://laurenoldja.net/) suggested the easiest and most descriptive way to represent turnstile data using cartodb.com. For this I aggregated data for each station for different lines and control units that recorded exits and entries. Values for exits and entries had to be preprocessed, since the counters were never reset. Students were suggested to use the first record of the day, 1:00am, for most days as a benchmark to recalculate the absolute values.
{: style="text-align: justify"}

Cartodb has an amazing tool for projecting your static or continuous data on the map. Below are some examples. This is the first view on solving the problem and finding the target stations. You can look at the heat map changes over time as stations accumulate number of entries for the day of 06/18/2016.
{: style="text-align: justify"}

<iframe width="100%" height="520" frameborder="0" src="https://jpiterbarg.cartodb.com/viz/5d964966-43a9-11e6-8279-0ea31932ec1d/embed_map" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>


And a static view for a change for the number of entries through turnstile for one week of data.


![cartodbmap2](https://github.com/jpiter/jpiter.github.io/blob/master/_posts/mtacolorplex.png?raw=true)


Later my group came out with the recipe for the clients to calculate an index, for each station which would be used by clients in order to make decisions and choose "the most important" stations. This criteria would use entries or exits values, number of colleges within a quarter mile radius and number of tech companies within certain proximity.
{: style="text-align: justify"}

$$ 
c = w_{MTA} \times N_{MTA} + w_{corp} \times N_{corp} + w_edu \times N_edu 
$$

The weights in the formular can be changed and customized per client. My group suggested to consider morning, afternoon, and evening shifts for the day, and analyze them separately. The most important question is how to normalize the data. One of suggestion was to use an absolute maximum acrooss all stations and days of the week per time shift. Will normalization by maximum produce non-robust results and potentially pick up outlier values? Can we used a trimmed data? Can we use a z-score normalization? All these questions are valid, but are delayed for the future.
{: style="text-align: justify"}

