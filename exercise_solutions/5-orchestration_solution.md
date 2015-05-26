---
# Container orchestration
---

With Docker, you create containers that each encapsulate a single service or application. 
When you divide a system into a bunch of collaborating services, the services need to be able to access each other in a convenient way. 
Docker Compose provides the concept of _container linking_ for this.


**1. Organize sources**

```
cd ~/excercise/wordpress
tar -xzf sources/latest.tar.gz
cp sources/router.php sources/wp-config.php wordpress
cd wordpress
```

**2. Create Docker file**

```
FROM sogeti:5000/php5
MAINTAINER Sogeti Dev
ADD . /code
```

**3. Build image**
	
```
docker build -t wordpress .
``` 


**4. Start wordpress**

```
docker run -d -p 8080:8080 wordpress php -S 0.0.0.0:8080 -t /code
```

And stop it:

```
docker stop wordpress
docker rm wordpress
```

**5. Start db and linked wordpress**

```
docker run -d --name mydb -e MYSQL_DATABASE=wordpress sogeti:5000/mysql
docker run -d -p 8080:8080 --link mydb:db wordpress php -S 0.0.0.0:8080 -t /code
```

**6. Verify**

Open http://localhost:8080

**7. Clean**

Stop the containers

```
docker stop mydb wordpress
docker rm mydb wordpress
```

## Docker orchestration ##

You can reuse the Dockerfile


The _docker-compose.yml_ file should contain :

```
wordpress:
  build: .
  command: php -S 0.0.0.0:8080 -t /code
  ports:
    - "8080:8080"
  links:
    - mydb:db
  volumes:
    - .:/code
mydb:
  image: sogeti:5000/mysql
  environment:
    MYSQL_DATABASE: wordpress

```

