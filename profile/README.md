# Ziekehuis management systeem
In deze publieke repo zitten verschillende projecten die samen een microservice vormen.

# Installatie
Node.js project opzetten
* Folder aanmaken
* Folder openen met cmd
* npm init -y

Vue.js project opzetten
* Folder aanmaken
* Folder openen met cmd
* npm create vue@latest
* volg aangegeven stappen
  
# Setups


# Documentatie


#Docker compose
version: '3.8'

services:

  front-end:
    build: ./ZMS-front-end
    ports:
      - "8083:80"

  patient-service:
    build: ./Patient-service
    restart: on-failure
    ports:
      - "8081:8081"
    labels:
      - "traefik.http.routers.patient-service.rule=Host(`patient-service.localhost`)"
    depends_on:
      - rabbitmq
      - traefik
    environment:
      RABBITMQ_HOST: rabbitmq
  
  zoek-service:
    build: ./Zoek-service
    restart: on-failure
    ports:
      - "8082:8082"
    labels:
      - "traefik.http.routers.zoek-service.rule=Host(`zoek-service.localhost`)"
    depends_on:
      - rabbitmq
      - traefik
    environment:
      RABBITMQ_HOST: rabbitmq

  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "15672:15672"
      - "5672:5672"
    labels:
      - "traefik.http.routers.rabbitmq.rule=Host(`rabbitmq.localhost`)"

  traefik:
    image: traefik:v3.0
    command: --api.insecure=true --providers.docker
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock

