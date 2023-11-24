# flink-match-recognize-in-batch-debugging

```bash
docker-compose build

docker-compose up
```

Initialize database

```bash
docker exec -it flink-match-recognize-in-batch-debugging-postgres-1 bash -c "psql -U test test -f /opt/events_input.sql"
```

Open SQL client

```bash
docker exec -it flink-match-recognize-in-batch-debugging-jobmanager-1 /opt/flink/bin/sql-client.sh
```

Run in Flink SQL

```bash
SET 'execution.runtime-mode' = 'batch';
```

```bash
%%flink_execute_sql
CREATE TABLE events (
  id INT,
  user_id INT,
  ts TIMESTAMP(3),
  active BOOLEAN,
  WATERMARK FOR ts AS ts - INTERVAL '5' SECOND,
  PRIMARY KEY (id) NOT ENFORCED
) WITH (
    'connector' = 'jdbc',
    'url' = 'jdbc:postgresql://postgres:5432/test',
    'username' = 'test',
    'password' = 'test',
    'table-name' = 'events'
);
```

```bash
%%flink_execute_sql
SELECT *
FROM events
    MATCH_RECOGNIZE (
        PARTITION BY user_id
        ORDER BY ts ASC
        MEASURES
            A.ts as _start,
            B.ts as _finish
        ONE ROW PER MATCH
        AFTER MATCH SKIP PAST LAST ROW
        PATTERN (A B) WITHIN INTERVAL '2' HOURS
        DEFINE
            A AS active is false,
            B AS active is true
    ) AS T;
```

For some result rows `_start > _finish`.

```
     user_id                  _start                 _finish
           1 2023-11-23 14:34:48.370 2023-11-23 14:34:44.264
           1 2023-11-23 14:34:45.997 2023-11-23 14:34:43.682
           1 2023-11-23 14:34:47.722 2023-11-23 14:34:46.851
           1 2023-11-23 14:34:49.356 2023-11-23 14:34:48.425
           1 2023-11-23 14:34:44.243 2023-11-23 14:34:49.017
```
