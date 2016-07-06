
![turnstile2](https://farm8.staticflickr.com/7541/15217776574_846f1afdc4_b.jpg)

## Mission Impossible. MTA turnstile data.

My first week at a data science bootcamp can be compared only with the first swimming experience. Racing against time, exhausting resources and patience while working on project Benson, was not my first choice of spending the first week at Metis. I am used to face predictable difficulties  - the choice of prior distributin or MCMC sampler, amortization schedule for certain mortgage backed securities or hedging products for credit derivatives. This time I simply lacked coding tools to solve the problem and did not have time to focus on modeling issues.

The goal was to help a prospective Women Tech Women Yes organization to raise money and awareness for their cause by analyzing MTA turnstile data and to make suggestion on where and when to place canvassers to hand out flyers for upcoming fundrasing event.

The data was easy to find and download: http://web.mta.info/developers/turnstile.html. As a beginner student with quite modest knowledge of Python, I tried to take small steps and work my way through with the group of four other classmates. Most of my time I focused on matching station names in MTA data to geographical coordinates, which turned out to be an ardious task.

It turns out there are a lot of databases that match stations to coordinates, for example https://data.cityofnewyork.us/Transportation/Subway-Entrances/drex-xx56. The question is how many of those can you match to station names in MTA turnstile data? 

MTA exploited non-cosistent abbreviations for streets, avenues and parkways: WHITEHALL S-FRY, ATL AV-BARCLAY, 5 AV/59 ST, ORCHARD BEACH, EASTN PKWY-MUSM, 82 ST-JACKSON H. To add to my frustration some stations like ORCHARD BEACH, line 6, or path stations are not in any database.

There were about 500 stations, and regex was something still to perfect, so I resorted to searching for a better dataset to match coordinates. The next data I found - http://web.mta.info/developers/sbwy_entrance.html from MTA website itself was very promising. Unfortunately, it turns out MTA is not very consistent with its own dictionaries, I still could not match more than a hundred stations. Luckily, I stumbled upon a thread in a google group for MTA developers from 2010 https://groups.google.com/forum/#!topic/mtadeveloperresources/rUnkyRQDN3s, which gave a link to an open source spreadsheet that provided the closest possible match I could find https://docs.google.com/spreadsheets/d/10sz0xWODQ02Kemx6ovS0NLQ_gA0YV9YQtdD7uiCcyjI/edit?hl=en&authkey=CMTzrvwE#gid=4 Still using regex, I was able to get down to about 60 station names that still needed somework.

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>MTA Station</th>
      <th>stop_id</th>
      <th>GTFS Station</th>
      <th>stop_lat</th>
      <th>stop_lon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>396</th>
      <td>5 AV/59 ST</td>
      <td>R13</td>
      <td>5TH AVENUE - 59 ST</td>
      <td>40.764811</td>
      <td>-73.973347</td>
    </tr>
    <tr>
      <th>984</th>
      <td>57 ST-7 AV</td>
      <td>R14</td>
      <td>57TH STREET / 7TH AVENUE (MIDTOWN)</td>
      <td>40.764664</td>
      <td>-73.980658</td>
    </tr>
    <tr>
      <th>1564</th>
      <td>49 ST</td>
      <td>R15</td>
      <td>49TH STREET</td>
      <td>40.759901</td>
      <td>-73.984139</td>
    </tr>
    <tr>
      <th>2492</th>
      <td>TIMES SQ-42 ST</td>
      <td>902</td>
      <td>TIMES  SQ. SHUTTLE - 42 ST</td>
      <td>40.755983</td>
      <td>-73.986229</td>
    </tr>
    <tr>
      <th>2884</th>
      <td>34 ST-HERALD SQ</td>
      <td>D17</td>
      <td>34TH STREET - HERALD SQ</td>
      <td>40.749719</td>
      <td>-73.987823</td>
    </tr>
    <tr>
      <th>3514</th>
      <td>28 ST</td>
      <td>R18</td>
      <td>28TH STREET</td>
      <td>40.745494</td>
      <td>-73.988691</td>
    </tr>
    <tr>
      <th>3870</th>
      <td>23 ST</td>
      <td>A30</td>
      <td>23RD STREET</td>
      <td>40.745906</td>
      <td>-73.998041</td>
    </tr>
    <tr>
      <th>4458</th>
      <td>14 ST-UNION SQ</td>
      <td>635</td>
      <td>14TH STREET - LEXINGTON - UNION SQ</td>
      <td>40.734673</td>
      <td>-73.989951</td>
    </tr>
  </tbody>
</table>
</div>


```

    Could not match  CITY HALL
    Could not match  KINGS HWY
    Could not match  BAY PKWY
    Could not match  ROCKAWAY BLVD
    Could not match  FAR ROCKAWAY
    Could not match  182-183 STS
    ould not match  GUN HILL RD
    Could not match  225 ST
    Could not match  PARKCHESTER
    Could not match  BEVERLY RD
    Could not match  FLATBUSH AV-B.C

```

Some of these I was able to match by introducing a dictionary of commonly used abreviations.

```python
common_words = {'RD':'ROAD', 'HWY' : 'HIGHWAY', 'ST':'STREET', 'STS': ' STREET',
                'PKWY':'PARKWAY','HTS':'HEIGHTS', 'HW':'HIGHWAY', 'SQ':'SQUARE', 
                'AV':'AVENUE', 'BLVD' : 'BOULEVARD', 'SNDVW':'SOUNDVIEW', 'B.C':'BROOKLYN COLLEGE'}

```

The rest, like PATH stations: Exchange Place, Twenty Third St, RIT Roosevelt stations or aforementioned ORCHARD BEACH, I had to work out manually.



Lauren Oldja suggested the easiest and most descriptive way to represent turnstile data using cartodb.com. For this I aggregated data for each station for different lines and control units that recorded exits and entries. Values for exits and entries had to be preprocessed, since the counters were never reset. Students were suggested to use the first record of the day, 1:00am, for most days as a benchmark to recalculate the absolute data.

Cartodb has an amazing tool for projecting your static or  continuous data on the map. Below are some examples. This is the first view on solving the problem and finding the target stations.

![heat_map](<iframe width="100%" height="520" frameborder="0" src="https://jpiterbarg.cartodb.com/viz/5d964966-43a9-11e6-8279-0ea31932ec1d/embed_map" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>?raw=true)

Later my group came out with the recipe for the clients to calculate an index, for each station which would be used by clients in order to make decisions and choose "the most important" stations. This criteria would use entries or exits values, number of colleges within a quarter mile radius and number of tech companies within some radius.

$$c =w_MTA * N_MTA + w_corp * N_corp + w_edu * N_edu$$


The weights in the formular can be changed and customized per client. The most important question is how to normalize the data. My group suggested to consider morning, afternoon, and evening shifts for the day, and analyze them separately. One of suggestion was to use an absolute maximum acrooss all stations and days of the week per time shift. My concern is whether normalization by maximum will produce non-robust results and potentially pick up outlier values. In this case, I suggest in the future to consider trimmed data for exits and entries.
