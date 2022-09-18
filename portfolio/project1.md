---
title: Predicting Madison, WI Weather with Machine Learning
subtitle: Inspired by DataQuest
author: Maxwell Hanson
---

## Introduction

The goal of this project is to predict weather in Python using data from the Dane County Regional Airport (Madison, WI). You can request the [data](https://www.ncdc.noaa.gov/cdo-web/search), and [view the documentation](https://www.ncei.noaa.gov/cdo-web/datasets#GHCND). Also, download the [jupyter notebook](/portfolio/madison-weather-project.ipynb).

According to the documentation (p.5), there are five core data values: `PRCP` (precipitation in inches), `SNOW` (snowfall in inches), `SNWD` (snow depth in inches), `TMAX` (maximum temperature in Fahrenheit), and `TMIN` (minimum temperature in Fahrenheit), and the core values are indexed by `DATE` (year, month, and day). The data from the Dane County Regional Airport spans from October 01, 1939 (1939-10-01) to September 10, 2022 (2022-09-10), as of my data request on Wednesday, September 14, 2022. We will use the core values as predictors when applying Ridge Regression, or Tikhonov Regularization, with scikit-learn.

Now, we have a question: What do we want to predict? We might want to predict values such as precipitation or snowfall, but for any given date, we'll more likely be interested in temperature, especially in the summer. (Of course, there's a lot to do with this data, but we'll limit our focus.) So, we will predict the maximum temperature, `TMAX`.

## Exploring and Preparing Data

We will use pandas to handle our data, `weather`, and we will extract the aforementioned core values into a smaller dataframe, `core_weather`, with more suggestive column names:

```python
import pandas as pd

weather = pd.read_csv('madison-weather.csv', index_col='DATE')

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

Now, we want to be careful about missing values, or null values, as they are not useful, and they will create problems when applying Ridge Regression. Let's check the precentage of missing values in each column of `core_weather`:

```python
core_weather.apply(pd.isnull).sum()/core_weather.shape[0]
```

    precip        0.000000
    snowfall      0.000891
    snow_depth    0.002212
    temp_max      0.000000
    temp_min      0.000000
    dtype: float64

Moreover, the documentation (p.5) mentions that "9's in a field (e.g., 9999) indicate missing data", so let's check for any entries of 9's:

```python
core_weather.apply(lambda x: (x==9999).sum())
```
    precip        0.0
    snowfall      0.0
    snow_depth    0.0
    temp_max      0.0
    temp_min      0.0
    dtype: float64

So, we don't have any entries of 9's, but we do have missing values for `snowfall` and `snow_depth`, though the percentage of missing values for eeach column is very small. Let's inspect the instances of missing values so that we can proceed with data analysis and building a model.

### Snowfall

Let's return the rows in `core_weather` for which `snowfall` has missing values:

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

When we inspect the instances of missing values for the `snowfall` column, we find that the missing values are clustered according to year and month (i.e., May 1996 and March 1997); otherwise the missing values are isolated instances (i.e., 1996-10-06 and 1999-02-13). We don't know why these values are missing, but we can observe the surrounding data points to discern the context of the missing values. Let's look at May 1996 (which has the most missing values):

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

The surrounding days in May 1996 which do not have missing values for `snowfall` have entries of 0.0. We can repeat this observation for all of the missing values. Further, we can return the counts for each value in the `snowfall` column, expecting, of course, that a majority of days have no snowfall:

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

At this point, we need to decide what to do with the missing values, as we have numerous options, such as forward propagation, replacing the missing values with 0.0, removing the rows containing the missing values, or mixing options on an individual basis. Here, we'll replace the missing values with 0.0, since the days surrounding the missing values have entries of 0.0:

```python
core_weather['snowfall'] = core_weather['snowfall'].fillna(0)
```

### Snow Depth

Now, let's return the rows in `core_weather` for which `snow_depth` has missing values:

```python
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

Again, when we inspect the instances of missing values for the 'snow_depth' column, we find that the missing values are clustered according to month and year; otherwise the missing values are isolated instances. In fact, a lot of missing values are from October and November 1997. (Note: there are more missing values for `snow_depth` than `snowfall`.) In observing the days surrounding the missing values, most of the entries for `snow_depth` are 0.0. We can, again, return counts for each value in the `snow_depth` column, expecting that, of course, a majority of days do not have snow accumulation:

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

We could be more nuanced, and there is time to be more nuanced, but here we'll replace the missing values with 0.0, since the days surrounding the missing values have entries of 0.0:

```python
core_weather['snow_depth'] = core_weather['snow_depth'].fillna(0)
```

## Checking Ourselves

OK, let's re-run some code to check ourselves, making sure there are no longer missing values. We'll also convert our dates to pandas DateTime. First, we'll check the percentage of missing values in each column of `core_weather`:

```python
core_weather.apply(pd.isnull).sum()/core_weather.shape[0]
```

    precip        0.0
    snowfall      0.0
    snow_depth    0.0
    temp_max      0.0
    temp_min      0.0
    dtype: float64

Next, we'll confirm that the data types we're using are type numeric:

```python
core_weather.dtypes
```

    precip        float64
    snowfall      float64
    snow_depth    float64
    temp_max        int64
    temp_min        int64
    dtype: object

Lastly, we'll convert our dates to pandas DateTime, allowing for more powerful indexing:

```python
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

## Optional Data Analysis

There's a lot to do with data analysis, though it's not the focus of this project, but let's visualize some of our data. For example, let's plot maximum temperature, `temp_max`, and minimum temperature, `temp_min`, against time:

```python
core_weather[['temp_max', 'temp_min']].plot(title='Max/Min Temperature each Year in Madison, WI')\
    .set(xlabel='Year', ylabel='Temperature (F)')
```

![jpg](/assets/img/project1_out1.jpg)

One important note is that there is not complete data for the year 1939 (when the data begins), nor for the year 2022 (for obvious reasons), as we can see with the following counts for each year:

```python
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

So, dropping the years 1939 and 2022, we can plot snowfall (inches) each year in Madison, WI:

```python
core_weather.groupby(core_weather.index.year).sum()['snowfall'].loc[1940:2022]\
    .plot(grid=True, title='Snowfall each Year in Madison, WI').set(xlabel='Year', ylabel='Snowfall (inches)')
```

![jpg](/assets/img/project1_out2.jpg)

## Machine Learning

### Creating a Target Column

As stated in the Introduction, we will predict the the maximum temperature, `temp_max`, using [Ridge Regression with scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Ridge.html). First, let's create a new column, `target`, which will contain the up-shifted entries of `temp_max`, so that the entry for `target` for a given day is the following day's entry for maximum temperature, `temp_max`.

```python
core_weather['target'] = core_weather.shift(-1)['temp_max']
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

We can drop the last row with the missing value we created:

```python
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

### Machine Learning, Iteration One

Let's initialize our model, defining our predictors to be `precip`, `snowfall`, `snow_depth`, `temp_max`, and `temp_min`.

```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_absolute_error

# initialize the model
reg = Ridge(alpha=.1)
```

```python
predictors = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min']
```

Now, we split our data into a training data set and a test data set, and we'll predict more recent weather data from older data, defining the split to be the end of 2020. (Note: We don't want to use more recent data to predict older data, since we would be using information we wouldn't otherwise have for predictions, which can lead to overfitting.)

```python
# define the training set
train = core_weather.loc[:'2020-12-31']

# define the test set
test = core_weather.loc['2021-01-01':]
```
```python
# fit our model
reg.fit(train[predictors], train['target'])
```

Next, we make predictions on our test data set, and we scrutinize our model:

```python
# make predictions on our test data set
predictions = reg.predict(test[predictors])
```

```python
mean_absolute_error(test['target'], predictions)
```

    6.042483798575727

Using `mean_absolute_error`, when we find the mean absolute error of our predictions, we find we are (on average) 6.04 degrees Farenheit from the actual value; of course, this result isn't great, but predicting weather is difficult. So, let's evaluate our model, and let's see if we can make some improvements.

### Evaluating Model, Iteration One

We can see how the predictors are being used (weighted) in the model, respectively:

```python
reg.coef_
```

    array([-2.35843911, -1.04412471, -0.12328918,  0.80994731,  0.13492626])

Let's print the target (actual) values and the predictions side-by-side, just to summarize our results:

```python
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

Further, let's plot the above data, with the target (actual) values and the predictions against the date:

```python
combined.plot(title='Actual vs. Predicted Values of Max. Temperature in Madison, WI')\
    .set(xlabel='Date', ylabel='Maximum Temperature (F)')
```

![jpg](/assets/img/project1_out3.jpg)

We want this information for comparison, as we will add more predictors to the model, and we can compare the results between successive iterations of our model.

### Prediction Function

Now, we'll work on improving our model; however, so we don't copy and paste a lot of code, we'll define a `create_predicitions` function which allows us to streamline the model predictions.

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

### Machine Learning, Iteration Two

We're going to define a rolling mean for the maximum temperature every 30 days, `month_max`, since perhaps the information of the average maximum temperature for a period of the past 30 days will help make predictions.

```python
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

Further, let's consider the difference between a day's maximum temperature and the average temperature for the past 30 days, `month_day_max`, and let's consider the range between maximum and minimum temperature, `max_min`:

```python
core_weather['month_day_max'] = core_weather['month_max'] - core_weather['temp_max']
```

```python
core_weather['max_min'] = core_weather['temp_max'] - core_weather['temp_min']
```

We'll add these newly created columns (`month_max`, `month_day_max`, and `max_min`) as predictors, and we'll remove the missing values we created from the rolling mean:

```python
predictors = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min', 'month_max', 'month_day_max', 'max_min']
```

```python
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

OK, let's call the `create_predictions` function:

```python
error, combined = create_predictions(predictors, core_weather, reg)
```

```python
error
```

    5.967879900130934

We have reduced our error slightly, from 6.042483798575727 to 5.967879900130934, which is not a significant difference, but we find that adding predictors helped to reduce the error.

### Evaluating Model, Iteration Two

Again, let's print the target (actual) values and the predictions side-by-side, just to summarize our results:

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

Further, let's plot the above data, with the target (actual) values and the predictions against the date:

```python
combined.plot(title='Actual vs. Predicted Values of Max. Temperature in Madison, WI')\
    .set(xlabel='Date', ylabel='Maximum Temperature (F)')
```

![jpg](/assets/img/project1_out4.jpg)

### Machine Learning, Iteration Three

Let's repeat the process, creating even more predictors; we'll create a monthly average (not the past 30 days, as before), and we'll create a a daily average, being careul to not use more recent data to calulate older averages:

```python
core_weather['monthly_avg'] = core_weather['temp_max'].groupby(core_weather.index.month)\
    .apply(lambda x: x.expanding(1).mean())
```

```python
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

Now, let's update our predictors, and let's run the `create_predictions` function:

```python
predictors = ['precip', 'snowfall', 'snow_depth', 'temp_max', 'temp_min', 'month_max', 'month_day_max', 'max_min',\
             'monthly_avg', 'day_of_year_avg']

error, combined = create_predictions(predictors, core_weather, reg)
```

```python
error
```

    5.664913922849373

We have further reduced our error, from 6.042483798575727 to 5.967879900130934 to 5.664913922849373. The predicitons are not great, but we have built a simple model, and we have succeeded in reducing our error. Of course, there's a lot more we can do with this model, but let's conclude by looking at how the model performed.

## Model Diagnostics

Let's see how the predictors are being used (weighted), respectively:

```python
reg.coef_
```

    array([-2.43004159, -0.91615688, -0.12344935,  0.3174611 ,  0.18783492,
            0.15277721, -0.16468398,  0.12962624,  0.11979533,  0.21604161])

Next, we observe that most of the predictors are highly correlated with the traget (actual) values, which is what we want for Ridge Regression:

```python
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

We now, on average, that our predictions are 5.66 degrees off from the target (actual) values, but let's see where the differences are the greatest:

```python
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

It's difficult to discern a pattern (even when printing more rows) for the greatest difference between predictions and the target (actual) values.

## Conclusion

Again, overall, the errors on our predictions are not great, but predicting weather is notoriously difficult! However, with three iterations of applying Ridge Regression, we managed to reduce the error by 0.38 degrees Farenheit, summarized below:

| Iteration One Error (F) | Iteration Two Error (F) | Iteration Three Error (F) |
|:----------------------: | :---------------------: | :-----------------------: |
| 6.042483798575727       | 5.967879900130934       | 5.664913922849373         |
