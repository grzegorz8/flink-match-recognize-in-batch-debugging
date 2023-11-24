ARG FLINK_VERSION
FROM flink:${FLINK_VERSION}-scala_2.12-java11
ARG FLINK_VERSION

ENV FLINK_LIB_DIR=/opt/flink/lib

# Reinstall java
RUN apt-get update && \
    apt-get install -y build-essential openjdk-11-jdk-headless vim && \
    apt-get clean -y && \
    apt-get autoremove -y && \
    rm -rf /tmp/* /var/tmp/* && \
    rm -rf /var/lib/apt/lists/*

# Get additional tools
RUN wget https://repo1.maven.org/maven2/org/apache/flink/flink-python/${FLINK_VERSION}/flink-python-${FLINK_VERSION}.jar \
    -O $FLINK_LIB_DIR/flink-python-${FLINK_VERSION}.jar
RUN wget https://repo1.maven.org/maven2/org/postgresql/postgresql/42.7.0/postgresql-42.7.0.jar -O $FLINK_LIB_DIR/postgresql-42.7.0.jar
RUN wget https://repo1.maven.org/maven2/org/apache/flink/flink-connector-jdbc/3.1.1-1.17/flink-connector-jdbc-3.1.1-1.17.jar \
    -O $FLINK_LIB_DIR/flink-connector-jdbc-3.1.1-1.17.jar

