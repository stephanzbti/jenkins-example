# CD/CI - React + Redis + Go

Essa aplicação tem o destino de demonstrar como é feito um CI/CD em Jenkins para produção, com push no Docker Hub, e como será feito a configuração do ambiente para o time de desenvolvimento, desta forma padronizando o ambiente de desenvolvimento, e ficando simples e fácil para o time poder trabalhar.

Repositorio Docker Hub com imagens montadas com este projeto:

[Stephanzbti DockerHub](https://hub.docker.com/u/stephanzbti)

## Desenvolvimento

Para o time de desenvolvimento, foi criado um Dockerfile em cada aplicação e um Docker-Compose, para que seja feito o deployment da aplicação completa de uma única vez, logo, não é necessário criar cada container e executa-lo. Com essa metodologia o time de desenvolvimento não precisa conhecer sobre o ambiente em que a aplicação está sendo gerada, apenas ter o conhecimento de como será feito a execução da aplicação como um todo.

Em cada aplicação existe um Dockerfile que é responsável pelos passos necessário para se execuar a Build completa da aplicação, acima disso existe um docker-compose que gera a build completa da aplicação, e assim sendo possível executar o serviço por inteiro.

## Produção

Para o ambiente de produção, foi feito a automatização do Build utilizando o Jenkins. No Jenkins foi desenvolvido o script(Jenkinsfile) necessário para que a aplicação seja feita a Build via Dockerfile e o Push para um repositório no Docker Hub, que poderia ser acessado, pela ferramenta de deployment, como por exemplo o Kubernetes ou AWS ECS.

Com o Jenkins é possível a fácil criação e versionamento de cada imagem, desta forma, é possível de gerenciar as images, e se caso necessário, um rollback é fácil de ser feito de jeito fácil e prático, graças a maneira que é gerada a Build.

## Dependências do Projeto

Para que você utilize esta aplicação, é necessário que seja instalado as seguintes dependências, sendo descrito o passo-a-passo no site das respectivas distribuidoras:

[NodeJS](https://nodejs.org/en/download/package-manager/)

[Jenkins](https://jenkins.io/doc/book/installing/)

[Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

[Docker-Compose](https://docs.docker.com/compose/install/)

[GoLang](https://golang.org/doc/install#install)

## React

A aplicação em React, é um Frontend responsável por troca de mensagens entre os participantes. Essa aplicação executa via server node, que proivisona todo o serviço HTTP necessário, sendo possível executar via Nginx, caso este seja configurado anteriomente.

### Configuração do Ambiente

Para se executar essa aplicação perfeitamente, é necessário fazer a configuração do ambiente desta aplicação. O ambiente desta aplicação é composto por 3 varáveis, sendo elas descritar abaixo:

```
REACT_APP_BACKEND_WS -> Destino do WebSocket. Responsável por ficar analisando alguma informação proveniente do BackEnd, que será atualizado na página automáticamente.
REACT_APP_BACKEND_URL -> Destino do Backend. Responsável por enviar as requisições proveniente do usuário para o backend processar e enviar as informações solicitadas.
NODE_ENV -> Environment da Execução. Responsável por buildar a aplicação no ambiente solicitado.
```

### Geração de Build

Para se gerar a Build desta aplicação é necessário executar os seguintes comandos abaixo:

```
$ npm install
$ npm run build
```

### Execução da Aplicação

Para se executar o serviço HTTP desta aplicação é necessário executar os seguinte comandos abaixo, após a geração da Build:

```
$ npm run start
```

### Configuração automática do Ambiente, Build e Serviço:

Para se configurar esta aplicação automaticamente é necessário executar os seguinte comandos abaixo:

```
$ export REACT_APP_BACKEND_WS=**ws://URL_BACKEND**
$ export REACT_APP_BACKEND_URL=**http://URL_BACKEND**
$ export NODE_ENV=**development/staging/production**
$ docker build -t frontend:latest .
$ docker run -dti -p 3000:3000 frontend:latest
```

## Go

A aplicação em Go, é uma API responsável pelo recebimento de solicitação e processamento destas solicitações, festa forma ele orquestra as mensagens, armazenando-as em Redis e distribuindo a todos que solicitar as mensagens em questão.

### Configuração do Ambiente

Para se executar essa aplicação perfeitamente, é necessário fazer a configuração do ambiente desta aplicação. O ambiente desta aplicação é composto por 2 varáveis, sendo elas descritar abaixo:

```
ALLOWED_ORIGIN -> Define qual o destino está configurado para aceitar as requisições. Podendo ser a URL de Destino ou * para aceitar requisição de qualquer origin.
REDIS_ADDR -> Redis URL.
ENVIRONMENT -> Environment da Execução. Responsável por buildar a aplicação no ambiente solicitado.
```

### Geração de Build

É necessário instalar as dependências do projeto para que ele execute perfeitamente. Para isso basta executar os comandos abaixos:

```
go get ./...
RUN go build .
```

### Execução da Aplicação

Para se executar o serviço HTTP desta aplicação é necessário executar os seguinte comandos abaixo, após a geração da Build:

```
$ ./backend
```

### Configuração automática do Ambiente, Build e Serviço:

Para se configurar esta aplicação automaticamente é necessário executar os seguinte comandos abaixo:

```
$ export ALLOWED_ORIGIN=* -> Destino do WebSocket. Responsável por ficar analisando alguma informação proveniente do BackEnd, que será atualizado na página automáticamente.
$ export REDIS_ADDR=**REDIS_URL**
$ export ENVIRONMENT=**development/staging/production**
$ docker build -t backend:latest .
$ docker run -dti -p 8000:8080 frontend:latest
```

## Desenvolvimento

Para o time de desenvolvimento é necessário executar o docker-compose existente dentro do diretório raiz desta aplicação, sendo necessário apenas executar as configuraçõs necessárias.

### Configuração do Ambiente

Para se executar o ambiente de desenvolvimento é necessário configurar as variáveis de ambiente dentro do arquivo **docker-compose.yml** de acordo com o que for necessário. Segue abaixo onde será necessário configurar:

```
environment:
    - ALLOWED_ORIGIN=* -> Define qual o destino está configurado para aceitar as requisições. Podendo ser a URL de Destino ou * para aceitar requisição de qualquer origin.
    - REDIS_ADDR=redis:6379 -> Redis URL.
    - ENVIRONMENT=development -> Environment da Execução. Responsável por buildar a aplicação no ambiente solicitado.

environment:
    - REACT_APP_BACKEND_WS=ws://localhost:8080 -> Destino do Backend. Responsável por enviar as requisições proveniente do usuário para o backend processar e enviar as informações solicitadas.
    - REACT_APP_BACKEND_URL=http://localhost:8080 -> Destino do Backend. Responsável por enviar as requisições proveniente do usuário para o backend processar e enviar as informações solicitadas.
    - NODE_ENV=development -> Environment da Execução. Responsável por buildar a aplicação no ambiente solicitado.
```

### Execução da Aplicação

Para se executar esta aplicação é necessário executar os seguintes comandos, após a configuração do ambiente:

```
$ docker-compose build .
$ docker-compose up -d
```

## Jenkins

Nesta aplicação utilizamos o Jenkins com Dockerfile, para automatizarmos as Builds de maneira prática e fácil. O Jenkins é uma aplicação OpenSource de CI/CD que fácilita a vida do SRE/DevOps responsável por cada estágio do Pipeline. Com ele é possível descrever várias formas de Pipelines diferentes, cada uma com seu estágio, sendo os mais comuns: **Source**, **Install**, **Build**, **Deploy**, cada um responsável por seu papel de utilização.

### Instalando o Jenkins

Nossa prioridade aqui não é ensinar a instalar/utilizar o Jenkins, é apenas para demonstrar como é feito um Pipeline nele, e como pode ser fácil a configuração de cada etapa do Pipeline. Para configurar o Jenkins é necessário acessar o site oficial dele, que segue abaixo:

[Jenkins](https://jenkins.io/doc/book/installing/)

Podendo ser feito também via Docker, tendo sua imagem diretamente no DockerHub, este processo também é descrito no site do Jenkins.

### Explicação Pipeline

Neste Build teremos apenas foco em 2 estágios que serão: **Build**, **RepositoryImage**, sendo eles respectivamentes geração de build e push para o repositório de imagens. Podendo ser feito também outros estágios, caso seja necessário, como por exemplo Deploy no Kubernetes.

### Configuração Pipeline

Para executar o Pipeline perfeitamente, é necessário fazer uma configuração interna do Jenkinsfile ou criar uma Credentials do tipo **Username with password**, contendo as configurações de acesso ao Docker Hub, o nome desta credentials é **docker-hub-credentials**.

Para configurar as variáveis de ambiente no Jenkins, é necessário editar as seguintes linhas de acordo com a informação necessária em cada uma:

```
environment {
    ALLOWED_ORIGIN = '*' -> Define qual o destino está configurado para aceitar as requisições. Podendo ser a URL de Destino ou * para aceitar requisição de qualquer origin.
    REDIS_ADDR     = 'redis:6379' -> Redis URL.
    ENVIRONMENT    = 'production' -> Environment da Execução. Responsável por buildar a aplicação no ambiente solicitado.
    REACT_APP_BACKEND_WS     = 'ws://localhost:8080' -> Destino do Backend. Responsável por enviar as requisições proveniente do usuário para o backend processar e enviar as informações solicitadas.
    REACT_APP_BACKEND_URL    = 'http://localhost:8080' -> Destino do Backend. Responsável por enviar as requisições proveniente do usuário para o backend processar e enviar as informações solicitadas.
    NODE_ENV       = 'production' -> Environment da Execução. Responsável por buildar a aplicação no ambiente solicitado.
}
```

### FrontEnd

No FrontEnd temos um arquivo Jenkins, responsável pela automatização da aplicação do Frontend, caso somente este seja necessário automatizar.

### BackEnd

No BackEnd temos um arquivo Jenkins, responsável pela automatização da aplicação do Frontend, caso somente este seja necessário automatizar.

### Geral

Caso seja necessário fazer uma Build completa da aplicação, existe um arquivo Jenkinsfile no diretório Raiz do projeto, com ele você faz a geração completa da aplicação, contendo o FrontEnd e o BackEnd, ficando a cargo da pessoa que deseja utilizar.

### Dependências Jenkins

As dependências do Build são somente a instalação do Docker, que pode ser feita seguindo o tutorial existente na página da própria Docker. Segue abaixo o link:

[Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)