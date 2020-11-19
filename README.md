# jenkins-ist
Repo do Github para compilações automatizadas de uma versão personalizada do Jenkins no hub do docker.

## Installation

Certifique-se de que o diretório / data / jenkins / seja criado e tenha as permissões corretas.
- Se você estiver usando o vagrant, o diretório deve pertencer ao vagrant.
  Se você estiver conectado a uma instância do Ubuntu EC2, o usuário do ubuntu deve possuir o diretório.
- Se precisar ser criado:
  1. Run *sudo mkdir /data*
  2. Run *sudo mkdir /data/jenkins*
  3. Run *sudo chown 1000 /data/jenkins*

## Building

Para construir a imagem docker do Jenkins que você deseja executar em um contêiner. Garantir
você está no diretório `/ jenkins` com o * Dockerfile * e execute

`` `Bash
docker build -t istresearch / jenkins: [Versão]
`` `

Alternativamente, você pode fazer.
`` `Bash
compilação docker-compose
`` `

> A primeira abordagem permite que você especifique as tags que deseja usar para o
imagem docker. Enquanto isso, com a segunda abordagem, você é forçado a usar
tudo o que está definido no arquivo `docker-compose.yml`

## Running

To run the container, type the following

```Bash
# Run without mounting the docker socket
docker run \
  -d \
  --name jenkins \
  --net=host \
  -p 8080:8080 \
  -p 5000:5000 \
  -v /data/jenkins:/var/jenkins_home \
  -u 1000 \
  istresearch/jenkins:latest

# Run with the docker socket mounted
docker run \
  -d \
  --name jenkins \
  --net=host \
  -p 8080:8080 \
  -p 5000:5000 \
  -v /data/jenkins:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -u 1000 \
  istresearch/jenkins:latest
```
- *-d* to run in the background
- *--net=host* so the container shares the host network stack and has access to the /etc/hosts for network communication
- *-p 8080:8080* so the 8080 port in the container receives all requests to port 8080 on the host. Jenkins runs on Tomcat, which uses port 8080 as the default
- *-p 5000:5000* required to attach slave servers; port 50000 is used to communicate between master and slaves
- *-v /data/jenkins:/var/jenkins_home* to bind host directory /data/jenkins to the container directory /var/jennkins_home
- *-v /var/run/docker.sock:/var/run/docker.sock* Mounts the docker socket into the container 
- *-u 1000* jenkins user uid is 1000, same as ubuntu and vagrant uid's are 1000

Alternatively, you can run one of the following

```Bash
# Uses the istresearch/jenkins:latest image
docker-compose up -d

# Runs a local dev version, which allows you to modify the Dockerfile for local experimentation.
docker-compose -f docker-compose.yml -f docker-compose-dev.yml up -d
```
