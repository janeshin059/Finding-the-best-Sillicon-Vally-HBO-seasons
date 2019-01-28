
# Finding-the-best-Sillicon-Valley-HBO-seasons
using IMDb rate, rotten tomatoes and viewers statistic to find the best episode and seasons &trends
of Sillicon Valley(TV show, HBO)

### PROCESS

#### 1. scraping IMDb rate from the web to get rate for each episode and seasons
```python
import pandas as pd #for data array handling
from bs4 import BeautifulSoup as soup
# for website parsing and scraping (rotten tomatoes)
from urllib.request import urlopen as uReq
import re#for reqular expressions
#from pandas.lib import Timestamp
from ggplot import * #for plotting

filename= "episodes.csv"
filename2="seasons.csv"
f=open(filename, "w")
f2=open(filename2, "w")

from pandas import Series,DataFrame
```
```python
j=1
a=0
b=0
k=0
df_e=pd.DataFrame()
df_s=pd.DataFrame({'Season': [1,2,3,4,5]})

for i in range(1,6):
    SeasonAVG=0
    season_avg=0       
    idx=0
    episode_num=0
    df_e = df_e.append({'Season': i},ignore_index=True)
    imdb_url = 'https://www.imdb.com/title/tt2575988/episodes?season=%d' %i
    iClient = uReq(imdb_url) #opening the connection, downloading the webpage
    ipage_html = iClient.read()
    iClient.close()
    
    #do html parsing
    ipage_soup = soup(ipage_html,"html.parser")
    episodenames = ipage_soup.findAll("a",{"itemprop":"name"})
    rates = ipage_soup.findAll("span",{"class":"ipl-rating-star__rating"})
    for episodename in episodenames:
            k+=1
            if k<48:
                ep_name = episodename["title"]
                df_e = df_e.append( {'Episode_Name':ep_name},ignore_index=True)
                df_e["Episode_Name"][a]=ep_name
                a+=1
                for rate in rates:
                    if idx==0 or idx%23==0:
                        imdb_rate = rate.text
                        season_avg+=float(imdb_rate)
                        df_e = df_e.append( {"IMDb_rate":imdb_rate},ignore_index=True)
                        
                        df_e["IMDb_rate"][b]=imdb_rate
                        b+=1
                        episode_num+=1
                    idx+=1
    
    SeasonAVG=float(season_avg/episode_num)
    df_s = df_s.append( {"IMDb_season_avg":SeasonAVG},ignore_index=True)
    df_s["IMDb_season_avg"][i-1]=SeasonAVG
                
```

after made the csv file, I revised it a little bit to make it easy for uploading it to BigQuery.

#### 2. scraping TOMATOMETER from the rottentomatoes.com to get rate for each easons

```python
my_url = 'https://www.rottentomatoes.com/tv/silicon_valley'
uClient = uReq(my_url) #opening the connection, downloading the webpage
page_html = uClient.read()
uClient.close()
#doing html parsing
page_soup = soup(page_html,"html.parser")
tomatometer = page_soup.findAll("span",{"class":"meter-value"})

i=6
for Tomatometers in tomatometer:
    TM = tomatometer[i].text
    i=i-1
    if i<=0:break
    df_s = df_s.append( {"Tomatometers":TM},ignore_index=True)
    df_s["Tomatometers"][i-1] = TM
```
#### 3. scraping the viewers in TVSeriesFinale


I tried to scrape the website, but the website made it hard to scraping.
to consume much less time,
I just decided to put it in xslx file. (viewers_all)
then i copied the viewer numbers and made it to seperate csv files.(viewers.csv &viewers_avg.csv)

then concatenate it with episodes.csv and seasons.csv

```python
df_v = pd.read_csv("viewers.csv",names=["viewers"])
df_va = pd.read_csv("viewers_avg.csv",names=["viewers_season_avg"])
df_ev = pd.concat([ df_e,df_v],axis=1)
df_sva = pd.concat([df_s,df_va],axis =1)

df_e.to_csv("episodes.csv",mode='w')
df_s.to_csv("seasons.csv", mode='w')
df_ev.to_csv("episodes_all.csv",mode='w')

df_sva.to_csv("seasons_all.csv", mode='w')
f2.close()
f.close()
```

#### 4. making the data into table format
#### 5. saving the data using MySQL& Google BigQuery

the data can be saved in both ways, but in here I used BigQuery
(the mySQL query is in separate file: SV.txt)


how to make table in BigQuery: https://cloud.google.com/bigquery/docs/tables


#### 6. data visualization with Google Data Studio

<img width="960" alt="bigd" src="https://user-images.githubusercontent.com/41497195/48993550-a2d8ea00-f181-11e8-9afc-d538ab232244.PNG">



#### outcome
<img width="539" alt="a" src="https://user-images.githubusercontent.com/41497195/48993955-0b28cb00-f184-11e8-8579-755a65619459.PNG">
<img width="319" alt="b" src="https://user-images.githubusercontent.com/41497195/48993961-0f54e880-f184-11e8-8347-a9693a03d26a.PNG">
<img width="502" alt="c" src="https://user-images.githubusercontent.com/41497195/48993968-13810600-f184-11e8-8737-8890b8d8c15d.PNG">
<img width="502" alt="d" src="https://user-images.githubusercontent.com/41497195/48994044-72df1600-f184-11e8-9718-5bf3e7c60832.PNG">
<img width="501" alt="e" src="https://user-images.githubusercontent.com/41497195/48993966-13810600-f184-11e8-980b-2730ff44334d.PNG">



##### why viewers were dramatically dropped in season 4?

the IMDb rate and rotten tomatoes rate were not that dramatically dropped in season 4. 
the viewer number of the first episode of season 4 is already sharply dropped.

<img width="461" alt="exc" src="https://user-images.githubusercontent.com/41497195/48993971-18de5080-f184-11e8-953f-bb250384b605.PNG">

if the show has problems in its storyline, viewers would have been gradually reduced while the season 4 is going on.


Also,there were no problems or huge differences at the cast of Sillicon Vally season 4.

So, we can know that the problem of sharply dropped viewers was
NOT from the inside of the show.
we have to find the problems in the OUTSIDE.


#### 'Sunday cable ratings: ‘Silicon Valley’ suffers without ‘Game of Thrones’ as lead-in'

from: <https://tvbythenumbers.zap2it.com/daily-ratings/sunday-cable-ratings-april-23-2017/> 

https://www.reddit.com/r/television/comments/6brdgv/silicon_valleys_live_ratings_have_taken_quite_a/


- in America, if we want to watch a broadcast then we should pay for that specific channel. 
in Sillicon Vally season 1~3, 'Game of Thrones', which is very popular TV show in HBO, aired in the same period with Sillicon Vally


-But in Season 4, Game of Thrones has delayed its air schedule.
-So people doesn't want to pay $15 for watching only Sillicon Vally.
Before, with the same amount of money they can watch two popular TV shows.
But since Game of Thrones was delayed they can only watch one TV show- so they didn't paid for the channel.



#### references & used websites

This project is inspired by http://rikunert.com/2017/05/18/star-trek-movies-ratings/

HERE is the list of websites that i used:

https://www.imdb.com/title/tt2575988/episodes?season=5
https://www.rottentomatoes.com/tv/silicon_valley
https://tvseriesfinale.com/tv-show/silicon-valley-season-two-ratings-36236/





