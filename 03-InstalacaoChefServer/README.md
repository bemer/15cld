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

Agora, execute o seguinte comando para atualizar o endereço IP do Chef Server utilizado para a geração dos certificados:

    echo api_fqdn "\""$(ifconfig enp0s3 | grep 'inet addr' | cut -d: -f2 | awk '{print $1}')"\"" | \
    sudo tee --append /etc/opscode/chef-server.rb > /dev/null

E em seguida execute execute o seguinte comando para configurar o servidor:

    $ sudo chef-server-ctl reconfigure

Este processo deverá demorar alguns minutos.

Ao final, você deverá ver o seguinte output:

    Chef Client finished, 495/1084 resources updated in 04 minutes 46 seconds
    Chef Server Reconfigured!

A partir deste momento, o Chef Server já está instalado em nosso servidor, sendo necessário agora instalarmos alguns módulos adicionais para interação com o mesmo.

## 2. Instalação dos módulos adicionais

O primeiro módulo que iremos instalar é a interface web do servidor. Para isto, vamos executar os seguintes comandos:

    $ sudo chef-server-ctl install chef-manage
    $ sudo chef-server-ctl reconfigure
    $ sudo chef-manage-ctl reconfigure

>Durante a execução do último comando, será necessário aceitarmos os termos de uso do Chef Manage. Será apresetado um arquivo com os termos em sua tela. Para sair deste arquivo, digite `:q` e em seguida digite `yes` e pressione `Enter`.

E em seguida, instalaremos o módulo de relatórios:

    $ sudo chef-server-ctl install opscode-reporting
    $ sudo chef-server-ctl reconfigure
    $ sudo opscode-reporting-ctl reconfigure

Agora, vamos criar um novo usuário para acessarmos a interface de administração do Chef Server utilizando o seguinte comando:

    $ sudo chef-server-ctl user-create brunoemer Bruno Emer brunoemer@gmail.com '123456' --filename ~/brunoemer.pem

> Lembre-se de substituir o comando, inserindo seu nome e endereço de email.

Uma vez executados os passos para a instalação do Chef Server e dos módulos adicionais, acesse o servidor através de seu browser. Você deverá ser direcionado para a tela de login do Chef Server. Utilize o usuário e senha que acabou de criar para realizar o primeiro acesso:

![chef server login](/03-InstalacaoChefServer/images/chef_server_login.png)

## 3. Criação de uma organização

No Chef Server, uma `organização` é uma entidade utilizada para controlar os acessos. cada organização contém grupos padrão, pelo menos um usuário e um nó onde o chef-client é instalado.

Quando trabalhamos com o Chef Server, podemos ter diversas organizações, sendo que uma destas organizações é criada justamente no processo de instalação.

Ao executarmos o login pela primeira vez no Chef Server, nos será apresentada uma tela para a criação de nossa organização padrão. Nesta tela, clique em `Create New Organization`:

![create organization](/03-InstalacaoChefServer/images/create_organization.png)

Em seguida preencha o `Full Name` e o `Short Name` desta organização como `mba-fiap` e clique em `Create Organization`:

![mba fiap organization](/03-InstalacaoChefServer/images/mba_fiap_organization.png)

Você será redirecionado para a tela principal do Chef Server:

![chef main screen](/03-InstalacaoChefServer/images/chef_main_screen.png)
