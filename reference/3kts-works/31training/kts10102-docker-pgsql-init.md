# Docker PgSQL Init

## 1.Environment Preparing

在项目目录中创建Dockerfile以及脚本文件：

```
dev_tool/docker/Dockerfile_postgres_training
dev_tool/docker/training.sql
dev_tool/shell/db-install.sh
dev_tool/shell/db-uninstall.sh
```

几个文件内容分别如下：

### Dockerfile\_postgres\_training

Dockerfile语法参考：[KM10005 - Dockerfile语法](/reference/basic-knowledge/131docker/km10005-dockerfileyu-fa.md)

```
FROM postgres
ADD training.sql /docker-entrypoint-initdb.d/
```

### training.sql

```sql
CREATE DATABASE db_ts_training WITH ENCODING = 'UTF8';
\c db_ts_training          --PostgreSQL中切换数据库命令
CREATE TABLE TST_MESSAGE(
  id               uuid PRIMARY KEY,
  "user"           VARCHAR(200) NOT NULL,
  message          TEXT NOT NULL,
  postedAt         TIMESTAMP
);

CREATE INDEX IDX_TST_MESSAGE ON TST_MESSAGE (postedAt)
```

### db-install.sh

```
#!/usr/bin/env bash

cd ../docker
img_name="tst_db_training"
container_name=${img_name}_container

docker stop ${container_name}
docker rm ${container_name}
docker rmi ${img_name}:latest

docker build -t ${img_name}:latest -f Dockerfile_postgres_training .
docker run -d -e POSTGRES_PASSWORD=training -d -p 5432:5432 --name ${container_name} ${img_name}
docker images
docker ps -a
cd -
```

### db-unistall.sh

```
#!/usr/bin/env bash

cd ../docker
img_name="tst_db_training"
container_name=${img_name}_container

docker stop ${container_name}
docker rm ${container_name}
docker rmi ${img_name}:latest

cd -
```

## 2.Execute/Start Container

准备好上述文件过后，从终端进入到dev\_tool/shell/目录中执行db-install.sh

![](/_images/kts/training/KTS10102-001.png)上述输出后，使用下边命令查看docker启动日志，注意最后的`CONTAINER_ID`

```
docker logs 6e629fe1d4af
```

看到下边日志就表示启动成功了：

```
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

Success. You can now start the database server using:

    pg_ctl -D /var/lib/postgresql/data -l logfile start


WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.
waiting for server to start....LOG:  database system was shut down at 2017-04-13 02:28:42 UTC
LOG:  MultiXact member wraparound protections are now enabled
LOG:  autovacuum launcher started
LOG:  database system is ready to accept connections
 done
server started
ALTER ROLE


/usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/training.sql
CREATE DATABASE
CREATE TABLE
CREATE INDEX


LOG:  received fast shutdown request
LOG:  aborting any active transactions
waiting for server to shut down....LOG:  autovacuum launcher shutting down
LOG:  shutting down
LOG:  database system is shut down
 done
server stopped

PostgreSQL init process complete; ready for start up.

LOG:  database system was shut down at 2017-04-13 02:28:44 UTC
LOG:  MultiXact member wraparound protections are now enabled
LOG:  autovacuum launcher started
LOG:  database system is ready to accept connections
```

注意最后一行：database system is ready to accept connections的信息出来过后就可等待客户端连接了。

## 3. Connection Checking





