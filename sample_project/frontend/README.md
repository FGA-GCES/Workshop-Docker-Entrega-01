# Next.js com SWR

## Pré-requisitos

- yarn ou npm

> aqui no tutorial usaremos yarn

## Passo a passo

Primeiro, clone o repositorio

```console
git clone https://github.com/FGA-GCES/Trabalho-Individual-2021-1

cd Trabalho-Individual-2021-1/frontend
```

Instale as dependencias:

```console
yarn
```

Rode a aplicação:

```console
yarn dev
```

## Fazendo requests pra Api

Existe uma api em prod que pode-se utilizar, caso necessario (para fins de debug): [`https://conduit.productionready.io/api`](https://conduit.productionready.io/api)

Caso queira alterar o Url para o qual o app fará os requests, vá em `lib/utils/constant.js` e mude `SERVER_BASE_URL` para a URL desejada (a que está configurada agora é `http://localhost:8000`)

## Funcionalidade

**General functionality:**

- Authenticate users via JWT (login/register pages + logout button on settings page)
- CRU\* users (sign up & settings page - no deleting required)
- CRUD Articles
- CR\*D Comments on articles (no updating required)
- GET and display paginated lists of articles
- Favorite articles
- Follow other users

**The general page breakdown looks like this:**

- Home page (URL: /)
  - List of tags
  - List of articles pulled from either Feed, Global, or by Tag
  - Pagination for list of articles
- Sign in/Sign up pages (URL: /user/login, /user/register)
  - Use JWT (store the token in localStorage)
- Settings page (URL: /user/settings )
- Editor page to create/edit articles (URL: /editor/new, /editor/article-slug-here)
- Article page (URL: /article/article-slug-here)
  - Delete article button (only shown to article's author)
  - Render markdown from server client side
  - Comments section at bottom of page
  - Delete comment button (only shown to comment's author)
- Profile page (URL: /profile/username-here, /profile/username-here?favorite=true)
  - Show basic user info
  - List of articles populated from author's created articles or author's favorited articles

## Fonte

O repositório original desse serviço pode ser encontrado [aqui](https://github.com/reck1ess/next-realworld-example-app)
