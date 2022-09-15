---
title: Predicting Madison, WI Weather with Machine Learning
subtitle: Code, Inspired by DataQuest
---

## Read and Explore Data


```python
import pandas as pd

weather = pd.read_csv('madison-weather.csv', index_col='DATE')
```


```python
# we can view the column names
weather.columns
```




    Index(['STATION', 'NAME', 'ACMH', 'ACSH', 'AWND', 'FMTM', 'PGTM', 'PRCP',
           'PSUN', 'SNOW', 'SNWD', 'TAVG', 'TMAX', 'TMIN', 'TSUN', 'WDF1', 'WDF2',
           'WDF5', 'WDFG', 'WDFM', 'WESD', 'WSF1', 'WSF2', 'WSF5', 'WSFG', 'WSFM',
           'WT01', 'WT02', 'WT03', 'WT04', 'WT05', 'WT06', 'WT07', 'WT08', 'WT09',
           'WT10', 'WT11', 'WT13', 'WT14', 'WT15', 'WT16', 'WT17', 'WT18', 'WT19',
           'WT21', 'WT22', 'WV03'],
          dtype='object')




```python
# we can view the data from August 1, 2022 to August 5, 2022 using 'loc' (label-based indexing)
weather.loc['2022-08-01':'2022-08-05']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>STATION</th>
      <th>NAME</th>
      <th>ACMH</th>
      <th>ACSH</th>
      <th>AWND</th>
      <th>FMTM</th>
      <th>PGTM</th>
      <th>PRCP</th>
      <th>PSUN</th>
      <th>SNOW</th>
      <th>...</th>
      <th>WT13</th>
      <th>WT14</th>
      <th>WT15</th>
      <th>WT16</th>
      <th>WT17</th>
      <th>WT18</th>
      <th>WT19</th>
      <th>WT21</th>
      <th>WT22</th>
      <th>WV03</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-08-01</th>
      <td>USW00014837</td>
      <td>MADISON DANE CO REGIONAL AIRPORT, WI US</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>6.49</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.35</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2022-08-02</th>
      <td>USW00014837</td>
      <td>MADISON DANE CO REGIONAL AIRPORT, WI US</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.74</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2022-08-03</th>
      <td>USW00014837</td>
      <td>MADISON DANE CO REGIONAL AIRPORT, WI US</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5.59</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.53</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2022-08-04</th>
      <td>USW00014837</td>
      <td>MADISON DANE CO REGIONAL AIRPORT, WI US</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>6.26</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2022-08-05</th>
      <td>USW00014837</td>
      <td>MADISON DANE CO REGIONAL AIRPORT, WI US</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4.92</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 47 columns</p>
</div>



## Preparing Data

Notice that there are many missing values, which are not useful. According to the [documentation](https://www.ncei.noaa.gov/data/daily-summaries/doc/GHCND_documentation.pdf), there are five core values: PRCP (precipitation, inches), SNOW (snowfall, inches), SNWD (snow depth, inches), TMAX (maximum temperature, Fahrenheit), TMIN (minimum temperature, Fahrenheit).


```python
# we can return the percentage of missing values for each column
weather.apply(pd.isnull).sum()/weather.shape[0]
```




    STATION    0.000000
    NAME       0.000000
    ACMH       0.623449
    ACSH       0.623284
    AWND       0.533569
    FMTM       0.665996
    PGTM       0.544527
    PRCP       0.000000
    PSUN       0.770993
    SNOW       0.000891
    SNWD       0.002212
    TAVG       0.797861
    TMAX       0.000000
    TMIN       0.000000
    TSUN       0.596580
    WDF1       0.964418
    WDF2       0.681179
    WDF5       0.683226
    WDFG       0.704383
    WDFM       0.718544
    WESD       0.725244
    WSF1       0.964418
    WSF2       0.681179
    WSF5       0.683193
    WSFG       0.704251
    WSFM       0.718544
    WT01       0.598891
    WT02       0.958641
    WT03       0.903552
    WT04       0.982473
    WT05       0.984684
    WT06       0.975079
    WT07       0.992969
    WT08       0.778354
    WT09       0.983826
    WT10       0.999868
    WT11       0.998713
    WT13       0.899261
    WT14       0.955440
    WT15       0.995214
    WT16       0.699927
    WT17       0.996864
    WT18       0.844501
    WT19       0.999670
    WT21       0.989173
    WT22       0.995643
    WV03       0.999604
    dtype: float64



### Extract Core Values


```python
# we copy the core values into a smaller dataframe, and we rename the columns to be more suggestive
core_weather = weather[['PRCP', 'SNOW', 'SNWD', 'TMAX', 'TMIN']].copy()
core_weather.columns = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min']
```


```python
core_weather
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1939-10-01</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>67</td>
      <td>33</td>
    </tr>
    <tr>
      <th>1939-10-02</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1939-10-03</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>74</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1939-10-04</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>81</td>
      <td>51</td>
    </tr>
    <tr>
      <th>1939-10-05</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>56</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>78</td>
      <td>53</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>83</td>
      <td>53</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>60</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>61</td>
    </tr>
    <tr>
      <th>2022-09-10</th>
      <td>0.22</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>75</td>
      <td>58</td>
    </tr>
  </tbody>
