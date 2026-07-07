FROM maven:3.9.9-eclipse-temurin-17
WORKDIR /app
RUN adduser --system --group appuser
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package
USER appuser
EXPOSE 8080
CMD ["java", "-jar", "target/demo-0.0.1-SNAPSHOT.jar"]
