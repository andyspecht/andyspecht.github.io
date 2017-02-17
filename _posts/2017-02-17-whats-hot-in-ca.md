---
layout: post
title: What Jobs are Hot?
image: /img/HotOccups/fire.jpg
tags: [OES Data, Data, Employment Numbers, SQL Server, DBA]
---

I'm back at it again for at least one more post on that [California salary data](https://github.com/andyspecht/OES-California-SQL) I put together. This time I want to look at our data with a bit more focus. I want to see which tech jobs have grown between 2013 and the present, with respect to the number of positions and salary. I'm starting with 2013, since some of the job titles are missing in the 2011 and 2012 data. We'll use our usual filter to only focus on the thirteen non-managerial tech job titles, and we'll look at the state as a whole.

```sql
WITH Year2013 AS
(
SELECT SOC_Code, Occupation, Year, Employment_Estimate FROM TotalData
WHERE [year] = '2013' AND
MSA_CODE= '000000'
AND SOC_Code LIKE '15-1%'
),
Year2016 AS
(
SELECT SOC_Code, Occupation, Year, Employment_Estimate FROM TotalData
WHERE [year] = '2016' AND
MSA_CODE= '000006'
AND SOC_Code LIKE '15-1%'
)
SELECT Year2013.Occupation, Year2013.Employment_Estimate AS Employment2013, 
Year2016.Employment_Estimate AS Employment2016,
(Year2016.Employment_Estimate - Year2013.Employment_Estimate) AS RawGrowth,
ROUND(
	(CONVERT(float,Year2016.Employment_Estimate) 
	     - CONVERT(float,Year2013.Employment_Estimate))
	/CONVERT(float,Year2013.Employment_Estimate)*100,2) AS PercentIncrease
FROM Year2013 INNER JOIN Year2016
ON Year2013.SOC_Code = Year2016.SOC_Code
ORDER BY ROUND((CONVERT(float,Year2016.Employment_Estimate) 
    - CONVERT(float,Year2013.Employment_Estimate))
	/CONVERT(float,Year2013.Employment_Estimate)*100,2) DESC
```

**Occupation** | **Employment 2013** | **Employment 2016** | **Raw Growth** | **Percent Increase**
Computer Occupations, All Other	|	16,980	|	24,130	|	7,150	|	42.11%
Web Developers	|	15,110	|	21,320	|	6,210	|	41.1%
Software Developers, Applications	|	88,260	|	123,950	|	35,690	|	40.44%
Computer Systems Analysts	|	61,430	|	73,860	|	12,430	|	20.23%
Information Security Analysts	|	6,840	|	8,090	|	1,250	|	18.27%
Database Administrators	|	10,510	|	12,040	|	1,530	|	14.56%
Computer Network Support Specialists	|	17,510	|	19,840	|	2,330	|	13.31%
Computer User Support Specialists	|	58,190	|	62,830	|	4,640	|	7.97%
Network and Computer Systems Administrators	|	40,080	|	43,270	|	3,190	|	7.96%
Software Developers, Systems Software	|	80,130	|	82,370	|	2,240	|	2.8%
Computer Programmers	|	41,540	|	38,650	|	-2,890	|	-6.96%
Computer Network Architects	|	15,590	|	14,320	|	-1,270	|	-8.15%
Computer and Information Research Scientists	|	6,460	|	5,280	|	-1,180	|	-18.27%

