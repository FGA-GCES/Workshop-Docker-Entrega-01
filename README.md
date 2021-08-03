# Trabalho Individual 2020.2

A Gestão de Configuração de Software é parte fundamental no curso de GCES, e dominar os conhecimentos de configuração de ambiente, containerização, virtualização, integração e deploy contínuo tem se tornado cada vez mais necessário para ingressar no mercado de trabalho.

Para exercitar estes conhecimentos, você deverá aplicar os conceitos estudados ao longo da disciplina no produto de software contido neste repositório.

O sistema se trata de uma aplicação Web que é composta de:

- Front-end escrito em Javascript, utilizando os frameworks Next.js + SWR;
- Back-end escrito em Python, utilizando o framework FastAPI;
- Banco de Dados PostgreSQL;

Para executar a aplicação em sua máquina, basta seguir o passo-a-passo descrito no arquivos s README das pastas [backend](./backend/README.md) e [frontend](./frontend/README.md).

## Resumo da aplicação

Essa aplicação é um blog + rede social (clone do Medium.com) chamado de "Conduit". Para ver uma demo funcionando, clique aqui: [https://next-realworld.now.sh/](https://next-realworld.now.sh/)

## Exemplos Anteriores

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

### 2. Integração contínua

Você deverá criar um 'Fork' deste repositório, onde será desenvolvida sua solução. Nele, cada commit submetido deverá passar por um sistema de integração contínua, realizando os seguintes estágios:

- Build: Construção completa do ambiente;
- Testes: Os testes automatizados da aplicação devem ser executados;
- Análise Estática de código: Deverá ser realizada a integração com algum serviço externo de análise estática de código fonte (Ex: Code Climate), análise de folhas de estilo (Stylesheet / Linter);
- Análise de cobertura de testes: Devera será ser incluida também uma ferramenta para apontar a métrica de cobertura de testes.

O sistema de integração contínua deve exibir as informações de cada pipeline, e impedir que trechos de código que não passem corretamente por todo o processo sejam adicionados à 'branch default' do repositório.

Para esta parte do trabalho, poderá ser utilizada qualquer tecnologia ou ferramenta que o aluno desejar, como GitlabCI, Actions, TravisCI, CircleCI, Jenkins, CodeClimate, entre outras.

### 3. Deploy contínuo

Também deve ser feita a configuração de um pipeline de modo a publicar a aplicação automaticamente, sempre que um novo trecho de código seja integrado à branch default.

Assim como na Integração Contínua, poderá ser utilizado qualquer servidor de cloud que o aluno desejar, como DigitalOcean, Heroku, AWS, entre outros.

### 4. Kubernets (extra)

Caso cumpra todos os requisitos descritos acima, será atribuída uma pontuação extra para o aluno que configure o deploy com Kubernets.

## Nota

A nota de cada aluno será a soma dos itens abaixo que serão avaliados tanto de forma quantitativa (se foi realizado a implementação + documentação), quanto qualitativamente (como foi implementado, entendimento dos conceitos na prática, complexidade da solução). Faça os commits atômicos, bem documentados, completos a fim de facilitar o entendimento e avaliação do seu trabalho. Lembrando que esse trabalho é individual. 

Os Itens de avaliação são (cada item tem peso 1 na nota final de 0 - 10):

**1. Containerização**

- Container do Back-end
- Container do Front-end
- Container do Banco de Dados
- Automação entre os containers (Docker-compose)

**2. Integração contínua (Front-end)**

- Build: Construção completa do ambiente
- Testes: Os testes automatizados da aplicação devem ser executados
- Coleta de métricas: Deverá ser realizada a integração com algum serviço externo de coleta de métricas de qualidade;

Regras de avaliação: O sistema de integração contínua deve exibir as informações de cada pipeline, e impedir que trechos de código que não passem corretamente por todo o processo sejam adicionados à 'branch default' do repositório.

**3. Integração contínua (Back-end)**

- Build: Construção completa do ambiente
- Testes: Os testes automatizados da aplicação devem ser executados
- Coleta de métricas: Deverá ser realizada a integração com algum serviço externo de coleta de métricas de qualidade;

Regras de avaliação: O sistema de integração contínua deve exibir as informações de cada pipeline, e impedir que trechos de código que não passem corretamente por todo o processo sejam adicionados à 'branch default' do repositório.

**4. Coverage (Extra)**

Caso cumpra todos os requisitos obrigatórios descritos acima, será atribuída uma pontuação extra para o aluno que configure sua pipeline de modo a coletar a porcentagem de cobertura de testes na aplicação (Back end e Front end).

**5. Deploy contínuo (Extra)**

Caso cumpra todos os requisitos obrigatórios descritos acima, será atribuída uma pontuação extra para o aluno que configure sua pipeline de modo a publicar a aplicação automaticamente, sempre que um novo trecho de código seja integrado à branch default.
