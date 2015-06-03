# Services:

* DB (postgres)
  * 8.4 (latest) <- debian:wheezy
  * ports
    * 5432
  * vol
    * /var/lib/postgresql/data
* OpenJDK 8
  * sonatype/nexus 
    * oss (latest) <- centos:centos7
    * ports
      * 8081
    * env
      * SONATYPE_WORK=/sonatype-work
    * vol
      * ${SONATYPE_WORK}
  * SonarQube (official) -> DB
    * 5.1(latest) <- java:openjdk-8u45-jre <- buildpack-deps:jessie-curl
    * ports
      * 9000
      * 9092
    * env
      * SONARQUBE_JDBC_USERNAME=sonar
      * SONARQUBE_JDBC_PASSWORD=sonar
      * SONARQUBE_JDBC_URL=jdbc:postgresql://db/sonar
    * Jenkins (official)
      * 1.609.1 < java:8u45-jdk (==java:openjdk-8u45-jdk) <- buildpack-deps:jessie-curl
      * ports 
        * 8080
      * volumes
        *  /var/jenkins_home




## Pulling images ##
```
docker pull jenkins
docker pull sonarqube
docker pull postgres
docker pull sonatype/nexus:oss
```

```
docker run -d --name sonar-db -e "POSTGRES_USER=sonar" -e "POSTGRES_PASSWORD=sonar" postgres
docker run -d  --name  jenkins jenkins
docker run -d --name nexus sonatype/nexus:oss
docker run -d  --name sonar -e "SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar" --link sonar-db:db sonarqube
```
Problem. Ports are only accessible at specific containers' IP address

Automatic-mapping

```
docker run -d --name sonar-db -e "POSTGRES_USER=sonar" -e "POSTGRES_PASSWORD=sonar" postgres
docker run -d -P --name  jenkins jenkins
docker run -d -P --name nexus sonatype/nexus:oss
docker run -d -P --name sonar -e "SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar" --link sonar-db:db sonarqube
```
Problem:  ports are random. Accepts traffic from everywhere

```
docker run -d --name sonar-db -e "POSTGRES_USER=sonar" -e "POSTGRES_PASSWORD=sonar" postgres
docker run -d -p 127.0.0.1:8080:8080 -p 127.0.0.1:50000:50000 --name  jenkins jenkins
docker run -d -p 127.0.0.1:8081:8081 --name nexus sonatype/nexus:oss
docker run -d -p 127.0.0.1:9000:9000 -p 127.0.0.1:9092:9092 --name sonar -e "SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar" --link sonar-db:db sonarqube
```



```
# removing container WITH volumes
docker rm -v {container}   

# inspect container's IP address
docker inspect -f '{{.NetworkSettings.IPAddress}}'  {container}
```




Componenten: DB, Nexus, Jenkins, Sonar
Configureren Dockerfiles voor componenten
Volumes
ports
Draaien van alle componenten, inc. linken
Orchestration
