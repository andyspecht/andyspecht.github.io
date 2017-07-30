---
layout: post
title: OES Tech Wages - 2017 Update
image: ../img/wagesUpdate/computerCat.jpg
tags: [OES Data, Data, Salaries, SQL Server]
---

About half a year ago, I created a database of the OES California salary data and analyzed a bit of it (see [this post](https://andyspecht.github.io/2017-01-29-new-project-wages-ca/), [this post](https://andyspecht.github.io/2017-02-04-dba-ca-salaries/), [this post](https://andyspecht.github.io/2017-02-09-more-avg-salaries/), [and this post](https://andyspecht.github.io/2017-02-17-whats-hot-in-ca/)). I just saw that they've [released 2017 data](http://www.labormarketinfo.edd.ca.gov/data/oes-employment-and-wages.html). That means it's time to update the dataset, and we'll take a look and see if there is anything interesting. [Here's a link to the Github repo](https://github.com/andyspecht/OES-California-SQL), so you can look at the data yourself. I'll probably do at least a couple posts on it, and I'll start by looking at tech in general (rather than specific jobs).


### General Tech Trends

First, let's take a look at which areas have had the largest changes in tech employment between 2016 and 2017. We'll set up some CTE's that we can use for multiple queries.

```sql
WITH Data2017 
AS
(
SELECT
	  [MSA_Code]
	  ,[Location]
      ,[SOC_Code]
      ,[Occupation]
      ,[Year]
      ,[Employment_Estimate]
      ,[Mean_Annual_Wage]
  FROM [CaliforniaSalaries].[dbo].[TotalData]
  WHERE [Year] = '2017'
  AND SOC_Code = '15-0000'
)
,Data2016
AS
(
SELECT
	  [MSA_Code]
	  ,[Location]
      ,[SOC_Code]
      ,[Occupation]
      ,[Year]
      ,[Employment_Estimate]
      ,[Mean_Annual_Wage]
  FROM [CaliforniaSalaries].[dbo].[TotalData]
  WHERE [Year] = '2016'
  AND SOC_Code = '15-0000'
)
SELECT 
Data2017.[Location]
,ROUND(CONVERT(FLOAT,(Data2017.[Employment_Estimate] - Data2016.[Employment_Estimate]))
	/Data2016.[Employment_Estimate] * 100,2) AS Emp_Est_Increase_Perc
FROM Data2017 
	INNER JOIN Data2016
		ON Data2017.MSA_Code = Data2016.MSA_Code
		AND Data2017.SOC_Code = Data2016.SOC_Code
WHERE Data2017.[Employment_Estimate] > 600
ORDER BY ROUND(CONVERT(FLOAT,(Data2017.[Employment_Estimate] - Data2016.[Employment_Estimate]))
	/Data2016.[Employment_Estimate] * 100,2) DESC
```

We will only include places with more than 600 tech jobs in 2017. Here are the biggest movers:

**Location** | **Employment Change** |
Chico, CA |	55.29% |
San Luis Obispo-Paso Robles-Arroyo Grande, CA |	12.06% |
San Francisco-Redwood City-South San Francisco, CA |	11.06% |
Santa Maria-Santa Barbara, CA |	10.77% |
Los Angeles-Long Beach-Glendale MD, CA | 9.91% |
Anaheim-Santa Ana-Irvine, CA | 8.75% |
**...California Statewide...** | **5.69%** |
Oakland-Hayward-Berkeley, CA | -2.74% |
Santa Cruz-Watsonville, CA | -5.45% |
San Rafael, CA | -10.32% |
Stockton-Lodi, CA | -12.68% |
Modesto, CA | -13.26% |

I'm personally happy to see nice growth on the Central Coast. However, if you zoom out and look at overall job growth, the Central Coast is below the state average of 3.03% (SLO is at 1.96%; SM/SB is at 0.62%). You can see those results by changing the SOC_Code in the above query from '15-0000' to '00-0000'. 

Let's also look at the changes in mean tech wages:

```sql
WITH Data2017 
AS
(
SELECT
	  [MSA_Code]
	  ,[Location]
      ,[SOC_Code]
      ,[Occupation]
      ,[Year]
      ,[Employment_Estimate]
      ,[Mean_Annual_Wage]
  FROM [CaliforniaSalaries].[dbo].[TotalData]
  WHERE [Year] = '2017'
  AND SOC_Code = '15-0000'
)
,Data2016
AS
(
SELECT
	  [MSA_Code]
	  ,[Location]
      ,[SOC_Code]
      ,[Occupation]
      ,[Year]
      ,[Employment_Estimate]
      ,[Mean_Annual_Wage]
  FROM [CaliforniaSalaries].[dbo].[TotalData]
  WHERE [Year] = '2016'
  AND SOC_Code = '15-0000'
)
SELECT 
Data2017.[Location] 
,ROUND(CONVERT(FLOAT,(Data2017.[Mean_Annual_Wage] - Data2016.[Mean_Annual_Wage]))
	/Data2016.[Mean_Annual_Wage] * 100,2) AS Mean_Wage_Perc
FROM Data2017 
	INNER JOIN Data2016
		ON Data2017.MSA_Code = Data2016.MSA_Code
		AND Data2017.SOC_Code = Data2016.SOC_Code
WHERE Data2017.[Employment_Estimate] > 600
ORDER BY ROUND(CONVERT(FLOAT,(Data2017.[Mean_Annual_Wage] - Data2016.[Mean_Annual_Wage]))
	/Data2016.[Mean_Annual_Wage] * 100,2) DESC
```

**Location** | **Wage Growth** |
Chico, CA |	6.25% |
Bakersfield, CA | 5.34% |
Santa Maria-Santa Barbara, CA | 5.07% |
Vallejo-Fairfield, CA | 4.83% |
Visalia-Porterville, CA | 3.81%  |
San Francisco-Redwood City-South San Francisco, CA | 3.48% |
**...California Statewide...** |	**-0.14%** |
Fresno, CA | -1.27% |
Santa Rosa, CA | -2.66% |
San Rafael, CA | -3.1% |
San Jose-Sunnyvale-Santa Clara, CA | -4.25% |
Modesto, CA | -12.2% |

This is surprising - the average non-management tech wage for the state actually **decreased** in the last year. A big part of that is Silicon Valley decreasing by 4.25%. It would be interesting to assess the causes of this. One possibility is that there are more lower-wage tech jobs being created (or higher-wage ones are dying off). Another possibility is that more jobs are being created in lower cost of living areas. At a first glance, this latter possibility doesn't seem likely, since both SF and LA were in the top 5 fastest growing areas. Indeed the percentage of tech jobs that Silicon Valley/SF/LA made up in 2017 is the same as in 2016 (46%).

Is it unusual for the average tech salary to decrease in CA? Between 2015 and 16, the average salary increased 3.55% (and 4.04% in Silicon Valley) and between 2014 and 15 the average salary increased 4.56% statewide. At the same time, the average salary for any job in CA increased by 2.81% between 2016 and 17, which is a greater increase than between 2015 and 16 (2.42%). This doesn't look like a great sign for tech jobs in CA, but it's still one year and one dataset focused on only employment numbers and wages. I won't jump to any conclusions yet.