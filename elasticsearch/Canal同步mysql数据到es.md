# Canal同步mysql数据到es

## 环境信息

## mysql

修改my.ini，开启binlog

```yaml
[mysqld]
log-bin=mysql-bin
binlog-format=ROW
server-id=1
```

使用docker-compose启动mysql

```
version: '3'

services:
  db:
    container_name: mysql8
    image: mysql:8.0.25
    command: mysqld --default-authentication-plugin=mysql_native_password --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: user
      MYSQL_ALLOW_EMPTY_PASSWORD: "yes"
    ports:
      - '3306:3306'
    volumes:
      - './docker/db/data:/var/lib/mysql'
      - './docker/db/my.cnf:/etc/mysql/conf.d/my.cnf'
      - './docker/db/sql:/docker-entrypoint-initdb.d'
```





## 完整代码











