# MySQL的NDB Cluster
MySQL的NDB Cluster的一个docker-compose方案，可以方便进行本机测试

## 使用
```
docker-compose up -d
```

## 修改密码
编辑docker-compose.xml，修改mysql1下面的
```yaml
mysql1:
    image: mysql/mysql-cluster
    ports:
      - "3306:3306"
    volumes:
       - ./mysql.conf/my.cnf:/etc/my.cnf
       - ./mysql.conf/mysql-cluster.cnf:/etc/mysql-cluster.cnf
#    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: test
      MYSQL_USER: test
      MYSQL_PASSWORD: test
    #command: bash -c 'sleep 60; exec mysqld'
    command: mysqld
    depends_on:
      - "management1"
      - "ndb1"
      - "ndb2"
```

## 添加NDB节点数量
修改mysql.conf/mysql-cluster.cnf，添加新的节点信息
```
[ndbd]
NodeId=5
hostname=ndb3
datadir=/var/lib/mysql
```

编辑docker-compose.yml
```yaml
ndb3:
    image: mysql/mysql-cluster
    volumes:
       - ./mysql.conf/my.cnf:/etc/my.cnf
       - ./mysql.conf/mysql-cluster.cnf:/etc/mysql-cluster.cnf
    #command: bash -c 'sleep 40; exec ndbd'
    command: ndbd
    depends_on:
      - "management1"
```

编辑下mysql1节点下面的
```
depends_on:
      - "management1"
      - "ndb1"
      - "ndb2"
      - "ndb3"
```