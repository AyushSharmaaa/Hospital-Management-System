# Use OpenJDK 17 as base image
FROM openjdk:17-jdk-slim

# Install curl and netcat for healthchecks
RUN apt-get update && apt-get install -y curl netcat && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy Maven wrapper and pom.xml first for caching
COPY .mvn .mvn
COPY mvnw .
COPY pom.xml .

# Make mvnw executable
RUN chmod +x ./mvnw

# Download dependencies
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src src

# Build the application
RUN ./mvnw clean package -DskipTests

# Expose port
EXPOSE 8080

# Healthcheck
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=5 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1

# Run the application
CMD ["java", "-Dserver.port=8080", "-Dspring.profiles.active=${SPRING_PROFILES_ACTIVE:-prod}", "-jar", "target/hospital-management-system-2.0.0.jar"]