1. Create two Podman networks called beeper-backend and beeper-frontend that have DNS enabled.
  podman network create beeper-backend,beeper-frontend
Create a PostgreSQL database container that matches the following criteria:
  Use the registry.ocp4.example.com:8443/rhel9/postgresql-13:1 container image.
  Use beeper-db for the name of the container.
    --name beeper-db
  Connect the container to the beeper-backend network.
    --net beeper-backend
Attach a new volume called beeper-data to the /var/lib/pgsql/data directory in the container.
  podman volume create beeper-data  -v beeper-data:/var/lib/pgsql1/data:Z
Pass the following environment variables to the container:
Name: POSTGRESQL_USER, value: beeper
  -e POSTGRESQL_USER=beeper
Name: POSTGRESQL_PASSWORD, value: beeper123
  -e POSTGRESQL_PASSWORD=beeper123
Name: POSTGRESQL_DATABASE, value: beeper
  -e POSTGRESQL_DATABASE=beeper
podman run -d --name beeper-db --network beeper-backend -v beeper-data:/var/lib/pgsql1/data:Z \
  -e POSTGRESQL_USER=beeper \
  -e POSTGRESQL_PASSWORD=beeper123 \
  -e POSTGRESQL_DATABASE=beeper \
  registry.ocp4.example.com:8443/rhel9/postgresql-13:1

2. cd ~/DO188/labs/comprehensive-review/beeper-backend  
vi Containerfile
Create a container image for the Beeper API that matches the following criteria:
Use a multi-stage build with a builder image to compile the Java application.
The build stage should perform the following actions:
Use the registry.ocp4.example.com:8443/ubi8/openjdk-17:1.12 container image. This image uses /home/jboss as the working directory.
  FROM registry.ocp4.example.com:8443/ubi8/openjdk-17:1.12 as builder
Copy the contents of the ~/DO188/labs/comprehensive-review/beeper-backend host directory to the image's working directory. Change the owner to the jboss user.
  COPY --chown=jboss ~/DO188/labs/comprehensive-review/beeper-backend . 
Use the mvn -s settings.xml package command to create the /home/jboss/target/beeper-1.0.0.jar binary file.
  RUN mvn -s setting.xml package
The execution stage should perform the following actions:
Use the container image registry.ocp4.example.com:8443/ubi8/openjdk-17-runtime:1.12.
  FROM registry.ocp4.example.com:8443/ubi8/openjdk-17-runtime:1.12
Copy the beeper-1.0.0.jar binary file from the builder image.
  COPY --from=builder /home/jboss/target/beeper-1.0.0.jar .
Run the java -jar beeper-1.0.0.jar command to start the Beeper API.
  ENTRYPOINT ["java", "-jar", "beeper-1.0.0.jar"]
Tag the image with beeper-api:v1.
  podman build -t beeper-api:v1
Create a container for the Beeper API that matches the following criteria:
Use the beeper-api:v1 image that you created.
Use beeper-api for the name of the container.
Pass an environment variable to the container called DB_HOST with beeper-db as the value.
  -e DB_HOST=beeper-db
Connect the container to the beeper-backend and beeper-frontend networks.
  --network beeper-backend, beeper-frontend
Final:  podman run -d --name beeper-api --network beeper-frontend, beeper-backend -e DB_HOST=beeper-db beeper-api:v1

3. cd ~/DO188/labs/comprehensive-review/beeper-ui
vi Containfile
Create a container image for the Beeper UI that matches the following criteria:
Use a multi-stage build with a builder image to compile the TypeScript React application.
The build stage should perform the following actions:
Use the registry.ocp4.example.com:8443/ubi8/nodejs-16:1 container image. This image uses /opt/app-root/src as the working directory.
  FROM registry.ocp4.example.com:8443/ubi8/nodejs-16:1 AS builder
Set root as the user.
  USER root
Copy the contents of the ~/DO188/labs/comprehensive-review/beeper-ui host directory into the image's working directory.
  COPY ~/DO188/labs/comprehensive-review/beeper-ui .
Run the npm install command in the application directory to install build dependencies within the image.
  RUN npm install 
Run the npm run build command in the application directory to build the application. This command saves the built artifacts in the /opt/app-root/src/build directory.
  RUN npm run build 
The execution stage should perform the following actions:
Use the registry.ocp4.example.com:8443/ubi8/nginx-118:1 container image.
  FROM registry.ocp4.example.com:8443/ubi8/nginx-118:1 
Copy the ~/DO188/labs/comprehensive-review/beeper-ui/nginx.conf host file into the /etc/nginx/ directory of the execution stage.
  COPY ~/DO188/labs/comprehensive-review/beeper-ui/nginx.conf /etc/nginx/
Copy the built application artifacts from the builder stage into the /usr/share/nginx/html directory of the execution stage.
  COPY --from=builder /opt/app-root/src/build /usr/share/nginx/html
Use the nginx -g "daemon off;" command to start the application.
  COMMAND nginx -g "daemon off;"
Tag the image with beeper-ui:v1.
  podman build -t beeper-ui:v1 .
Create a container for the Beeper UI that matches the following criteria:
Use the beeper-ui:v1 image that you created.
Use beeper-ui for the container name.
Map container port 8080 to host port 8080.
  -p 8080:8080
Connect the container to the beeper-frontend network.
  --net beeper-frontend
With the three containers running and configured correctly, the UI is available at http://localhost:8080.
Final: podman run -d --name beeper-ui --net beeper-frontend -p 8080:8080 beeper-ui:v1