</table>
<p>30296 rows × 5 columns</p>
</div>




```python
# again, we return the percentage of missing values for each column in our new dataframe
core_weather.apply(pd.isnull).sum()/core_weather.shape[0]
```




    precip        0.000000
    snowfall      0.000891
    snow_depth    0.002212
    temp_max      0.000000
    temp_min      0.000000
    dtype: float64



We still have missing values, but the percentage of missing values for each column is very small. So, we'll keep each column, though we still have to deal with the missing values; however, we only have to worry about snowfall and snow depth.

#### Snowfall

Let's return the rows for which the 'snowfall' column has missing values:


```python
core_weather[pd.isnull(core_weather['snowfall'])]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1996-05-02</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>63</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-03</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>56</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1996-05-04</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>60</td>
      <td>39</td>
    </tr>
    <tr>
      <th>1996-05-05</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51</td>
      <td>42</td>
    </tr>
    <tr>
      <th>1996-05-06</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>54</td>
      <td>39</td>
    </tr>
    <tr>
      <th>1996-05-09</th>
      <td>0.94</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>59</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1996-05-11</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>55</td>
      <td>36</td>
    </tr>
    <tr>
      <th>1996-05-12</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>55</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-13</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>59</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1996-05-15</th>
      <td>0.13</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>54</td>
      <td>45</td>
    </tr>
    <tr>
      <th>1996-05-16</th>
      <td>0.14</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>62</td>
      <td>51</td>
    </tr>
    <tr>
      <th>1996-05-22</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>77</td>
      <td>47</td>
    </tr>
    <tr>
      <th>1996-05-23</th>
      <td>0.22</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>61</td>
      <td>49</td>
    </tr>
    <tr>
      <th>1996-05-24</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>60</td>
      <td>44</td>
    </tr>
    <tr>
      <th>1996-05-25</th>
      <td>0.24</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>57</td>
      <td>44</td>
    </tr>
    <tr>
      <th>1996-05-26</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>59</td>
      <td>47</td>
    </tr>
    <tr>
      <th>1996-05-27</th>
      <td>0.08</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>60</td>
      <td>45</td>
    </tr>
    <tr>
      <th>1996-05-28</th>
      <td>0.10</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>64</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1996-05-29</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>69</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1996-05-30</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>68</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-31</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>73</td>
      <td>37</td>
    </tr>
    <tr>
      <th>1996-10-06</th>
      <td>0.30</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>73</td>
      <td>53</td>
    </tr>
    <tr>
      <th>1997-03-19</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>44</td>
      <td>19</td>
    </tr>
    <tr>
      <th>1997-03-20</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>54</td>
      <td>27</td>
    </tr>
    <tr>
      <th>1997-03-27</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>73</td>
      <td>34</td>
    </tr>
    <tr>
      <th>1997-03-28</th>
      <td>0.16</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>57</td>
      <td>44</td>
    </tr>
    <tr>
      <th>1999-02-13</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>33</td>
      <td>15</td>
    </tr>
  </tbody>
</table>
</div>



When we inspect the instances of missing values for the 'snowfall' column, we find that the missing values are clustered according to month and year, otherwise the missing values are isolated instances. We don't know why these values are missing, but we can return all rows for a specific month and year to observe the surrounding data points. Let's look at May 1996 (which has the most missing values):


```python
core_weather.loc['1996-05-01':'1996-05-31']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1996-05-01</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>56</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-02</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>63</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-03</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>56</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1996-05-04</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>60</td>
      <td>39</td>
    </tr>
    <tr>
      <th>1996-05-05</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51</td>
      <td>42</td>
    </tr>
    <tr>
      <th>1996-05-06</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>54</td>
      <td>39</td>
    </tr>
    <tr>
      <th>1996-05-07</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>57</td>
      <td>42</td>
    </tr>
    <tr>
      <th>1996-05-08</th>
      <td>0.01</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>52</td>
      <td>43</td>
    </tr>
    <tr>
      <th>1996-05-09</th>
      <td>0.94</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>59</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1996-05-10</th>
      <td>0.50</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>52</td>
      <td>41</td>
    </tr>
    <tr>
      <th>1996-05-11</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>55</td>
      <td>36</td>
    </tr>
    <tr>
      <th>1996-05-12</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>55</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-13</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>59</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1996-05-14</th>
      <td>0.08</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>52</td>
      <td>41</td>
    </tr>
    <tr>
      <th>1996-05-15</th>
      <td>0.13</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>54</td>
      <td>45</td>
    </tr>
    <tr>
      <th>1996-05-16</th>
      <td>0.14</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>62</td>
      <td>51</td>
    </tr>
    <tr>
      <th>1996-05-17</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>85</td>
      <td>53</td>
    </tr>
    <tr>
      <th>1996-05-18</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>86</td>
      <td>70</td>
    </tr>
    <tr>
      <th>1996-05-19</th>
      <td>0.06</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>84</td>
      <td>68</td>
    </tr>
    <tr>
      <th>1996-05-20</th>
      <td>0.45</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>56</td>
    </tr>
    <tr>
      <th>1996-05-21</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>75</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1996-05-22</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>77</td>
      <td>47</td>
    </tr>
    <tr>
      <th>1996-05-23</th>
      <td>0.22</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>61</td>
      <td>49</td>
    </tr>
    <tr>
      <th>1996-05-24</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>60</td>
      <td>44</td>
    </tr>
    <tr>
      <th>1996-05-25</th>
      <td>0.24</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>57</td>
      <td>44</td>
    </tr>
    <tr>
      <th>1996-05-26</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>59</td>
      <td>47</td>
    </tr>
    <tr>
      <th>1996-05-27</th>
      <td>0.08</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>60</td>
      <td>45</td>
    </tr>
    <tr>
      <th>1996-05-28</th>
      <td>0.10</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>64</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1996-05-29</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>69</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1996-05-30</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>68</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1996-05-31</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>73</td>
      <td>37</td>
    </tr>
  </tbody>
