+++
date = 2019-10-29T00:29:00Z
description = ""
draft = true
image = "__GHOST_URL__/content/images/2019/10/heroku-docker-1.png"
slug = "criando-imagem-docker-customizada-e-publicando-no-heroku"
title = "Criando imagem Docker customizada e publicando no Heroku"

+++


Nesse artigo vamos criar uma imagem Docker com uma simple aplicação `NodeJs` a partir da imagem padrão `NodeJs`, depois vamos publicar na nuvem **Heroku**.

Heroku: https://heroku.com/
Docker: https://www.docker.com/

Neste tutorial, você aprenderá como:

> * Comandos básicos do `Docker`
> * Criar uma imagem customizada no `Docker`
> * Publicar uma imagem `Docker` na nuvem `Heroku`

## Pré-requisitos

* [Docker](https://docs.docker.com/docker-for-windows/install/)
* [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)

##Preparando para criar a imagem Docker

Primeiro abra seu console favorito (estou utilizando o `powershell` do Windows) e digite:

```shell
PS C:\Users\ricardo>docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Isso irá validar se o Docker está instalado e rodando corretamente.

Vamos ao que interessa.

1 - Vamos baixar a imagem do ubuntu base.

```shell
PS C:\Users\ricardo>docker pull ubuntu

Using default tag: latest
latest: Pulling from library/ubuntu
22e816666fd6: Downloading [==========>                                        ]  5.831MB/26.69MB                                         079b6d2a1e53: Download complete                                                                                                          11048ebae908: Download complete                                                                                                          c58094023a2e: Download complete                                                                   
```

Quando finalizado teremos a imagem do *Ubuntu* pronta para customizarmos.

2 - Agora verifique se a imagem que acabamos de baixar está na lista de imagens disponíveis.

```shell
PS C:\Users\ricardo>docker images

REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
ubuntu                                   latest              cf0f3ca922e0        10 days ago         64.2MB
```

3 - Vamos criar um container com a imagem recem baixada

```shell
PS C:\Users\ricardo>docker run -t -i ubuntu /bin/bash

root@1f81d43b7f5a:/#                                                                  
```

Esse comando irá criar um container `ubuntu` com o processo `bash` rodando. O parâmetro `-i -t` nos permitirá rodar comandos dentro do container rodando (-i "iterativo" e -t "Allocate a pseudo-TTY" - irá permitir comandos Ctrl Z e C).

Perceba que você agora está executando comandos dentro do `Ubuntu` no `bash`, ou seja, você está dentro do container que acabamos de criar.

Aqui podemos customizar nossa imagem com os componentes necessários.

Agora execute o comando `Ctrl + Z` e `Ctrl + C` (no windows) ou `Ctrl + P` e `Ctrl + Q` (no linux). Isso irá fazer sair do container, mas sem pará-lo. Se você quiser sair do container e pará-lo, digite `exit` no `bash` do container.

Mas por enquanto execute somente o `Ctrl + Z` e `Ctrl + C` para sair do container sem pará-lo.

4 - Rode o comando 

```shell
PS C:\Users\ricardo>docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
58402372a956        ubuntu              "/bin/bash"         4 minutes ago       Up 4 minutes                            relaxed_wu                                                            
```

Perceba que temos um container rodando, no meu caso é o `58402372a956`, o seu deve ser algo diferente.

##Criando a imagem customizada

1 - Vamos voltar novamente ao container. Rode o comando

```shell
PS C:\Users\ricardo>docker attach 58402372a956

root@58402372a956:/#                                                               
```

Substitua o id do container pelo seu.

2 - Vamos instalar o `NodeJs` na nossa imagem. Para isso é fácil, rode o comando.

```shell
root@fbe348641467:/# apt-get update

Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
Get:2 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]
0% [2 InRelease 102 kB/242 kB 42%] [Connecting to security.ubuntu.com (9                                                            
```

3 - Aguarde o termino da atualização dos pacotes e rode o seguinte comando (não se esqueça de pressionar `Y` quando lhe for solicitado confirmação).

```shell
root@fbe348641467:/# apt-get install curl -y

root@fbe348641467:/# curl -sL https://deb.nodesource.com/setup_12.x | bash -

root@fbe348641467:/# apt-get install nodejs -y                                                         
```

### Configurando o nginx para uma porta dinâmica

Precisamos configurar o `nginx` para rodar em uma porta dinâmica, isso porque o Heroku atribui uma porta dinamicamente para cada container que ele executa e não tem como saber antecipamente qual porta será atribuida.

Vamos editar o arquivo de configuração do `nginx` para executar a partir de uma porta dinâmica.
Primeira vamos instalar o `nano`, um editor de texto. Execute o seguinte comando.

```shell
apt-get install nano
```

Após instalado execute:

```shell
nano /etc/nginx/sites-enabled/default
```

Modifique a parte do arquivo que configura a porta de escuta.

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
```

Para porta dinâmica (vindo de variáveis de ambiente).

```
server {
        listen ${{PORT}} default_server;
        listen [::]:${{PORT}} default_server;
```

Para salvar o arquivo execute os comandos `Ctrl + O` e `Enter` e depois `Ctrl + X` para fechar o arquivo.

Agora rode o comando abaixo para iniciar o serviço do `nginx`

```shell
root@b677895b7d6b:/# /etc/init.d/nginx start

 * Starting nginx nginx                                                         
```

Rodando o comando `top` podemos ver os processos do `nginx` já rodando. Para sair do `top` execute `Ctrl + C`.
 
```shell
top - 01:29:08 up  1:08,  0 users,  load average: 0.07, 0.10, 0.04
Tasks:   5 total,   1 running,   4 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  2047012 total,  1182460 free,   340496 used,   524056 buff/cache
KiB Swap:  1048572 total,  1048572 free,        0 used.  1557032 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
    1 root      20   0   18504   3492   3064 S   0.0  0.2   0:00.05 bash
  783 root      20   0  141108   1548     64 S   0.0  0.1   0:00.00 nginx
  784 www-data  20   0  141484   3396   1692 S   0.0  0.2   0:00.00 nginx
  785 www-data  20   0  141484   3396   1692 S   0.0  0.2   0:00.00 nginx
  787 root      20   0   36624   3260   2792 R   0.0  0.2   0:00.02 top                                                      
```

### Comitando o novo container

Agora saia do container sem pará-lo (`Ctrl + Z` e `Ctrl + C`) e execute os comandos abaixo.

`docker ps` irá listar os containers rodando. Pegue o id do container `ubuntu` e execute o segundo comando `docker commit {ID DO SEU CONTAINER} {SEU-ALIAS}/nginx`

```shell
PS C:\Users\ricardo> docker ps                           
                                                                                                                                       CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b677895b7d6b        ubuntu              "/bin/bash"         12 minutes ago      Up 12 minutes                           elated_pare

PS C:\Users\ricardo> docker commit b677895b7d6b rmauro/nginx                                                                                                                                    sha256:ba0b7b2a3e3df417305e9ff27e055a00af729075696ccf4f7e1f2248d22428b5
```

Rode o comando `docker images` e você verá a nova imagem criada.

```shell
PS C:\Users\ricardo> docker images

REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
rmauro/nginx                             latest              ba0b7b2a3e3d        4 minutes ago       152MB
ubuntu                                   latest              cf0f3ca922e0        10 days ago         64.2MB
PS C:\Users\ricardo>                                                                                            
```

##Testando o container localmente
Podemos testar local antes de subir no heroku e isso que vamos fazer.

1 - Execute o comando a seguir.

```shell
```

##Subindo a imagem para o Heroku
####Criando o Dyno no Heroku

Se você não tem conta no heroku, crie uma e acesse o dashboard. 


![Heroku-dashboard](__GHOST_URL__/content/images/2019/10/heroku-1.png)
https://dashboard.heroku.com/apps

1 - Crie um novo **App** com um nome único, no meu caso será **rmauro-nginx**.

![Heroku-app](__GHOST_URL__/content/images/2019/10/heroku-2.png)

Após criado app heroku já podemos executar os comandos para subir a aplicação.

