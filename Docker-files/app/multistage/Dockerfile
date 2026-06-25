FROM maven:3.9.9-eclipse-temurin-21-jammy AS build_image
RUN git clone https://github.com/hkhcoder/vprofile-project.git
RUN cd vprofile-project && git checkout docker && mvn install

FROM tomcat:10-jdk21

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=build_image vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Install curl
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

# Create directory and download JMX exporter
RUN mkdir -p /opt/jmx && \
    curl -fsSL \
    https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.20.0/jmx_prometheus_javaagent-0.20.0.jar \
    -o /opt/jmx/jmx_prometheus_javaagent.jar

COPY Docker-files/app/multistage/jmx-config.yaml /opt/jmx/jmx-config.yaml

ENV CATALINA_OPTS="-javaagent:/opt/jmx/jmx_prometheus_javaagent.jar=9404:/opt/jmx/jmx-config.yaml"

EXPOSE 8080
EXPOSE 9404

CMD ["catalina.sh", "run"]
