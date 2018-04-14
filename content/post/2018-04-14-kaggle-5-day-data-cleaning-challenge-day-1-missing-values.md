---
title: 'Kaggle 5-Day Data Cleaning Challenge: Day 1(Missing Values)'
author: ~
date: '2018-04-14'
slug: kaggle-5-day-data-cleaning-challenge-day-1-missing-values
categories: []
tags: []
---


# Day 1 of the 5-Day Kaggle Data Challenge 


```python
import pandas as pd
import numpy as np


#read in data
nfl_data = pd.read_csv("NFL Play by Play 2009-2017 (v4).csv")
sf_permits = pd.read_csv("Building_Permits.csv")
                         
#set seed for reproducibility
np.random.seed(0)
```

    C:\Users\Joey\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py:2717: DtypeWarning: Columns (25,51) have mixed types. Specify dtype option on import or set low_memory=False.
      interactivity=interactivity, compiler=compiler, result=result)
    C:\Users\Joey\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py:2717: DtypeWarning: Columns (22,32) have mixed types. Specify dtype option on import or set low_memory=False.
      interactivity=interactivity, compiler=compiler, result=result)
    


```python
# look at a sample of 5 rows in the sf_permits file. 
sf_permits.sample(5)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Permit Number</th>
      <th>Permit Type</th>
      <th>Permit Type Definition</th>
      <th>Permit Creation Date</th>
      <th>Block</th>
      <th>Lot</th>
      <th>Street Number</th>
      <th>Street Number Suffix</th>
      <th>Street Name</th>
      <th>Street Suffix</th>
      <th>...</th>
      <th>Existing Construction Type</th>
      <th>Existing Construction Type Description</th>
      <th>Proposed Construction Type</th>
      <th>Proposed Construction Type Description</th>
      <th>Site Permit</th>
      <th>Supervisor District</th>
      <th>Neighborhoods - Analysis Boundaries</th>
      <th>Zipcode</th>
      <th>Location</th>
      <th>Record ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40553</th>
      <td>201403039652</td>
      <td>8</td>
      <td>otc alterations permit</td>
      <td>03/03/2014</td>
      <td>3732</td>
      <td>008</td>
      <td>400</td>
      <td>NaN</td>
      <td>Clementina</td>
      <td>St</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>constr type 1</td>
      <td>NaN</td>
      <td>6.0</td>
      <td>South of Market</td>
      <td>94103.0</td>
      <td>(37.780460571778164, -122.40450626524974)</td>
      <td>1334094491645</td>
    </tr>
    <tr>
      <th>169731</th>
      <td>201510159735</td>
      <td>3</td>
      <td>additions alterations or repairs</td>
      <td>10/15/2015</td>
      <td>2609</td>
      <td>028</td>
      <td>79</td>
      <td>NaN</td>
      <td>Buena Vista</td>
      <td>Tr</td>
      <td>...</td>
      <td>5.0</td>
      <td>wood frame (5)</td>
      <td>5.0</td>
      <td>wood frame (5)</td>
      <td>NaN</td>
      <td>8.0</td>
      <td>Castro/Upper Market</td>
      <td>94117.0</td>
      <td>(37.76757916496494, -122.43793170417105)</td>
      <td>1399356139170</td>
    </tr>
    <tr>
      <th>19180</th>
      <td>M409787</td>
      <td>8</td>
      <td>otc alterations permit</td>
      <td>07/22/2013</td>
      <td>4624</td>
      <td>031</td>
      <td>178</td>
      <td>NaN</td>
      <td>West Point</td>
      <td>Rd</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10.0</td>
      <td>Bayview Hunters Point</td>
      <td>94124.0</td>
      <td>(37.73524725436046, -122.38063828309745)</td>
      <td>1311685491725</td>
    </tr>
    <tr>
      <th>68047</th>
      <td>201411191888</td>
      <td>8</td>
      <td>otc alterations permit</td>
      <td>11/19/2014</td>
      <td>0039</td>
      <td>109</td>
      <td>294</td>
      <td>NaN</td>
      <td>Francisco</td>
      <td>St</td>
      <td>...</td>
      <td>5.0</td>
      <td>wood frame (5)</td>
      <td>5.0</td>
      <td>wood frame (5)</td>
      <td>NaN</td>
      <td>3.0</td>
      <td>North Beach</td>
      <td>94133.0</td>
      <td>(37.805257822817126, -122.40998545760392)</td>
      <td>1362881288870</td>
    </tr>
    <tr>
      <th>64238</th>
      <td>M527228</td>
      <td>8</td>
      <td>otc alterations permit</td>
      <td>10/14/2014</td>
      <td>1251</td>
      <td>002</td>
      <td>707</td>
      <td>NaN</td>
      <td>Cole</td>
      <td>St</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>Haight Ashbury</td>
      <td>94117.0</td>
      <td>(37.76836885973765, -122.45074431487859)</td>
      <td>135886493776</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 43 columns</p>
</div>




```python
# number of missing data points per column
missing_values_count = sf_permits.isnull().sum()

