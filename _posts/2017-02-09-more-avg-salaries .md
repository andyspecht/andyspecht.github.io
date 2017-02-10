---
layout: post
title: More On Average Tech Salaries in California
image: /img/MoreSalCA/CASeal.png
tags: [OES Data, Data, Salaries, SQL Server, DBA]
---

[Last time](https://andyspecht.github.io/2017-02-04-dba-ca-salaries/) I presented some comparisons of average DBA salaries in California. But [the data set](https://github.com/andyspecht/OES-California-SQL) I've been looking at has more than just info on DBA's. This time I'm going to walk through some queries that look at other tech salaries in California and the distribution of tech jobs accross the state. 

To get started, let's run a straightforward query to find the average salary for the tech occupations listed in the data.


```sql
SELECT Location, SOC_Code, Occupation, Mean_Annual_Wage, Employment_Estimate 
FROM TotalData
WHERE [year] = '2016' AND MSA_Code = '000006' AND SOC_Code LIKE '15-1%' 
ORDER BY Mean_Annual_Wage DESC
```

**SOC Code** | **Occupation** | **Mean Wage** | **Employment Estimate** |
15-1111	|	Computer and Information Research Scientists	|	$131,346 |	5,280
15-1143	|	Computer Network Architects	|	$128,830	|	14,320
15-1133	|	Software Developers, Systems Software	|	$128,394	|	82,370
15-1132	|	Software Developers, Applications	|	$125,696	|	123,950
15-1122	|	Information Security Analysts	|	$110,691 |	8,090
15-1121	|	Computer Systems Analysts	|	$100,841	|	73,860
15-1199	|	Computer Occupations, All Other	|	$97,097	|	24,130
15-1141	|	Database Administrators	|	$95,880	|	12,040
15-1131	|	Computer Programmers	|	$93,506	|	38,650
15-1142	|	Network and Computer Systems Administrators	|	$93,354	|	43,270
15-1134	|	Web Developers	|	$82,793	|	21,320
15-1152	|	Computer Network Support Specialists	|	$79,404	|	19,840
15-1151	|	Computer User Support Specialists	|	$62,726	|	62,830

Nothing too surprising here. One thing to note is that the OES makes a somewhat unusual distinction between computer programers and software developers (I always thought computer programming was just "rebranded" as software development to try and sound more impressive). The distinction is roughly that computer programmers create code from a specification, whereas software developers are heavily involved in the larger software design process. 

Anyway, let's do another query. Let's see which areas have the highest percentage of tech jobs compared to total jobs. We'll exclude places with fewer than 500 tech jobs.

```sql
WITH TotTech AS
(
SELECT Location, SUM(Employment_Estimate) AS TechTotEmp
FROM TotalData
where [year] = '2016' AND SOC_Code LIKE '15-1%' 
AND Employment_Estimate IS NOT NULL  
GROUP BY Location
HAVING SUM(Employment_Estimate) > 500
)
, TotArea AS
(
SELECT Location, Employment_Estimate AS TotEmp
FROM TotalData
WHERE [year] = '2016' AND SOC_Code = '00-0000'
)
SELECT TotArea.Location, TotTech.TechTotEmp, TotArea.TotEmp, 
ROUND(CONVERT(float,TotTech.TechTotEmp)/CONVERT(float,TotArea.TotEmp),3) AS Ratio
FROM TotTech INNER JOIN TotArea
ON TotTech.Location = TotArea.Location
ORDER BY CONVERT(float,TotTech.TechTotEmp)/CONVERT(float,TotArea.TotEmp) DESC
```

Rank | Location | Total Tech Jobs | Total Jobs | Ratio
1	|	San Jose-Sunnyvale-Santa Clara, CA	|	122,600	|	1,011,840	|	0.121
2	|	San Francisco-Redwood City-South San Francisco, CA 	|	78,320	|	1,020,020	|	0.077
3	|	Oakland-Hayward-Berkeley, CA Metropolitan Division	|	42,800	|	1,053,950	|	0.041
4	|	San Diego-Carlsbad, CA	|	47,590	|	1,353,180	|	0.035
5	|	Sacramento--Roseville--Arden-Arcade, CA	|	30,390	|	874,610	|	0.035
**6**	|	**California Statewide**	|	**529,950**	|	**15,496,600**	|	0.034
7	|	San Rafael, CA Metropolitan Division	|	3,550	|	112,400	|	0.032
8	|	Anaheim-Santa Ana-Irvine, CA Metropolitan Division	|	47,950	|	1,523,780	|	0.031
9	|	Santa Maria-Santa Barbara, CA	|	4,330	|	181,190	|	0.024
10	|	Los Angeles-Long Beach-Glendale MD, CA	|	93,410	|	4,103,590	|	0.023
11	|	Oxnard-Thousand Oaks-Ventura, CA	|	6,590	|	295,700	|	0.022
12	|	Santa Cruz-Watsonville, CA	|	1,740	|	89,660	|	0.019
13	|	San Luis Obispo-Paso Robles-Arroyo Grande, CA	|	1,650	|	110,730	|	0.015
14	|	Santa Rosa, CA	|	2,790	|	187,850	|	0.015
15	|	Vallejo-Fairfield, CA	|	1,690	|	125,020	|	0.014
16	|	Salinas, CA	|	1,940	|	161,580	|	0.012
17	|	Bakersfield, CA	|	3,440	|	295,130	|	0.012
18	|	Riverside-San Bernardino-Ontario, CA	|	13,990	|	1,310,300	|	0.011
19	|	Fresno, CA	|	3,550	|	340,660	|	0.01
20	|	Chico, CA	|	710	|	72,300	|	0.01
21	|	Northern Mountains Region	|	590	|	62,340	|	0.009
22	|	Merced, CA	|	560	|	64,040	|	0.009
23	|	Stockton-Lodi, CA	|	1,630	|	218,840	|	0.007
24	|	Modesto, CA	|	1,170	|	166,250	|	0.007
25	|	Visalia-Porterville, CA	|	880	|	141,020	|	0.006
26	|	North Coast Region	|	570	|	95,040	|	0.006

This is interesting. Silicon Valley truly is an usual region, with 12% of the total jobs categorized as non-managerial tech jobs; this doesn't even include workers in non-tech roles at tech companies. Closer to home, San Luis Obispo with only 1.5% of jobs classified as non-managerial tech, has a long way to go before it would feel like one of the tech hubs in the major cities.

Finally, let's calculate something a little more difficult (at least from a SQL perspective). Let's take a look at which tech jobs are most common in which cities and see if there are any interesting discrepancies. I'm going to have to pivot the data (boo), so I'll be somewhat arbitrarily selective about the locations and occupations.

```sql
WITH TotTechByLoc
AS
(
	SELECT Location, SUM(Employment_Estimate) AS TotTechLoc
	FROM TotalData
	WHERE [year] = '2016' AND SOC_Code LIKE '15-1%'
	GROUP By Location
)
SELECT Location, [Computer Systems Analysts], [Database Administrators], 
[Web Developers], [Software Developers, Applications],
[Software Developers, Systems Software], [Computer User Support Specialists]
FROM 
(
	SELECT TotalData.Location, TotalData.Occupation,
		ROUND(CONVERT(FLOAT,TotalData.Employment_Estimate) / 
		CONVERT(FLOAT,TotTechByLoc.TotTechLoc),2) AS PercOfTech
	FROM TotalData INNER JOIN TotTechByLoc
	ON TotalData.Location = TotTechByLoc.Location
	WHERE [year] = '2016' AND SOC_Code LIKE '15-1%'
) AS BaseTable
PIVOT
(
	MAX(PercOfTech) for Occupation IN
	([Computer Systems Analysts], [Database Administrators], [Web Developers],
	[Software Developers, Applications],
	[Software Developers, Systems Software], 
	[Computer User Support Specialists])
) AS OccuPivot
```

**Location**                | **Sys Analyst** | **DBA** | **Web Dev** | **App Dev** | **Sys Dev** | **User Support** | 
California Statewide	|	14%	|	2%	|	4%	|	23%	|	16%	|	12%	|
Los Angeles-Long Beach-Glendale MD, CA	|	13%	|	3%	|	6%	|	16%	|	12%	|	17%	|
San Diego-Carlsbad, CA	|	12%	|	2%	|	4%	|	21%	|	16%	|	12%	|
San Jose-Sunnyvale-Santa Clara, CA	|	12%	|	1%	|	2%	|	**35%**	|	22%	|	6%	|
San Francisco-Redwood City-South San Francisco, CA 	|	14%	|	2%	|	5%	|	29%	|	13%	|	10%	|
Oakland-Hayward-Berkeley, CA Metropolitan Division	|	17%	|	2%	|	3%	|	22%	|	17%	|	9%	|
Sacramento--Roseville--Arden-Arcade, CA	|	**29%**	|	2%	|	2%	|	13%	|	17%	|	10%	|
Santa Maria-Santa Barbara, CA	|	9%	|	2% |	7%	|	2%	|	2%	|	12%	|
San Luis Obispo-Paso Robles-Arroyo Grande, CA	|	13%	|	4%	|	6%	|	19%	| N/A	|	26%	|

I highlighted a couple of the numbers that were especially notable.

* Sacramento has the highest concentration of systems analysts, and it's significantly higher than any other major metro area. If I had to guess, I would suspect it's because governmental agencies are more likely to employ systems analysts rather than developers.
* Silicon Valley also has an extremely unusual distribution of jobs. Applications and systems developers are way more common there and user support is especially uncommon. 
* I only included Santa Barbara/SLO because that's my neck of the woods. 
	* I'm surprised by the lack of developers in Santa Barbara. 
	* I'm not surprised by the number of user support workers in SLO. It's got a big software company with a large customer support team.

Alright! That's it for today.