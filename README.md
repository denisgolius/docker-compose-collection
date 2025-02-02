# docker-compose collection

### Contents

- [Database](#database)
- [Logs](#logs)
- [Messaging](#messaging)
- [Metrics](#metrics)
- [Proxy](#proxy)
- [Services](#services)
- [Storage](#storage)

### Database

- [MariaDB](#MariaDB)
- [Mongo](#Mongo)
- [MySQL](#MySQL)
- [Postgres](#Postgres)

#### MariaDB
```yaml
services:
  db-mariadb:
    image: mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: example
      MYSQL_USER: example
      MYSQL_PASSWORD: example
      MYSQL_ALLOW_EMPTY_PASSWORD: yes # no
      MYSQL_RANDOM_ROOT_PASSWORD: yes # <not set>
```

#### Mongo
```yaml
services:
  db-mongo:
    image: mongo
    restart: always
    environment:
      MONGO_INITDB_DATABASE: db
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: example

  db-mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: example
```

#### MySQL
```yaml
services:
  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE:
      MYSQL_USER:
      MYSQL_PASSWORD:
      MYSQL_ALLOW_EMPTY_PASSWORD: 
      MYSQL_RANDOM_ROOT_PASSWORD: 
      MYSQL_ONETIME_PASSWORD: 
```

#### Postgres:
```yaml
services:
  db-postgres:
    image: postgres:11.3
    container_name: postgres_container
    restart: unless-stopped
    tty: true
    ports:
      - "5432:5432"
    environment:
      - PGHOST=localhost
      - PGDATABASE=postgres
      - PGUSER=postgres
      - POSTGRES_PASSWORD=
    volumes:
      - my-db-postgres:/var/lib/postgresql/data

volumes:
  my-db-postgres:
```

### Logs
### Messaging

- [RabbitMQ](#RabbitMQ)

#### RabbitMQ
```yaml
services:
  rabbitmq:
    image: rabbitmq:3.7-management-alpine
    restart: unless-stopped
    ports:
      - "5672:5672" # queue connection
      - "15672:15672" # management UI
    environment:
      - RABBITMQ_DEFAULT_USER=
      - RABBITMQ_DEFAULT_PASS=
      - RABBITMQ_DEFAULT_VHOST=/
      - RABBITMQ_HIPE_COMPILE=0
    volumes:
      - rabbitmq:/var/lib/rabbitmq
    hostname: rabbitmq

volumes:
  rabbitmq:
```

### Metrics
### Proxy

- [Nginx](#Nginx)

#### Nginx
```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
    environment:
      - NGINX_HOST=foobar.com
      - NGINX_PORT=80
    volumes:
      - ./mysite.template:/etc/nginx/conf.d/mysite.template
    # command: [nginx-debug, '-g', 'daemon off;']
```

### Services

- [Consul](#Consul)
- [Zookeeper](#Zookeeper)

#### Consul
```yaml
  consul:
    image: consul
    command: "agent -dev -bind=0.0.0.0 -client=0.0.0.0 -server -ui -bootstrap -config-file=/config/consul.json -enable-script-checks"
    ports:
      - 8500:8500
    dns_search: .
    volumes:
      - ./consul.json:/config/consul.json
```

#### Zookeeper
```yaml
services:
  zoo1:
    image: zookeeper
    restart: always
    hostname: zoo1
    ports:
      - 2181:2181
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
      ZOO_TICK_TIME: 2000
      ZOO_INIT_LIMIT: 5
      ZOO_SYNC_LIMIT: 2
      ZOO_MAX_CLIENT_CNXNS: 60
      ZOO_STANDALONE_ENABLED: true
      ZOO_AUTOPURGE_PURGEINTERVAL: 0
      ZOO_AUTOPURGE_SNAPRETAINCOUNT: 3

  zoo2:
    image: zookeeper
    restart: always
    hostname: zoo2
    ports:
      - 2182:2181
    environment:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=0.0.0.0:2888:3888 server.3=zoo3:2888:3888

  zoo3:
    image: zookeeper
    restart: always
    hostname: zoo3
    ports:
      - 2183:2181
    environment:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=0.0.0.0:2888:3888
```

### Storage

- [Memcached](#Memcached)
- [Redis](#Redis)

#### Memcached
```yaml
services:
  memcached:
    image: memcached:1-alpine
    restart: unless-stopped
    ports:
      - "11211:11211"
```

#### Redis
```yaml
services:
  redis:
    image: redis:5-alpine
    restart: unless-stopped
    ports:
      - "6379:6379"
    volumes:
      - redis:/data
    command: redis-server --appendonly yes

volumes:
  redis:
```