</table>
</div>




```python
core_weather['snowfall'].value_counts()
```




    0.0     27261
    0.1       470
    0.2       365
    0.3       259
    0.4       200
            ...  
    12.0        1
    9.6         1
    12.7        1
    8.4         1
    6.4         1
    Name: snowfall, Length: 98, dtype: int64



Since the surrounding days (and most days) are 0, and since the percentage of missing values is exceedingly low, we replace the missing values with 0.


```python
core_weather['snowfall'] = core_weather['snowfall'].fillna(0)
```

#### Snow Depth

Now, let's return the rows for which the 'snow_depth' column has missing values:


```python
# now, we return the rows which have missing values in the 'snow_depth' column
core_weather[pd.isnull(core_weather['snow_depth'])]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1996-05-05</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>51</td>
      <td>42</td>
    </tr>
    <tr>
      <th>1996-05-06</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>54</td>
      <td>39</td>
    </tr>
    <tr>
      <th>1996-05-11</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>55</td>
      <td>36</td>
    </tr>
    <tr>
      <th>1996-05-18</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>86</td>
      <td>70</td>
    </tr>
    <tr>
      <th>1996-05-31</th>
      <td>0.00</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>73</td>
      <td>37</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1997-11-27</th>
      <td>0.06</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>43</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1997-11-28</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>44</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1997-11-29</th>
      <td>0.30</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>41</td>
      <td>35</td>
    </tr>
    <tr>
      <th>1997-11-30</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>44</td>
      <td>31</td>
    </tr>
    <tr>
      <th>2009-07-01</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>63</td>
      <td>54</td>
    </tr>
  </tbody>
</table>
<p>67 rows × 5 columns</p>
</div>



Again, when we inspect the instances of missing values for the 'snow_depth' column, we find that the missing values are clustered according to month and year, otherwise the missing values are isolated instances. In fact, a lot of missing values are from October and November 1997. (Note: there are more missing values for 'snow_depth' than 'snowfall'.) We can return all rows for a specific month and year to observe the surrounding data points. Let's look at October 1997:


```python
core_weather.loc['1997-11-01':'1997-11-30']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1997-11-01</th>
      <td>0.22</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>57</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1997-11-02</th>
      <td>0.02</td>
      <td>0.1</td>
      <td>NaN</td>
      <td>40</td>
      <td>33</td>
    </tr>
    <tr>
      <th>1997-11-03</th>
      <td>0.06</td>
      <td>0.3</td>
      <td>NaN</td>
      <td>41</td>
      <td>33</td>
    </tr>
    <tr>
      <th>1997-11-04</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>40</td>
      <td>33</td>
    </tr>
    <tr>
      <th>1997-11-05</th>
      <td>0.25</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>41</td>
      <td>35</td>
    </tr>
    <tr>
      <th>1997-11-06</th>
      <td>0.04</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>42</td>
      <td>36</td>
    </tr>
    <tr>
      <th>1997-11-07</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>46</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1997-11-08</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>43</td>
      <td>39</td>
    </tr>
    <tr>
      <th>1997-11-09</th>
      <td>0.04</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>42</td>
      <td>36</td>
    </tr>
    <tr>
      <th>1997-11-10</th>
      <td>0.01</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>36</td>
      <td>29</td>
    </tr>
    <tr>
      <th>1997-11-11</th>
      <td>0.00</td>
      <td>0.1</td>
      <td>NaN</td>
      <td>34</td>
      <td>21</td>
    </tr>
    <tr>
      <th>1997-11-12</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>31</td>
      <td>18</td>
    </tr>
    <tr>
      <th>1997-11-13</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>35</td>
      <td>25</td>
    </tr>
    <tr>
      <th>1997-11-14</th>
      <td>0.10</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>39</td>
      <td>29</td>
    </tr>
    <tr>
      <th>1997-11-15</th>
      <td>0.04</td>
      <td>0.5</td>
      <td>NaN</td>
      <td>30</td>
      <td>24</td>
    </tr>
    <tr>
      <th>1997-11-16</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>28</td>
      <td>18</td>
    </tr>
    <tr>
      <th>1997-11-17</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>39</td>
      <td>20</td>
    </tr>
    <tr>
      <th>1997-11-18</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>39</td>
      <td>23</td>
    </tr>
    <tr>
      <th>1997-11-19</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>32</td>
      <td>23</td>
    </tr>
    <tr>
      <th>1997-11-20</th>
      <td>0.11</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>36</td>
      <td>23</td>
    </tr>
    <tr>
      <th>1997-11-21</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>36</td>
      <td>18</td>
    </tr>
    <tr>
      <th>1997-11-22</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>35</td>
      <td>17</td>
    </tr>
    <tr>
      <th>1997-11-23</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>28</td>
      <td>14</td>
    </tr>
    <tr>
      <th>1997-11-24</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>34</td>
      <td>9</td>
    </tr>
    <tr>
      <th>1997-11-25</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>49</td>
      <td>32</td>
    </tr>
    <tr>
      <th>1997-11-26</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>48</td>
      <td>33</td>
    </tr>
    <tr>
      <th>1997-11-27</th>
      <td>0.06</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>43</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1997-11-28</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>44</td>
      <td>30</td>
    </tr>
    <tr>
      <th>1997-11-29</th>
      <td>0.30</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>41</td>
      <td>35</td>
    </tr>
    <tr>
      <th>1997-11-30</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>44</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>




