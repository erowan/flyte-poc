# DB



```

$ kubectl exec -it <postgres-pod> -n flyte -- /bin/sh

bash-5.1# psql -U postgres
psql (10.16)
Type "help" for help.


postgres=# \list
                                  List of databases
    Name     |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-------------+----------+----------+------------+------------+-----------------------
 datacatalog | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 postgres    | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
             |          |          |            |            | postgres=CTc/postgres
 template1   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
             |          |          |            |            | postgres=CTc/postgres
(4 rows)

postgres=# \c datacatalog
You are now connected to database "datacatalog" as user "postgres".
datacatalog=# \dt
             List of relations
 Schema |      Name      | Type  |  Owner   
--------+----------------+-------+----------
 public | artifact_data  | table | postgres
 public | artifacts      | table | postgres
 public | datasets       | table | postgres
 public | partition_keys | table | postgres
 public | partitions     | table | postgres
 public | tags           | table | postgres
(6 rows)

datacatalog=# \c postgres
You are now connected to database "postgres" as user "postgres".
postgres=# \dt
                 List of relations
 Schema |         Name          | Type  |  Owner   
--------+-----------------------+-------+----------
 public | execution_events      | table | postgres
 public | executions            | table | postgres
 public | launch_plans          | table | postgres
 public | migrations            | table | postgres
 public | named_entity_metadata | table | postgres
 public | node_execution_events | table | postgres
 public | node_executions       | table | postgres
 public | projects              | table | postgres
 public | resources             | table | postgres
 public | task_executions       | table | postgres
 public | tasks                 | table | postgres
 public | workflows             | table | postgres
(12 rows)

datacatalog=# select name,location from artifact_data;
    name    |                                                                                                         location                                                                                                          
------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 test_data  | s3://my-s3-bucket/metadata/datacatalog/flytepoc/development/flyte_task-house_price_prediction.house_price_predictor.generate_and_split_data/0.1-O0Y3QMhH-ya7Z6BP_/760ce221-1223-455e-b1d7-aa3205618cc9/test_data/data.pb
 val_data   | s3://my-s3-bucket/metadata/datacatalog/flytepoc/development/flyte_task-house_price_prediction.house_price_predictor.generate_and_split_data/0.1-O0Y3QMhH-ya7Z6BP_/760ce221-1223-455e-b1d7-aa3205618cc9/val_data/data.pb
 train_data | s3://my-s3-bucket/metadata/datacatalog/flytepoc/development/flyte_task-house_price_prediction.house_price_predictor.generate_and_split_data/0.1-O0Y3QMhH-ya7Z6BP_/760ce221-1223-455e-b1d7-aa3205618cc9/train_data/data.pb
 model      | s3://my-s3-bucket/metadata/datacatalog/flytepoc/development/flyte_task-house_price_prediction.house_price_predictor.fit/1.0-rSGTPbqd-byqaYTTH/4a6e838d-b874-43ed-b72b-18ba500f3f09/model/data.pb
 o0         | s3://my-s3-bucket/metadata/datacatalog/flytepoc/development/flyte_task-house_price_prediction.house_price_predictor.predict/1.0-Ku8YJlXs-ZpkNLNnf/d81d95f6-e8aa-4449-bef1-1f585b8eae30/o0/data.pb

```