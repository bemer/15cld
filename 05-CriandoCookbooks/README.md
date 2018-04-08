# Criando Chef Cookbooks

Neste momento, já temos nosso ambiente pronto com o Chef Server instalado, um container simulando um servidor e com o bootstrap já realizado. Vamos agora começar o processo de criação de um cookbook.


## 1. Criando um cookbook

Para realizar a criação de nosso primeiro `cookbook`, deveremos acessar o `chef-server` e no diretório `chef-repo` executar o seguinte comando:

    # chef generate cookbook cookbooks/motd

>Alguns erros serão gerados, pois não realizamos as configurações globais de usuário do Git. Em nosso laboratório, podemos ignorar estes erros.

Após criar o `cookbook`, o próximo passo é editar a nossa receita. Para isto, o primeiro passo é acessar o diretório `cookbooks` em nosso chef-repo. Utilize o comando:

    # cd cookbooks/motd

Explore o conteúdo deste diretório, afim de entender como funciona a estrutura padrão dos cookbooks do chef. Você pode inclusive explorar o conteúdo dos arquivos utilizando o comando `cat`.

Mais informações sobre a estrutura dos cookbooks podem ser encontrados [neste link](https://docs.chef.io/cookbooks.html).


## 2. Editando a receita default

Vamos editar nosso cookbook para que possamos executar algo em nosso novo node. Neste caso, vamos criar uma receita simples, que cria um arquivo chamado `motd.txt` no diretório `/tmp` de nosso container.

Para isto, edite o conteúdo do arquivo `recipes/default.rb` e insira o seguinte:

    file '/tmp/motd.txt' do
      content 'Arquivo criado utilizando o Chef Server!'
    end

Após realizar a edição, salve o arquivo.

## 3. Enviando o cookbook para o Chef Server

Como vimos anteriormente, todas as receitas são disponibilizadas em nossos nodes através do Chef Server. Sendo assim, devemos enviar a receita recém criada para o servidor. Para isto, vamos executar os seguintes comandos:

    # knife upload cookbooks/motd

> NOTA: Este arquivo deverá ser executado a partir do diretório `chef-repo`.

Após realizar o upload de seu cookbook, você poderá verificar o conteúdo do mesmo através da interface web, clicando em `Policy` na parte superior da tela, selecionando seu cookbook chamado `motd` e em seguida, na parte inferior da tela, selecionando `Content`, `Recipes` e em seguida `default.rb`, conforme a imagem abaixo:

![cookbook list](/05-CriandoCookbooks/images/cookbook_list.png)


Você também conseguirá listar os cookbooks presentes no servidor através do seguinte comando, executado no diretório `chef-repo` dentro do `Chef Server`:

    # knife cookbook list

## 4. Alterando o Run List do Node

Como nosso cookbook já está presente em nosso servidor, o próximo passo é editar o `Run list` para que o Chef Client instalado em nosso container possa realizar o download e a execução de nossa receita.

Para isto, na interface web do servidor, vamos clicar em `Nodes` na parte superior da tela, selecionar o nosso node chamado `motd_server` e clicar no campo `Edit Run List` em actions:

![node_run_list_1](/05-CriandoCookbooks/images/node_run_list_1.png)

Em seguida, devemos arrastar a receita `motd` presente em `Available Recipes` para o campo `Current Run List`:

![node_run_list_2](/05-CriandoCookbooks/images/node_run_list_2.png)

Em seguida, vamos clicar em `Save Run List`.

Você pode validar a nova Run List de seu node através do comando:

    # knife node show motd-server

Verifique se a receita `motd` está sendo exibida, conforme abaixo:

    Node Name:   motd-server
    Environment: default
    FQDN:        a32d2fdfc79e
    IP:          
    Run List:    recipe[motd]
    Roles:       
    Recipes:     
    Platform:    ubuntu 16.04
    Tags:        

## 5. Executando nossa receita

O próximo passo para isto, é executar a nossa Run List no node `motd-server` para que a receita `motd` possa criar o arquivo no diretório `/tmp`.

Vamos então acessar o nosso `Chef Client` e executar o seguinte comando:

    $ sudo docker exec -ti motd-server /bin/bash

> NOTE: Este comando irá nos mover para dentro do container `motd-server` que está simulando um servidor em nosso ambiente. A partir daí, todos os comandos são executados dentro do container, e não mais no Chef Client.

Vamos agora utilizar o chef-client que foi instalado em nosso container durante o processo de bootstrap. Para isto, utilizamos o comando:

    # chef-client

Note que o `chef-client` irá realizar o download de seu cookbook e executar os passos localmente. O output deste comando deverá ser semelhante ao seguinte:

    Starting Chef Client, version 13.8.5
    [2018-04-08T17:17:03+00:00] WARN: Plugin Network: unable to detect ipaddress
    resolving cookbooks for run list: ["motd"]
    Synchronizing Cookbooks:
      - motd (0.1.0)
    Installing Cookbook Gems:
    Compiling Cookbooks...
    Converging 1 resources
    Recipe: motd::default
      * file[/tmp/motd.txt] action create
        - create new file /tmp/motd.txt
        - update content in file /tmp/motd.txt from none to 38ac4a
        --- /tmp/motd.txt	2018-04-08 17:17:03.733992469 +0000
        +++ /tmp/.chef-motd20180408-315-1d1f2xo.txt	2018-04-08 17:17:03.733992469 +0000
        @@ -1 +1,2 @@
        +Arquivo criado utilizando o Chef Server!

    Running handlers:
    Running handlers complete
    Chef Client finished, 1/1 resources updated in 01 seconds

Para validar o funcionamento da execução desta receita, utilize o seguinte comando para listar o conteúdo do diretório `/tmp` dentro do container:

    # ls /tmp/

Note que existirá um arquivo chamado `motd.txt`. Liste o conteúdo deste arquivo através do seguinte comando:

    # cat /tmp/motd.txt;echo

Após executar estes comandos e validar o funcinamento de sua receita, utilize o comando `exit` para sair do Container e voltar para o terminal do Chef Client.

## 6. Verificando os relatórios

Outra forma de verificar o status de nossa execução é utilizando a ferramenta de `Reporting` do Chef Server. Na interface web do Chef Server, clique em `Reports` na parte superior da tela, e verifique o status de suas execuções:

![reporting](/05-CriandoCookbooks/images/reporting.png)

## 14. Criando um novo Node para instalação do Apache

Vamos criar um novo Node para que possamos simular a instalação do Apache. Para isto, vamos precisar gerar um novo container, expondo além da porta 22 para o ssh, também a porta 80 para o nosso web server.

Para isto, vamos voltar à nossa instância `Chef Client` e editar o nosso `Dockerfile`. Vamos editar a linha que contém a intrução `EXPOSE` adicionando também a porta `80`. Esta linha deverá ficar da seguinte forma:

    EXPOSE 22 80

Agora, vamos gerar uma nova imagem a partir de nosso Dockerfile. Esta imagem irá se chamar `ssh_apache_image`. Para isto, utilizaremos o seguinte comando:

    # docker build -t ssh_apache_image .

Após a geração desta imagem, vamos executar um novo container, expondo também a porta 80. Utilize o comando:

    # docker run -d -p 80:80 -P --name apache_server ssh_apache_image

Vamos agora listar as portas utilizadas em nosso novo container utilizando o comando:

    # docker port apache_server 22 | awk '{split($0,a,":"); print a[2]}'

Novamente, anote a porta apresentada para que possamos utilizar no processo de bootstrap.

Após a criação de uma nova imagem e a execução do container, realize o bootstrap deste novo node através do knife, em seu Chef Server. Execute os mesmos passos descritos no [passo 7](https://github.com/bemer/23ati#7-bootstrap-de-nosso-novo-node).

## 15. Criação de um novo Cookbook

Agora, ainda em seu Chef Server, realize a criação de um novo Cookbook, desta vez chamado `apache`. Utilize o comando:

    # chef generate cookbook cookbooks/apache

Edite o arquivo `cookbooks/apache/recipes/default.rb` e insira o seguinte conteúdo:

    package 'apache2' do
      action :install
    end

    service 'apache2' do
      action [:enable,:start]
      supports :reload => true
    end

Note que agora, além de instalar o apache, estamos utilizando a receita também para habilitar e iniciar o serviço `apache2`.

Envie seu novo cookbook para o servidor através do comando:

    # knife upload cookbooks/apache

## 16. Editando o Run List do Novo node

Siga o mesmo procedimento descrito no [passo 11](https://github.com/bemer/23ati#11-alterando-o-run-list-do-node), porém desta vez para o novo node utilizando a receita `apache`:

![apache_run_list](https://github.com/bemer/23ati/blob/master/images/apache_run_list.png)

## 17. Executando a o Run List no novo node

Em sua instância `Chef Client`, execute o seguinte comando para ter acesso ao container onde será instalado o Apache:

    # docker exec -ti apache_server /bin/bash

E em seguida, já dentro do container, execute o comando:

    # chef-client

Note que o Apache será instalado.

A primeira maneira para validar o funcionamento do Apache em nosso novo node, é verificando se o serviço foi devidamente instalado e está ativo. Faça isto através do comando:

    # service apache2 status

A saída deste comando deverá ser:

    \* apache2 is running

## 18. Acessando através de seu browser

Como estamos realizando a instalação de um servidor web, nada melhor do que utilizarmos o nosso browser para validação do funcionamento. Desta maneira, o primeiro passo é a liberação das regras do `Security Group` para que possamos ter acesso à porta utilizada pelo container.

Para isto, vamos abrir a console da AWS e lá, acessar o serviço `EC2`, clicar em `Security Groups` no menu lateral esquerdo e selecionar o Security Group `chef-client` criado anteriormente:

![security_group_1](https://github.com/bemer/23ati/blob/master/images/security_group_1.png)

Clique então na aba `Inbound` e então em `Edit`. Na próxima tela, clique em `Add Rule` e adicione uma regra com as seguintes informações:

* Type: Custom HTTP
* Source: Anywhere

E em seguida clique em `Save`:

![security_group_2](https://github.com/bemer/23ati/blob/master/images/security_group_2.png)

Agora, obtenha o hostname de sua instância chamada `Chef Client` e acesse o mesmo utilizando seu Browser. Você deverá se deparar com a interface do Apache Server.