```python
core_weather['snow_depth'].value_counts()
```




    0.0     24180
    1.0      1059
    2.0       945
    3.0       616
    4.0       534
    5.0       447
    6.0       309
    7.0       307
    8.0       237
    9.0       182
    10.0      171
    11.0      145
    12.0      124
    1.2       124
    14.0      116
    3.1       100
    5.9        93
    13.0       87
    3.9        64
    5.1        55
    17.0       47
    7.1        45
    15.0       44
    7.9        40
    16.0       39
    9.1        23
    18.0       16
    9.8        14
    28.0        8
    24.0        7
    23.0        6
    27.0        6
    26.0        6
    19.0        6
    31.0        4
    25.0        4
    20.0        4
    14.2        4
    21.0        3
    11.8        3
    22.0        2
    29.0        1
    32.0        1
    16.1        1
    Name: snow_depth, dtype: int64



Again, we find the surrounding days (and most days) are 0, and since the percentage of missing values is exceedingly low, we replace the missing values with 0.


```python
core_weather['snow_depth'] = core_weather['snow_depth'].fillna(0)
```

### Checking Ourselves


```python
# let's re-run some code to confirm there are no longer missing values
core_weather.apply(pd.isnull).sum()/core_weather.shape[0]
```




    precip        0.0
    snowfall      0.0
    snow_depth    0.0
    temp_max      0.0
    temp_min      0.0
    dtype: float64




```python
# let's confirm that the data types we'll use are numeric
core_weather.dtypes
```




    precip        float64
    snowfall      float64
    snow_depth    float64
    temp_max        int64
    temp_min        int64
    dtype: object




