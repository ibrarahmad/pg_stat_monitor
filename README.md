
### pg_stat_monitor - Statistics collector for [PostgreSQL][1].

The pg_stat_monitor is statistics collector tool based on PostgreSQL's contrib module "pg_stat_statements". PostgreSQL’s “pg_stat_statments” provides the basic statistics which is sometimes not enough. The major shortcoming in pg_stat_statment is that it accumulates all the queries and its statistics and does not provide aggregate statistics or histogram information. in that case, the user needs to calculate the aggregate which is quite expensive.

pg_stat_monitor is developed on the basis of pg_stat_statments as more advanced replacement for pg_stat_statment. It provides all the features of pg_stat_statment plus its own feature set.

#### Supported PostgreSQL Versions.
Pg_stat_monitor should work on the latest version of PostgreSQL but only tested with these versions of PostgreSQL.

    *   PostgreSQL Version 11
    *   PostgreSQL Version 12
    *   Percona Distribution for PostgreSQL

#### Installation
There are two ways to install pg_stat_monitor. The first is by downloading the pg_stat_monitor source code and compiling it. The second is to download the deb or rpm packages.

##### Download and compile
The latest release of pg_stat_monitor can be downloaded from this GitHub page:

    https://github.com/Percona/pg_stat_monitor/releases

or it can be downloaded using the git:

    git clone git://github.com/Percona/pg_stat_monitor.git

After downloading the code, set the path for the [PostgreSQL][1] binary:

###### Compile and Install extension
    cd pg_stat_monitor
    make USE_PGXS=1
    make USE_PGXS=1 install

###### Enable and Create Extension

This extension needs to be loaded at the start time. Which requires adding the pg_stat_monitor extension shared_preload_libraries and restart the PostgreSQL Instance.

    postgres=# alter system set shared_preload_libraries=pg_stat_monitor;
    ALTER SYSTEM

    sudo systemctl restart postgresql-11

Create the extension in the desired database.

    postgres=# create extension pg_stat_monitor;
    CREATE EXTENSION


#### Usage
There are four views, and complete statistics can be accessed using these views.

  * pg_stat_monitor
  * pg_stat_agg_database
  * pg_stat_agg_user
  * pg_stat_agg_host

##### pg_stat_monitor
This is the main view which stores per query-based statistics, similar to pg_stat_statment with some additional columns.

    postgres=# \d pg_stat_monitor;
                               View "public.pg_stat_monitor"
           Column        |           Type           | Collation | Nullable | Default 
    ---------------------+--------------------------+-----------+----------+---------
     bucket              | oid                      |           |          | 
     bucket_start_time   | timestamp with time zone |           |          | 
     userid              | oid                      |           |          | 
     dbid                | oid                      |           |          | 
     queryid             | text                     |           |          | 
     query               | text                     |           |          | 
     calls               | bigint                   |           |          | 
     total_time          | double precision         |           |          | 
     min_time            | double precision         |           |          | 
     max_time            | double precision         |           |          | 
     mean_time           | double precision         |           |          | 
     stddev_time         | double precision         |           |          | 
     int8                | bigint                   |           |          | 
     shared_blks_hit     | bigint                   |           |          | 
     shared_blks_read    | bigint                   |           |          | 
     shared_blks_dirtied | bigint                   |           |          | 
     shared_blks_written | bigint                   |           |          | 
     local_blks_hit      | bigint                   |           |          | 
     local_blks_read     | bigint                   |           |          | 
     local_blks_dirtied  | bigint                   |           |          | 
     local_blks_written  | bigint                   |           |          | 
     temp_blks_read      | bigint                   |           |          | 
     temp_blks_written   | bigint                   |           |          | 
     blk_read_time       | double precision         |           |          | 
     blk_write_time      | double precision         |           |          | 
     host                | bigint                   |           |          | 
     client_ip           | inet                     |           |          | 
     resp_calls          | text[]                   |           |          | 
     cpu_user_time       | double precision         |           |          | 
     cpu_sys_time        | double precision         |           |          | 
     tables_names        | text[]                   |           |          | 


