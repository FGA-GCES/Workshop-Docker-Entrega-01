# Construindo um container mínimo

- ⚫️ [1. O que é conteinerização?](./README.md)
- ⚫️ [2. Diferenças entre VMs e Containers](./1_difference_between_vm_and_containers.md)
- ⚪️ [3. Construindo um container mínimo](./2_container_from_scratch.md)
- ⚫️ [4. Instalação do Docker](./3_installing_docker.md)
- ⚫️ [5. Conceitos de Docker](./4_docker_concepts.md)
- ⚫️ [6. Conceitos de Docker Compose](./5_docker-compose_concepts.md)

Para a construção desse container, precisaremos, basicamente, de 4 coisas:

- `chroot`: significa **change root**. traz **isolamento** pro nosso container; ao utilizar isso, cria-se um novo processo que têm o root aparente modificado. Ou seja, dentro desse novo root, voce é incapaz de modificar os arquivos do root original.

- `namespace`: feature do kernel Linux que traz **isolamento**; basicamente, um container não consegue visualizar/manipular recursos de outros containers (ou do próprio host) [o host também não consegue visualizar os recursos/processos dentro dos containers]
    - **controla o que voce pode ver/fazer**

- `cgroup`: significa **control group**. traz o controle de recursos; basicamente, voce pode especificar quanto de RAM, CPU etc aquele container poderá utilizar (de outra forma, ele tem acesso ilimitado aos recursos do host*)
    - **controla o que voce pode usar**

- `overlay`: um dos sistemas de arquivos utilizados no kernel*. traz a **integridade** da imagem. ou seja, permite criar uma imagem limpa que será utilizada como base pra construção de containers. dessa forma, vc pode manipular os conteudos dos containers (adicionar libs arquivos, etc), mas isso não modificará a imagem original
    > *existem inumeros tipos de filesystems (sistemas de arquivos); para listar os disponiveis no seu computador, rode o comando `cat /proc/filesystems`

    exemplo de alguns:

    > rootfs: o core de um sistema linux. contém todas as aplicações, configurações, dispositivos etc necessários para rodar seu sistema Linux
    <br /><br />procfs: um pseudo-filesystem que provê uma interface de comunicação entre o kernel e o usuario (é utilizado por exemplo pelo "gerenciador de tarefas" [system monitor] para visualizar os recursos atuais do pc etc)
    <br /><br />ext2-4: o sistema de arquivos utilizado por sistemas Unix; lida com conceitos de blocos, inodes, diretorios etc

## Resumo dos comandos

Essa seção serve como um "cheatsheet" para recapitular quais comandos executar etc; caso seja a primeira vez lendo esse capitulo, pode passar para a proxima parte =)

Criando imagem do container (dentro da VM já):

```bash
mkdir container_minimo && cd "$_"

mkdir -p {,usr/}{{,s}bin,lib{,64}}

wget https://www.busybox.net/downloads/binaries/1.31.0-defconfig-multiarch-musl/busybox-i686 -O bin/busybox

chmod +x bin/busybox

chroot . /bin/busybox --install -s

cp /bin/bash bin/

cp /lib/x86_64-linux-gnu/libtinfo.so.6 /lib/x86_64-linux-gnu/libdl.so.2 /lib/x86_64-linux-gnu/libc.so.6 lib/

cp /lib64/ld-linux-x86-64.so.2 lib64/

cd
```

Criando o script `make_container.sh`:

```bash
echo $'#!/bin/bash

IMAGE_PATH=$1

for ARGUMENT in "$@"
    do
    KEY=$(echo $ARGUMENT | cut -f1 -d=)
    VALUE=$(echo $ARGUMENT | cut -f2 -d=)

    case "$KEY" in
        CPU)   CPU=${VALUE} ;;
        RAM)   RAM=${VALUE} ;;     
        *)   
    esac    
done


function createRandomContainerName()
{
    local prefix=$(</dev/urandom tr -dc a-z | head -c 1)
    local randomName=$(</dev/urandom tr -dc a-z0-9_ | head -c 12)
    echo "$prefix$randomName"
}
containerName=$(createRandomContainerName)
export containerName


function prepareFoldersForOverlayFS() {
    mkdir -p /tmp/$containerName/{upper,workdir,overlay}
}


function createOverlayFS()
{
    mount -t \
        overlay -o lowerdir=$IMAGE_PATH,upperdir=/tmp/$containerName/upper,workdir=/tmp/$containerName/workdir \
        none \
        /tmp/$containerName/overlay
}


function installBusybox() {  
    chroot /tmp/$containerName/overlay/ /bin/busybox --install -s
}

function createCGroup() {
    sleep 1

    PID=$(ps aux | grep unshare | tail -2 | head -1 | awk \'{print $2}\') 
    
    cgcreate -a $containerName -g cpu,memory:$containerName

    set -x
    echo 5MB > /sys/fs/cgroup/memory/$containerName/memory.limit_in_bytes
    echo 100 > /sys/fs/cgroup/cpu/$containerName/cpu.shares

    # cgexec -g cpu,memory:$containerName $PID
    cgclassify -g cpu,memory:$containerName $PID
    set +x

    # Limit usage at 5% for a multi core system
    # cgset -r cpu.cfs_period_us=100 -r cpu.cfs_quota_us=$[ 5000 * $(getconf _NPROCESSORS_ONLN) ] $containerName

    # Set a limit of 80M
    # cgset -r memory.limit_in_bytes=80M $containerName
}

function setUpContainer() {
    export PS1="$containerName-# ";
    mkdir proc;
    mount -t proc none proc;
    bash
}
export -f setUpContainer

function launchContainer() {
    unshare --mount --uts --ipc --net --pid --fork --user --map-root-user \
    chroot /tmp/$containerName/overlay \
    bash -c "setUpContainer"
}
export -f launchContainer

function makeContainer() {
    set -x

    sudo -u $containerName bash -c "launchContainer"

    set +x
}

prepareFoldersForOverlayFS
createOverlayFS
installBusybox
adduser --disabled-password --gecos "" $containerName
usermod -aG sudo $containerName
printf "\n$containerName ALL=(ALL) NOPASSWD: /usr/sbin/chroot, /usr/bin/unshare\n" >> /etc/sudoers

createCGroup &

makeContainer
' > make_container.sh
```

## Instalando uma máquina virtual para rodar esse tutorial

Para evitar poluir seu sistema operacional com esse tutorial, fazer testes livremente, vamos instalar uma VM (extremamente leve) para servir de sandbox:

