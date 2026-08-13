FROM eclipse-temurin:17-jdk-jammy
ARG GF_VERSION=7.0.21

RUN apt-get update && apt-get install -y wget unzip \
    && wget -q https://download.eclipse.org/ee4j/glassfish/glassfish-${GF_VERSION}.zip -O /tmp/gf.zip \
    && unzip -q /tmp/gf.zip -d /opt \
    && rm /tmp/gf.zip \
    && rm -rf /var/lib/apt/lists/*

ENV GLASSFISH_HOME=/opt/glassfish7
ENV PATH="$GLASSFISH_HOME/bin:$PATH"

ADD https://repo1.maven.org/maven2/com/mysql/mysql-connector-j/8.4.0/mysql-connector-j-8.4.0.jar \
    $GLASSFISH_HOME/glassfish/domains/domain1/lib/mysql-connector-j-8.4.0.jar

COPY PROYECTO-VITALD.war /app.war

EXPOSE 8080

CMD ["/bin/sh", "-c", "\
    asadmin start-domain && \
    asadmin set server-config.network-config.network-listeners.network-listener.http-listener-1.address=0.0.0.0 && \
    asadmin deploy --contextroot / /app.war && \
    tail -f $GLASSFISH_HOME/glassfish/domains/domain1/logs/server.log \
"]
