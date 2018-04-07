# Instalação do Chef Server

O Chef Server é um componente que age como um "hub" para os dados de configuração, sendo responsável por armazenar os cookbooks e suas receitas, as políticas que deverão ser aplicadas a cada um dos servidores em nossa infra estrutura e também os metadados que descrevem cada um destes servidores.

Neste tutorial, vamos realizar o processo de instalação do Chef Server em nosso servidor.

## 1. Instalando o Chef Server

Para realizarmos a instalação do Chef Server, vamos utilizar a máquina virtual com o nome `chef-server`. Inicie a máquina virtual em seu computador e siga os passos descritos no tutorial anterior, nos capítulos [1. Obtendo o endereço IP do servidor](/02-ChefClient#01-obtendo-o-endereço-ip-do-servidor) e [2. Configurando o Putty](/02-ChefClient#02-configurando-o-putty) para realizar acesso ao novo servidor utilizando os seguinte dados:

    Usuário: chef-admin
    Senha: chefserver

>Ao salvar a conexão no Putty, utilize desta vez o nome `chef-server`.

Agora que estamos conectados ao servidor através do Putty, vamos iniciar o processo de instalação do `Chef Server`. Para isto, vamos primeiro realizar o download da última versão do Chef Server. O link para download da versão mais recente do Chef Server para instalação no Ubuntu poderá ser encontrado [nesta url](https://downloads.chef.io/chef-server#ubuntu).

Neste caso, vamos utilizar a versão `12.17.33`.

Em seu Chef Server, execute o seguinte comando para realizar o download do pacote:

    $ wget https://packages.chef.io/files/stable/chef-server/12.17.33/ubuntu/16.04/chef-server-core_12.17.33-1_amd64.deb

Aguarde o processo de download do arquivo, e quando o mesmo for completado, instale o Chef Server utilizando o seguinte comando:

    $ sudo dpkg -i chef-server-core_12.17.33-1_amd64.deb

>Por se tratar da instalação de um pacote, precisaremos utilizar um usuário com permissões de `root`. Por isso o uso do comando `sudo`.

Este processo deverá demorar alguns minutos.
