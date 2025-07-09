FROM openjdk:11 AS build_image
RUN apt update && apt install -y maven git
RUN git clone https://github.com/Mikzzer/prom.git
RUN cd prom && mvn install

FROM tomcat:9-jre11

ENV JMX_EXPORTER_VERSION=0.20.0
ADD https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/${JMX_EXPORTER_VERSION}/jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar /opt/jmx_prometheus_javaagent.jar

COPY jmx-exporter-config.yaml /opt/jmx-exporter-config.yaml

RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=build_image prom/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

ENV CATALINA_OPTS="-javaagent:/opt/jmx_prometheus_javaagent.jar=9404:/opt/jmx-exporter-config.yaml"

EXPOSE 8080 9404
CMD ["catalina.sh", "run"]
