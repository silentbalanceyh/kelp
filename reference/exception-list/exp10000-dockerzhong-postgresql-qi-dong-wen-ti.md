# EXP10001 - Docker中PostgreSQL启动问题

## 1.日志记录

运行下边命令查看启动postgres的docker模式启动日志：

```
docker logs <container_id>
```

日志记录如下：

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

\*：最终数据库启动起来了，但启动模式不对，通过账号以及提供的密码无法看到表信息。



