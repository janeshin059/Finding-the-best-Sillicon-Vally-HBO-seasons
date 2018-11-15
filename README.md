# Finding-the-best-Sillicon-Vally-HBO-seasons
using IMDb rate, rotten tomatoes and viewers statistic to find the best episode and seasons
of Sillicon Vally(tv show, HBO)

PROCESS

1. using MySQL in python(pymysql)
2. scraping IMDb rate from the web to get rate for each episode and seasons
3. scraping TOMATOMETER from the web to get rate for each easons
4. scraping the viewers in TVSeriesFinale
/*since the website made it hard to scraping, I just decided to put it in csv file with other data.It would be much less time consuming*/
5. making the data into table format
6. saving the data in MySQL( pymysql)
7. data visualization using Matplotlib

This project is inspired by http://rikunert.com/2017/05/18/star-trek-movies-ratings/
HERE are the list of websites that i used:

https://www.imdb.com/title/tt2575988/episodes?season=5
https://www.rottentomatoes.com/tv/silicon_valley
https://tvseriesfinale.com/tv-show/silicon-valley-season-two-ratings-36236/
