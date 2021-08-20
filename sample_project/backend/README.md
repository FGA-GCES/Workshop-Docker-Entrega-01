# FastAPI com PostgreSQL

## Pré-requisitos

- poetry (`pip install poetry`)

## Passo a passo

Vamos rodar o Postgres num container para simplificar. Caso queira instalar localmente, [clique aqui](https://www.postgresql.org/download/).

Primeiro, clone o repositorio

```console
git clone https://github.com/FGA-GCES/Trabalho-Individual-2021-1

cd Trabalho-Individual-2021-1/backend
```

Crie o `.env` para configurarmos o container que rodará o postgres

```console
printf '%s\n' \
       'POSTGRES_DB=rwdb' \
       'POSTGRES_PORT=5432' \
       'POSTGRES_USER=postgres' \
       'POSTGRES_PASSWORD=postgres' \
       > .env

source .env

docker run -d --name pgdb --rm \
       -e POSTGRES_USER="$POSTGRES_USER" \
       -e POSTGRES_PASSWORD="$POSTGRES_PASSWORD" \
       -e POSTGRES_DB="$POSTGRES_DB" \
       postgres
```

Adicione o host ao `.env`:

```console
echo "POSTGRES_HOST=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pgdb)" >> .env
```

Instale as dependencias num virtualenv (criado pelo poetry):

```console
poetry install

poetry shell
```

Exporte as variaveis de ambiente novamente, já que entramos no virtualenv:

```console
source .env
```

Complemente o `.env`:

```console
echo DB_CONNECTION=postgresql://$POSTGRES_USER:$POSTGRES_PASSWORD@$POSTGRES_HOST:$POSTGRES_PORT/$POSTGRES_DB >> .env

echo SECRET_KEY=$(openssl rand -hex 32) >> .env
```

Faça as migrations pro banco:

```console
alembic upgrade head
```

Por fim, rode a aplicação:

```console
uvicorn app.main:app --reload
```

## Rodando os Testes

Para rodar os testes do projeto execute o comando:

```console
pytest
```

## Estrutura do projeto

Arquivos relacionados a aplicação estão nas pastas `app` e `tests`

```text
    app
    ├── api              - web related stuff.
    │   ├── dependencies - dependencies for routes definition.
    │   ├── errors       - definition of error handlers.
    │   └── routes       - web routes.
    ├── core             - application configuration, startup events, logging.
    ├── db               - db related stuff.
    │   ├── migrations   - manually written alembic migrations.
    │   └── repositories - all crud stuff.
    ├── models           - pydantic models for this application.
    │   ├── domain       - main models that are used almost everywhere.
    │   └── schemas      - schemas for using in web routes.
    ├── resources        - strings that are used in web responses.
    ├── services         - logic that is not just crud related.
    └── main.py          - FastAPI application creation and configuration.
```

## Rotas

Caso queira visualizar a documentação da api, basta acessar o caminho `/docs` ou `/redoc` (Swagger ou ReDoc, respectivamente)

Isso é, no seu navegador, acesse (por exemplo): [localhost:8000/docs](http://localhost:8000/docs)

## Fonte

O repositório original desse serviço pode ser encontrado [aqui](https://github.com/nsidnev/fastapi-realworld-example-app)
