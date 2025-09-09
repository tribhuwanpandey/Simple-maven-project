# Simple Maven App - CI/CD + Docker + Nexus

This is a simple Java application using Maven, Jenkins pipeline, Docker, and Nexus.

## Structure

- `src/main/java/com/example/App.java` — simple Java Hello World.
- `pom.xml` — Maven build configuration.
- `Dockerfile` — containerization.
- `Jenkinsfile` — CI/CD pipeline.
- `README.md` — project documentation.

## Instructions

1. Build locally: `mvn clean package`
2. Run locally: `java -jar target/my-app-1.0-SNAPSHOT.jar`
3. Build Docker: `docker build -t my-app-image:latest .`
4. Run Docker: `docker run --rm my-app-image:latest`
5. Push to GitHub & configure Jenkins pipeline.