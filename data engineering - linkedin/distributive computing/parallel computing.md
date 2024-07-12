modern data processing tool require -> Parallel computing

why?
- memory
- processing power

what it does?
 
1. break down the task into chunks (smaller subtasks)
2. and distribute those subtasks among multiple computers


benifits

- more processing power
- more scalable
- cost effective
- fault tolerant

memory req per computer is even lesser so you can break the data into subsets and load into diff computers, data can fit into ram (this reduces the cost and helps in fault tolerance, how? idk yet)

risks

- overhead (nodes)
- tasks need to be large not usefull for small tasks
- if you have very few processing units then it's not worth splitting - if you do it doesn't make much difference and you are even slowing it down by adding splitting and stuff in the process

it's called parallel slow down

suppose you have some olympic data. it includes Year and age of participants for that year

say you have 4 processing units

### Multiprocessing (low level) in python

```
from multiprocessing import Pool
def avg_age(grouped_data):
    year, group = grouped_data
    return pd.DataFrame({"Age": group["Age"].mean()}, index=[year])

with Pool(4) as p:
    average_age = p.map(avg_age, df.groupby("Year"))
```

we distributed these subtasks over 4 processing units... we did it here in the same machine using 4 cores instead of 4 machines

### Dask framework (avoid using low level code) in python

it provides dataframe objects. it divides, groups data and applies multiprocessing

you need to define the no. of partitions

```
import dask.dataframe as dd

ath_df = dd.from_pandas(df, n partitions=4)
results_df = ath_df.groupby("Year").Age.mean().compute() #lazy computing

```

### Hadoop
- framework for distributed processing of large datasets across clusters of computers
- collection of open source projects
- uses mapReduce algorithm
- plays central role in ETL process

### MapReduce
