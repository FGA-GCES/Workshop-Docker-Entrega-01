# Phpmyadmin

## Pré-requisitos

- docker

## Passo a passo

1. Primeiro, clone o repositorio

    ```console
    git clone https://github.com/FGA-GCES/Workshop-Docker-Entrega-01

    cd Workshop-Docker-Entrega-01/trabalho_individual/phpmyadmin
    ```

2. Inicie os bancos de dados [1](../chat-service/README.md) e [2](../users-service/README.md)

3. Inicie um container [`phpmyadmin`](https://hub.docker.com/r/phpmyadmin/phpmyadmin/)

    ```console
    $ docker run \
        --name phpmyadmin \
        -d \
        --link users-service-db:users-service-db \
        --link chat-service-db:chat-service-db \
        -p 7300:80 \
        -v config.user.inc.php:/etc/phpmyadmin/config.user.inc.php \
        phpmyadmin
    ```

4. Acesse em [http://localhost:7300](http://localhost:7300)