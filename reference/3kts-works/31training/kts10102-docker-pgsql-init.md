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

```
FROM postgres
ADD training.sql /docker-entrypoint-initdb.d/
```

### training.sql

```sql
CREATE DATABASE db_ts_training WITH ENCODING = 'UTF8';

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



