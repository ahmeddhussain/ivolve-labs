FROM eclipse-temurin:17-jre
WORKDIR /app2
RUN adduser --system --group appuser
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
USER appuser
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
