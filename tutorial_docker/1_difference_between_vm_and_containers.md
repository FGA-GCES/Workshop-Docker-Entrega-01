# Diferenças entre VMs e Containers

- ⚫️ [1. O que é conteinerização?](./README.md)
- ⚪️ [2. Diferenças entre VMs e Containers](./1_difference_between_vm_and_containers.md)
- ⚫️ [3. Construindo um container mínimo](./2_container_from_scratch.md)
- ⚫️ [4. Instalação do Docker](./3_installing_docker.md)
- ⚫️ [5. Conceitos de Docker](./4_docker_concepts.md)
- ⚫️ [6. Conceitos de Docker Compose](./5_docker-compose_concepts.md)

## Recomendação de materiais

- RedHat: [\<artigo> Containers x máquinas virtuais](https://www.redhat.com/pt-br/topics/containers/containers-vs-vms)

- IBM Cloud: [<vídeo 8min> Containers vs VMs: Qual é a diferença?](https://www.youtube.com/watch?v=cjXI-yxqGTI)

## Relembrando FSO

| Conceito | "Definição" |
| -------- | ----------- |
| Sistema Operacional | Kernel + Apps de Sistema |
| Kernel | coração do S.O., primeiro programa executado quando se inicia um SO;<br />responsavel por gerenciar recursos do computador [uso da cpu, memoria ram, etc] de acordo com as demandas de Apps de Sistema e Apps comuns<br />**A interface entre Software e Hardware** |
| Apps de Sistema | sistema de arquivos, programas de network, drivers etc; <br />*(nao sao os programas "comuns", como navegador, vscode, discord etc)* |

## Aprofundando um pouco em Máquinas Virtuais

Existem dois tipos de Hypervisors (softwares que lidam com VMs):

| Distinções | **Tipo 2** | **Tipo 1** |
| ---------- | ---------- | ---------- |
| Conhecido como: | Virtual Machine Monitor (VMM) | Nativo/Bare Metal |
| Softwares: | Qemu, VMware Workstation, Oracle Virtual Box | Xen HVM, KVM*, VMware vSphere |
| Onde roda: | Em cima do sistema operacional que voce estiver utilizando | No Hardware do seu computador (usa a bios e td mais) |
| Na prática: | O mais comum/conhecido: você instala um aplicativo no seu PC, e nele tem uma tela pra você adicionar os SO's que quiser. Então, voce pode bootar em qualquer um desses SO's que estarão disponiveis em janelas de aplicativo diferentes. | No geral, em vez de instalar um sistema operacional, se instala o Hypervisor, e neste você será capaz de instalar varios S.Os (chamados de guests) distintos; A diferença disso p/ um dual-boot, por exemplo, é que multiplos SOs podem ser executados simultaneamente |

Ou seja, no tipo 1, o Hypervisor pode ser considerado um Kernel; no tipo 2, o Hypervisor se aproveita do Kernel utilizado para gerenciar os recursos de suas VMs

Lembrando que cada máquina virtual, naturalmente, terá seu próprio Sistema Operacional e, consequentemente, seu próprio Kernel, também!

------

Fontes:

- Pluralsight: [<vídeo 5min> Type 1 vs. Type 2 Hypervisors](https://www.youtube.com/watch?v=UEk0CKoeUnA)

- Phoenixnap: [\<artigo> What is a Hypervisor? Types of Hypervisors 1 & 2](https://phoenixnap.com/kb/what-is-hypervisor-type-1-2)

## Aprofundando um pouco em Containers

Containers consistem de virtualizações do Sistema Operacional (em vez do Hardware). Têm como caracteristicas o isolamento e gerenciamento de recursos. Isso é possível pela existencia das seguintes funcionalidades do Kernel Linux:

- Namespaces: responsavel pelo **isolamento** [processos distintos enxergam recursos (hostnames, ids de usuarios, ids de processos, etc) distintos]

- Cgroups: responsavel pelo **gerenciamento de recursos** [limita, prioriza e controla grupos de processos quanto ao uso de CPU, memória, I/O, network, etc]

Ou seja, com apenas 1 Kernel (o do computador que está utilizando agora) consegue-se instanciar inumeros containers (todos se utilizando do seu Kernel), sendo que cada um destes é apenas um processo a mais no seu computador.

> Justamente por isso, você não conseguiria rodar um container com imagem Windows num pc Linux: via de regra, o kernel especificado no seu Dockerfile tem de ser compativel com o sistema operacional do seu computador.

> **mas é possivel utilizar imagens linux dentro do windows, caso utilize o WSL (Windows Subsystem for Linux)*

------

Fontes:

- IBM Cloud: [<video 8min> Containerization Explained](https://www.youtube.com/watch?v=0qotVMX-J5s)

- dotCloud: [\<artigo> PaaS under the hood, episode 1: kernel namespaces](http://web.archive.org/web/20150326185901/http://blog.dotcloud.com/under-the-hood-linux-kernels-on-dotcloud-part)

- McMaster University: [\<slides> Docker – OS Level Virtualization](https://lics.cas.mcmaster.ca/sites/default/files/2018-03/Docker%20-%20OS%20Level%20Virtualization%20__%20SMALL.pdf)

- Wikipedia:
  - [OS-level virtualization](https://en.wikipedia.org/wiki/OS-level_virtualization)
  - [cgroups](https://en.wikipedia.org/wiki/Cgroups)
  - [Linux namespaces](https://en.wikipedia.org/wiki/Linux_namespaces)

- Stackoverflow: [How is Docker different from a virtual machine?](https://stackoverflow.com/a/16048358)

## Resumo

| Virtualização | Comportamento |
| ------------- | ------------- |
| Máquinas Virtuais | <img src="https://imgur.com/HtEnKMV.png" width="400"/> <img src="https://imgur.com/uikZBHB.png" width="400"/> |
| Container Docker | ![Container](https://i.imgur.com/JMAGIzw.png) |

Ou seja, num cenário onde você gostaria de rodar seu app multiplas vezes simultaneamente (por questões de escalabilidade, por exemplo):

| VMs | Containers |
| --- | ---------- |
| Necessitaria de 1 máquina virtual para cada instancia; <br />cada máquina virtual necessita, normalmente, de dezenas de GB para armazenar no Host <br />geralmente precisa-se de alguns minutos para bootar | necessita-se de apenas 1 imagem (que pesa lá seus 200mb~1gb) e pode-se instanciar infinitas vezes, de forma quase instantanea |
