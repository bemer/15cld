# Bootstrap de Nodes utilizando o Knife

Como vimos no laboratório anterior, o Knife é uma ferramenta que nos permite interagir com o Chef Server para várias tarefas. Neste laboratório, vamos ver na prática como funciona o processo de bootstrap de um novo servidor, ou seja, o processo de instalação do Chef Client em servidores de nossa rede de remotamente, através do Knife.

Antes de começarmos a execução deste laboratório, é importante no entanto entender qual é a arquitetura que vamos utilizar para provisionar nossos nodes. A idéia aqui, é possuir diversos servidores diferentes, para que possamos interagir com todos eles, no entanto como não temos uma quantidade grande de máquinas virtuais, vamos utilizar containers para esta tarefa.

O que vamos fazer inicialmente é:

* Criar uma imagem Docker com um SSH server para que possamos acessar a mesma remotamente;
* Iniciar containers a partir desta imagem;
* Utilizar o Knife para realizar o bootstrap destas imagens, que estarão simulando nossos servidores.

Estes containers serão executados dentro de nosso `Chef Client`, e a partir do `Chef Server` vamos utilizar o Knife para realizar o bootstrap.

Este desenho demonstra a arquitetura utilizada neste laboratório:

![lab architecture](/04-BootstrapUsandoKnife/images/lab_architecture.png)

## 1. Criando a imagem Docker

O primeiro passo a ser executado neste caso, é a criação da imagem Docker que vamos utilizar para simular os servidores. Acesse o servidor `chef-client` e crie uma pasta chamada `docker` e acesse a mesma utilizando o seguinte comando:

    $ mkdir docker && cd docker

Crie um arquivo chamado `Dockefile`:

    $ touch Dockerfile

E utilizando um editor de texto de sua preferência, insira o seguinte conteúdo a este arquivo:

    FROM ubuntu:16.04

    RUN apt-get update && apt-get install -y openssh-server
    RUN mkdir /var/run/sshd
    RUN echo 'root:123456' | chpasswd
    RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

    # SSH login fix. Otherwise user is kicked off after login
    RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
    
    ENV NOTVISIBLE "in users profile"
    RUN echo "export VISIBLE=now" >> /etc/profile
    
    EXPOSE 22
    CMD ["/usr/sbin/sshd", "-D"]

Note que este este Dockerfile está utilizando a imagem base `ubuntu:16.04` e realizando a instalação e configuração de um `ssh server`.

Após criar este arquivo, execute o comando:
