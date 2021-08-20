# Express.js & ORM

## Pré-requisitos

- yarn ou npm

> aqui no tutorial usaremos yarn

## Passo a passo

Primeiro, clone o repositorio

```console
git clone https://github.com/FGA-GCES/Workshop-Docker-Entrega-01

cd Workshop-Docker-Entrega-01/trabalho_individual/chat-service
```

Inicie o banco de dados

```console
docker run \
    --name chat-service-db \
    -e MYSQL_ROOT_PASSWORD=password \
    -e MYSQL_DATABASE=db \
    -p 7200:3306 \
    -d \
    mysql:5.7.20
```

Instale as dependencias:

```console
yarn
```

Rode a aplicação:

```console
yarn watch
```

Acesse a api em [http://localhost:7100](http://localhost:7100).
