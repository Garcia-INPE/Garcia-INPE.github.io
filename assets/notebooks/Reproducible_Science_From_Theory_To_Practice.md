# Reproducible Science - From theory to practice (by Roberto Garcia - INPE/BR)


### Overview
This notebook contains some practical approach programmed in Python in order to achieve [reproducible science](https://www.earthdatascience.org/courses/intro-to-earth-data-science/open-reproducible-science/get-started-open-reproducible-science/).

#### Opinions about Open and Reproducible Science 
---

> Q1: Open Reproducible Science meaning:<br/>
<p>A: Open reproducible science means to me that science toolkit must be prepared, documented and disseminated in a way that it is accessible and clearly understandable to everyone. By toolkit I mean the complete set of elements of the research: data, methodology, and results.</p>

> Q2: Example of a tool that fits Open Science:<br/>
<p>Jupyter notebook is a great example of tool that matches open science needs, it can mix code and textual and visual explanation of the research. Moreover, it can be shared to people.</p>

> Q3: This notebook has a machine-readable name:<br/>
<p>A: In some manner, I think yes, because, besides it has no stardadization with respect to the words, it the words are ordinary ones, that can be easy found in a dictionary, thus it can be readable after some inference.</p>

> Q4: I can write clean code by:<br/>
* Establishing a well-known e consensual format to apply along the construction of code
* Establishing a block documentation of functions
* Applying a tool to check the code concerning the format
* Using expressive variables and function names
* Using modular code if some block is executed multiple times
* Documenting the code using the right amount of comments, nor too small, nor too big

> Q5: Advantages of clean code include:<br/>
* Easy to read due to code standardization
* Is easier to debug due to modularization
* Easy to understand by others due to expressive names 
* Well-documented due to comments


#### In order to make the science reproducible some approaches must be taken into account.

##### Making science easier to reproduce:
* Reproducible science is also about making the code easier to read and understand for people who didn't develop the code (a **clean** code).
* One way to achieve this is to apply some standardized rules for code formatting, such as [PEP 8](https://peps.python.org/pep-0008/).
* There are standards for Code Layout, Documentation, Naming, etc. 
* All these standards can be **easily** checked and fixed by adding a extension to the IDE.
* How-to install and use the official Microsoft PEP 8 extension for MS Visual Studio Code (VS Code) can be found [here](https://github.com/Garcia-INPE/Tutorials/blob/main/Tut-PEP8_in_VSCode.md).

##### Making science explainable:
  * Reproducible science is also about making the reseach easier to understand.
  * One way to achieve this is to explain the methodology as accurately as possible, balancing short terms and sufficient explanations. Some actions include:
    * Using descriptive notebook file name and a main header that explains what will be presented.
    * Describing the data, such as:
      - Who takes the data.
      - Where the data were taken.
      - What the maximum temperature units are.
      - How the data are collected.
      - And don't forget to include a citation of the data.
    * Writing an overview of each sub analisys.
    * Including headlines and legend for figures
    * Making plots that have: 
      - A title that explains where and when the data are from 
      - x/y axis labels with **units** where appropriate
      - A legend where appropriate

# Research: Time Series analisys for Boulder / CO / USA

* The goal of this research is to compare the montly behaviour of the mininum and maximum temperature and precipitation for the period of 1900 to 2023 in Boulder, CO, USA.
* A few steps is necessary in order to complete a research:

### Doing research: Step 1 - Check the availability of the data

* According to an empirical knowledge acquired from [ESILL - Short Course](https://cu-esiil-edu.github.io/esiil-learning-portal/shortcourse/pages/00-course-overviews/shortcourse/00-home.html) 2024 edition, taught by [Nate Quarder](https://nquarder.github.io/) and [Elsa Culler](https://eculler.github.io/), the NOAA National Climatic Data Center (NOAA/NCEI) [Climate Data Online portal](https://www.ncei.noaa.gov/cdo-web/) has the target daily data from the [Global Historical Climate Network dataset (GHCND)](https://www.ncei.noaa.gov/cdo-web/search?datasetid=GHCND).

* Daily Summaries data from Boulder ground stations were chosen from [this form](https://www.ncei.noaa.gov/cdo-web/search?datasetid=GHCND), for the mentioned period.

* One should select the data source according to its needs, in this case, from [the station list provided](https://www.ncei.noaa.gov/cdo-web/datasets/GHCND/locations/CITY:US080001/detail#stationlist), a station which had the most of the chosen period of data covered was selected. 
  - <ins>**Hint:**</ins> Play with the order of the columns to find what you want.

* A special care was taken in order to guarantee that the selected station had all climate variables (data types) used in this analisys ([Click in 'Station List' in this page](https://www.ncei.noaa.gov/cdo-web/datasets/GHCND/locations/CITY:US080001/detail)).

### Doing research: Step 2 - Getting the data

* Also according to [ESILL - Short Course](https://cu-esiil-edu.github.io/esiil-learning-portal/shortcourse/pages/00-course-overviews/shortcourse/00-home.html), there is an [API to access NCEI GHCND Data](https://www.ncei.noaa.gov/support/access-data-service-api-user-documentation).

* An **[API (Application Programming Interface)](https://en.wikipedia.org/wiki/API)** is a way for client programs (installed on a client computer) to establish communication with a server program (a **service** installed on a remote server computer), creating a [M2M (Machine to Machine)](https://en.wikipedia.org/wiki/Machine_to_machine) comunication. 

* The intuition of communication is that we (the client) want to send a request message to the service and receive a response. For the NCEI GHCND API service, this request is a string representing a [URL](https://en.wikipedia.org/wiki/URL). This URL must be made up of special tags in which we specify the filtering option we want as a response.

* The metadata of the selected station are:

```
Selected station name:  LONGMONT 2 ESE, CO US
Network ID...........:  GHCND:USC00055116
Latitude/Longitude...:  40.16958°, -105.0774°
Elevation............:	1508.8 m
PERIOD OF RECORD
PRCP	Precipitation       1893-10-01	2024-04-28	100%
TMAX	Maximum temperature 1893-10-01	2024-04-29	100%
TMIN	Minimum temperature 1893-10-01	2024-04-29	100%
```

* In the cell below the URL is created according to the corresponding tags to match the station metadata.


```python
# Best practices
# Expressive variable name for the URL
# Respect the 79-character line limit
# Further lines aligned with opening delimiter
# Lines always break before tag separator
url = ("https://www.ncei.noaa.gov/access/services/data/v1"
               "?dataset=daily-summaries"
               "&dataTypes=PRCP,TMIN,TMAX"
               "&stations=USC00055116"
               "&startDate=1900-01-01"
               "&endDate=2023-12-31"
               "&includeStationName=true"
               "&includeStationLocation=1"
               "&units=metric")
# Observations
# 'includeLocation' means Lat and lon in the response
# Special care with the "units"

url    # Show the URL
```




    'https://www.ncei.noaa.gov/access/services/data/v1?dataset=daily-summaries&dataTypes=PRCP,TMIN,TMAX&stations=USC00055116&startDate=1900-01-01&endDate=2023-12-31&includeStationName=true&includeStationLocation=1&units=metric'




```python
### Loading an well-known package to manipulate dataframes (tabular data)
import pandas as pd
# Downloading the original data to a Pandas DataFrame
df_ori = pd.read_csv(url, index_col="STATION")
```

### Doing research: Step 3 - Exploratory Data Analisys (EDA)
Lets know the data!

#### Configuring the output


```python
# The output of some examples below cuts out the content of lines  
# making it difficult to read, expand the output width using:
pd.options.display.width = 500
```

#### Check if the data was imported into a pandas DataFrame


```python
type(df_ori)
```




    pandas.core.frame.DataFrame



#### Showing 5 first and 5 last rows of the DataFrame


```python
df_ori
# Check if PRCP is in mm, TEMP fields are in Celsius (we especified
#    units=metric in our API request)
# Check the amount of rows and cols at the bottom (36961 rows × 8 cols)
# Note thar 'STATION' is an 'index col'
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
      <th>NAME</th>
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>ELEVATION</th>
      <th>DATE</th>
      <th>PRCP</th>
      <th>TMAX</th>
      <th>TMIN</th>
    </tr>
    <tr>
      <th>STATION</th>
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
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>1909-06-01</td>
      <td>7.6</td>
      <td>16.7</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>1909-06-02</td>
      <td>1.0</td>
      <td>23.3</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>1909-06-03</td>
      <td>0.0</td>
      <td>27.2</td>
      <td>5.6</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>1909-06-04</td>
      <td>0.0</td>
      <td>32.8</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>1909-06-05</td>
      <td>0.3</td>
      <td>35.0</td>
      <td>8.3</td>
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
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>2023-12-27</td>
      <td>0.0</td>
      <td>6.7</td>
      <td>-1.1</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>2023-12-28</td>
      <td>0.0</td>
      <td>10.6</td>
      <td>-8.9</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>2023-12-29</td>
      <td>0.0</td>
      <td>10.0</td>
      <td>-8.9</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>2023-12-30</td>
      <td>0.0</td>
      <td>14.4</td>
      <td>-6.1</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>LONGMONT 2 ESE, CO US</td>
      <td>40.16958</td>
      <td>-105.0774</td>
      <td>1508.8</td>
      <td>2023-12-31</td>
      <td>0.0</td>
      <td>13.3</td>
      <td>-5.0</td>
    </tr>
  </tbody>
</table>
<p>36961 rows × 8 columns</p>
</div>



#### Checking the type of each attribute (column)


```python
df_ori.dtypes
# Object can be strings

```




    NAME          object
    LATITUDE     float64
    LONGITUDE    float64
    ELEVATION    float64
    DATE          object
    PRCP         float64
    TMAX         float64
    TMIN         float64
    dtype: object



#### DataFrame statistics


```python
descr = df_ori.describe()
descr  # The summarization is also a DataFame
# Take a look at the statistics for each meaninful attributes (columns)
# We can have good insights aboult the data here
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
      <th>LATITUDE</th>
      <th>LONGITUDE</th>
      <th>ELEVATION</th>
      <th>PRCP</th>
      <th>TMAX</th>
      <th>TMIN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>3.696100e+04</td>
      <td>3.696100e+04</td>
      <td>3.696100e+04</td>
      <td>36694.000000</td>
      <td>36747.000000</td>
      <td>36784.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>4.016958e+01</td>
      <td>-1.050774e+02</td>
      <td>1.508800e+03</td>
      <td>0.954698</td>
      <td>18.118840</td>
      <td>0.605638</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.170280e-11</td>
      <td>6.363707e-11</td>
      <td>8.703982e-10</td>
      <td>3.800475</td>
      <td>11.198193</td>
      <td>9.542416</td>
    </tr>
    <tr>
      <th>min</th>
      <td>4.016958e+01</td>
      <td>-1.050774e+02</td>
      <td>1.508800e+03</td>
      <td>0.000000</td>
      <td>-50.600000</td>
      <td>-38.900000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>4.016958e+01</td>
      <td>-1.050774e+02</td>
      <td>1.508800e+03</td>
      <td>0.000000</td>
      <td>10.000000</td>
      <td>-6.100000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>4.016958e+01</td>
      <td>-1.050774e+02</td>
      <td>1.508800e+03</td>
      <td>0.000000</td>
      <td>18.900000</td>
      <td>1.100000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.016958e+01</td>
      <td>-1.050774e+02</td>
      <td>1.508800e+03</td>
      <td>0.000000</td>
      <td>27.800000</td>
      <td>8.900000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>4.016958e+01</td>
      <td>-1.050774e+02</td>
      <td>1.508800e+03</td>
      <td>111.800000</td>
      <td>41.100000</td>
      <td>23.300000</td>
    </tr>
  </tbody>
</table>
</div>



#### Extracting data from the summary DataFrame


```python
# Use [row, col] notation to 'subselect' the data we want
print("TEMP records:", 
      descr.loc["min", "TMIN"], "to",
      descr.loc["max", "TMAX"])
print("RAIN record.:", 
      descr.loc["max", "PRCP"])
```

    TEMP records: -38.9 to 41.1
    RAIN record.: 111.8


#### Subselecting only the interesting columns


```python
# Let's create a subset of the Dataframe with only the 4 interesting
# columns (the date and its corresponding data)
df = df_ori[["DATE", "PRCP", "TMAX", "TMIN"]]
# And show it
df
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
      <th>DATE</th>
      <th>PRCP</th>
      <th>TMAX</th>
      <th>TMIN</th>
    </tr>
    <tr>
      <th>STATION</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>USC00055116</th>
      <td>1909-06-01</td>
      <td>7.6</td>
      <td>16.7</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>1909-06-02</td>
      <td>1.0</td>
      <td>23.3</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>1909-06-03</td>
      <td>0.0</td>
      <td>27.2</td>
      <td>5.6</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>1909-06-04</td>
      <td>0.0</td>
      <td>32.8</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>1909-06-05</td>
      <td>0.3</td>
      <td>35.0</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>2023-12-27</td>
      <td>0.0</td>
      <td>6.7</td>
      <td>-1.1</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>2023-12-28</td>
      <td>0.0</td>
      <td>10.6</td>
      <td>-8.9</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>2023-12-29</td>
      <td>0.0</td>
      <td>10.0</td>
      <td>-8.9</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>2023-12-30</td>
      <td>0.0</td>
      <td>14.4</td>
      <td>-6.1</td>
    </tr>
    <tr>
      <th>USC00055116</th>
      <td>2023-12-31</td>
      <td>0.0</td>
      <td>13.3</td>
      <td>-5.0</td>
    </tr>
  </tbody>
</table>
<p>36961 rows × 4 columns</p>
</div>



#### Adjusting the DataFrame index


```python
# Let's drop the index created for STATION (useless) and make DATE 
#    to be the new index (more meaninful)
# Let's also auto reassign the result to df2 itself with 'inplace'
df.set_index("DATE", inplace=True)
df
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
      <th>PRCP</th>
      <th>TMAX</th>
      <th>TMIN</th>
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
      <th>1909-06-01</th>
      <td>7.6</td>
      <td>16.7</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>1909-06-02</th>
      <td>1.0</td>
      <td>23.3</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>1909-06-03</th>
      <td>0.0</td>
      <td>27.2</td>
      <td>5.6</td>
    </tr>
    <tr>
      <th>1909-06-04</th>
      <td>0.0</td>
      <td>32.8</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>1909-06-05</th>
      <td>0.3</td>
      <td>35.0</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2023-12-27</th>
      <td>0.0</td>
      <td>6.7</td>
      <td>-1.1</td>
    </tr>
    <tr>
      <th>2023-12-28</th>
      <td>0.0</td>
      <td>10.6</td>
      <td>-8.9</td>
    </tr>
    <tr>
      <th>2023-12-29</th>
      <td>0.0</td>
      <td>10.0</td>
      <td>-8.9</td>
    </tr>
    <tr>
      <th>2023-12-30</th>
      <td>0.0</td>
      <td>14.4</td>
      <td>-6.1</td>
    </tr>
    <tr>
      <th>2023-12-31</th>
      <td>0.0</td>
      <td>13.3</td>
      <td>-5.0</td>
    </tr>
  </tbody>
</table>
<p>36961 rows × 3 columns</p>
</div>



#### Let's check missing data (VERY! important in EDA)


```python
print("Checking missind data with isnull()\n", df.isnull().sum())
print("Checking missind data with isna()\n", df.isna().sum())
# These functions are the same, isna() is an alias to isnull()
# If the function print() were not used, only the last line would be 
#    printed for the output
```

    Checking missind data with isnull()
     PRCP    267
    TMAX    214
    TMIN    177
    dtype: int64
    Checking missind data with isna()
     PRCP    267
    TMAX    214
    TMIN    177
    dtype: int64


#### Let's check which rows have at least one missing data


```python
null_mask = df.isnull().any(axis=1)
null_rows = df[null_mask]
print(null_rows)
# 537 rows
```

                PRCP  TMAX  TMIN
    DATE                        
    1909-09-01   NaN  28.3  12.8
    1909-09-02   NaN  32.8  16.1
    1909-09-08   NaN  28.3  10.0
    1909-09-09   NaN  27.8   8.3
    1909-09-10   NaN  29.4  15.0
    ...          ...   ...   ...
    2018-01-26   0.0   NaN   NaN
    2018-08-16   0.0   NaN   NaN
    2018-11-16   0.0   NaN   NaN
    2019-01-10   0.0   NaN   NaN
    2023-01-06   0.0   NaN   NaN
    
    [537 rows x 3 columns]


#### IMPORTANT! MISSING DATA MANIPULATION

We faced with a very common impasse in data science, **how to handle missing data**. Some options for this case would be:

* Complete the data with data from another station
* Complete the data with interpolation techniques
* And eliminate missing data

In this case, to speed up the research process, the lines that contain any missing data will be eliminated. 


```python
df_ok = df[~null_mask]   # from the previous code cell
print(df_ok)
#rows_ori - row_null = rows_ok
len(df) - len(null_rows)    # 36961 - 537 = 36424
```

                PRCP  TMAX  TMIN
    DATE                        
    1909-06-01   7.6  16.7   7.8
    1909-06-02   1.0  23.3   8.3
    1909-06-03   0.0  27.2   5.6
    1909-06-04   0.0  32.8   6.7
    1909-06-05   0.3  35.0   8.3
    ...          ...   ...   ...
    2023-12-27   0.0   6.7  -1.1
    2023-12-28   0.0  10.6  -8.9
    2023-12-29   0.0  10.0  -8.9
    2023-12-30   0.0  14.4  -6.1
    2023-12-31   0.0  13.3  -5.0
    
    [36424 rows x 3 columns]





    36424



#### Plotting the cleaned DataFrame


```python
df_ok.plot(title="Boulder Time Series Analisys by Date",
           xlabel="Date",
           ylabel="Scale")
# The plot has a very weird output:
#   - Y-axis scale is not good (mixings data scales)
#   - X-axis labels are not clear
```




    <Axes: title={'center': 'Boulder Time Series Analisys by Date'}, xlabel='Date', ylabel='Scale'>




    
![png](Reproducible_Science_From_Theory_To_Practice_files/Reproducible_Science_From_Theory_To_Practice_31_1.png)
    


#### Summarizing data monthly mean


```python
# Summarizing must respect the year and month)
# Pandas has a special function for this
df_group = df_ok.groupby([df_ok.index.year, df_ok.index.month]).mean()

# ERROR: The index column (DATE) is not a Date object but a String
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    Cell In[86], line 3
          1 # Summarizing must respect the year and month)
          2 # Pandas has a special function for this
    ----> 3 df_group = df_ok.groupby([df_ok.index.year, df_ok.index.month]).mean()
          5 # ERROR: The index column (DATE) is not a Date object but a String


    AttributeError: 'Index' object has no attribute 'year'


#### Converting the index to DateTime


```python
df_ok.index = pd.to_datetime(df_ok.index)
type(df_ok.index)
```




    pandas.core.indexes.datetimes.DatetimeIndex



#### Trying to summarize the DataFrame by month and year again


```python
df_group = df_ok.groupby([df_ok.index.year, df_ok.index.month]).mean()
# And show it
df_group
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
      <th></th>
      <th>PRCP</th>
      <th>TMAX</th>
      <th>TMIN</th>
    </tr>
    <tr>
      <th>DATE</th>
      <th>DATE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">1909</th>
      <th>6</th>
      <td>1.276667</td>
      <td>27.546667</td>
      <td>9.486667</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0.600000</td>
      <td>31.148387</td>
      <td>12.903226</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0.629032</td>
      <td>31.438710</td>
      <td>12.567742</td>
    </tr>
    <tr>
      <th>9</th>
      <td>7.085714</td>
      <td>20.857143</td>
      <td>10.414286</td>
    </tr>
    <tr>
      <th>10</th>
      <td>0.180645</td>
      <td>20.793548</td>
      <td>-0.238710</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">2023</th>
      <th>8</th>
      <td>1.754839</td>
      <td>29.945161</td>
      <td>14.012903</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0.900000</td>
      <td>27.236667</td>
      <td>9.386667</td>
    </tr>
    <tr>
      <th>10</th>
      <td>0.451613</td>
      <td>19.119355</td>
      <td>2.448387</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0.420000</td>
      <td>13.773333</td>
      <td>-3.413333</td>
    </tr>
    <tr>
      <th>12</th>
      <td>0.206452</td>
      <td>9.912903</td>
      <td>-4.877419</td>
    </tr>
  </tbody>
</table>
<p>1214 rows × 3 columns</p>
</div>



#### Rechecking the plot


```python
df_group.plot(title="Boulder Time Series Analisys by Year-Month",
              xlabel="Year-Month",
              ylabel="Scale")
# Still a weind plot!
```




    <Axes: title={'center': 'Boulder Time Series Analisys by Year-Month'}, xlabel='Year-Month', ylabel='Scale'>




    
![png](Reproducible_Science_From_Theory_To_Practice_files/Reproducible_Science_From_Theory_To_Practice_39_1.png)
    


#### Spliting data and shared x-axis


```python

df_group.plot(
    subplots=True,
    title="Boulder Time Series Analisys by Year-Month",
    xlabel="Year",
    ylabel="Scale")
```




    array([<Axes: xlabel='Year', ylabel='Scale'>,
           <Axes: xlabel='Year', ylabel='Scale'>,
           <Axes: xlabel='Year', ylabel='Scale'>], dtype=object)




    
![png](Reproducible_Science_From_Theory_To_Practice_files/Reproducible_Science_From_Theory_To_Practice_41_1.png)
    


#### How if we group it by year?


```python
df_grp_year = df_ok.groupby([df_ok.index.year]).mean()
df_grp_year
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
      <th>PRCP</th>
      <th>TMAX</th>
      <th>TMIN</th>
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
      <th>1909</th>
      <td>0.902094</td>
      <td>21.100524</td>
      <td>2.823037</td>
    </tr>
    <tr>
      <th>1910</th>
      <td>0.394326</td>
      <td>15.619858</td>
      <td>-3.743262</td>
    </tr>
    <tr>
      <th>1911</th>
      <td>0.598619</td>
      <td>18.079558</td>
      <td>-0.014088</td>
    </tr>
    <tr>
      <th>1912</th>
      <td>1.298857</td>
      <td>15.447714</td>
      <td>-0.978286</td>
    </tr>
    <tr>
      <th>1913</th>
      <td>1.193315</td>
      <td>16.598886</td>
      <td>-0.734819</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2019</th>
      <td>1.299451</td>
      <td>17.505220</td>
      <td>1.601648</td>
    </tr>
    <tr>
      <th>2020</th>
      <td>0.937432</td>
      <td>19.324863</td>
      <td>2.424590</td>
    </tr>
    <tr>
      <th>2021</th>
      <td>1.099726</td>
      <td>19.115890</td>
      <td>2.783836</td>
    </tr>
    <tr>
      <th>2022</th>
      <td>1.060822</td>
      <td>18.547123</td>
      <td>1.912329</td>
    </tr>
    <tr>
      <th>2023</th>
      <td>1.474451</td>
      <td>18.104396</td>
      <td>2.550824</td>
    </tr>
  </tbody>
</table>
<p>103 rows × 3 columns</p>
</div>




```python
#### Plotting by year
```


```python
df_grp_year.plot(
    subplots=True,
    title="Boulder Time Series Analisys by Year",
    xlabel="Year",
    ylabel="Scale")
```




    array([<Axes: xlabel='Year', ylabel='Scale'>,
           <Axes: xlabel='Year', ylabel='Scale'>,
           <Axes: xlabel='Year', ylabel='Scale'>], dtype=object)




    
![png](Reproducible_Science_From_Theory_To_Practice_files/Reproducible_Science_From_Theory_To_Practice_45_1.png)
    


------------------------------------------------------------------------

------------------------------------------------------------------------

# **THIS ISN’T THE END!** 😄

## From now on we can continue as we wish

#### Have Fun!


```python
%%capture
%%bash
jupyter nbconvert Reproducible_Science_From_Theory_To_Practice.ipynb --to markdown
jupyter nbconvert Reproducible_Science_From_Theory_To_Practice.ipynb --to html
```
