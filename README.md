# clickhouse-jdbc-bridge-test

To reproduce `jdbc-bridge` stability issues.

# Usage

```bash
# clone the repository
git clone https://github.com/zhicwu/clickhouse-jdbc-bridge-test.git

# start the containers
cd clickhouse-jdbc-bridge-test
docker-compose up -d

# check if containers are up and healthy
docker-compose ps
docker-compose exec jdbc-bridge curl -s 'http://ch-server:8123/?user=default&password=&query=select%201'

# check server error log - should be empty
docker-compose exec ch-server cat /var/log/clickhouse-server/server.err

# run stress test(5 concurrent users and 500 requests in total)
docker-compose exec jdbc-bridge ab -c 5 -n 500 "http://ch-server:8123/?query=select+*+from+jdbc%28%27ch%27%2C+%27%27%2C+%27select+*+from+system.processes%27%29&user=default&password="

# check server error log again and you'll likely see errors like not running and timeout
docker-compose exec ch-server cat /var/log/clickhouse-server/server.err
```