```python
# let's convert the indices (dates) to pandas datetime, which allows for more powerful indexing
print(core_weather.index)

core_weather.index = pd.to_datetime(core_weather.index)
print(core_weather.index)
```

    Index(['1939-10-01', '1939-10-02', '1939-10-03', '1939-10-04', '1939-10-05',
           '1939-10-06', '1939-10-07', '1939-10-08', '1939-10-09', '1939-10-10',
           ...
           '2022-09-01', '2022-09-02', '2022-09-03', '2022-09-04', '2022-09-05',
           '2022-09-06', '2022-09-07', '2022-09-08', '2022-09-09', '2022-09-10'],
          dtype='object', name='DATE', length=30296)
    DatetimeIndex(['1939-10-01', '1939-10-02', '1939-10-03', '1939-10-04',
                   '1939-10-05', '1939-10-06', '1939-10-07', '1939-10-08',
                   '1939-10-09', '1939-10-10',
                   ...
                   '2022-09-01', '2022-09-02', '2022-09-03', '2022-09-04',
                   '2022-09-05', '2022-09-06', '2022-09-07', '2022-09-08',
                   '2022-09-09', '2022-09-10'],
                  dtype='datetime64[ns]', name='DATE', length=30296, freq=None)



```python
# as a last check, the documentation mentions values of 9999 indicate missing data, so let's return a count for 9999's
core_weather.apply(lambda x: (x==9999).sum())
```




    precip        0
    snowfall      0
    snow_depth    0
    temp_max      0
    temp_min      0
    dtype: int64



## Analyzing the Data


```python
# let's plot the maximum and minimum temperature across time
core_weather[['temp_max', 'temp_min']].plot(title='Max/Min Temperature each Year in Madison, WI')\
    .set(xlabel='Year', ylabel='Temperature (F)')
```




    [Text(0.5, 0, 'Year'), Text(0, 0.5, 'Temperature (F)')]




    
![png](output_35_1.png)
    



```python
# we don't notice any large gaps in the data, but we can view the value counts for each year
core_weather.index.year.value_counts().sort_index()
```




    1939     92
    1940    366
    1941    365
    1942    365
    1943    365
           ... 
    2018    365
    2019    365
    2020    366
    2021    365
    2022    253
    Name: DATE, Length: 84, dtype: int64




```python
# we can return a series with the sum of snowfall (inches) for each year (note: we drop 1939)
core_weather.groupby(core_weather.index.year).sum()['snowfall'].loc[1940:2022]
```




    DATE
    1940    49.5
    1941    26.9
    1942    29.5
    1943    42.9
    1944    32.7
            ... 
    2018    49.7
    2019    66.5
    2020    50.9
    2021    36.6
    2022    24.2
    Name: snowfall, Length: 83, dtype: float64




```python
# we can plot the aforementioned series
core_weather.groupby(core_weather.index.year).sum()['snowfall'].loc[1940:2022]\
    .plot(grid=True, title='Snowfall each Year in Madison, WI').set(xlabel='Year', ylabel='Snowfall (inches)')
```




    [Text(0.5, 0, 'Year'), Text(0, 0.5, 'Snowfall (inches)')]




    
![png](output_38_1.png)
    


## Training a Machine Learning Model

Now, we ask the question: What do we want to predict? Well, we could think about precipitation or snowfall, but, for any given date, we'll more likely be interested in temperature, especially in the summer months. Let's focus on the maximum temperature, 'temp_max'.


```python
# we create a new column 'target' which contains the shifted entries of 'temp_max'
core_weather['target'] = core_weather.shift(-1)['temp_max']
```


```python
# the effect is that, for a given date, the 'target' entry corresponds to tomorrow's maxmimum temperature, 'temp_max'
core_weather
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
      <th>target</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1939-10-01</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>67</td>
      <td>33</td>
      <td>70.0</td>
    </tr>
    <tr>
      <th>1939-10-02</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>38</td>
      <td>74.0</td>
    </tr>
    <tr>
      <th>1939-10-03</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>74</td>
      <td>48</td>
      <td>81.0</td>
    </tr>
    <tr>
      <th>1939-10-04</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>81</td>
      <td>51</td>
      <td>70.0</td>
    </tr>
    <tr>
      <th>1939-10-05</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>56</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>78</td>
      <td>53</td>
      <td>83.0</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>83</td>
      <td>53</td>
      <td>82.0</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>60</td>
      <td>82.0</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>61</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>2022-09-10</th>
      <td>0.22</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>75</td>
      <td>58</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>30296 rows × 6 columns</p>
</div>




```python
# we ignore the last row containing the missing value we created
core_weather = core_weather.iloc[:-1].copy()
core_weather
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
      <th>target</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1939-10-01</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>67</td>
      <td>33</td>
      <td>70.0</td>
    </tr>
    <tr>
      <th>1939-10-02</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>38</td>
      <td>74.0</td>
    </tr>
    <tr>
      <th>1939-10-03</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>74</td>
      <td>48</td>
      <td>81.0</td>
    </tr>
    <tr>
      <th>1939-10-04</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>81</td>
      <td>51</td>
      <td>70.0</td>
    </tr>
    <tr>
      <th>1939-10-05</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>56</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-05</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>72</td>
      <td>54</td>
      <td>78.0</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>78</td>
      <td>53</td>
      <td>83.0</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>83</td>
      <td>53</td>
      <td>82.0</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>60</td>
      <td>82.0</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>61</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
