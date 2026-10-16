1-1. We can set an environment variable in the container by using the flag -e, which is useful when dealing with sensitive information like passwords, without hardcoding it in the Dockerfile.

1-2. If a database is destroyed, all the data will be reset. By attaching a volume to our postgres container, the data won't be lost.

1-3.  build -t elodiefong/db-img .
docker network create app-network
docker run -p "8090:8080" --net=app-network --name=adminer -e POSTGRES_PASSWORD=securepwd -d adminer
docker run --name=db --net=app-network -d elodiefong/db-img
docker volume create db-data

1-4. Multi-stage builds are useful to optimize Dockerfiles while keeping them easy to read and maintain. They separate compilation from execution, reducing the final image size by excluding unnecessary build tools.
- FROM eclipse-temurin:21-jdk-alpine AS myapp-build: creates the build stage with the JDK.
- ENV MYAPP_HOME=/opt/myapp: defines the application directory.
- WORKDIR $MYAPP_HOME: sets the working directory.
- RUN apk add --no-cache maven: installs Maven.
- COPY pom.xml . and COPY src ./src: copy the project files.
- RUN mvn package -DskipTests: compiles the application and creates the JAR.
- FROM eclipse-temurin:21-jre-alpine: starts a smaller runtime stage.
- WORKDIR $MYAPP_HOME: sets the runtime directory.
- COPY --from=myapp-build ...: copies the JAR from the build stage.
- ENTRYPOINT ["java", "-jar", "myapp.jar"]: starts the application.

1-5. We need a reverse proxy to isolate the backend infrastructure from the external network for security, high availability, load balancing, and/or centralized authentication.

1-6. docker-compose manages multiple containers through a single YAML file, making it easier to configure, start, stop, and rebuild the entire application.

1-7. docker compose up: Starts services
docker compose up -d: Starts services in the background
docker compose up --build: Rebuilds images and starts services
docker compose down: Stops and removes containers and networks
docker compose ps: Lists containers
docker compose logs: Displays logs
docker compose build: Builds images
docker compose pull: Downloads images
docker compose restart: Restarts services
docker compose stop: Stops services

1-8. The Compose file defines three services: backend, database, and httpd.
- build: specifies the directory containing each Dockerfile.
- networks: connects containers through app-network.
- depends_on: defines startup dependencies.
- ports: exposes Apache to the host.
- environment: configures database credentials.
- volumes: persists PostgreSQL data.
- restart: can configure automatic container restarts.

1-9. docker tag docker_tp1-backend:latest elodiefong/tp1-backend:1.0
docker tag docker_tp1-database:latest elodiefong/tp1-database:1.0
docker tag docker_tp1-httpd:latest elodiefong/tp1-httpd:1.0
docker push elodiefong/tp1-backend:1.0
docker push elodiefong/tp1-database:1.0
docker push elodiefong/tp1-httpd:1.0

1-10. Publishing images to Docker Hub allows other developers and machines to download and run them without rebuilding them. It simplifies sharing, deployment, and version management.

2-1. Testcontainers is a Java library that allows you to run Docker containers during tests. 

2-2. Secured variables store sensitive information such as passwords or access tokens without exposing them in the source code. This prevents other people from accessing your accounts or using your credentials.

2-3. needs: test-backend ensures that the Docker image build job only runs after the test-backend job has succeeded. If the tests fail, the Docker images will not be built or published.

2-4. We push docker images to store them and they can be accessed by other machines or servers. The application can be deployed without rebuilding the images each time.

3-1,2,3. In the folder ansible. The screenshots of the commands and the outputs can be seen here: https://docs.google.com/document/d/1SnOcGZpv-2mUryC5VJhJXFDURuvcfEKry8sLP3YxiYo/edit?usp=sharing

3-4. Automatically deploying every new image from Docker Hub is not completely safe because an image could contain bugs or security vulnerabilities. To make the deployment more secure, tests should be run before deployment, images should be scanned for vulnerabilities, and version tags should be used instead of only `latest`. Credentials should also be stored securely using GitHub Secrets. Finally, manual approval and a rollback option can help prevent problems in production.
