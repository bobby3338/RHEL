TASK1:
  Pull the latest httpd image from the registry.do180.lab registry and run it as root it in the background with the following parameters:
  Publish container port 80 on host port 8080  
  Attach the /web directory as a volume that maps to /usr/local/apache2/htdocs
  Give the container the name reg-httpd
  Make the container start as a service called reg-httpd.service.

Solution:
podman run --name=reg-httpd -d -p 8080:80 -v /web:/usr/local/apache2/htdocs:Z registry.do180.lab:5000/httpd
podman generate systemd --name reg-httpd --new --files 
systemctl enable reg-httpd.service
systemctl start reg-httpd.service
systemctl status reg-httpd.service
loginctl enable-linger
loginctl enable-linger <username>
$HOME/.config/systemd/user
$ podman pod create --name systemd-pod
$ podman create --pod systemd-pod alpine top
$ podman create --pod systemd-pod alpine top
$ podman generate systemd --files --name systemd-pod
systemctl --user enable <.service>
systemctl --user start pod-systemd-pod.service
To perform systemctl actions as a non-root user use the --user flag when interacting with systemctl.


podman generate systemd --name bb310a0780ae--new --files --name bb310a0780ae
TASK2:
  Pull the latest version of the mariadb image from the registry on registry.do180.lab. Run a container in the background with the following parameters:
  Give the container the name of testql
  Publish port 3306.
  Set the following environment variables:
  MYSQL_USER: duffman
  MYSQL_PASSWORD: saysoyeah
  MYSQL_ROOT_PASSWORD: SQLp4ss
  MYSQL_DATABASE: beer
  Connect to your database as the root user and check for the existence of the beer database:
    echo “show databases;” | mysql -h192.168.88.4 -uduffman -psaysoyeah
  Run the command located in /sql/beer.sql to insert values into the database.
    mysql -uroot -h 192.168.88.4 -pSQLp4ss < /sql/beer.sql

Solution:
  podman run -d --name testq1 -p 3306:3306 -e MYSQL_USER=duffman \
  -e MYSQL_PASSWORD=saysoyeah \
  -e MYSQL_ROOT_PASSWORD=SQLp4ss \
  -e MYSQL_DATABASE=beer \
  registry.do180.lab:5000/mariadb

TASK3:
  Use skopeo to find all of the tags associated with httpd on the registry.do180.lab server. Create and run a container with the following parameters:
  Use an httpd image from registry.do180.lab that is NOT tagged with latest
  Publish port 80 on 8008
  Give the container the name alp-httpd
  Run the container in detached mode

Solution:
  skopeo inspect docker://registry.do180.lab:5000/httpd
  podman run -d --name alp-httpd -p 80:8080 registry.do180.lab:5000/httpd:2.4-alpine

TASK5:
  Do the following with your newly created alp-httpd container:
  Execute an interactive shell (/bin/sh) in the alp-httpd container
  Change the text in /usr/local/apache2/htdocs/index.html from “It works!” to “It Twerks!”
  Use curl to test your changes.
  Commit the container to an image named registry:5000/httpd:twerks.
  Push your new image to the registry server (you can check this by visiting http://registry.do180.lab -- the page updates every minute)
  Kill your alp-httpd container and run a new one based on the twerks image using port 8008. Name it tw-httpd.

Solution:
  podman exec -it alp-httpd /bin/sh 
  vi /usr/local/apache2/htdocs/index.html
  podman commit -q containerid registry:5000/httpd:twerks 
  podman push registry:5000/httpd:twerks registry.do180.lab:5000/httpd:twerks
  podman kill alp-httpd
  podman run -d --name tw-httpd -p 8080:8080 registry.do180.lab:5000/httpd:twerks

TASK6:
  Use the incomplete file located at /dockerfiles/nginx/Dockerfile to create a new image. It must meet these requirements:
  Pull from centos:8
  Install nginx
  Publish port 80 to the outside world
  Copy index.html and duffman.png into /usr/share/nginx/html directory
  Run the command nginx -g daemon off;
  Tag this image as quay.io/YOURUSERNAME/duff-nginx:1.0 and push it to your quay.io account.
  Run a container in detached mode named duffman that publishes port 80 to 8989. Test it with curl localhost:8989.

Solution:
  vi Containerfile
  FROM centos:8
  RUN yum install -y nginx && yum clean all
  EXPOSE 80
  COPY ./index.html /usr/share/nginx/html
  COPY ./duffman.png /usr/share/nginx/html
  CMD ["nginx", "-g", "daemon off;"]

TASK7:
  Use the incomplete files located in /dockerfiles/mosquitto/Dockerfile to create a new image. Use the comments in the Dockerfile to guide you through creating an image that meets the   following requirements:
  Use the centos 7 base image
  Add your maintainer info
  Create a user named duffman
  Install epel-release and mosquitto (epel-release must be installed first)
  Create a /colors directory using colors.tar
  Move skeeter.sh over to /skeeter.sh
  Make skeeter.sh executable
  Allows connections to port 1883
  Switch to the duffman user
  Run the skeeter.sh script
  Build your image with a tag of skeeter:1.0. Run a container in detached mode named mosquitto-1 that publishes port 1883 on port 11883.
  You can test your container by running
  mosquitto_sub -h 127.0.0.1 -p 11883 -t “#”. 
  If your container is working correctly, every 3 seconds you will see:
  Fri Mar 19 15:03:10 UTC 2021 (a timestamp)
  purple (a random color)
  duffman (the user running the script)

Solution:
  vi Containerfile
  FROM centos:7
  MAINTAINER Bobby 
  RUN useradd duffman
  RUN yum install -y epel-release && yum install -y mosquitto
  ADD ./colors.tar /
  COPY ./skeeter.sh /skeeter.sh
  RUN chmod +x /skeeter.sh
  EXPOSE 1883
  USER duffman
  CMD ["/skeeter.sh"]
  podman build --layers=false -t skeeter:1.0 .
  podman run -d --name mosquitto-1 -p 1883:11883 localhost/skeeter:1.03

TASK8:
  Use the MariaDB container from task 3.
  Refactor your configuration in such a way that you create four Podman secrets:
  mysql_user: "duffman", from file ~/mysql_user
  mysql_password: "saysoyeah", from file ~/mysql_password
  mysql_root_password: "SQLp4ss", from file ~/mysql_root_password
mysql_database: "beer", from file ~/mysql_database
Run the container in the background, with the following parameters:
u will again use the image "mariadb" from the registry on registry.do180.lab.
Give the container the name secretsdb.
Publish port 3306 on external port 3307.
The variables for database startup should be set as secret environment variables.
MYSQL_USER
MYSQL_PASSWORD
MYSQL_ROOT_PASSWORD
MYSQL_DATABASE
Once the container is up and running, prove that your settings are correctly applied.
Connect to your database as the root user and check for the existence of the beer database:
echo "show databases;" | mysql -uduffman -h 0.0.0.0 -psaysoyeah -P 3307
Run the command located in /sql/beer.sql to insert values into the database.
mysql -uroot -h 0.0.0.0 -pSQLp4ss -P 3307 < /sql/beer.sql.

echo -n "duffman" > ~/mysql_user
echo -n "saysoyeah" > ~/mysql_password
echo -n "SQLp4ss" > ~/mysql_root_password
echo -n "beer" > ~/mysql_database

podman secret create -d=file mysql_user ~/mysql_user
podman secret create -d=file mysql_password ~/mysql_password
podman secret create -d=file mysql_root_password ~/mysql_root_password
podman secret create -d=file mysql_database ~/mysql_database

podman run -d --name secretsdb \
        -p 3307:3306 \
        --secret mysql_user,type=env,target=MYSQL_USER \
        --secret mysql_password,type=env,target=MYSQL_PASSWORD \
        --secret mysql_root_password,type=env,target=MYSQL_ROOT_PASSWORD \
        --secret mysql_database,type=env,target=MYSQL_DATABASE \
        registry:5000/mariadb

TASK9:
Create a Dockerfile which creates a container image with the following requirements. Store the Dockerfile as ~/task9.dockerfile.
Based on centos:7.
During the build, create a user account.
"joe" must be the default user.
Read an argument during build-time to override the name "joe".
The container must run "whoami" to show the active user.
NOTE: Arguments are NOT passed during runtime of the container! They are passed during the container build.
Create two container images using this Dockerfile, named hello-joe:1.0 and hello-lisa:1.0.
When run, they should respectively output "joe" and "lisa".


FROM centos:7

ARG buildname
ENV buildname=${buildname:-joe}

RUN useradd -m $buildname
USER $buildname

CMD ["whoami"]

podman build -t hello-joe:1.0 -f task9.dockerfile .

podman build --build-arg buildname=lisa -t hello-lisa:1.0 -f task9.dockerfile .

podman run -ti --name joe hello-joe:1.0
podman run -ti --name lisa hello-lisa:1.0

TASK10:
Use the incomplete Podman Stack file located in /dockerfiles/voting/docker-compose.yml. Use the comments in the file to guide you through the process of creating a Podman Stack with the following requirements:
There are two networks: front-end (of type bridge) and back-end (of type internal).
There is one volume: db-data
The Stack consists of five services:
Using the "redis" image, named "redis", in network "front-end"
Using the "postgres:9.4" image, named "db", in network "back-end", with volume "db-data" mounted on /var/postgres.
The Postgres container needs to have two environment variables: POSTGRES_USER and POSTGRES_PASSWORD, both set to "postgres".
Using the "dockersamples/examplevotingapp_vote" image, named "vote", in networks "back-end" and in "front-end".
The "vote" container exposes port 5000 on public port 5000. It depends on "redis".
Using the "dockersamples/examplevotingapp_result" image, named "result", in networks "back-end" and in "front-end".
The "vote" container exposes port 5001 on public port 5001. It depends on "db".
Using the "dockersamples/examplevotingapp_worker" image, named "worker", in networks "back-end" and "front-end".
The "worker" container depends on both "db" and "redis".

version: "3" 
services:
  redis:
    image: redis
    networks:
      - back-end
  db:
    image: postgres:9.4
    networks:
      - back-end
    environment: 
      - POSTGRES_USER=postgres 
      - POSTGRES_PASSWORD=postgres 
    volumes: 
      - db-data:/var/postgres
  vote:
    image: dockersamples/examplevotingapp_vote
    networks:
      - front-end
      - back-end
    depends_on:
      - redis
    ports:
      - "5000:80" 
  result:
    image: dockersamples/examplevotingapp_result
    networks:
      - front-end
      - back-end
    depends_on:
      - db
    ports:
      - "5001:80" 
  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - back-end
    depends_on:
      - db
      - redis
networks:
  front-end:
    driver: bridge
  back-end:
    internal: true
volumes:
  db-data:
    driver: local
