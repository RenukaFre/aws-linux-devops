# Docker Multi-Stage Build

## Overview
Docker Multi-Stage builds are mainly used to **reduce the size of the Docker image** by separating the build environment from the final runtime environment.

## Start & Enable Docker
```sh
sudo systemctl start docker
sudo systemctl enable docker
```

## Check Docker Images
```sh
docker images
```

If the storage size is high, we can use the **multi-stage build** concept to optimize it.

## Clone the Repository
```sh
git clone https://github.com/rskTech/multi-stage-example.git
ls
```
You will find a `multi-stage-example` folder.
```sh
cd multi-stage-example
```

## Sample Dockerfile for a Single-Stage Build
```dockerfile
FROM openjdk:8-jdk-alpine as builder
RUN mkdir -p /app/source
COPY . /app/source
WORKDIR /app/source
RUN ./mvnw clean package
EXPOSE 8080
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom", "-jar", "/app/app.jar"]
```

### Build & Check the Image
```sh
docker ps
docker build -t maven2:v2 . --target=builder
docker images
```
You will notice the JAR file size has been reduced.

### Build the Full Image
```sh
docker build -t maven1:v1 .
```

## Using a Multi-Stage Build

```sh
mkdir /multistage
cd /multistage
git clone https://github.com/rskTech/multi-stage-example.git
cd multi-stage-example
```

### Optimized Multi-Stage Dockerfile
```dockerfile
# Stage 1: Build the application
FROM openjdk:8-jdk-alpine as builder
RUN mkdir -p /app/source
COPY . /app/source
WORKDIR /app/source
RUN ./mvnw clean package

# Stage 2: Create a minimal runtime image
FROM openjdk:8-jdk-alpine
COPY --from=builder /app/source/target/*.jar /app/app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app/app.jar"]
```

### Build & Verify the Multi-Stage Image
```sh
docker build -t maven2:v2 . --target=builder
docker images
```

## Benefits of Multi-Stage Builds
✅ **Reduces Image Size**: Only necessary runtime files are included.
✅ **Better Security**: No build tools like Maven are in the final image.
✅ **Faster Deployments**: Smaller images result in quicker downloads and startups.
✅ **Lightweight Alpine Base**: Ensures minimal dependencies and reduces attack surface.

Would you like further optimizations, such as using a JRE instead of a JDK for a smaller runtime image? 🚀

