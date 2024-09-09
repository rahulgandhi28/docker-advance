# Docker-Advance- Assignment

 1. Optimized Dockerfile for Build Speed and Image Size

 
# Original Docker File

##
  
FROM maven:3.5-jdk-8

COPY src /usr/src/myapp/src
COPY pom.xml /usr/src/myapp
RUN mvn -f /usr/src/myapp/pom.xml clean package

ENV WILDFLY_VERSION 10.1.0.Final
ENV WILDFLY_HOME /usr

RUN cd $WILDFLY_HOME && curl http://download.jboss.org/wildfly/$WILDFLY_VERSION/wildfly-$WILDFLY_VERSION.tar.gz | tar zx && mv $WILDFLY_HOME/wildfly-$WILDFLY_VERSION $WILDFLY_HOME/wildfly

RUN cp /usr/src/myapp/target/people-1.0-SNAPSHOT.war $WILDFLY_HOME/wildfly/standalone/deployments/people.war

EXPOSE 8080

CMD ["/usr/wildfly/bin/standalone.sh", "-b", "0.0.0.0"]

'''

# Optimised Docker File 
'''
# Stage 1: Build the application
FROM maven:3.5-jdk-8 AS build

WORKDIR /usr/src/myapp

COPY pom.xml .
COPY src ./src

# Build the application
RUN mvn clean package

# Stage 2: Setup WildFly and deploy the application
FROM openjdk:8-jdk-alpine

ENV WILDFLY_VERSION 10.1.0.Final
ENV WILDFLY_HOME /opt/wildfly

# Install necessary dependencies
RUN apk --no-cache add curl tar

# Download and install WildFly
RUN curl -L http://download.jboss.org/wildfly/$WILDFLY_VERSION/wildfly-$WILDFLY_VERSION.tar.gz | tar xz -C /opt && \
    mv /opt/wildfly-$WILDFLY_VERSION $WILDFLY_HOME

# Copy the WAR file from the build stage
COPY --from=build /usr/src/myapp/target/people-1.0-SNAPSHOT.war $WILDFLY_HOME/standalone/deployments/people.war

# Expose the application port
EXPOSE 8080

# Start WildFly
CMD ["sh", "-c", "$WILDFLY_HOME/bin/standalone.sh -b 0.0.0.0"]

'''

Thanks