These are new column added to have more detail about the query.

    client_ip: Client IP or Hostname
    hist_calls: Hourly based 24 hours calls of query histogram
    hist_min_time: Hourly based 24 hours min time of query histogram
    Hist_max_time: Hourly based 24 hours max time of query histogram
    hist_mean_time: Hourly based 24 hours mean time of query histogram
    slow_query: Slowest query with actual parameters.
    cpu_user_time: CPU user time for that query.
    cpu_sys_time: CPU System time for that query.


    postgres=# \d pg_stat_agg_database
                    View "public.pg_stat_agg_database"
        Column     |       Type       | Collation | Nullable | Default 
    ---------------+------------------+-----------+----------+---------
     bucket        | oid              |           |          | 
     queryid       | text             |           |          | 
     dbid          | bigint           |           |          | 
     userid        | oid              |           |          | 
     client_ip     | inet             |           |          | 
     total_calls   | integer          |           |          | 
     min_time      | double precision |           |          | 
     max_time      | double precision |           |          | 
     mean_time     | double precision |           |          | 
     resp_calls    | text[]           |           |          | 
     cpu_user_time | double precision |           |          | 
     cpu_sys_time  | double precision |           |          | 
     query         | text             |           |          | 
     tables_names  | text[]           |           |          | 

    postgres=# \d pg_stat_agg_user
                      View "public.pg_stat_agg_user"
        Column     |       Type       | Collation | Nullable | Default 
    ---------------+------------------+-----------+----------+---------
     bucket        | oid              |           |          | 
     queryid       | text             |           |          | 
     dbid          | bigint           |           |          | 
     userid        | oid              |           |          | 
     client_ip     | inet             |           |          | 
     total_calls   | integer          |           |          | 
     min_time      | double precision |           |          | 
     max_time      | double precision |           |          | 
     mean_time     | double precision |           |          | 
     resp_calls    | text[]           |           |          | 
     cpu_user_time | double precision |           |          | 
     cpu_sys_time  | double precision |           |          | 
     query         | text             |           |          | 
     tables_names  | text[]           |           |          | 

    postgres=# \d pg_stat_agg_ip 
                       View "public.pg_stat_agg_ip"
        Column     |       Type       | Collation | Nullable | Default 
    ---------------+------------------+-----------+----------+---------
     bucket        | oid              |           |          | 
     queryid       | text             |           |          | 
     dbid          | bigint           |           |          | 
     userid        | oid              |           |          | 
     client_ip     | inet             |           |          | 
     host          | bigint           |           |          | 
     total_calls   | integer          |           |          | 
     min_time      | double precision |           |          | 
     max_time      | double precision |           |          | 
     mean_time     | double precision |           |          | 
     resp_calls    | text[]           |           |          | 
     cpu_user_time | double precision |           |          | 
     cpu_sys_time  | double precision |           |          | 
     query         | text             |           |          | 
     tables_names  | text[]           |           |          | 

Examples
1 - In this query we are getting the exact value of f1 which is '05:06:07-07' (special setting is required for this).

    # select userid, queryid, query, max_time, total_calls from pg_stat_agg_user;
    -[ RECORD 1 ]----------------------------------------------------------------------------------------
    userid      | 10
    queryid     | -203926152419851453
    query       | SELECT f1 FROM TIMETZ_TBL WHERE f1 < '05:06:07-07';
    max_time    | 1.237875
    total_calls | 8

2 - Collect all statistics based on the user.

    # select usename, query, max_time, total_calls from pg_stat_agg_user au, pg_user u where au.userid = u.usesysid;
     usename |                          query                          | max_time | total_calls
    ---------+---------------------------------------------------------+----------+-------------
     vagrant | select userid, query, total_calls from pg_stat_agg_user | 0.268842 |           7
     foo     | select userid, query, total_calls from pg_stat_agg_user | 0.208551 |           1
     vagrant | select * from pg_stat_monitor_reset()                   |   0.0941 |           1
    (3 rows)


#### Limitation
There are some limitations and Todos.

#### Licence
Copyright (c) 2006 - 2019, Percona LLC.
See [`LICENSE`][2] for full details.

[1]: https://www.postgresql.org/
[2]: https://github.com/Percona/pg_stat_monitor/blob/master/LICENSE
[3]: https://github.com/Percona/pg_stat_monitor/issues/new
[4]: CONTRIBUTING.md
