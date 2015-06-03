# Services:
DB (postgres)
OpenJDK 8
Nexus -> DB
SonarQube (official) -> DB
5.1 <- java:openjdk-8u45-jre
  
Jenkins (official)
p: 8080
v:  /var/jenkins_home









Componenten: DB, Nexus, Jenkins, Sonar
Configureren Dockerfiles voor componenten
Volumes
ports
Draaien van alle componenten, inc. linken
Orchestration
