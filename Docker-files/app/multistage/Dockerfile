FROM openjdk:11 AS BUILD_IMAGE
RUN apt update && apt install -y maven git
RUN git clone https://github.com/devopshydclub/vprofile-project.git
RUN cd vprofile-project && git checkout docker && mvn install

FROM tomcat:9-jre11

# --- JMX Exporter ---
ENV JMX_EXPORTER_VERSION=0.20.0
ADD https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/${JMX_EXPORTER_VERSION}/jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar /opt/jmx_prometheus_javaagent.jar

# Dodaj konfigurację exportera
COPY jmx-exporter-config.yaml /opt/jmx-exporter-config.yaml

# Usuń domyślne aplikacje
RUN rm -rf /usr/local/tomcat/webapps/*

# Skopiuj WAR
COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Ustaw JAVA_OPTS do uruchamiania JMX Exportera na porcie 9404
ENV JAVA_OPTS="-javaagent:/opt/jmx_prometheus_javaagent.jar=9404:/opt/jmx-exporter-config.yaml"

EXPOSE 8080 9404
CMD ["catalina.sh", "run"]
