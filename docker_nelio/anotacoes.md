# Docker

## Introdução
Virtualização é uma tecnologia que permite criar ambientes virtuais e simulados a partir de uma única máquina física, como um servidor ou computador. por exemplo: Virtualbox

Os containers compartilham recursos do kernel da máquina hospedeira, mas cada um está alocado em um namespaces próprio

Docker é uma plataforma.

Versão do Docker
```
docker -v
```
Versão Docker Client e Docker Server (Daemon)
```
docker version
```


## Imagens, containers e registry
#### Imagem
  - Conceito: é a definição estática de como um container deve ser instanciado
  - Uma imagem é composta por camadas
  - Uma imagem tipicamente é definida a partir de outra imagem existente
  - Uma imagem "inicial" é definida a partir de "scratch"
  - Uma imagem é como um modelo ou receita para criar containers. Ela é somente leitura e serve como base para gerar containers, que são instâncias em execução dessa imagem.
  - Modelo pronto com tudo que o app precisa. É somente leitura.

#### Container
  - Conceito: é uma instância de uma imagem
  - Pode estar em execução ou parado
  - Container é STATEFUL: mantém estado. Ele possui uma camada superior de escrita.
  - Um container no Docker é uma instância em execução de uma imagem.
  - Um container é um ambiente leve e isolado que executa um aplicativo com base em uma imagem Docker.
  - Ele contém tudo que o app precisa, mas não carrega um sistema operacional completo como uma VM.


#### Registry
  - É um serviço que armazena imagens
  - Registry padrão para Docker: Docker Hub
  - Para instanciar um container, a imagem precisa existir localmente ou em um Registry
  - Um registry (registro) é um repositório central onde as imagens Docker são armazenadas, organizadas e distribuídas. Ele funciona como o GitHub das imagens Docker.
  - Registry é onde as imagens Docker são publicadas, versionadas e compartilhadas, seja com o mundo ou com um time específico.

  ##### Resumo
  - Uma imagem seria tipo uma class em java, um tipo
  - Um container seria tipo a instancia, o objeto dessa classe.
  - Um registry seria tipo um github. Um local para registrar as imagens docker

## Comandos ps, images, pull e run, stop, start

Listar os containers (inclusive os parados: -a)
```
docker ps
```
Baixar uma imagem do Docker Hub (se não especificar tag, vem a "latest")
```
docker pull <image:tag>
```
Listar as imagens baixadas
```
docker images
```
Instanciar (e iniciar) um container com base em uma imagem (se não estiver baixada, baixa do Docker Hub)
```
docker run [OPTIONS] <image:tag> [COMMAND] [ARGS]
```
Documentação: https://docs.docker.com/engine/reference/run/

Exemplo 1: instancia um container do Ubuntu com linha de comando disponível
```
docker run -it ubuntu:20.04 /bin/bash
```
Exemplo 2: banco Postgres rodando na porta 5432 com base de dados "minha_base" e senha "123456"
```
docker run -p 5433:5432 --name meu-container-pg12 -e POSTGRES_PASSWORD=1234567 -e POSTGRES_DB=minha_base postgres:12-alpine
```

## Manutenção básica
Acessar o terminal de um container em execução
```
docker attach <id>
```
Sair do container sem pará-lo
```
CTRL + P + Q
```
Acompanhar os logs do container (tail)
```
docker logs [-f] <id>
```
Inspecionar o tamanho em disco
```
docker ps --size

docker system df
```
Parar um container
```
docker stop <id>
```
Iniciar um container
```
docker start <id>
```
Deletar um container
```
docker rm <id>
```
Deletar uma imagem
```
docker rmi <id>
```

## Criação da imagem Docker: arquivo Dockerfile
- Dockerfile é um arquivo de texto plano com instruções de como criar a imagem

#### COMANDOS MAIS COMUNS
- FROM
- EXPOSE
- ADD
- RUN (Tempo de build. Cada RUN cria uma nova camada na imagem. Use && para encadear vários RUN sem criar camada para cada.)
- VOLUME
- ENTRYPOINT (Tempo de executação)
- CMD (Tempo de executação)

#### Comando para criar a imagem
```
docker build -t imagem:tag .
```

### Exemplo 1: Ubuntu, escreve Hello World
```
FROM ubuntu:20.04
CMD ["echo", "Hello World"]
```
### Exemplo 2: Ubuntu, bash rodando (gere o container com -it)
```
FROM ubuntu:20.04
CMD ["bash"]
```
### Exemplo 3: Ubuntu, ping, bash rodando (gere o container com -it)
```
FROM ubuntu:20.04
RUN apt-get update \
 && apt-get install -y iputils-ping
CMD ["bash"]
```
### Exemplo 4: Ubuntu, Curl, AWS CLI, Ping, Vim, SSH, pasta compartilhada: tmp, bash rodando
```
FROM ubuntu:20.04

RUN apt-get update \
  && apt-get install -y curl unzip

RUN curl https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip -o awscliv2.zip \
  && unzip awscliv2.zip \
  && ./aws/install \
  && rm -rf aws awscliv2.zip

RUN apt-get install -y iputils-ping \
  && apt-get install -y vim \
  && apt-get install -y openssh-server

VOLUME /tmp

CMD ["bash"]
```


#### Volumes
Informações detalhadas do container
```
docker inspect <id>
```
Localização no Windows: https://stackoverflow.com/questions/43181654/locating-data-volumes-in-docker-desktop-windows

Listar volumes
```
docker volume ls
```
Remover volume
```
docker rm -v <container-id>

docker volume rm <volume-name>
```

## Salvando imagem no Docker Hub
- No Docker Hub: Create Repository

Criar tag para nossa imagem com usuário do Docker Hub:
```
docker tag <imagem:tag> <usuario/imagem:tag>
```
Logar no Docker Hub
```
docker login docker.io
```
Enviar a imagem para o Docker Hub
```
docker push usuario/imagem:tag
```
Baixar a imagem do Docker Hub
```
docker pull usuario/imagem:tag
```
Listar imagens de um usuário
```
docker search <usuario>
```

## Estudo de caso Docker Compose

Estudo de caso: Postgres e pgAdmin

Script:

https://gist.github.com/acenelio/5e40b27cfc40151e36beec1e27c4ff71

Subir a estrutura:

``` 
docker-compose up -d
```

Parar a estrutura:
```
docker-compose down
```