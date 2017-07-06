---
layout: post
title: Identifying NYC's busiest subway stations
---

The first project and set of assignments involved analyzing the MTA subway station data. This data can be found [here](http://web.mta.info/developers/turnstile.html) where each file contains one week of data for every subway station turnstile in New York City's MTA.

#### Initital approach
Let's load up the data and take a peak at what we have:
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>C/A</th>
      <th>UNIT</th>
      <th>SCP</th>
      <th>STATION</th>
      <th>LINENAME</th>
      <th>DIVISION</th>
      <th>DATE</th>
      <th>TIME</th>
      <th>DESC</th>
      <th>ENTRIES</th>
      <th>EXITS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>06/17/2017</td>
      <td>00:00:00</td>
      <td>REGULAR</td>
      <td>6224816</td>
      <td>2107317</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>06/17/2017</td>
      <td>04:00:00</td>
      <td>REGULAR</td>
      <td>6224850</td>
      <td>2107322</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>06/17/2017</td>
      <td>08:00:00</td>
      <td>REGULAR</td>
      <td>6224885</td>
      <td>2107352</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>06/17/2017</td>
      <td>12:00:00</td>
      <td>REGULAR</td>
      <td>6225005</td>
      <td>2107452</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>06/17/2017</td>
      <td>16:00:00</td>
      <td>REGULAR</td>
      <td>6225248</td>
      <td>2107513</td>
    </tr>
  </tbody>
</table>
</div>

It looks like we have a cumulative count of turnstile entries in the `ENTRIES` column that increases over time. To get an idea how many people are passing through these turnstiles, we'll need to take the difference between a row's value for `ENTRIES` and the previous row's value for `ENTRIES`. Once we've determined the amount of entries for a turnstile over time, we can calculate the total amount of entries for a period of time and use that to find the stations with the largest amount of entries for that time period. Simple enough right?

#### Initial results
Here are the top 20 stations for a given week after summing up all the entries for each turnstile in each station:

    125 ST           23            1929735724.000
    167 ST           BD            1771741344.000
    125 ST           1              635243569.000
    45 ST            R              285273927.000
    COURT SQ         7              117578766.000
    34 ST-PENN STA   ACE             69477181.000
    2 AV             F               67670166.000
    8 ST-NYU         NR              67654321.000
    ATL AV-BARCLAY   2345BDNQR       66392968.000
    CHAMBERS ST      JZ456           33849890.000
    36 ST            MR              16924947.000
    METROPOLITAN AV  M               16011842.000
    GRD CNTRL-42 ST  4567S            3628736.000
    34 ST-HERALD SQ  BDFMNQR          3552639.000
    TIMES SQ-42 ST   1237ACENQRS      2601016.000
    14 ST-UNION SQ   LNQR456          2303606.000
    THIRTY ST        1                2142620.000
    42 ST-PORT AUTH  ACENQRS1237      2114297.000
    86 ST            456              1987222.000
    59 ST COLUMBUS   ABCD1            1782208.000

The results appear to indicate that something is off with the entry count calculations. The highest counts are signifcantly higher than the rest and are clearly outliers. Additionally, the stations with the highest entry counts only serve 1 or 2 lines while `TIMES SQ-42 ST` serves 11 lines and it's entry count is drastically lower in comparison. Lets also look at the bottom 5:

    NASSAU ST        G         -50179175.000
    ELMHURST AV      MR        -53972268.000
    JAMAICA 179 ST   F        -116775999.000
    170 ST           BD       -150763646.000
    LORIMER ST       JM      -2130607704.000

There clearly shouldn't be any entry counts below 0, so we'll need to figure out what's causing this.