# # of missing points in the first 10 columns
missing_values_count[0:10]
```




    Permit Number                  0
    Permit Type                    0
    Permit Type Definition         0
    Permit Creation Date           0
    Block                          0
    Lot                            0
    Street Number                  0
    Street Number Suffix      196684
    Street Name                    0
    Street Suffix               2768
    dtype: int64



There appears to be many missing values, specifically in the "Street Number Suffix" column. Let's look at the percentage of the values in the sf_permits dataset to give us a better sense of the scale of missing values.


```python
# total missing values
total_cells = np.product(sf_permits.shape)
total_missing = missing_values_count.sum()

# percent of data missing
(total_missing/total_cells) * 100
```




    26.26002315058403



A little over a quarter of the data appears to be missing. Next, we will see what is happening with these missing values and take a closer look at the data.  
<br>

## A good question to ask when dealing with missing values is this:

**"Is this value missing because it wasn't recorded or because it doesn't exist?"**
<br>
<br>
As an example of this question, let's take a closer look at the Zipcode column and Street Number Suffix from our sf_permits dataset. By the documentation, we see the zipcode column contains the zipcode of the building's address and the Street Number Suffix column is related to the address as well. We know that every building must have a zipcode, so, therefore, we know that the missing values for this column must exist, but weren't recorded. As for the Street Number Suffix column, a street number suffix is when there aren't enough numbers for buildings on a street(ex. 550A Front Street). In this case, a street number suffix is a value that very likely is non-existent in most of the missing value cases, but also possible that is wasn't recorded as well.
<br>
<br>
# Drop missing values

One must be careful when choosing to drop missing values from their data for various reasons. For example, if you wanted to just drop all observations with missing values, this is what would happen:


```python
# remove all rows containing a missing value
sf_permits.dropna()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Permit Number</th>
      <th>Permit Type</th>
      <th>Permit Type Definition</th>
      <th>Permit Creation Date</th>
      <th>Block</th>
      <th>Lot</th>
      <th>Street Number</th>
      <th>Street Number Suffix</th>
      <th>Street Name</th>
      <th>Street Suffix</th>
      <th>...</th>
      <th>Existing Construction Type</th>
      <th>Existing Construction Type Description</th>
      <th>Proposed Construction Type</th>
      <th>Proposed Construction Type Description</th>
      <th>Site Permit</th>
      <th>Supervisor District</th>
      <th>Neighborhoods - Analysis Boundaries</th>
      <th>Zipcode</th>
      <th>Location</th>
      <th>Record ID</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
<p>0 rows × 43 columns</p>
</div>



As you can see, we have lost all of our data. This is because every row in the sf_permits dataset contained at least one missing value. 
<br> 
Say you wanted to remove all columns with at least one missing value instead.


```python
columns_to_drop = sf_permits.dropna(axis = 1)
print(columns_to_drop.shape)
(columns_to_drop.head())
```

    (198900, 12)
    




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Permit Number</th>
      <th>Permit Type</th>
      <th>Permit Type Definition</th>
      <th>Permit Creation Date</th>
      <th>Block</th>
      <th>Lot</th>
      <th>Street Number</th>
      <th>Street Name</th>
      <th>Current Status</th>
      <th>Current Status Date</th>
      <th>Filed Date</th>
      <th>Record ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>201505065519</td>
      <td>4</td>
      <td>sign - erect</td>
      <td>05/06/2015</td>
      <td>0326</td>
      <td>023</td>
      <td>140</td>
      <td>Ellis</td>
      <td>expired</td>
      <td>12/21/2017</td>
      <td>05/06/2015</td>
      <td>1380611233945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>201604195146</td>
      <td>4</td>
      <td>sign - erect</td>
      <td>04/19/2016</td>
      <td>0306</td>
      <td>007</td>
      <td>440</td>
      <td>Geary</td>
      <td>issued</td>
      <td>08/03/2017</td>
      <td>04/19/2016</td>
      <td>1420164406718</td>
    </tr>
    <tr>
      <th>2</th>
      <td>201605278609</td>
      <td>3</td>
      <td>additions alterations or repairs</td>
      <td>05/27/2016</td>
      <td>0595</td>
      <td>203</td>
      <td>1647</td>
      <td>Pacific</td>
      <td>withdrawn</td>
      <td>09/26/2017</td>
      <td>05/27/2016</td>
      <td>1424856504716</td>
    </tr>
    <tr>
      <th>3</th>
      <td>201611072166</td>
      <td>8</td>
      <td>otc alterations permit</td>
      <td>11/07/2016</td>
      <td>0156</td>
      <td>011</td>
      <td>1230</td>
      <td>Pacific</td>
      <td>complete</td>
      <td>07/24/2017</td>
      <td>11/07/2016</td>
      <td>1443574295566</td>
    </tr>
    <tr>
      <th>4</th>
      <td>201611283529</td>
      <td>6</td>
      <td>demolitions</td>
      <td>11/28/2016</td>
      <td>0342</td>
      <td>001</td>
      <td>950</td>
      <td>Market</td>
      <td>issued</td>
      <td>12/01/2017</td>
      <td>11/28/2016</td>
      <td>144548169992</td>
    </tr>
  </tbody>
</table>
</div>




```python
# how much data did we lose
print("Number of columns in original dataset: %d \n" % sf_permits.shape[1])
print("Number of columns in na's dropped: %d \n" % columns_to_drop.shape[1])

```

    Number of columns in original dataset: 43 
    
    Number of columns in na's dropped: 12 
    
    

Looks like we lost quite a bit of our data. Almost 75% of it!
<br>
<br>
<br>
# Filling in missing values automatically
Our next option in trying to deal with missing values is to try and fill them known as "imputation". 


```python
# subset of the SF Permits dataset
subset_sf_permits = sf_permits.loc[:,"Lot":"Issued Date"].head()
subset_sf_permits
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lot</th>
      <th>Street Number</th>
      <th>Street Number Suffix</th>
      <th>Street Name</th>
      <th>Street Suffix</th>
      <th>Unit</th>
      <th>Unit Suffix</th>
      <th>Description</th>
      <th>Current Status</th>
      <th>Current Status Date</th>
      <th>Filed Date</th>
      <th>Issued Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>023</td>
      <td>140</td>
      <td>NaN</td>
      <td>Ellis</td>
      <td>St</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>ground fl facade: to erect illuminated, electr...</td>
      <td>expired</td>
      <td>12/21/2017</td>
      <td>05/06/2015</td>
      <td>11/09/2015</td>
    </tr>
    <tr>
      <th>1</th>
      <td>007</td>
      <td>440</td>
      <td>NaN</td>
      <td>Geary</td>
      <td>St</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>remove (e) awning and associated signs.</td>
      <td>issued</td>
      <td>08/03/2017</td>
      <td>04/19/2016</td>
      <td>08/03/2017</td>
    </tr>
    <tr>
      <th>2</th>
      <td>203</td>
      <td>1647</td>
      <td>NaN</td>
      <td>Pacific</td>
      <td>Av</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>installation of separating wall</td>
      <td>withdrawn</td>
      <td>09/26/2017</td>
      <td>05/27/2016</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>011</td>
      <td>1230</td>
      <td>NaN</td>
      <td>Pacific</td>
      <td>Av</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>repair dryrot &amp; stucco at front of bldg.</td>
      <td>complete</td>
      <td>07/24/2017</td>
      <td>11/07/2016</td>
      <td>07/18/2017</td>
    </tr>
    <tr>
      <th>4</th>
      <td>001</td>
      <td>950</td>
      <td>NaN</td>
      <td>Market</td>
      <td>St</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>demolish retail/office/commercial 3-story buil...</td>
      <td>issued</td>
      <td>12/01/2017</td>
      <td>11/28/2016</td>
      <td>12/01/2017</td>
    </tr>
  </tbody>
</table>
</div>



We could also replace missing values with whatever value comes directly after it in the same column. This approach makes sense for datasets where the observation have some sort of logical order to them.


```python
# replace all NA's with 0
subset_sf_permits.fillna(0)

# replace all NA's the value that comes directly after it in the same colmun, 
# then replace all the remaining na's with 0

subset_sf_permits.fillna(method = 'bfill',axis = 0).fillna(0)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lot</th>
      <th>Street Number</th>
      <th>Street Number Suffix</th>
      <th>Street Name</th>
      <th>Street Suffix</th>
      <th>Unit</th>
      <th>Unit Suffix</th>
      <th>Description</th>
      <th>Current Status</th>
      <th>Current Status Date</th>
      <th>Filed Date</th>
      <th>Issued Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>023</td>
      <td>140</td>
      <td>0.0</td>
      <td>Ellis</td>
      <td>St</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>ground fl facade: to erect illuminated, electr...</td>
      <td>expired</td>
      <td>12/21/2017</td>
      <td>05/06/2015</td>
      <td>11/09/2015</td>
    </tr>
    <tr>
      <th>1</th>
      <td>007</td>
      <td>440</td>
      <td>0.0</td>
      <td>Geary</td>
      <td>St</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>remove (e) awning and associated signs.</td>
      <td>issued</td>
      <td>08/03/2017</td>
      <td>04/19/2016</td>
      <td>08/03/2017</td>
    </tr>
    <tr>
      <th>2</th>
      <td>203</td>
      <td>1647</td>
      <td>0.0</td>
      <td>Pacific</td>
      <td>Av</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>installation of separating wall</td>
      <td>withdrawn</td>
      <td>09/26/2017</td>
      <td>05/27/2016</td>
      <td>07/18/2017</td>
    </tr>
    <tr>
      <th>3</th>
      <td>011</td>
      <td>1230</td>
      <td>0.0</td>
      <td>Pacific</td>
      <td>Av</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>repair dryrot &amp; stucco at front of bldg.</td>
      <td>complete</td>
      <td>07/24/2017</td>
      <td>11/07/2016</td>
      <td>07/18/2017</td>
    </tr>
    <tr>
      <th>4</th>
      <td>001</td>
      <td>950</td>
      <td>0.0</td>
      <td>Market</td>
      <td>St</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>demolish retail/office/commercial 3-story buil...</td>
      <td>issued</td>
      <td>12/01/2017</td>
      <td>11/28/2016</td>
      <td>12/01/2017</td>
    </tr>
  </tbody>
</table>
</div>



Another way we could've dealt with the missing values for the Zipcode is by using the addresses that are provided combined with an external dataset of some kind. Hanlding missing values is a crucial part in dealing with datasets. We learned mulitple ways of dealing with them here. Here we will end the first Day of the 5-day challenge. 
