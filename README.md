# Avita_Nursing_Homes
A project that analysis the performance of Aviata's nursing homes and compares it to the average in Florida

GOAL

This projects looks at the performance of a specific nursing home group in Florida, the Aviata Health Group. I was reporting on nursing homes in Florida when I realized that several of my sources kept mentioning as an example of an especially bad player. Over several months, I have collected testimonies from workers at the facility that described horrific conditions. Some said they were resorting to bringing their own toilet paper and soap because the cost-cutting measures from the basement were so extreme. This data analysis is an attempt to corroborate these testimonies with data.

FINDINGS

The analysis of 13,574 federal inspection records filed against Florida nursing homes over the past three years corroborates the workers' accounts. The group's nursing homes received 46% more citations than the state average. The majority of the violations mirrored what workers described, such as unhygienic conditions and old equipment. The most common citation is for infringing "residents' right to a safe, clean, comfortable and homelike environment," a category where Aviata was cited 54% more often than the rest of the nursing homes in Florida. The consequences are severe. Federal inspectors issued "Immediate Jeopardy" citations — the most serious CMS level reserved for risks of serious harm or death — 39% more often than the Florida norm.


DATA COLLECTION

The federal government collects a huge amount of data on nursing homes and make their databses publicly accessible through APIs. The two databases I was most interested in are:

1. Health deficiencies: where every row is a different deficiency https://data.cms.gov/provider-data/dataset/r5ix-sfxw#api
2. Provider information: where every row is a different facility https://data.cms.gov/provider-data/dataset/4pq5-n9py#api

The Health deficiencies database is too big to simply collect all the information at once. I first had to filter for florida ([value]=FL) and give it a limit of 1500 calls, to then send it on a while True loop to collect all the health citations for the state (a total of 13,581) 

The problem is that this dataset doesn't tell us how many patients a facility has. Which is why I need to combine this new dataframe with the second dataset. Fortunately, each facility has a unique number known as ccn. I used it to combine the two databases. I also limited my analysis to 2024 and 2025, when they overlap.


DATA ANALYSIS

From this point on, it will become important to separate calculations for Florida and for Aviata, so that I can make comparisons between the two and see if Aviata's health citations are indeed worse than the average. To do this, I created a list of identification numbers (CCNs) that belong to Aviata using this code: 

np.where(fl_providers['provider_name'].str.contains('aviata|aspire', case=False, na=False), '1', '0')

What is special about it is that I included facilities also named aspire, a name previously used by the group but sometimes still present in the federal data.

The first calculaiton is pretty straighforward:I calculated the average "overall score" (a metric used by inspector that assigns an overal score to nursing homes) for all the Aviata facilities, and that of the rest of the state (meaning of all Florida facilities excluding 

The Average n of citation was much harder to calculate because I want to account for the number of patients present at a facility. Larger facilities are more likely to have more citations. The API only gives the average number of residents fot June 2026, so it reflects current resident counts, not a specific year average. So I went here: https://data.cms.gov/provider-data/archived-data/nursing-homes#2025-annual-files , downloaded all the residents count for each month for 2024 and 2025 (I have already restricted the health citations for those two years) and combined them. The rest is pretty straighforward: calculate the tot number of citations, divide it by the average number of residents, divide by the number of facilities.

I then did a simple value_counts on the type of citations Aviata receives and used the result to create a pie chart. The result shows that most of the deficiencies are in areas that mirror the problems mentioned during my interviews with current workers.

I added three more simple calculations to check how many more ciations than the Florida average Aviata gets on Quality of Life and Care Deficiencies, Resident Rights Deficiencies, and on citations that are marked with the most sever codes (J, K, L) which indicate immidiate danger for residents.

I described this process as straightforward. In reality, it took me an entire week. The main problem was that apparently little omissions or imprecisions in the datasets, or in the way I was doing my calculations were leading to imprecisions. It took forever to spot them and fix them.
