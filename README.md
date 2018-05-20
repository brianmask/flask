Basic setup for distributed python web application using docker - gunicorn - nginix and flask

sample swarm file for docker-compose:

version: "3"
services:
  web:
    image: brianmask/flaskr:v1
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: "0.3"
          memory: 250M
    volumes:
      - "db:/deploy/instance"
    ports:
      - "80:80"
    networks:
      - webnet
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet
volumes:
  db:
    driver: vieux/sshfs
    driver_opts:
      sshcmd: "<user>@<host>:/home/docker/db"
      allow_other: ""
      password: "<password>"
   
networks:
  webnet:
