# Comandos utiles de "Docker-Compose"

sudo docker-compose -f [compose-file] up -d
sudo docker-compose -f [compose-file] down
sudo docker-compose -f [compose-file] ps

# Comandos utiles de "Docker"

sudo docker network ls
sudo docker network rm
sudo docker network  disconnect [network-id] [container]
sudo docker container prune -f