<p>30295 rows × 6 columns</p>
</div>



### Machine Learning

We will use the 'Ridge' regression model from'scikitlearn'.


```python
from sklearn.linear_model import Ridge

# we initialize the model
reg = Ridge(alpha=.1)
```


```python
predictors = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min']
```

We split our data into a training data set and a test data set, and we'll predict more recent weather data from older data, defining the split to be the end of 2020.


```python
# we define the training set
train = core_weather.loc[:'2020-12-31']

# we define the test set
test = core_weather.loc['2021-01-01':]
```


```python
# we fit our model
reg.fit(train[predictors], train['target'])
```




    Ridge(alpha=0.1)




```python
# now, we make predictions on our test data set
predictions = reg.predict(test[predictors])
```


```python
# and we scrutinize our predictions
from sklearn.metrics import mean_absolute_error
```


```python
mean_absolute_error(test['target'], predictions)
```




    6.042483798575727



We find, on average, we are roughly 6.04 degrees Farenheit from the actual value; of course, this result isn't great.

### Evaluating Our Model


```python
# we print the target (actual) values and the predictions side-by-side
combined = pd.concat([test['target'], pd.Series(predictions, index=test.index)], axis=1)
combined.columns = ['actual', 'predictions']
combined
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>actual</th>
      <th>predictions</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>24.0</td>
      <td>27.980006</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>23.0</td>
      <td>27.168681</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>29.0</td>
      <td>26.763512</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>33.0</td>
      <td>31.201912</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>29.0</td>
      <td>34.188354</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-05</th>
      <td>78.0</td>
      <td>71.913776</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>83.0</td>
      <td>76.638534</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>82.0</td>
      <td>80.688270</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>82.0</td>
      <td>80.822807</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>75.0</td>
      <td>80.957733</td>
    </tr>
  </tbody>
</table>
<p>617 rows × 2 columns</p>
</div>




```python
combined.plot(title='Actual vs. Predicted Values of Max. Temperature in Madison, WI')\
    .set(xlabel='Date', ylabel='Maximum Temperature (F)')
```




    [Text(0.5, 0, 'Date'), Text(0, 0.5, 'Maximum Temperature (F)')]




    
![png](output_57_1.png)
    



```python
# let's see how the predictors are being used in the model
reg.coef_
```




    array([-2.35843911, -1.04412471, -0.12328918,  0.80994731,  0.13492626])



### Prediction Function


```python
def create_predictions(predictors, core_weather, reg):
    train = core_weather.loc[:'2020-12-31']
    test = core_weather.loc['2021-01-01':]
    reg.fit(train[predictors], train['target'])
    predictions = reg.predict(test[predictors])
    error = mean_absolute_error(test['target'], predictions)
    combined = pd.concat([test['target'], pd.Series(predictions, index=test.index)], axis=1)
    combined.columns = ['actual', 'predictions']
    return error, combined
```

### Rolling Mean and New Predictors


```python
# let's find the rolling mean every 30 days for 'temp_max'
core_weather['month_max'] = core_weather['temp_max'].rolling(30).max()
core_weather
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
      <th>target</th>
      <th>month_max</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1939-10-01</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>67</td>
      <td>33</td>
      <td>70.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1939-10-02</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>38</td>
      <td>74.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1939-10-03</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>74</td>
      <td>48</td>
      <td>81.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1939-10-04</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>81</td>
      <td>51</td>
      <td>70.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1939-10-05</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>70</td>
      <td>56</td>
      <td>77.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-05</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>72</td>
      <td>54</td>
      <td>78.0</td>
      <td>85.0</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>78</td>
      <td>53</td>
      <td>83.0</td>
      <td>85.0</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>83</td>
      <td>53</td>
      <td>82.0</td>
      <td>85.0</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>60</td>
      <td>82.0</td>
      <td>85.0</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>61</td>
      <td>75.0</td>
      <td>85.0</td>
    </tr>
  </tbody>
</table>
<p>30295 rows × 7 columns</p>
</div>




```python
# we can determine how different the mothly average temperature is from the day's temperature
core_weather['month_day_max'] = core_weather['month_max'] - core_weather['temp_max']
```


```python
# we can determine the range between maximum and minimum temperature
core_weather['max_min'] = core_weather['temp_max'] - core_weather['temp_min']
```


```python
# let's add our new columns as predictors
predictors = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min', 'month_max', 'month_day_max', 'max_min']
```


```python
# we need to deal with the missing values again; here, we'll delete the rows containing missing values
core_weather = core_weather.iloc[30:].copy()
```


```python
core_weather
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
      <th>target</th>
      <th>month_max</th>
      <th>month_day_max</th>
      <th>max_min</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1939-10-31</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>48</td>
      <td>31</td>
      <td>40.0</td>
      <td>85.0</td>
      <td>37.0</td>
      <td>17</td>
    </tr>
    <tr>
      <th>1939-11-01</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40</td>
      <td>29</td>
      <td>40.0</td>
      <td>85.0</td>
      <td>45.0</td>
      <td>11</td>
    </tr>
    <tr>
      <th>1939-11-02</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40</td>
      <td>27</td>
      <td>44.0</td>
      <td>85.0</td>
      <td>45.0</td>
      <td>13</td>
    </tr>
    <tr>
      <th>1939-11-03</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>44</td>
      <td>23</td>
      <td>48.0</td>
      <td>85.0</td>
      <td>41.0</td>
      <td>21</td>
    </tr>
    <tr>
      <th>1939-11-04</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>48</td>
      <td>28</td>
      <td>46.0</td>
      <td>85.0</td>
      <td>37.0</td>
      <td>20</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-05</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>72</td>
      <td>54</td>
      <td>78.0</td>
      <td>85.0</td>
      <td>13.0</td>
      <td>18</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>78</td>
      <td>53</td>
      <td>83.0</td>
      <td>85.0</td>
      <td>7.0</td>
      <td>25</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>83</td>
      <td>53</td>
      <td>82.0</td>
      <td>85.0</td>
      <td>2.0</td>
      <td>30</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>60</td>
      <td>82.0</td>
      <td>85.0</td>
      <td>3.0</td>
      <td>22</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>61</td>
      <td>75.0</td>
      <td>85.0</td>
      <td>3.0</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
<p>30265 rows × 9 columns</p>
</div>




```python
# call our previously defined function
error, combined = create_predictions(predictors, core_weather, reg)
```


```python
# we reduce our error slightly
error
```




    5.967879900130934




```python
combined.plot(title='Actual vs. Predicted Values of Max. Temperature in Madison, WI')\
    .set(xlabel='Date', ylabel='Maximum Temperature (F)')
```




    [Text(0.5, 0, 'Date'), Text(0, 0.5, 'Maximum Temperature (F)')]




    
![png](output_70_1.png)
    



```python
combined
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>actual</th>
      <th>predictions</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>24.0</td>
      <td>28.716511</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>23.0</td>
      <td>28.051108</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>29.0</td>
      <td>27.636773</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>33.0</td>
      <td>31.281952</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>29.0</td>
      <td>34.280048</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-05</th>
      <td>78.0</td>
      <td>71.871311</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>83.0</td>
      <td>76.057270</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>82.0</td>
      <td>79.628900</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>82.0</td>
      <td>79.614554</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>75.0</td>
      <td>79.714552</td>
    </tr>
  </tbody>
</table>
<p>617 rows × 2 columns</p>
</div>



### More Predictors


```python
# we create a monthly average, being careful not to use more recent data to calculate older averages
core_weather['monthly_avg'] = core_weather['temp_max'].groupby(core_weather.index.month)\
    .apply(lambda x: x.expanding(1).mean())
```


```python
# similarly, we create a daily average
core_weather['day_of_year_avg'] = core_weather['temp_max'].groupby(core_weather.index.day_of_year)\
    .apply(lambda x: x.expanding(1).mean())
```


