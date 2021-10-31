[![GitHub license](https://img.shields.io/badge/dwsclass-009--compose-orange?style=plastic)](https://github.com/mi-alkhamis/docker-compose-cheatsheet/)
[![GitHub license](https://img.shields.io/github/license/mi-alkhamis/docker-compose-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-compose-cheatsheet/blob/main/LICENSE)
[![GitHub forks](https://img.shields.io/github/forks/mi-alkhamis/docker-compose-cheatsheet?style=plastic)](https://github.com/mi-alkhamis/docker-compose-cheatsheet/network)
[![GitHub stars](https://img.shields.io/github/stars/mi-alkhamis/docker-compose-cheatsheet?color=red&style=plastic)](https://github.com/mi-alkhamis/docker-compose-cheatsheet/stargazers)

# Docker Compose Cheatsheet

![docker-compose-logo](https://tcude.net/content/images/2021/08/docker-compose-button.jpg)

 1. What is docker-compose 

    We use Docker-compose to build and deploy our app(Dockerize them
    to do this, need a file named compose file in YAML format like
   
```yaml
version: !!str 3
services:
  app:
    image: alpine:latest
```

2. simple Compose file to build and deploy a simple app


```yaml
version: !!str 3
services:
  myapp:
    build:
      context: .
    dns:
      - 127.0.0.11
      - 8.8.8.8
    restart: unless-stopped
    domainname: airtable-ir
    hostname: db-backup
```

3. Write a compose file to deploy MySql and phpMyAdmin (Production way)
```yaml
version: !!str 3
services:
  nginx:
    image: nginx:stable
    restart: unless-stopped
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - log_volume:/var/log/nginx/
    ports:
      - 80:80
    labels:
      - "com.airdb.app=airdb"
      - "com.airdb.db=mysql"
      - "com.airdb.partof=zoogila"
      - "com.airdb.made_by=airdb team"
    domainname: airdb.ir
    hostname: loadbalancer
    dns:
      - 127.0.0.11
      - 4.2.2.4 
    depends_on:
      - mysql
      - phpmyadmin
    networks:
      - edge

  phpmyadmin:
    image: phpmyadmin:5-apache
    restart: unless-stopped  
    environment:
      PMA_HOST: mysql
    labels:
      - "com.airdb.app=airdb"
      - "com.airdb.db=mysql"
      - "com.airdb.partof=zoogila"
      - "com.airdb.made_by=airdb team"
    domainname: airdb.ir
    deploy:
      mode: replicated
      replicas: 3 
    dns:
      - 127.0.0.11
      - 4.2.2.4
    depends_on:
      - mysql
    networks:
      - db_connection
      - edge
  
  mysql:
    image: mysql:8
    restart: unless-stopped
    labels:
      - "com.airdb.app=airdb"
      - "com.airdb.db=mysql"
      - "com.airdb.partof=zoogila"
      - "com.airdb.made_by=airdb team"
    domainname: airdb.ir 
    hostname: db
    dns:
      - 127.0.0.11
      - 4.2.2.4
    env_file:
      - .env
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - db_connection    

networks:
  db_connection:
  edge:
    external: yes

volumes:
  mysql_data:
  log_volume:
```


4. How to use a pre-defined network in compose file
```yaml
version: !!str 3
services:
  nginx:
    image: nginx:stable
    ....
    ....
    ....
    networks:
      - edge
      
networks:
  db_connection:
  edge:
    external: yes

```
5. How to scale Docker containers in production?

     first of all, the app must be stateless(scaling stateful apps a bit different like MySQL)
     in compose file, add
```yaml
 deploy:
      mode: replicated
      replicas: 3 
```
     after that, you need a Load Balancer like Nginx or HAProxy, I write one for Q2 

6. how to see the log of stack app deployed with docker-compose

` docker-compose logs -f`


[@dwsclass](https://github.com/dwsclass) dws-ops-008-compose
