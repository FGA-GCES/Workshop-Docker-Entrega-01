# Conceitos de Docker

- ⚫️ [1. O que é conteinerização?](./README.md)
- ⚫️ [2. Diferenças entre VMs e Containers](./1_difference_between_vm_and_containers.md)
- ⚫️ [3. Construindo um container mínimo](./2_container_from_scratch.md)
- ⚫️ [4. Instalação do Docker](./3_installing_docker.md)
- ⚪️ [5. Conceitos de Docker](./4_docker_concepts.md)
- ⚫️ [6. Conceitos de Docker Compose](./5_docker-compose_concepts.md)

## Dockerfile

### principais comandos

#### from

#### copy

#### run

#### workdir

#### expose

#### cmd

#### entrypoint

#### env

#### user

#### label

### otimizando build

#### docker layered cache

![torre de hanoi](https://upload.wikimedia.org/wikipedia/commons/4/4f/Tower_of_Hanoi.gif)

> Only the instructions RUN, COPY, ADD create layers. Other instructions create temporary intermediate images, and do not increase the size of the build.

#### comando onbuild

#### multistage build

### dockerignore

## volumes

## docker pull

### repositories

## run interactively

## run on background

## log files

## remove containers

## remove images

## docker ps

## docker swarm