Since "other" isn't a very helpful designation, Web Developer is the fastest growing tech occupation between 2013 and 2016. This is interesting, since it's on the lower side of the salary scale (see [my last post](https://andyspecht.github.io/2017-02-09-more-avg-salaries/)). This leads to another question: how did salaries change during this time period? To run this query, just take the one above and replace each instance of "Employment_Estimate" with "Mean_Annual_Wage"

**Occupation** | **Mean Salary 2013** | **Mean Salary 2016** | **Raw Growth** | **Percent Increase**
Software Developers, Applications	|	$106,558.13	|	$125,696.00	|	$19,137.87	|	17.96%
Computer Network Architects	|	$109,940.68	|	$128,830.00	|	$18,889.32	|	17.18%
Computer Occupations, All Other	|	$84,024.65	|	$97,097.00	|	$13,072.35	|	15.56%
Computer and Information Research Scientists	|	$114,592.33	|	$131,346.00	|	$16,753.67	|	14.62%
Web Developers	|	$72,503.48	|	$82,793.00	|	$10,289.52	|	14.19%
Information Security Analysts	|	$99,333.83	|	$110,691.00	|	$11,357.17	|	11.43%
Database Administrators	|	$86,106.69	|	$95,880.00	|	$9,773.31	|	11.35%
Computer Systems Analysts	|	$91,354.36	|	$100,841.00	|	$9,486.64	|	10.38%
Software Developers, Systems Software	|	$117,014.96	|	$128,394.00	|	$11,379.04	|	9.72%
Computer User Support Specialists	|	$57,372.70	|	$62,726.00	|	$5,353.30	|	9.33%
Network and Computer Systems Administrators	|	$85,546.14	|	$93,354.00	|	$7807.86	|	9.13%
Computer Network Support Specialists	|	$74,050.31	|	$79,404.00	|	$5,353.69	|	7.23%
Computer Programmers	|	$88,346.86	|	$93,506.00	|	$5,159.14	|	5.84%

Overall, there is a lot of very impressive growth for a three year period (though how much did California rents go up by in the last three years?...) It is clear that Application Development and Web Development have been the two "hottest" areas if you consider both job and salary growth.

Just for fun, let's expand our query to all jobs in California. First, let's see which jobs have increased in number the most in the last three years, and let's only look at jobs with at least 1000 employed in it.

**Rank** |**Occupation** | **Employment 2013** | **Employment 2016** | **Raw Growth** | **Percent Increase**
1	|	Reinforcing Iron and Rebar Workers	|	1,560	|	4,050	|	2,490	|	159.62%
2	|	Transportation Attendants, Except Flight Attendants	|	1,630	|	3,830	|	2200	|	134.97%
3	|	Metal Workers and Plastic Workers, All Other	|	1,480	|	3,220	|	1,740	|	117.57%
4	|	Food Processing Workers, All Other	|	1,840	|	3,980	|	2,140	|	116.3%
5	|	Personal Care Aides	|	58,110	|	124,210	|	66,100	|	113.75%
6	|	Graduate Teaching Assistants	|	7,640	|	16,210	|	8,570	|	112.17%
7	|	Camera Operators, Television, Video, and Motion Picture	|	2,430	|	5,110	|	2,680	|	110.29%
8	|	Outdoor Power Equipment and Other Small Engine Mechanics	|	1,030	|	2,130	|	1,100	|	106.8%
9	|	Floor Layers, Except Carpet, Wood, and Hard Tiles	|	1,140	|	2,220	|	1,080	|	94.74%
10	|	Film and Video Editors	|	5,830	|	11,250	|	5,420	|	92.97%

I'm surprised to see Graduate TA on this list. Hopefully it's because they are giving more graduate students funding. I'm also somewhat surprised to see camera operators and video editors make the top 10. The growth in personal care aides is definitely a sign of our times and demographics. Let's take a look at the bottom of this list...

**Rank** |**Occupation** | **Employment 2013** | **Employment 2016** | **Raw Decline** | **Percent Decline**
670	|	Audiologists	|	1,560	|	8,70	|	-690	|	-44.23%
671	|	Tailors, Dressmakers, and Custom Sewers	|	3,980	|	2,140	|	-1,840	|	-46.23%
672	|	Actors	|	32,300	|	17,090	|	-15,210	|	-47.09%
673	|	Electro-Mechanical Technicians	|	3,640	|	1,910	|	-1,730	|	-47.53%
674	|	Therapists, All Other	|	2,150	|	1,070	|	-1,080	|	-50.23%
675	|	Motion Picture Projectionists	|	1,020	|	490	|	-530	|	-51.96%
676	|	Special Education Teachers, All Other	|	11,810	|	4,740	|	-7,070	|	-59.86%
677	|	Correspondence Clerks	|	1,890	|	740	|	-1,150	|	-60.85%
678	|	Helpers--Roofers	|	1,300	|	450	|	-850	|	-65.38%
679	|	Choreographers	|	2,500	|	570	|	-1,930	|	-77.2%

Huh? So we need video editors and camer operators but not actors apparently. You know what's next; let's run the same query but with salaries. I set a minimum of 100 people in the occupation in 2016.

**Rank**| **Occupation** | **Mean Salary 2013** | **Mean Salary 2016** | **Raw Growth** | **Percent Increase**
1	|	Genetic Counselors	|	$56,340.80	|	$86,800.00	|	$30,459.20	|	54.06%
2	|	Mine Cutting and Channeling Machine Operators	|	$37,241.84	|	$57,162.00	|	$19,920.16	|	53.49%
3	|	Costume Attendants	|	$37,678.08	|	$56,279.00	|	$18,600.92	|	49.37%
4	|	Religious Workers, All Other	|	$33,722.17	|	$48,959.00	|	$15,236.83	|	45.18%
5	|	Personal Financial Advisors	|	$93,781.52	|	$133,557.00	|	$39,775.48	|	42.41%
6	|	Rail Transportation Workers, All Other	|	$53,250.83	|	$75,834.00	|	$22,583.17	|	42.41%
7	|	Designers, All Other	|	$58,289.04	|	$82,783.00	|	$24,493.96	|	42.02%
8	|	Psychiatrists	|	$181,716.36	|	$253,714.00	|	$71,997.64	|	39.62%
9	|	Rail Car Repairers	|	$39,811.11	|	$55,452.00	|	$15,640.89	|	39.29%
10	|	Rail-Track Laying and Maintenance Equipment Operators	|	$50,185.43	|	$69,247.00	|	$19,061.57	|	37.98%

What a fascinating mix of old and new. Genetic counselor is the job of the future, but there are three railroad-related occupations in the top 10. It looks like there aren't enough people to fill those old school railroad jobs, so salaries went up. And the jobs with the largest decrease in wages...

**Rank**| **Occupation** | **Mean Salary 2013** | **Mean Salary 2016** | **RawDecline** | **Percent Increase**
778	|	Geological and Petroleum Technicians	|	$81,586.81	|	$65,891.00	|	-$15,695.81	|	-19.24%
779	|	Ship Engineers	|	$83,902.69	|	$66,849.00	|	-$17,053.69	|	-20.33%
780	|	Career/Technical Education Teachers, Middle School	|	$70,381.90	|	$54,823.00	|	-$15,558.90	|	-22.11%
781	|	Helpers--Pipelayers, Plumbers, Pipefitters, and Steamfitters	|	$42,703.14	|	$32,992.00	|	-$9,711.14	|	-22.74%
782	|	Captains, Mates, and Pilots of Water Vessels	|	$87,121.81	|	$67,155.00	|	-$19,966.81	|	-22.92%
783	|	Funeral Service Managers	|	$82,410.79	|	$62,844.00	|	-$19,566.79	|	-23.74%
784	|	Arbitrators, Mediators, and Conciliators	|	$104,107.12	|	$77,406.00	|	-$26,701.12	|	-25.65%
785	|	Graduate Teaching Assistants	|	$43,797.87	|	$31,743.00	|	-$12,054.87	|	-27.52%
786	|	Material Moving Workers, All Other	|	$53,697.48	|	$37,051.00	|	-$16,646.48	|	-31%
787	|	Morticians, Undertakers, and Funeral Directors	|	$68,681.43	|	$44,562.00	|	-$24,119.43	|	-35.12%

This is very interesting as well. Graduate TA's are both one of the fastest growing occupations but have a severely decreasing salary at the same time. The average salary in 2013 (~$44k) does seem surprisingly high, though it would make sense if you consider that TAships are often halftime, and they are calculating the salary on a fulltime basis. It also looks like the funeral industry is facing decreased wages.

I've enjoyed looking at this data set. It offers a really interesting glimpse into the California tech labor market. Next time, however, I'll write about something else.