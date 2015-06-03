# Services:

* DB (postgres)
* OpenJDK 8
  * Nexus -> DB
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
    *Jenkins (official)
      * 1.609.1 < java:8u45-jdk (==java:openjdk-8u45-jdk) <- buildpack-deps:jessie-curl
      * ports 
        * 8080
      * volumes
        *  /var/jenkins_home




## Pulling images ##
```
docker pull jenkins
docker pull sonarqube
```



Componenten: DB, Nexus, Jenkins, Sonar
Configureren Dockerfiles voor componenten
Volumes
ports
Draaien van alle componenten, inc. linken
Orchestration
