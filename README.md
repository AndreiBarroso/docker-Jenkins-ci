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


Para executar o contêiner, digite o seguinte


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
- * -d * para executar em segundo plano
- * - net = host * para que o contêiner compartilhe a pilha de rede do host e tenha acesso ao / etc / hosts para comunicação de rede
- * -p 8080: 8080 * para que a porta 8080 no contêiner receba todas as solicitações para a porta 8080 no host. Jenkins é executado no Tomcat, que usa a porta 8080 como padrão
- * -p 5000: 5000 * necessário para conectar servidores escravos; a porta 50000 é usada para a comunicação entre mestre e escravos
- * -v / data / jenkins: / var / jenkins_home * para vincular o diretório do host / data / jenkins ao diretório do contêiner / var / jennkins_home
- * -v /var/run/docker.sock:/var/run/docker.sock* Monta o soquete do docker no contêiner
- * -u 1000 * jenkins usuário uid é 1000, o mesmo que ubuntu e vagrant uid é 1000

Como alternativa, você pode executar um dos seguintes

```Bash
# Uses the istresearch/jenkins:latest image
docker-compose up -d

# Runs a local dev version, which allows you to modify the Dockerfile for local experimentation.
docker-compose -f docker-compose.yml -f docker-compose-dev.yml up -d
```
