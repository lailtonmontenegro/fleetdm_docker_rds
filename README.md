# Fleetdm

The Infrastructure will be similar to the official infrastructure [ https://fleetdm.com/docs/deploying/introduction#infrastructure-dependencies.] An exception is that we are using a docker swarm to up fleetdm.

Considering that the RDS Mysql infrastructure is already up, we need to create a database, user and grant the privileges.

## MySql - Amazon RDS Mysql 
```
CREATE DATABASE fleetdm;
CREATE USER 'fleetdm'@'ip' IDENTIFIED BY 'Senha';
GRANT ALL PRIVILEGES ON fleetdm.* TO 'fleetdm'@'ip';
```

## Fleetdm
Install Docker on ubuntu
```
https://docs.docker.com/engine/install/ubuntu/
```

Init docker Swarm
```
sudo docker swarm init --advertise-addr IP_MANAGER
```

Creating a secret to mysql.
```
echo "RDS-Fleetm-password" | docker secret create rds-aws-secret -
```
Creating a secret to redis.
```
openssl rand -base64 32 | tr -cd '[:alnum:]' | sudo docker secret create fleetdm-jwt-key -
```
Cleaning the history.
```
history -c
```

Clone this git project and organize the path like below.  
Note that we need to copy the certificates to the correct path(/opt/fleet/conf/tls). 

```
/opt/fleet/
├── conf
│   ├── docker
│   │   ├── fleetdm
│   │   │   └── fleetdm-swarm.yml
│   └── tls
│       ├── fleet.ca-bundle
│       ├── fleet.crt
│       └── fleet.key
├── docker-compose-fleet-swarm.yml
└── log
    ├── osquery_result
    └── osquery_status
```
Up the Container.
```
sudo docker stack deploy -c docker-compose-fleet-swarm.yml fleetdm
```

Useful Comands. 
```
# See the status of containers.
sudo docker stats
# Service list
sudo docker service ls
# Service logs
sudo docker service logs -f NOME_DO_SERVIÇO
# down container
sudo docker service rm
```

This script is a good form to manage your environment. https://github.com/genilsoncm
