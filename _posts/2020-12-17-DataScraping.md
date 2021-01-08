# Summary 
[Data scientists spend about 45% of their time on data preparation tasks, including loading and cleaning data](https://www.datanami.com/2020/07/06/data-prep-still-dominates-data-scientists-time-survey-finds/). As an aspiring data scientist, I aimed to scrape turnstile data from the New York subways. The data is [public](http://web.mta.info/developers/turnstile.html) and the idea for this came as a request, applying for an unpaid internship I was given an assignement( and decided not to follow thru as it was unpaid) but wanted to give it a try. To solve each problem requires solid skills in Pandas, SQL, Beatiful Soup, general problem-solving skills and a solid internet connection. .

# Question A

## What station was the busiest on December $24^{th}$ 2016 ?

The [data](http://web.mta.info/developers/turnstile.html) is split into many files and listed on a single html page. When looking for a single file, I analyzed the formating of the data and used the Windows CTRL+F tool to look for the desired date. Loading the data with the popular data analysis tool, Pandas, filtering was done as every linked data set contains multiple dates. 



```python
import pandas as pd
#Pandas makes it convinent to load data. 
#We use the Python engine to avoid a column naming error, but this could also be done in standard C engine.
df = pd.read_csv('http://web.mta.info/developers/data/nyct/turnstile/turnstile_161224.txt', sep="\s*[,]\s*", header=0, engine = 'python')
```


```python
#Looking at documentation found online, we see if it was loaded properly. 
df.head()
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
      <td>12/17/2016</td>
      <td>03:00:00</td>
      <td>REGULAR</td>
      <td>5967477</td>
      <td>2022101</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>12/17/2016</td>
      <td>07:00:00</td>
      <td>REGULAR</td>
      <td>5967485</td>
      <td>2022116</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>12/17/2016</td>
      <td>11:00:00</td>
      <td>REGULAR</td>
      <td>5967553</td>
      <td>2022233</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>12/17/2016</td>
      <td>15:00:00</td>
      <td>REGULAR</td>
      <td>5967790</td>
      <td>2022331</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A002</td>
      <td>R051</td>
      <td>02-00-00</td>
      <td>59 ST</td>
      <td>NQR456W</td>
      <td>BMT</td>
      <td>12/17/2016</td>
      <td>19:00:00</td>
      <td>REGULAR</td>
      <td>5968186</td>
      <td>2022421</td>
    </tr>
  </tbody>
</table>
</div>




```python
#To check if all the data was loaded properly, we can sum any nulls. 
df.isnull().sum()
```




    C/A         0
    UNIT        0
    SCP         0
    STATION     0
    LINENAME    0
    DIVISION    0
    DATE        0
    TIME        0
    DESC        0
    ENTRIES     0
    EXITS       0
    dtype: int64




```python
df.DATE.unique()
```




    array(['12/17/2016', '12/18/2016', '12/19/2016', '12/20/2016',
           '12/21/2016', '12/22/2016', '12/23/2016'], dtype=object)



This unveiled date structuring for the files. Despite a file upload occuring on 12/24/2016, the file contains dates for the 7 days before. Thus the desired date is on the 12/31/2016 page. 

With this in mind, we need to grab the next data posting. 


```python
df = pd.read_csv('http://web.mta.info/developers/data/nyct/turnstile/turnstile_161231.txt', sep="\s*[,]\s*", header=0, engine = 'python')
df.DATE.unique()
```




    array(['12/24/2016', '12/25/2016', '12/26/2016', '12/27/2016',
           '12/28/2016', '12/29/2016', '12/30/2016'], dtype=object)



With this we find "busiest" in the Exit and Entry categories. 'Busiest' is ambiguous and is not a defined variable in the data set so we must filter max ENTIRES,  EXITS, and both.  


```python
#Grabbing the desired date
target_date =  df.loc[ df["DATE"] == '12/24/2016']

#Look for the max exits
target_date = target_date.groupby(['STATION']).sum()
target_date.EXITS.loc[target_date.EXITS == target_date.EXITS.max()]
```




    STATION
    42 ST-PORT AUTH    37456538982
    Name: EXITS, dtype: int64




```python
#Looking for the max entries
target_date.loc[target_date.ENTRIES == target_date.ENTRIES.max()]
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
      <th>ENTRIES</th>
      <th>EXITS</th>
    </tr>
    <tr>
      <th>STATION</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>42 ST-PORT AUTH</th>
      <td>45549967830</td>
      <td>37456538982</td>
    </tr>
  </tbody>
</table>
</div>




```python
#We can sum the entries and exits, then find the max value. With .loc we can find the position correlated with this. 
busiest = target_date[['ENTRIES', 'EXITS']].sum(axis = 1).max()
```


```python
#We can save the position
target_date[['ENTRIES', 'EXITS']].sum(axis=1).loc[ target_date[['ENTRIES', 'EXITS']].sum(axis =1) == busiest ].index[0]

```




    '42 ST-PORT AUTH'



Overall the busiest Station on 12/24/2016 was 42 ST-PORT AUTH.

# Question b
## Create a bar plot showing the 5 stations with the least entries and exits in the 2012 - 2014 period ( 3 years). The x-axis should have the station name and the y-axis is the sum of entries $\&$ exits. Submit your work and final output documents below. ${ }^{*}$

In 2014 MTA decided to restructure their data because of this we need to handle the entire date range as two subsets. The first subset will contain the well structed data, the other 'messy' data. Rather than collect hundreds of links by hand, we can use html knowledge with bs4 to automate this process. 


```python
from bs4 import BeautifulSoup as bs
import requests as req
```


```python
#Firt get the html page.
page = req.get("http://web.mta.info/developers/turnstile.html")

#Next we want to parse the information for further searching. 
soup = bs(page.text, 'html.parser')

#The links are contained in  div' and class. Finding this will allow up to quickly pull any links 
container = soup.find( 'div', class_ = 'span-84 last')

#Furthermore, we need the actual links. Using href = True, allows us to grab the href category. 
links = container.find_all("a", href =True)
```

Date is an important feature which we can index by, filter on, or even save by. Unfortantely the dates need to be cleaned up but with a little manipulation we can make it work.


```python
# Experimenting we can view how the date is displayed
links[0].text.split(', ')[1:3]
```




    ['January 02', '2021']




```python
# We can also grab the exact items. 
links[0].text.split(', ')[1]
```




    'January 02'




```python
links[0].text.split(', ')[1][-2:]
```




    '02'



With the structure of the links we can iterate thru each one and format. 


```python
#datetime will allow formating and make it easier to change the month name to a number. 
from datetime import datetime
link_frame = pd.DataFrame()
```


```python
#Adding to empy lists will work.
url = []
years = []
m = []
d =[]
for i in links:
    url.append(i['href'])

#With the above exploration, year, day, and month. 
for i in links:
    #Appends the year 
    years.append(int(i.text.split(', ')[2]))
    #Appends the day of the month
    d.append(int(i.text.split(', ')[1][-2:]))
    
    #Datetime has a function to change the first 3 characters of a month to its respective numeric place
    #Thus we grab the first 3 characters of each month
    month_name = i.text.split(', ')[1][:3]
    datetime_object = datetime.strptime(month_name, "%b")
    month_number = datetime_object.month
    m.append(int(month_number))

```


```python
#Lastly, we can create the proper date structure. 
date = []
for i in range(len(years)):
    #While datetime will create the date stamp for us, it saves it a unique manner we cannnot search by. 
    #Thus we make sure to save it as a string. 
    date.append( str(datetime( year = years[i], month = m[i], day = d[i]).date()) )
```


```python
#Adding the lists we can look at anything we desite.
link_frame['url'] = url
link_frame['dates'] = date
```


```python
#Now that the link frame is complete, we want to get the correct range. 
link_frame.loc[ (link_frame['dates'] > '2011-12-31') &  (link_frame['dates'] <='2015-01-01') ]
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
      <th>url</th>
      <th>dates</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>314</th>
      <td>data/nyct/turnstile/turnstile_141227.txt</td>
      <td>2014-12-27</td>
    </tr>
    <tr>
      <th>315</th>
      <td>data/nyct/turnstile/turnstile_141220.txt</td>
      <td>2014-12-20</td>
    </tr>
    <tr>
      <th>316</th>
      <td>data/nyct/turnstile/turnstile_141213.txt</td>
      <td>2014-12-13</td>
    </tr>
    <tr>
      <th>317</th>
      <td>data/nyct/turnstile/turnstile_141206.txt</td>
      <td>2014-12-06</td>
    </tr>
    <tr>
      <th>318</th>
      <td>data/nyct/turnstile/turnstile_141129.txt</td>
      <td>2014-11-29</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>465</th>
      <td>data/nyct/turnstile/turnstile_120204.txt</td>
      <td>2012-02-04</td>
    </tr>
    <tr>
      <th>466</th>
      <td>data/nyct/turnstile/turnstile_120128.txt</td>
      <td>2012-01-28</td>
    </tr>
    <tr>
      <th>467</th>
      <td>data/nyct/turnstile/turnstile_120121.txt</td>
      <td>2012-01-21</td>
    </tr>
    <tr>
      <th>468</th>
      <td>data/nyct/turnstile/turnstile_120114.txt</td>
      <td>2012-01-14</td>
    </tr>
    <tr>
      <th>469</th>
      <td>data/nyct/turnstile/turnstile_120107.txt</td>
      <td>2012-01-07</td>
    </tr>
  </tbody>
</table>
<p>156 rows × 2 columns</p>
</div>



With the filtering we have two different structes. MTA restructed data on "2014-10-18". Thus we need to futher divide this subset for data cleaning. 


```python
#We are now making subsets based on data structure. 
messy_links = link_frame.loc[ (link_frame['dates'] > '2011-12-31') &  (link_frame['dates'] <'2014-10-18') ]
```


```python
#Clean structured data
clean_links = link_frame.loc[ (link_frame['dates'] > '2014-10-17') &  (link_frame['dates'] <='2015-01-01') ]
```

The clean dfs could simply be appended to a larger dataframe while the messy ones need to be restructered before usage

We are working with many CSV pulls, thus we are going to write to a single CSV file. In addition, any file written before $10/18/14$ will have entries and exits divided into n categories. We must sum these up for usage with the newer files. 


```python
#This is the base used in all links
base_url = 'http://web.mta.info/developers/'
```


```python
clean_links.reset_index(drop = True, inplace =True)
messy_links.reset_index(drop =True, inplace =True)
```

# Grabing Data


```python
#Internet connections sometimes break and ruin the process of grabbing many data sets at once.
#To fix this it is safer to save each dataset onto the machine running the script.
#More so, it is quicker to read from our machine than constantly going to a website that may deny our requests. 
#The OS library is useful to creating new folders, saving dataframes and doing so with little work 
import os 
```

## Clean path 


```python
#First we see if the clean_data folder has already been created. 
if not os.path.exists('clean_data'):
    os.makedirs('clean_data')

#Rather than always updating the data, we can see if it already has been done. 
for i, date in enumerate(clean_links['dates']):
    #To verify we save each file with the upload date as the name. If it is not there we grab it and save it.
    if not os.path.exists( 'clean_data/{}.csv'.format(date) ):
        df = pd.read_csv(base_url+clean_links['url'][i], sep="\s*[,]\s*", header=0, engine = 'python')
        df.to_csv( 'clean_data/{}.csv'.format(date), index =False )
    
    #If it is there we want to save time and skip it. 
    else: 
        print('Already have {}'.format(date) )
        
```

    Already have 2014-12-27
    Already have 2014-12-20
    Already have 2014-12-13
    Already have 2014-12-06
    Already have 2014-11-29
    Already have 2014-11-22
    Already have 2014-11-15
    Already have 2014-11-08
    Already have 2014-11-01
    Already have 2014-10-25
    Already have 2014-10-18
    

# Messy paths


```python
#Again we save the data locally. 
if not os.path.exists('messy_data'):
    os.makedirs('messy_data')

#When running the urls forward, I ran into an error with the python parser. 
#I attempted to grab the other files by running it from the end of the list and when I did so,
#The python parser no longer ran an error. If this happens just follow my method and the if loop should save time.  
for i, date in enumerate(messy_links['dates'][::-1]):
    if not os.path.exists( 'messy_data/{}.csv'.format(date) ):
        df = pd.read_csv(base_url+messy_links['url'][i], sep="\s*[,]\s*", header=None, engine = 'python')
        df.to_csv( 'messy_data/{}.csv'.format(date),index = False )
    
    else: 
        print('Already have {}'.format(date) )
        
```

    Already have 2012-01-07
    Already have 2012-01-14
    Already have 2012-01-21
    Already have 2012-01-28
    Already have 2012-02-04
    Already have 2012-02-11
    Already have 2012-02-18
    Already have 2012-02-25
    Already have 2012-03-03
    Already have 2012-03-10
    Already have 2012-03-17
    Already have 2012-03-24
    Already have 2012-03-31
    Already have 2012-04-07
    Already have 2012-04-14
    Already have 2012-04-21
    Already have 2012-04-28
    Already have 2012-05-05
    Already have 2012-05-12
    Already have 2012-05-19
    Already have 2012-05-26
    Already have 2012-06-02
    Already have 2012-06-09
    Already have 2012-06-16
    Already have 2012-06-23
    Already have 2012-06-30
    Already have 2012-07-07
    Already have 2012-07-14
    Already have 2012-07-21
    Already have 2012-07-28
    Already have 2012-08-04
    Already have 2012-08-11
    Already have 2012-08-18
    Already have 2012-08-25
    Already have 2012-09-01
    Already have 2012-09-08
    Already have 2012-09-15
    Already have 2012-09-22
    Already have 2012-09-29
    Already have 2012-10-06
    Already have 2012-10-13
    Already have 2012-10-20
    Already have 2012-10-27
    Already have 2012-11-03
    Already have 2012-11-10
    Already have 2012-11-17
    Already have 2012-11-24
    Already have 2012-12-01
    Already have 2012-12-08
    Already have 2012-12-15
    Already have 2012-12-22
    Already have 2012-12-29
    Already have 2013-01-05
    Already have 2013-01-12
    Already have 2013-01-19
    Already have 2013-01-26
    Already have 2013-02-02
    Already have 2013-02-09
    Already have 2013-02-16
    Already have 2013-02-23
    Already have 2013-03-02
    Already have 2013-03-09
    Already have 2013-03-16
    Already have 2013-03-23
    Already have 2013-03-30
    Already have 2013-04-06
    Already have 2013-04-13
    Already have 2013-04-20
    Already have 2013-04-27
    Already have 2013-05-04
    Already have 2013-05-11
    Already have 2013-05-18
    Already have 2013-05-25
    Already have 2013-06-01
    Already have 2013-06-08
    Already have 2013-06-15
    Already have 2013-06-22
    Already have 2013-06-29
    Already have 2013-07-06
    Already have 2013-07-13
    Already have 2013-07-20
    Already have 2013-07-27
    Already have 2013-08-03
    Already have 2013-08-10
    Already have 2013-08-17
    Already have 2013-08-24
    Already have 2013-08-31
    Already have 2013-09-07
    Already have 2013-09-14
    Already have 2013-09-21
    Already have 2013-09-28
    Already have 2013-10-05
    Already have 2013-10-12
    Already have 2013-10-19
    Already have 2013-10-26
    Already have 2013-11-02
    Already have 2013-11-09
    Already have 2013-11-16
    Already have 2013-11-23
    Already have 2013-11-30
    Already have 2013-12-07
    Already have 2013-12-14
    Already have 2013-12-21
    Already have 2013-12-28
    Already have 2014-01-04
    Already have 2014-01-11
    Already have 2014-01-18
    Already have 2014-01-25
    Already have 2014-02-01
    Already have 2014-02-08
    Already have 2014-02-15
    Already have 2014-02-22
    Already have 2014-03-01
    Already have 2014-03-08
    Already have 2014-03-15
    Already have 2014-03-22
    Already have 2014-03-29
    Already have 2014-04-05
    Already have 2014-04-12
    Already have 2014-04-19
    Already have 2014-04-26
    Already have 2014-05-03
    Already have 2014-05-10
    Already have 2014-05-17
    Already have 2014-05-24
    Already have 2014-05-31
    Already have 2014-06-07
    Already have 2014-06-14
    Already have 2014-06-21
    Already have 2014-06-28
    Already have 2014-07-05
    Already have 2014-07-12
    Already have 2014-07-19
    Already have 2014-07-26
    Already have 2014-08-02
    Already have 2014-08-09
    Already have 2014-08-16
    Already have 2014-08-23
    Already have 2014-08-30
    Already have 2014-09-06
    Already have 2014-09-13
    Already have 2014-09-20
    Already have 2014-09-27
    Already have 2014-10-04
    Already have 2014-10-11
    



# Merging Data

# 1. Merging individual files 

While we have all the file we need, it is not in one workable data frame. This is because we must work the Messy data to work with the clean data. 

## Clean Data


```python
#The dataframe we are using to join on is called finaldf. Despite this, this is an excellent way to showcase how a varibale 
#can be recycled.
finaldf= pd.DataFrame()

if not os.path.exists('joined_clean_data.csv'):
    for i, date in enumerate(clean_links['dates']):

        df = pd.read_csv('clean_data/{}.csv'.format(date))

        if finaldf.empty:
            finaldf = df
        else: 
            finaldf = finaldf.merge(df, how = 'outer')
    #Since the data has been merged, we can save it.
    finaldf.to_csv('joined_clean_data.csv',index = False)
```

## Messy Data


```python
#We clean anything that was in finaldf by reassigning it as an empty dataframe
finaldf= pd.DataFrame()

if not os.path.exists('joined_messy_data.csv'):
    for i, date in enumerate(messy_links['dates']):
        df = pd.read_csv('messy_data/{}.csv'.format(date),index_col=False)
        df.fillna(value = 0 , inplace = True) 
        if finaldf.empty:
            finaldf = df
        else: 
            finaldf = finaldf.merge(df, how = 'outer')
    finaldf.to_csv('joined_messy_data.csv', index = False)
```

# Adding STATIONS to the messy data

In order to map this, we must first find how the mapping works from the clean files.

## Cleaned data

The cleaned data has the "STATION" column that is missing on messy data. Lucikly a quick look at the data set shows that station can be tracked from 'C/A' and 'UNIT'.


```python
#Once again we clear anything saved in finaldf by reassigning it as an empyty df. 
finaldf= pd.DataFrame()
#loading the merged clean data
df =  pd.read_csv( 'joined_clean_data.csv' )
df.columns
```




    Index(['Unnamed: 0', 'C/A', 'UNIT', 'SCP', 'STATION', 'LINENAME', 'DIVISION',
           'DATE', 'TIME', 'DESC', 'ENTRIES', 'EXITS'],
          dtype='object')




```python
if finaldf.empty:
    #A deep copy avoids chaning the original copy by chaning the reference number. 
    finaldf = df.copy(deep= True)
else: 
    finaldf = finaldf.merge(df, how = 'outer')
```


```python
#If we didn't deep copy, this df.drop would also change finaldf
df.drop( ['Unnamed: 0', 'LINENAME', 'DIVISION',
       'DATE', 'TIME', 'DESC', 'ENTRIES', 'EXITS', 'SCP'], axis = 1,inplace = True )
stations = df.STATION.unique()
```


```python
df.head()
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
      <th>C/A</th>
      <th>UNIT</th>
      <th>STATION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A002</td>
      <td>R051</td>
      <td>LEXINGTON AVE</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A002</td>
      <td>R051</td>
      <td>LEXINGTON AVE</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A002</td>
      <td>R051</td>
      <td>LEXINGTON AVE</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A002</td>
      <td>R051</td>
      <td>LEXINGTON AVE</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A002</td>
      <td>R051</td>
      <td>LEXINGTON AVE</td>
    </tr>
  </tbody>
</table>
</div>




```python
#We use count not because we want to count how many of each station there is, but because it does the job of narrowing 
#"C/A" and "UNIT" to the staiton
path = df.groupby(['C/A','UNIT','STATION']).count()

#The best way to map is with a dictonary as it has O(1) runtime. 
station_mapping = {}

#path was saved as tuple with 3 elements. thus we need to grab each element. The last being station
for i, j, k in path.index:
    station_mapping.update({(i,j):k })
```


```python
finaldf.drop( ['Unnamed: 0', 'C/A', 'UNIT','LINENAME', 'DIVISION','DATE', 'TIME', 'SCP', 'DESC'], axis =1 ,inplace= True)
```

With the mapping done, we can apply this to the messy data.

## Messy data

The question makes it clear that the only columns we should be intrested in are: station, entries, and exits. 


```python
finaldf.columns
```




    Index(['STATION', 'ENTRIES', 'EXITS'], dtype='object')



Looking at documentation its clear that the station is the first column. The ENTRIES begin in column 6, the next in column 11, the next in column 16, etc. The EXITS begin in column 7, then column 12, then column 17, etc.  


```python
entry_columns = ['{}'.format(i) for i in range(6,43,5)]
exit_columns = [ '{}'.format(i) for i in range(7,43,5)]
```


```python
#Creates a df that will contained the structured information.
structdf = pd.DataFrame()
```


```python
#We need to load the saved data    
df = pd.read_csv('joined_messy_data.csv')

#When initally creating my code, I did not use index = False when saving. 
#This created an index in the csvfile. We get rid of this saved index with the line below.
df.drop('Unnamed: 0',axis = 1, inplace = True)

#lastly, we fill any missing or Nas with the value 0.
df.fillna(0,inplace = True )


#Finds the sum of all columns
structdf['C/A'] = df['0']
structdf['UNIT'] = df['1']
structdf['ENTRIES'] = df[entry_columns].sum()
structdf['EXITS'] = df[exit_columns].sum()
    
#This will clear any station that has negative EXITS and replace the value with  a nan
# While I do this, there could be some underlying reason the number is negative 
structdf[(structdf.EXITS.gt(0) == True)]
structdf.fillna(0,inplace = True)
```


```python
#Lastly, we need to map the station. In my opinion, its easiest on a series.
structdf['STATION'] = pd.Series(zip(structdf['C/A'],structdf['UNIT'] ) ).map(station_mapping)
```


```python
if finaldf.empty: 
    finaldf = structdf
else: 
    structdf.drop(['C/A', 'UNIT'], axis= 1, inplace = True)
    finaldf = finaldf.merge(structdf, how = 'outer')
```


```python
finaldf = finaldf.groupby(['STATION']).sum()
```

# Visualisation 


```python
#Sort_values does it in ascending order. thus the top 5 will get min values. 
finaldf['ENTRIES'].sort_values().head(5)
```




    STATION
    PATH WTC 2           8910435.0
    NEWARK HM HE       157764927.0
    BEACH 105 ST       437005856.0
    NEWARK HW BMEBE    539184990.0
    ATLANTIC AVE       539456104.0
    Name: ENTRIES, dtype: float64




```python
entries = finaldf['ENTRIES'].sort_values().head(5).plot(kind = 'bar',  figsize= (12,4))
entries.set_ylabel("Frequency in hundreds of millions")
```




    Text(0, 0.5, 'Frequency in hundreds of millions')




![png](output_69_1.png)



```python
#Personally, a horizontal bar graph looks better. 
entries = finaldf['ENTRIES'].sort_values().head(5).plot(kind = 'barh',  figsize= (12,4))
entries.set_xlabel("Frequency in hundreds of millions")
```




    Text(0.5, 0, 'Frequency in hundreds of millions')




![png](output_70_1.png)


Despite the data being summed into groups. Each seperate df was summed seperatly. We need to sum them all at the end to find the final number. 

I'm certain the grouping in this manner will produce station for each C/A and UNIT. If we apply this logic to the messy data, we can add station to that data set. 


```python
exits = finaldf['EXITS'].sort_values().head(5).plot(kind = 'bar',  figsize= (12,4))
exits.set_ylabel("Frequency in hundreds of millions")
```




    Text(0, 0.5, 'Frequency in hundreds of millions')




![png](output_73_1.png)



```python
#Personally, a horizontal bar graph looks better. 
exits = finaldf['EXITS'].sort_values().head(5).plot(kind = 'barh',  figsize= (12,4))
entries.set_xlabel("Frequency in hundreds of millions")
```




    Text(0.5, 17.200000000000003, 'Frequency in hundreds of millions')




![png](output_74_1.png)


# Summary 

In this assignment we used many differeny Python libraries and problem solving skills that a Data Scientist often uses. Considering the complexity of the data scraping and my ability to achieve results, I am confident in my data scraping skills and problem solving abilities using Python.
