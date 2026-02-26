# Spotify_IntermediateLevel_SQL_Practice_Project

**Project Overview**
This is a SQL practice project focused on analyzing a Spotify dataset to strengthen core data analysis skills. The project covers database creation, bulk data import, data cleaning, exploratory data analysis (EDA), and solving business-oriented queries. Various SQL concepts were applied, including aggregate functions, GROUP BY, subqueries, Common Table Expressions (CTEs), and window functions such as DENSE_RANK and cumulative SUM.


**The objective of this project is to strengthen SQL concepts including:**

Database creation
Data cleaning
Aggregate functions
GROUP BY & ORDER BY
Subqueries
Common Table Expressions (CTEs)
Window Functions
Data filtering & transformation

**Database Setup**
**Create Database & Table**

```sql
Create database Spotify;
Use Spotify;

Create table Spotify(
					Artist Varchar(100),	
					Track Varchar(300),	
					Album Varchar(300),	
					Album_type Varchar(100),	
					Danceability Float,	
					Energy Float,	
					Loudness Float,
					Speechiness Float,	
					Acousticness Float, 	
					Instrumentalness Float,	
					Liveness Float,	
					Valence	Float,
					Tempo Float,	
					Duration_min Float,	
					Title Varchar(200),	
					Channel	Varchar(100),
					Views BigInt,	
					Likes BigInt,	
					Comments BigInt,	
					Licensed varchar(10),	
					official_video varchar(10),	
					Stream BigInt,
					EnergyLiveness Float,	
					most_playedon Varchar(30)
					);
```
**--Bulk upload data**
```sql
BULK insert Spotify
from 'D:\Ratna\Personal Folder\BUSINESS ANALYTICS\SQL\SPOTIFY\cleaned_dataset.csv'
with
	(
	FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDQUOTE = '"', -- Text values in the field are wrapped in " ", so lets treat them same
    CODEPAGE = '65001',-- File is UTF-8 (Unicode Transformation Format – 8 bit.meaning it tells the computer how to store letters, symbols, numbers, and emojis as binary (0s and 1s).)
    TABLOCK -- Lock table during insert (faster)
);

Select * from Spotify;
```

**EDA**
**Count the total rows**

```sql
Select count(*) as total_rows
from Spotify;
```

**How many artists we have?**

```sql
Select count(distinct(artist)) as disinct_artists
from Spotify;
```
**How many Albums we have?**

Select count(distinct(Album)) as no_Albums
from Spotify;

**Different types of Albums**

Select count(distinct(Album_type))
from Spotify;

Select distinct(Album_type)
from Spotify;

**Select Maximum duration song**

Select max(Duration_min) as Max_duration
from Spotify;

**Select Minimum duration song**

Select min(Duration_min) as Min_duration
from Spotify; -- here the Min duration is 0 hence this is wrong

```sql
Select 
*
from Spotify
where duration_min = 0 ;
```
--there are two records with Zero duration hence delete it
```sql
Delete
from Spotify
where Duration_min = 0;
```

**Check different types of channel**
```sql
Select
distinct(Channel)
from Spotify;
```
**Most Played on which platform are available**
```sql
Select distinct(most_playedon)
from Spotify;
```
---------------------------------------------------
**Data Analysis challenges Easy Level**
---------------------------------------------------

**Retreive the names of all tracks that have more than 1 billion streams**
```sql
Select track, Stream
from Spotify
where Stream > 1000000000;
```
**List all the albums along with their respective artists**
```sql
Select distinct(Album), Artist  -- one album can have 2 artist
from 
Spotify
order by Album
```
**Get the total number of comments for tracks where licensed = True**
```sql
Select count(comments)
from Spotify where licensed LIKE 'TRUE';
```
**TOTAL COOMMETS FOR  tracks where licensed = True**
```sql
Select Sum(comments)
from Spotify where licensed LIKE 'TRUE';
```
**Find all the tracks that belong to the album type Single**
```sql
Select Track, Album_type
from Spotify
where Album_type like 'single';
```
**Count the total number of tracks by each artists**
```sql
Select 
Artist, count(Track) as total_track
from Spotify
Group by Artist
Order by count(Track) desc;
```
---------------------------------------------------------
--Intermediate Level --
---------------------------------------------------------

**Calculate the average danceability of tracks in each album**
```sql
Select Album, Track , Round(Avg(Danceability),4) As average_danceability
from Spotify
Group by Album, Track
Order by Avg(Danceability) DESC;
```
**Find the Top 5 tracks with the highest energy values**
```sql
Select distinct Top 5 Track, EnergyLiveness
from Spotify
Order by EnergyLiveness desc;
```
**List all tracks along with their total views and total likes where official vedio = True**
```sql
Select Track, Sum(Views) as 'total_Views',sum(Likes) as 'total_likes'
from Spotify
where official_video like 'TRUE'
group by Track
order by Sum(Views) desc;
```
**For each album, calculate the total views of all associated tracks**
```sql
Select
album, track, sum(views) as total_views
from Spotify
Group by album, track
order by sum(views) desc
```

**Retrieve the track names that have been streamed on Spotify more than Youtube**
```sql
Select track
from Spotify
where most_playedon = 'Spotify';
```
**Find out overall, mostly tracks are played on You tube or Spotify?**
```sql
Select
most_playedon, count(most_playedon) as total_numbers
from Spotify
group by most_playedon;
```
----------------------------------------------------------------------
**Advanced Problems**
----------------------------------------------------------------------

**Find the top 3 most-viewed tracks for each artist using window functions**
```sql
Select * from(
				Select Artist, Track, Views,
				Dense_Rank() over (Partition by Artist order by Views desc) as Ranking
				from Spotify	
			 ) as Rk
where Ranking <= 3
```

**Write a query to find tracks where the liveness score is above the average**
```sql
Select Track, Liveness
from Spotify
where Liveness > (Select ROUND(avg(Liveness),2) as avg_liveness from spotify)
 ```
**Use a with clause to calculate the difference between the highest and the lowest energy values for tracks in each album**
```sql
with CTE as
(
Select album, Round(max(Energy),2) as maximum, Round(min(Energy),2) as minimum
from Spotify
group by album
)
Select album, maximum - minimum as energy_difference
from CTE
```
**Find the tracks where the energy-to-liveness ratio is greater than 1.2**
```sql
Select track, (Energy/Liveness) as Energy_liveness_ration
from
Spotify
where (Energy/Liveness) > 1.2
```

**Calculate the cumulative sum of likes for tracks ordered by the number of views, using windows functions**
```sql
Select track, Views, Likes, Sum(likes) over(order by Views) as Cumulutive_sum
from Spotify;
```

**Key SQL Concepts Practiced**

Aggregate Functions (SUM, AVG, COUNT, MAX, MIN)
GROUP BY
ORDER BY
DISTINCT
Subqueries
CTE (WITH clause)
Window Functions (DENSE_RANK, SUM OVER)
Data Cleaning
Filtering with WHERE







