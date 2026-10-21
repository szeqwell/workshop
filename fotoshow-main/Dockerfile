# ---- Build stage ----
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /build

# Copy the POM first so dependencies are cached as their own layer
COPY pom.xml .
RUN mvn -B dependency:go-offline

# Copy sources and build the fast-jar
COPY src ./src
RUN mvn -B package -DskipTests

# ---- Runtime stage ----
FROM eclipse-temurin:21-jre-alpine

ENV LANGUAGE="en_US:en"
WORKDIR /work/

# Copy the quarkus fast-jar layout from the build stage
COPY --from=build /build/target/quarkus-app/lib/ /work/lib/
COPY --from=build /build/target/quarkus-app/*.jar /work/
COPY --from=build /build/target/quarkus-app/app/ /work/app/
COPY --from=build /build/target/quarkus-app/quarkus/ /work/quarkus/

# Quarkus app port
EXPOSE 18181

# Ensure Quarkus binds to all interfaces inside the container
ENV JAVA_OPTS="-Dquarkus.http.host=0.0.0.0 -Djava.util.logging.manager=org.jboss.logmanager.LogManager"

# Run the application
ENTRYPOINT ["java","-jar","/work/quarkus-run.jar"]