```python
core_weather
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>precip</th>
      <th>snowfall</th>
      <th>snow_depth</th>
      <th>temp_max</th>
      <th>temp_min</th>
      <th>target</th>
      <th>month_max</th>
      <th>month_day_max</th>
      <th>max_min</th>
      <th>monthly_avg</th>
      <th>day_of_year_avg</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1939-10-31</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>48</td>
      <td>31</td>
      <td>40.0</td>
      <td>85.0</td>
      <td>37.0</td>
      <td>17</td>
      <td>48.000000</td>
      <td>48.000000</td>
    </tr>
    <tr>
      <th>1939-11-01</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40</td>
      <td>29</td>
      <td>40.0</td>
      <td>85.0</td>
      <td>45.0</td>
      <td>11</td>
      <td>40.000000</td>
      <td>40.000000</td>
    </tr>
    <tr>
      <th>1939-11-02</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40</td>
      <td>27</td>
      <td>44.0</td>
      <td>85.0</td>
      <td>45.0</td>
      <td>13</td>
      <td>40.000000</td>
      <td>40.000000</td>
    </tr>
    <tr>
      <th>1939-11-03</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>44</td>
      <td>23</td>
      <td>48.0</td>
      <td>85.0</td>
      <td>41.0</td>
      <td>21</td>
      <td>41.333333</td>
      <td>44.000000</td>
    </tr>
    <tr>
      <th>1939-11-04</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>48</td>
      <td>28</td>
      <td>46.0</td>
      <td>85.0</td>
      <td>37.0</td>
      <td>20</td>
      <td>43.000000</td>
      <td>48.000000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2022-09-05</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>72</td>
      <td>54</td>
      <td>78.0</td>
      <td>85.0</td>
      <td>13.0</td>
      <td>18</td>
      <td>72.663286</td>
      <td>77.506024</td>
    </tr>
    <tr>
      <th>2022-09-06</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>78</td>
      <td>53</td>
      <td>83.0</td>
      <td>85.0</td>
      <td>7.0</td>
      <td>25</td>
      <td>72.665450</td>
      <td>78.253012</td>
    </tr>
    <tr>
      <th>2022-09-07</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>83</td>
      <td>53</td>
      <td>82.0</td>
      <td>85.0</td>
      <td>2.0</td>
      <td>30</td>
      <td>72.669639</td>
      <td>76.891566</td>
    </tr>
    <tr>
      <th>2022-09-08</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>60</td>
      <td>82.0</td>
      <td>85.0</td>
      <td>3.0</td>
      <td>22</td>
      <td>72.673420</td>
      <td>76.204819</td>
    </tr>
    <tr>
      <th>2022-09-09</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>82</td>
      <td>61</td>
      <td>75.0</td>
      <td>85.0</td>
      <td>3.0</td>
      <td>21</td>
      <td>72.677197</td>
      <td>76.060241</td>
    </tr>
  </tbody>
</table>
<p>30265 rows × 11 columns</p>
</div>




```python
# run the previously define function
predictors = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min', 'month_max', 'month_day_max', 'max_min',\
             'monthly_avg', 'day_of_year_avg']

error, combined = create_predictions(predictors, core_weather, reg)
```


```python
# we further reduce our error
error
```




    5.664913922849373



### Model Diagnostics


```python
# see how the predictors are being used in the model
reg.coef_
```




    array([-2.43004159, -0.91615688, -0.12344935,  0.3174611 ,  0.18783492,
            0.15277721, -0.16468398,  0.12962624,  0.11979533,  0.21604161])




```python
# most of the predictors are highly correlated with the target (actual) values
core_weather.corr()['target']
```




    precip             0.080390
    snowfall          -0.250390
    snow_depth        -0.489770
    temp_max           0.934344
    temp_min           0.883779
    target             1.000000
    month_max          0.844996
    month_day_max     -0.482590
    max_min            0.383295
    monthly_avg        0.887381
    day_of_year_avg    0.899946
    Name: target, dtype: float64




```python
# we can see the difference between the actual and predicted values, and return the highest differences
combined['diff'] = (combined['actual'] - combined['predictions']).abs()
```


```python
combined.sort_values('diff', ascending=False).head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>actual</th>
      <th>predictions</th>
      <th>diff</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-04-22</th>
      <td>81.0</td>
      <td>53.304888</td>
      <td>27.695112</td>
    </tr>
    <tr>
      <th>2022-02-19</th>
      <td>51.0</td>
      <td>23.706251</td>
      <td>27.293749</td>
    </tr>
    <tr>
      <th>2021-04-30</th>
      <td>87.0</td>
      <td>61.653394</td>
      <td>25.346606</td>
    </tr>
    <tr>
      <th>2021-12-14</th>
      <td>68.0</td>
      <td>42.916475</td>
      <td>25.083525</td>
    </tr>
    <tr>
      <th>2021-05-26</th>
      <td>51.0</td>
      <td>74.563033</td>
      <td>23.563033</td>
    </tr>
  </tbody>
</table>
</div>