| # | Passo | Comando |
| - | ----- | ------- |
| 1 | Instalar VirtualBox | Abra o link e selecione o metodo de sua preferencia: https://www.virtualbox.org/wiki/Linux_Downloads |
| 2 | Instalar [bakerX](https://github.com/ottomatica/bakerx) *(front-end for creating and managing (micro) virtual environments)*| Opção 1: `npm install ottomatica/bakerx -g`<br />Opção 2: [acesse o link](https://github.com/ottomatica/bakerx#installation) |
| 3 | Baixar iso do ubuntu | `bakerx pull focal cloud-images.ubuntu.com` |
| 4 | Criar a VM | `bakerx run construindo_container_minimo focal` |
| 5 | Entrar na VM | `bakerx ssh construindo_container_minimo` |
| 6 | Garantir que sempre entraremos nessa VM como root | `printf "sudo -i\n" >> ~/.bashrc && exec $SHELL` |
| 6 | Dar update p instalar alguns utilitarios | `apt update` |
| 7 | Instalar:<br />`tree` (listar diretorios e subdiretorios) | `apt install tree` |

Atenção
----- 

Caso o terminal esteja tendo comportamento inesperado (ex: ao apertar Backspace, surge um espaço), talvez tenha que modificar o $TERM

```console
# no seu pc mesmo, no host
~# echo $TERM
xterm-kitty
```

```console
# na VM {que acessa com o bakerx}
~# echo "export TERM=xterm" >> ~/.bashrc
~# exec $SHELL
```

## Preparando um diretorio para ser nosso container

Ainda dentro de nossa VM (passo 5):

Vamos criar algumas pastas replicando o filesystem do Linux:

> Obs: omitirei o nome `root@ubuntu-focal:~# ` aqui para poluir menos

> Ou seja, no seu terminal você verá `root@ubuntu-focal:~# `, aqui no tutorial apenas `~#`

```console
~# mkdir container_minimo && cd "$_"

~/container_minimo# mkdir -p {,usr/}{{,s}bin,lib{,64}}
```

A estrutura atual da pasta deve estar assim:

```console
~/container_minimo# tree

.
├── bin
├── lib
├── lib64
├── sbin
└── usr
    ├── bin
    ├── lib
    ├── lib64
    └── sbin

7 directories, 0 files
```

Vamos adicionar um pacote para utilizarmos dentro desse container que estamos criando?

Copie o pacote `ls` para a pasta atual:

```console
~/container_minimo# cp /bin/ls bin/ls
```

Vamos rodar o comando `ls` dentro do nosso novo container (utilizando o comando `chroot`):

```console
~/container_minimo# chroot . ls

chroot: can't execute 'ls': No such file or directory
```

Hmm... O problema é que esse comando (`ls`) necessita de algumas bibliotecas para funcionar. Felizmente, o comando `ldd` nos lista todas as dependencias que esse pacote possa ter:

```console
~/container_minimo# ldd /bin/ls

	/lib/ld-musl-x86_64.so.1 (0x7f951dcf1000)
	libc.musl-x86_64.so.1 => /lib/ld-musl-x86_64.so.1 (0x7f951dcf1000)
```

Agora, basta copiar essa biblioteca para nosso container:

```console
~/container_minimo# cp /lib/ld-musl-x86_64.so.1 lib/.
```

Agora, vamos tentar novamente rodar `ls`:

```console
~/container_minimo# chroot . ls /
bin   lib   sbin  usr
```

Funcionou :)

Vamos adicionar o `bash` também:

```console
~/container_minimo# cp /bin/bash bin/

~/container_minimo# cp /lib/x86_64-linux-gnu/libtinfo.so.6 /lib/x86_64-linux-gnu/libdl.so.2 /lib/x86_64-linux-gnu/libc.so.6 lib/

~/container_minimo# cp /lib64/ld-linux-x86-64.so.2 lib64/
```

Entretanto, seria muito tedioso e exaustivo copiar cada comando que julgassemos necessarios (como, por exemplo, o shell `bash`, o comando `cp`, `mv` e assim por diante)

Portanto, vamos utilizar o [Busybox](https://en.wikipedia.org/wiki/BusyBox) (= varios [comandos unix](https://en.wikipedia.org/wiki/List_of_Unix_commands) uteis, como `cd`, `alias`, `mv` etc)

```console
~/container_minimo# wget https://www.busybox.net/downloads/binaries/1.31.0-defconfig-multiarch-musl/busybox-i686 -O bin/busybox
```

Torne esse arquivo em executavel

```console
~/container_minimo# chmod +x bin/busybox
```

Instale os symlinks dentro do container:

```console
~/container_minimo# chroot . /bin/busybox --install -s
```

Verifique que agora existem inumeros pacotes dentro da pasta de binarios:

```console
~/container_minimo# chroot . ls /bin/
```

## Interagindo com nosso container

Para entrarmos nesse nosso novo container (e utilizar o shell dentro do mesmo), basta rodar o comando `bash`:

```console
~/container_minimo# PS1="C-$ " chroot . bash
```

> o `PS1` modifica o prompt; é so para distinguirmos o nome do shell dentro do container em relação ao mundo externo :)

Vamos criar um arquivo aleatorio dentro desse container:

```console
C-$ touch teste.txt

C-$ ls

bin        lib        linuxrc    sbin       teste.txt  usr

C-$ exit
```

Agora que saímos do container (com o `exit`), podemos ver um problema. A modificação que fizemos dentro do container veio para o "mundo real"...

