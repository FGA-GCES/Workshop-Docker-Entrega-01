# Trabalho Individual 01 2021.1

A Gestão de Configuração de Software é parte fundamental no curso de GCES, e dominar os conhecimentos de configuração de ambiente, containerização, virtualização, integração e deploy contínuo tem se tornado cada vez mais necessário para ingressar no mercado de trabalho.

Para exercitar estes conhecimentos, você deverá aplicar os conceitos estudados ao longo da disciplina no produto de software contido neste repositório.

O sistema se trata de uma aplicação Web em Typescript, que é composta de:

- Front-end escrito em React (`chat-app`);
- Back-end dividido em três microsserviços:
  - `users-service`: express + ORM
  - `chat-service`: não implementado
  - `api-gateway`: graphql
- 2 Bancos de Dados MySQL 5.7.20 para users-service e chat-service (mesmo este não tendo sido implementado ainda)
  - `phpmyadmin`, como interface para gerenciamento dos bancos de dados

Para executar a aplicação em sua máquina, basta seguir o passo-a-passo descrito no arquivos README das pastas.

- [users-service](./trabalho_individual/users-service/README.md)
- [chat-service](./trabalho_individual/chat-service/README.md).
- [api-gateway](./trabalho_individual/api-gateway/README.md).
- [chat-app](./trabalho_individual/chat-app/README.md).
- [phpmyadmin](./trabalho_individual/phpmyadmin/README.md).

## Resumo da aplicação

É uma aplicação extremamente simples, não possui muitas features, então o foco é justamente na containerização (e orquestração) dessa aplicação. Por ora, só é possivel fazer login (alem de interagir com o banco etc)

Aqui um esquema simples de como a aplicação se comunica:

![diagrama](https://imgur.com/RKfPSxI.png)

### Prints de telas da aplicação

#### Frontend - chat-app

<figure>
<img
src="https://imgur.com/qA6nPGF.png"
alt="tela de carregamento">
<figcaption>tela de carregamento</figcaption>
</figure>

<figure>
<img
src="https://imgur.com/rKmSwkF.png"
alt="tela de login">
<figcaption>tela de login</figcaption>
</figure>

<figure>
<img
src="https://imgur.com/npIvJhU.png"
alt="tela pós-login">
<figcaption>tela pós-login</figcaption>
</figure>

### phpmyadmin

<figure>
<img
src="https://imgur.com/2zUF6QB.png"
alt="tela de gerenciamento do banco">
<figcaption>tela de gerenciamento do banco</figcaption>
</figure>

### api-gateway

<figure>
<img
src="https://imgur.com/lWo6F2S.png"
alt="tela de queries em graphql">
<figcaption>tela de queries em graphql</figcaption>
</figure>

## Trabalhos Anteriores

Alguns trabalhos de exemplo do [semestre passado](https://github.com/FGA-GCES/Trabalho-Individual-2020-2):

- [Ridersk/gces-trab-individual-lucas-maciel](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/27)
- [lucasfcm9/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/17)
- [lucasqmc/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/20)
- [lucianosz7/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/22)
- [lorryaze/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/23)
- [WelisonR/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/24)
- [lucasgomesgs0/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/25)
- [sammyzord/Trabalho-Individual-2020-2](https://github.com/FGA-GCES/Trabalho-Individual-2020-2/issues/26)

## Critérios de avaliação

### 1. Containerização

A aplicação deverá ter seu ambiente completamente containerizado. Desta forma, cada subsistema (Front-end, Back-end e Banco de Dados) deverá ser isolado em um container individual.

Deverá ser utilizado um orquestrador para gerenciar comunicação entre os containers, o uso de credenciais, networks, volumes, entre outras configurações necessárias para a correta execução da aplicação.

Para realizar esta parte do trabalho, recomenda-se a utilização das ferramentas:

- Docker versão 17.04.0+
- Docker Compose com sintaxe na versão 3.2+

## Nota

A nota de cada aluno será a soma dos itens abaixo que serão avaliados tanto de forma quantitativa (se foi realizado a implementação + documentação), quanto qualitativamente (como foi implementado, entendimento dos conceitos na prática, complexidade da solução). Faça os commits atômicos, bem documentados, completos a fim de facilitar o entendimento e avaliação do seu trabalho. Lembrando que esse trabalho é individual. 

Os Itens de avaliação são (cada item tem peso 2.5 na nota final de 0 - 10):

**1. Containerização**

- Containers do Back-end
- Container do Front-end
- Containers dos Banco de Dados
- Container para o phpmyadmin
- Automação entre os containers (Docker-compose)
