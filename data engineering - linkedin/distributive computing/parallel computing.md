modern data processing tools require ----> Parallel computing

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

ath_df = dd.from_pandas(df, npartitions=4)
results_df = ath_df.groupby("Year").Age.mean().compute() #lazy computing

```

### Hadoop
- framework for distributed processing of large datasets across clusters of computers
- collection of open source projects
- uses mapReduce algorithm
- plays central role in ETL process

#### HDFS 
- distributed file system
- files reside on different computers
- essential part of big data ecosystem
- hdfs is replaced by cloud managed storage systems like google cloud storage and amazon S3

#### MapReduce
- big data processing paradigm (was first big...)
- it distributes workload in several processing units like we did earlier
- these processing units are multiple computers in the cluster
- it is difficult to write these processing jobs

#### Hive
- on top of hadoop
- helps in Extraction (ETL) in Data pipeline
- makes data queriable from different sources using Hive SQL
- also extract data from databases and file stystems that integrate with hadoop
- Hive SQL looks like SQL but it's internally translated into a job that operates on cluster of computers using the mapReduce algorithm

### Spark
- data transformaion
- distributes data processing tasks between clusters
- processing is done in memory
- faster processing as it avoids disk writes (unlike mapReduce)
- relies on RDD 
    - Data structure that maintains data accross multiple nodes
    - immutable, partitioned collection of elements
    - Tracks lineage of informtion to recover lost data
    - supports 2 type operations - transformation and action
    - Transformation - filter(), map(), groupByKey(), union()
    - Actions - count(), first(), collect(), reduce()
- pyspark
    - similar to pandas
    - because it has dataframe abstraction
    - you use this (dataframe abstraction od pyspark)
    ```
    (olympics_spark_df
        .groupBy('Year')
        .mean('Age')
        .show()
    )
    ```
    - instead of this (SQL)
    ```
    SELECT year, AVG(age)
    FROM views.olympic_athletes
    GROUP BY year
    ```

### Airflow
- workflow automation/scheduling
- for interdependent jobs that can't be done using linux cron
- uses DAGs (directed acyclic graphs)

```
dag = DAG('my_dag', start_date= datetime(2024, 7, 7))

# Define tasks of the DAG
# you can use simple ones like python operator and bash operator 
# or define your own like start cluster operator and spark job operator given below
start_cluster = StartClusterOperator(task_id = 'start_cluster', dag=dag)
input_athlete_data = SparkJobOperator(task_id = 'input_athlete_data', dag=dag)
input_venue_data = SparkJobOperator(task_id = 'input_venue_data', dag=dag)

# Setup Dependency flow
start_cluster.set_downstream(input_athlete_data)
input_athlete_data.set_downstream(enrich_athlete_data)
input_venue_data.set_downstream(enrich_athlete_data)
```