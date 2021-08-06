# Conceitos de Docker

- ⚫️ [1. O que é conteinerização?](./README.md)
- ⚫️ [2. Diferenças entre VMs e Containers](./1_difference_between_vm_and_containers.md)
- ⚫️ [3. Construindo um container mínimo](./2_container_from_scratch.md)
- ⚫️ [4. Instalação do Docker](./3_installing_docker.md)
- ⚪️ [5. Conceitos de Docker](./4_docker_concepts.md)
- ⚫️ [6. Conceitos de Docker Compose](./5_docker-compose_concepts.md)

## Dockerfile

O Dockerfile provê instruções de como se construir a imagem de um container (usando o comando `docker build -t <nome_dessa_img> <caminho_do_dockerfile>`). Começa a partir de alguma imagem Base (usando o comando `FROM`), seguido de quaisquer outras instruçoes necessárias

Então, a gente "compila" esse código, tendo como resultado uma imagem que poderemos interagir com.


### principais comandos

<table style="width: 100%">

<tr>
<th>Comando</th>
<th>Descrição</th>
<th>Uso</th>
</tr>

<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#arg"><code>ARG</code></a></td>
<td>Define uma variavel que o usuário pode passar ao buildar a imagem (usando a flag <code>--build-arg</code><br /><br />essa variável só fica disponivel nas etapas de build da imagem (ou seja, só no dockerfile, mas nao no container)<br /><br /><blockquote>nao use isso pra passar 'segredos' já que os valores podem ser acessados usando <code>docker history</code></blockquote></td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
ARG <name>[=<default value>]
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
ARG version
ARG user_name=admin
```

como se usaria:

```console
$ docker build --build-arg version=1.0 -t <nome_img> .

$ docker build --build-arg version=1.1 \
               --build-arg user_name=joao \
               -t <nome_img> .
```

</td>
</tr>

<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#env"><code>ENV</code></a></td>
<td>Define uma variavel de ambiente que será utilizada tanto em <i>build-time</i> como em <i>run-time</i> (ou seja, os containers em execução também possuem essa ENV definida). O usuário pode passar o valor utilizando a flag <code>--env</code> ou <code>-e</code><br /><br /><blockquote>nao use ENV caso so precise do valor em <i>build-time</i></blockquote></td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
ENV <key>=<value> ...
ENV <key> <value> # com essa sintaxe, apenas um por linha
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
ENV NODE_ENV="development"

ENV POSTGRES_USER="username" \
    POSTGRES_PASSWORD="password" \

ARG build_value
ENV run_value=$build_value
```

como se usaria:

```bash
# cenario 1
$ docker run --env NODE_ENV=production <nome_img>


# cenario 2
$ docker build --build-arg build_value=5 -t <nome_img> .

$ docker run -e run_value=8 <nome_img>
```

</td>
</tr>

<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#from"><code>FROM</code></a></td>
<td>especifica a imagem base que será utilizada<br /><br />primeiro busca-se essa imagem localmente, caso nao encontre, busca-se no dockerhub o repositorio mais adequado</td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
```

ᵉˣᵉᵐᵖˡᵒˢ ↴

<div style="text-align: center">padrão</div>

```dockerfile
FROM node:14-alpine
```

<br/>

<div style="text-align: center">usando imagem de outro registry (ECR da AWS)</div>

```dockerfile
FROM public.ecr.aws/micahhausler/alpine:3.14.0
```

<br/>

<div style="text-align: center">recebendo plataformas da cli como <code>ARG</code></div>

```dockerfile
ARG architecture
FROM --platform=linux/${architecture} openjdk
```

</td>
</tr>


<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#label"><code>LABEL</code></a></td>
<td>adiciona metadados a imagem.<br />é feito através de pares de key-value (criados por você mesmo)<br /><br />isso pode ser, então, verificado com o comando <a href="#docker-inspect">docker inspect</a></td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
LABEL aleatorio.nome="valor1"
LABEL aleatorio.data="valor2"
LABEL chave1="c1" chave2="c2" \
      chave3="c3"
```

</td>
</tr>


<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#workdir"><code>WORKDIR</code></a></td>
<td>define qual será o diretorio de trabalho em <i>build-time</i> (o caminho relativo de qualquer comando <code>RUN</code>, <code>CMD</code>, <code>ENTRYPOINT</code>, <code>COPY</code> e <code>ADD</code> será esse diretório);<br /><br />caso o diretório não exista, este será criado<br /><br />caso passe um caminho relativo (não-absoluto), este será usado em relação ao ultimo <code>WORKDIR</code> definido</td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
WORKDIR /path/to/workdir
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
WORKDIR /pasta1
WORKDIR /pasta2
WORKDIR pasta3
```

o resultado seria:

.<br />
├── pasta1<br />
└── pasta2<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└── pasta3 \<diretorio atual>
</td>
</tr>


<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#user"><code>USER</code></a></td>
<td>configura qual será o usuário (opcionalmente o grupo, também [caso não especifique, será <i>root</i>]) que será utilizado dali em diante (tanto em <i>build-time</i> como <i>run-time</i>)<br /><br /><blockquote>o usuário tem que ser criado por você previamente no dockerfile</td>

<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
USER <user>[:<group>]
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
RUN groupadd -r grupo \
 && useradd usuario --groups grupo

USER usuario
USER usuario:grupo
```

</td>
</tr>


<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#copy"><code>COPY</code></a></td>
<td>

copia arquivos/diretorios do Host (em relação ao contexto da build) para o Container (em relação ao `WORKDIR`)<br /><br />

pode-se utilizar *wildcards* nos caminhos advindos do host:

* `*` da match em qualquer sequencia de chars que não sejam separador (`/`, ou `\` no windows)
* `?` o mesmo, mas pra 1 char só

<blockquote>

`--chown` não funciona no windows<br />[não é mt recomendado o uso disso](https://sysdig.com/blog/dockerfile-best-practices/), já que o usuario só precisa de **permissão pra executar**, e não o **~domínio/ownership**<br /><br />para efeito de comparação/entendimento:<br />
* [exemplo com a flag chown](./Dockerfile.COPY_com_chown)
* [exemplo sem chown](./Dockerfile.COPY_sem_chown)

</blockquote>
</td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
COPY [--chown=<user>:<group>] <caminho_no_seu_pc>... <destino_no_container>
COPY [--chown=<user>:<group>] ["<caminho_no_seu_pc>",... "<destino_no_container>"]
```

A versão com `[]` é utilizada qnd se tem whitespace no path (ex: `'/home/joao/minha pasta/'`)

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
COPY minha_pasta/ /tmp/my_folder
COPY ["package*.json", "yarn.lock", "/meu_app/"]
COPY . .
```

</td>
</tr>

<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#add"><code>ADD</code></a></td>
<td>

O mesmo que o `COPY`, mas:
1. a fonte pode ser uma URL também (não recomendado, melhor usar `RUN curl` ou `RUN wget` etc)
2. auto-extrai arquivos `.tar`

[Mais sobre as diferenças aqui.](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#add-or-copy)

</td>

<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
ADD hom* /mydir/
ADD rootfs.tar.xz /
ADD https://example.com/music.mp3 /usr/src/things/
```

</td>
</tr>

<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#cmd"><code>CMD</code></a></td>

<td>

Provê algum padrão pro container em execução<br /><br />só pode um `CMD` por dockerfile (caso tenham multiplos apenas o ultimo é considerado)<br /><br />o formato *exec* não invoca um shell (a nao ser que especifique [ex: `CMD ["sh", "-c", "echo $HOME"]` ])

> justamente por isso, `CMD [ "echo", "$HOME" ]` não teria o resultado esperado já que não há um shell pra processar/substituir a variavel

<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
# exec
CMD ["executable","param1","param2"]

# parametros pro ENTRYPOINT
CMD ["param1","param2"]

# shell
CMD command param1 param2
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
CMD echo "This is a test."
CMD ["npm", "start"]
```

</td>
</tr>

<tr>
<td><a href="https://docs.docker.com/engine/reference/builder/#entrypoint"><code>ENTRYPOINT</code></a></td>
<td>

permite configurar um container pra ser rodado como um executavel

</td>
<td>

ˢᶦⁿᵗᵃˣᵉ ↴

```dockerfile
# exec
ENTRYPOINT ["executable", "param1", "param2"]

# shell
ENTRYPOINT command param1 param2
```

ᵉˣᵉᵐᵖˡᵒ ↴

```dockerfile
# exec
ENTRYPOINT ["top", "-b"]
CMD ["-c"]

# shell
ENTRYPOINT exec top -b
```

</td>
</tr>

</table>

### BuildKit - Segredos

No Windows/Mac, BuildKit já é utilizado por padrão; no Linux, não. Para saber como ativar, [leia aqui](https://docs.docker.com/develop/develop-images/build_enhancements/#to-enable-buildkit-builds)

Caso queira, o comando abaixo verifica se o arquivo de configurações personalizadas já existe e, caso *nao* exista, cria o mesmo já especificando o uso do Buildkit:

```console
$ test -f /etc/docker/daemon.json || (echo '{ "features": { "buildkit": true } }' |  sudo tee /etc/docker/daemon.json)
```

~~ dar exemplo com segredo do Github, JWT ou bCrypt etc

### otimizando build


#### docker layered cache

![torre de hanoi](https://upload.wikimedia.org/wikipedia/commons/4/4f/Tower_of_Hanoi.gif)

> Only the instructions RUN, COPY, ADD create layers. Other instructions create temporary intermediate images, and do not increase the size of the build.

#### comando onbuild

#### multistage build

### dockerignore

## volumes

## docker pull

### registry x repositories

https://stackoverflow.com/a/34004418/11947314

## running a container

### interactively

### background

## log files

## remove containers

## remove images

## docker ps

## docker swarm
