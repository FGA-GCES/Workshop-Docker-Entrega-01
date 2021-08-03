# O que é conteinerização?

- ⚪️ [1. O que é conteinerização?](./README.md)
- ⚫️ [2. Diferenças entre VMs e Containers](./1_difference_between_vm_and_containers.md)
- ⚫️ [3. Construindo um container mínimo](./2_container_from_scratch.md)
- ⚫️ [4. Instalação do Docker](./3_installing_docker.md)
- ⚫️ [5. Conceitos de Docker](./4_docker_concepts.md)
- ⚫️ [6. Conceitos de Docker Compose](./5_docker-compose_concepts.md)

> Disclaimer: esse capítulo (de introdução) é 100% baseado nesse vídeo -> <br/> <p align="center" width="100%" >
<a href="https://www.youtube.com/watch?v=Gjnup-PuquQ">
<img src="https://img.youtube.com/vi/Gjnup-PuquQ/0.jpg" alt="Docker in 100 seconds">
</a>
</p>


## Problema

Suponha que voce está desenvolvendo um app em Cobol, que roda numa plataforma Linux aleatória (ex: archlinux), e você deseja compartilhar esse app com seu amigo; entretanto, ele utiliza um sistema totalmente diferente (ex: debian, windows 7, etc)...

Então, surge a questão:

**Como replicar esse ambiente que meu app precisa em qualquer computador?**

### Máquinas Virtuais

Uma das soluções possíveis é utilizar uma **V**irtual **M**achine (e existem varias opções para isso, como: VirtualBox, Vmware, GnomeBoxes, Qemu, dentre outros)

O que são máquinas virtuais? São computadores completos: possuem CPU, memória RAM, disco rigido, sistema operacional, arquivos, aplicativos... tudo. Mas, tudo isso é virtual/simulado, utilizando recursos do computador físico/servidor "real" -- também conhecido como Host.

Então, a idéia seria: um simulador de computador que será configurado de forma igual (idealmente) pelos devs do projeto, utilizando um mesmo sistema operacional e instalando as dependencias necessárias pro projeto.

> exemplo: todos instalam uma VM usando Manjaro, instalam os pacotes g++, libncurses etc (qualquer app/dependencia necessária pra rodar aquele projeto)

Mas... isso não é muito escalável quando se considera uma organização com múltiplos apps sendo desenvolvidos simultaneamente, por exemplo. Como as VMs tendem a ser pesadas (tanto em consumo de recursos computacionais do computador [real], como na performance individual), essa opção se torna meio inviável

### Docker ao resgate

> obs: existem outras opções surgindo atualmente, como o [Podman (da Redhat)](https://podman.io/); mas, no geral, container = Docker

Um Container de Docker é conceitualmente muito similar a uma VM, com uma distinção chave: em vez de virtualizar o Hardware (um computador inteiro), os Containers virtualizam apenas o S.O.; ou seja, todos os apps (ou, Containers) são executados por um único Kernel

É isso que torna a utilização de Docker tão mais rápida, flúida e eficiente.

## Estrutura básica do Docker

Existem 3 pilares que constituem o universo de Docker:

![3 pilares](https://imgur.com/MTgPfYj.png)

### Dockerfile

Este arquivo é como o DNA: um código que especifica ao Docker como ele deve *buildar* uma `imagem`.

### Imagem

A imagem é um snapshot da sua aplicação: contém desde as dependencias do seu projeto (ex: pacotes npm etc) até o "sistema operacional". Vale ressaltar que essa imagem é imutavel, e pode ser utilizada para construir múltiplos `containers`.

### Container

É a instancia em execução, criada a partir da imagem.

> fazendo um paralelo com OO: img = classe; container = objeto [instancia da classe]

## Exemplo Mínimo

> Não é necessário reproduzir esses comandos, já que praticaremos isso junto no [segundo sub-capítulo](./2_installing_docker.md).

A partir desse `Dockerfile` de exemplo:

![Dockerfile](https://i.imgur.com/eQ24k97.png)

```dockerfile
FROM ubuntu:20.04
```

> Como um "import"; é uma imagem pré-definida que será utilizada como base nesse arquivo. Essa imagem é "puxada" do [docker hub](https://hub.docker.com/_/ubuntu); essa imagem em específico se refere a [este Dockerfile](https://github.com/tianon/docker-brew-ubuntu-core/blob/a967c2b8734c77f7f89449d0b87c2e1eebf8b26e/focal/Dockerfile).

> Existem N imagens pré-definidas, que podem ser utilizadas de acordo com a necessidade do seu projeto. Por exemplo: [Python](https://hub.docker.com/_/python), [Ruby](https://hub.docker.com/_/ruby), [Node](https://hub.docker.com/_/node), dentre outras infinitas possibilidades.

> Normalmente, quando há de se partir de uma imagem de um S.O. mais básico (como esse caso do exemplo), há uma certa preferência para a distribuição [Alpine](https://hub.docker.com/_/alpine) em vez de Ubuntu, por exemplo, por se tratar de uma distribuição mais minimalista.

```dockerfile
RUN apt-get update -y && apt-get install -y tree
```

> Com o comando `RUN` você pode utilizar basicamente qualquer comando bash; nesse caso, estamos instalando o pacote `tree` (basicamente um `ls` que verifica as subpastas).

```dockerfile
RUN mkdir -p test/nested && \
    cd test && \
    touch nested/a.txt b.txt
```

> Para verificar o comando `tree` em ação, dentro dessa imagem, a partir do endereço base `/`, criamos uma pasta aleatoria e outra subpasta; entramos* na pasta criada, e criamos dois arquivos.


```dockerfile
RUN echo "current path: `pwd`"

RUN ls --format=across

RUN tree test/
```

> Mesmo utilizando o comando `cd` na linha passada, ao utilizarmos os comandos `pwd` e `ls` verificamos que retornamos ao endereço base; isso porque cada comando `RUN` é executado a partir do `WORKDIR` atual.

```dockerfile
CMD ["echo", "Hello World!"]
```

> O comando `CMD` distingue-se do `RUN` por se tratar de um comando que sera rodado ao executarmos um container; ou seja, no processo de build da imagem, este comando não é executado.


Então, ao executarmos o comando de build:

```bash
$ docker build -t exemplo_minimo .
```

> A flag `-t` serve pra especificar o nome da imagem que será construída

![Output 1/2](https://imgur.com/3jl9D4G.png)

![Output 2/2](https://imgur.com/chewTnl.png)

Após isso, podemos finalmente executar essa imagem (iniciando um container):

![Container](https://imgur.com/MPIYRvy.png)

----

> Observação:

> A linha de criação de pastas etc poderia ser reescrita da seguinte forma:

```dockerfile
WORKDIR test
RUN mkdir nested
RUN touch nested/a.txt b.txt
```

> O comando `WORKDIR` funciona como um `mkdir` (se necessário) + `cd` (modificando o contexto ao longo de toda a imagem)
<br />Entao, o comando `tree` poderia ser utilizado no contexto atual `.`, já que o `WORKDIR` dessa imagem não é mais o caminho base `/`, e, sim, o caminho `/test/`