```console
~/container_minimo# ls

bin        lib        linuxrc    sbin       teste.txt  usr
```

### Aprimorando nosso container

#### Overlay

Para mantermos a integridade, vamos utilizar o overlayFS. Esse sistema consiste de 3 camadas:

- Lower: read-only, a imagem (não modificavel dentro do container) utilizada como base pra criação do container
- Upper: read-write, onde será armazenado as modificações feitas
- Overlay: o container de fato. a composição das duas camadas

> existe ainda o `workdir`. mas ele não têm tanto valor semantico, é so um diretorio aleatorio utilizado pelo kernel enquanto ele monta o Overlay

![overlay](https://imgur.com/ZA1vq3d.png)

Como bônus, vamos montar o filesystem `proc`, também, para monitorarmos os recursos dentro desse container.

Crie um novo arquivo dentro da VM (não container) chamado `make_container.sh` (e vamos sair dessa pasta do container, também):

```console
~/container_minimo# cd
~# vim make_container.sh
```

Aperte `i` para entrar no modo inserção dentro do editor de texto `vim`, copie o codigo abaixo e cole (`ctrl + shift + v`)

```bash
#!/bin/bash

CONTAINER_PATH=$1


function createRandomContainerName()
{
    local randomName=$(</dev/urandom tr -dc A-Za-z0-9-_ | head -c 10)
    echo "$randomName"
}


containerName=$(createRandomContainerName)


function prepareFoldersForOverlayFS()
{
    mkdir -p /tmp/$containerName/upper \
             /tmp/$containerName/workdir \
             /tmp/$containerName/overlay
}


function createOverlayFS()
{
    mount -t \
        overlay -o lowerdir=$CONTAINER_PATH,upperdir=/tmp/$containerName/upper,workdir=/tmp/$containerName/workdir \
        none \
        /tmp/$containerName/overlay
}


function installBusybox() {  
    chroot /tmp/$containerName/overlay/ /bin/busybox --install -s
}


function launchContainer() { 
    PS1="$containerName-# " \
    chroot /tmp/$containerName/overlay \
    bash -c "mkdir /proc;
    mount -t proc none /proc;
    bash"
}


prepareFoldersForOverlayFS
createOverlayFS
installBusyLogarbox
launchContainer
```

Salve o arquivo (aperte `ESC`, digite `:wq` e aperte `ENTER`)

#### Vulnerabilidade: visualizando recursos que não deveria

Para visualizarmos melhor o motivo de precisarmos de `namespaces`, façamos o seguinte:

Inicie um container no background (adicionando `&` ao final do comando):

```console
~# bash make_container.sh container_minimo/ &
```

Vamos verificar qual o PID (process id) do mesmo:

```console
~# ps aux | grep make_container.sh
1327 root      0:00 bash make_container.sh container_minimo/
1445 root      0:00 grep make_container.sh
```

Mas, quando se inicia um processo no background (utilizando o operador `&`), podemos verificar o PID deste processo de forma mais simples:

```console
~# echo $!
1327
```

Ok, sabemos agora que o PID desse container rodando no background é `1327`.
Logar
Vamos verificar quais processos são possiveis de ser visualizados dentro desse container:

```console
qqyioEo0gM-# ps

...
 1097 0         0:00 sshd: root@pts/0
 1099 0         0:00 -ash
 1326 0         0:00 [kworker/u2:1-ev]
 1327 0         0:00 bash make_container.sh container_minimo/
 1337 0         0:00 bash
 1412 0         0:00 bash make_container.sh container_minimo/
 1422 0         0:00 bash
 1427 0         0:00 ps
```

Opa! Eu consigo visualizar o PID do outro container, o que é uma vulnerabilidade bem grande. Com isso, eu poderia encerrar o outro container facilmente:

```console
qqyioEo0gM-# kill -9 1327
```

Saindo do container atual, vamos verificar se o container que estava em background ainda está em execução:

```console
qqyioEo0gM-# exit
~# ps aux | grep make_container.sh
1447 root      0:00 grep make_container.sh
```
Logaro Namespace. De acordo com a [Wikipedia](https://en.wikipedia.org/wiki/Linux_namespaces):

```text
Namespaces are a feature of the Linux kernel that partitions kernel resources such that one set of processes sees one set of resources while another set of processes sees a different set of resources. The feature works by having the same namespace for a set of resources and processes, but those namespaces refer to distinct resources. Resources may exist in multiple spaces. Examples of such resources are process IDs, hostnames, user IDs, file names, and some names associated with network access, and interprocess communication.

. . .

Three syscalls can directly manipulate namespaces:

clone,   flags to specify which new namespace the new process should be migrated to.

unshare,     allows a process (or thread) to disassociate parts of its execution context that are currently being shared with other processes (or threads)

setns,  enters the namespace specified by a file descriptor.
```

Para o nosso caso, utilizaremos o comando [`unshare`](https://man7.org/linux/man-pages/man1/unshare.1.html).

Modifique a função `launchContainer()` dentro do arquivo `make_container.sh` da seguinte forma:

```bash
function launchContainer() { 
    PS1="$containerName-# " \
    unshare --mount --uts --ipc --net --pid --fork --user --map-root-user \
    chroot /tmp/$containerName/overlay \
    bash -c "mkdir /proc;
    mount -t proc none /proc;
    bash"
}
```

> existe uma flag do `unshare` que já monta o procfs para nós (`--mount-proc`), o que significa que bastaria instanciar o shell (sem necessidade de rodar a flag `-c` e os respectivos comandos criando & montando o procfs)

Vamos instanciar um processo aleatorio em background novamente:

```console
~# sleep 6000 &
```

Verificando qual o PID do mesmo:

```console
~# echo $!
1107
```

Agora, vamos entrar num container e tentar encerrar esse processo:

```console
uaLxGqwWPW-# kill -9 1107
bash: can't kill pid 1107: No such process
```

Epa! Vamos verificar quais processos podemos enxergar:

```console
uaLxGqwWPW-# ps
PID   USER     TIME  COMMAND
    1 0         0:00 bash
    4 0         0:00 ps
```

Pronto! Criamos um novo namespace isolado do host :D

#### CGroups

Bem, a ultima questão é o fato de que nosso container tem acesso irrestrito ao uso de recursos computacionais do host.

Significa que pode usar quanta cpu, ram etc precisar

Isso é um problema pois, imagine o seguite cenario:

```text
voce tem um servidor com 500tb de armazenamento, 512gb de ram, cpu brabo etc
e ai decide alugar esse servidor da seguinte forma: 
qualquer pessoa manda o container da aplicação que tem, e esse servidor vai rodar elas

imagine que Joao mandou o container dele contendo o e-commerce dele, e a Ana mandou o container dela contendo um site de noticias

durante a black friday, houve um pico de acessos no e-commerce do joao, que sugou todos os recursos computacionais do servidor (host)

o servidor travou e, consequentemente, parou de rodar o container da Ana tambem
```

> obs: esse ~storytelling foi roubado [daqui](https://btholt.github.io/complete-intro-to-containers/cgroups)

Vamo bota a mao na massa entao.

Primeiramente, vamos instalar o `cgroup-tools`:

```console
~# apt install cgroup-tools -y
```



----
Logar
Fontes:

- Rahul Singh: [<video 6min> overlayFS | CVE-2021-3493 | Technical Details](https://www.youtube.com/watch?v=uJbJKcUsILI)

- School of Devops: [<video 10min> Lesson 4: Whats under the hood - Namespaces, Cgroups and OverlayFS](https://www.youtube.com/watch?v=2ZdJ_3sBr6A)

- Archlinux: [\<artigo> Overlay filesystem](https://wiki.archlinux.org/title/Overlay_filesystem)

- Brian Holt (Frontend Masters, Microsoft): [\<artigo>Complete intro to Containers](https://btholt.github.io/complete-intro-to-containers)

- CSC-Devops (NC State University): [\<tutorial>Containers](https://github.com/CSC-DevOps/Containers)