
# Finding-the-best-Sillicon-Vally-HBO-seasons
using IMDb rate, rotten tomatoes and viewers statistic to find the best episode and seasons &trends
of Sillicon Vally(TV show, HBO)

### PROCESS

#### 1. scraping IMDb rate from the web to get rate for each episode and seasons
```
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
```j=1
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

#### 2. scraping TOMATOMETER from the rottentomatoes.com to get rate for each easons

```
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

```
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


#### outcome












This project is inspired by http://rikunert.com/2017/05/18/star-trek-movies-ratings/
HERE are the list of websites that i used:

https://www.imdb.com/title/tt2575988/episodes?season=5
https://www.rottentomatoes.com/tv/silicon_valley
https://tvseriesfinale.com/tv-show/silicon-valley-season-two-ratings-36236/





