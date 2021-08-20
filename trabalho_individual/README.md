# Utilizando a aplicação

Após orquestrar os serviços e tudo transcorrer sem erros, para fazer um teste poderá:

1. Criar usuário
   1. Na porta da [`api-gateway`](./api-gateway/README.md), acesse a rota `'graphql'`:

        > ex: [http://localhost:7000/graphql](http://localhost:7000/graphql)

   2. Crie o usuário fazendo uma [`mutation`](https://graphql.org/learn/queries/):

        ```graphql
        mutation {
            createUser 
            (
                password: "password"
                username: "bob"
            ) {
                username
            }
        }
        ```

        > Obs: pode criar sessão de usuário também, por exemplo:

        ```graphql
        createUserSession (
            password: "password"
            username: "bob"
        ) {
            user {
            username
            }
        }
        ```

2. Fazer login
   1. Na porta do [`chat-app`](./chat-app/README.md), acesse a rota padrão:

        > ex: [http://localhost:7001](http://localhost:7001)

   2. Tente fazer login, você deverá ser redirecionado para a seguinte tela:

        ![tela pós-login](https://imgur.com/npIvJhU.png)

> Obs: Caso já esteja logado na aplicação e queira reiniciar a sessão, pode tentar utilizar as queries em graphql ou acessar o [phpmyadmin](./phpmyadmin/README.md), clicar na tabela userSession, e deletar as sessões :)

Pronto!
