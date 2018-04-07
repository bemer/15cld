# Instalação do Chef Client

## 01. Obtendo o endereço IP do servidor

Neste laboratório, vamos instalar o chef-client em uma máquina virtual e vamos utilizar o mesmo para executar algumas receitas que vão criar arquivos e instalar pacotes em nosso servidor.

Para instalar o chef-client, inicie a máquina virtual com o nome `chef-client`. Ao acessar a máquina virtual, realize acesso à mesma utilizando as seguintes credenciais:

    Usuário: chef-admin
    Senha: chefclient

O próximo passo é obter o endereço IP de nossa máquina virtual para que possamos realizar acesso remoto através do `putty`. Vamos utilizar o putty para facilitar a interação entre nossos computadores e as máquinas virtuais.

Ao acessar nossa máquina virtual, vamos executar o seguinte comando para obter o endereço IP da mesma:

    $ ifconfig

A saída deste comando deverá ser semelhante a:

    docker0   Link encap:Ethernet  HWaddr 02:42:6a:16:c6:38  
              inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
              UP BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

    enp0s3    Link encap:Ethernet  HWaddr 08:00:27:eb:75:ab  
              inet addr:192.168.0.14  Bcast:192.168.0.255  Mask:255.255.255.0
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:1071 errors:0 dropped:0 overruns:0 frame:0
              TX packets:382 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000
              RX bytes:349036 (349.0 KB)  TX bytes:42585 (42.5 KB)

    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:160 errors:0 dropped:0 overruns:0 frame:0
              TX packets:160 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1
              RX bytes:11840 (11.8 KB)  TX bytes:11840 (11.8 KB)

Note que esta máquina virtual possui 3 interfaces de rede: uma dedicada ao Docker, que já está instalado, uma interface de loopback e uma interface denominada `enmp0s3`. Esta é a interface que iremos utilizar. Note que o endereço IP desta interface é apresentado na seção `inet addr`. No exemplo acima, o endereço a ser utilizado é `192.168.0.14`. Anote este endereço, pois precisaremos dele no próximo passo.


## 02. Configurando o Putty

Agora que já sabemos o endereço IP de nosso servidor, vamos configurar o Putty para realizar acesso remoto ao mesmo. Abra o Putty em seu computador e no campo `Host Name (or IP Address)` insira o endereço IP do servidor após `chef-admin@`. No campo `Saved Sessions` insira o nome `chef-client` e em seguida clique em `Save` no lado direito. Isto irá permitir um acesso mais facilitado ao servidor durante os próximos laboratórios:

![putty screen](/02-ChefClient/images/putty_screen.png)

>Note que o endereço IP de seu servidor poderá ser alterado durante as aulas. Realize este processo todas as vezes que criar uma nova máquina virtual no seu ambiente.

Feito isto, clique em `Open` na parte inferior da tela. O Putty irá então abrir uma nova conexão ao seu servidor chamado `chef-client` e irá solicitar uma senha. Insira a senha do usuário `chef-admin` e pressione enter. Você estará conectado ao seu servidor.

A partir daqui, todos os comandos deverão ser executados diretamente na janela do Putty.

## 03. Instalando o chef-client

O próximo passo em nosso laboratório, é realizar a instalação do `chef-client`. O processo para instalação do chef-client é extremamente simples, sendo basicamente realizado pelos comandos:

    $ wget https://omnitruck.chef.io/install.sh
    $ chmod 700 ./install.sh
    $ sudo ./install.sh

>Durante o processo de instalação, será requisitada a senha do usuário chef-admin. Insira a mesma senha utilizada para se logar no servidor.

Para validar o processo de instalação do chef-client, execute o seguinte comando:

    $ chef-client --version

A saída do comando deverá ser semelhante a:

    Chef: 14.0.190

## 04. Criando uma receita

Agora que temos o chef-client instalado, vamos criar uma receita simples, para ser executada pelo mesmo. Esta receita irá basicamente criar um arquivo chamado `motd` dentro do diretório `/tmp` de nosso servidor.

Crie um arquivo chamado `motd.rb` através do comando:

    $ touch motd.rb

Utilizando o `vim` (ou algum outro editor de texto de sua preferência) insira o seguinte conteúdo ao arquivo `motd.rb`:

    file '/tmp/motd' do
      content 'Olá mundo'
    end

Caso esteja utilizando o `vim`, execute os seguintes comandos:

    vim motd.rb

* Aperte a tecla `i` no teclado, afim de entrar no modo de inserção;
* Cole o conteúdo desejado no arquivo;
* Para sair do editor de texto, pressione a tecla `esc` em seu teclado e em seguida digite `:wq` e pressione enter.

Note que agora estamos interagindo com um script escrito em `Ruby`. O Ruby é uma linguagem de programação orientada a objetos criada por Yukihiro “Matz” Matsumoto, e é a linguagem utilizada pelo Chef para a criação das receitas.

> Você pode aprender mais sobre o Ruby [neste link](https://www.ruby-lang.org/pt/).

## 05. Executando a receita

Agora que já instalamos o chef-client e criamos nossa primeira receita, chegou a hora de executá-la. Para isto, devemos utilizar o seguinte comando:

    $ chef-client --local-mode motd.rb

A saída deste comando deverá ser semelhante a:

    [2018-04-07T14:25:43-03:00] WARN: No config file found or specified on command line, using command line options.
    [2018-04-07T14:25:43-03:00] WARN: No cookbooks directory found at or above current directory.  Assuming /home/chef-admin.
    Starting Chef Client, version 14.0.190
    resolving cookbooks for run list: []
    Synchronizing Cookbooks:
    Installing Cookbook Gems:
    Compiling Cookbooks...
    [2018-04-07T14:25:45-03:00] WARN: Node chef-client has an empty run list.
    Converging 1 resources
    Recipe: @recipe_files::/home/chef-admin/motd.rb
      * file[/tmp/motd] action create
        - create new file /tmp/motd
        - update content in file /tmp/motd from none to 44dae5
        --- /tmp/motd	2018-04-07 14:25:45.893352458 -0300
        +++ /tmp/.chef-motd20180407-3516-1lxq05i	2018-04-07 14:25:45.893352458 -0300
        @@ -1 +1,2 @@
        +Olá mundo

    Running handlers:
    Running handlers complete
    Chef Client finished, 1/1 resources updated in 02 seconds

A partir deste momento, deverá existir um arquivo chamado `motd` no diretório `/tmp` com o conteúdo `Olá mundo`. Para validarmos a execução de nossa receita, vamos utilizar o comando:

    $ cat /tmp/motd;echo

A saída deverá ser:

    Olá mundo

## 05. Instalando um programa com o Chef

Agora que já temos uma receita responsável por criar um arquivo e editar seu conteúdo, vamos passar para uma atividade um pouco mais complexa: instalar o Apache Server em nosso host.

Para isto, vamos criar uma nova receita, chamada `apache_server.rb`. Crie um novo arquivo com este nome e insira o seguinte conteúdo:

    package 'apache2' do
      action :install
    end

    service 'apache2' do
      action [:enable, :start]
      supports :reload => true
    end

No entanto, antes de utilizarmos o chef-client para executar esta receita, vamos nos atentar ao que estamos de fato realizando.

A primeira parte do arquivo, identificada por `package` está passando como parâmetro o pacote `apache2`, com a ação de instalar. Logo em seguida, começamos a interagir com o `serviço`. Neste caso, estamos utilizando as ações para habilitar o serviço e em seguida iniciá-lo. Utilizando o parâmetro `supports` estamos dizendo para o Chef que este serviço suporta o comando de `reload`.

Agora que entendemos o que nossa receita está fazendo, vamos executá-la através do chef-client.

Para a execução desta receita, devemos utilizar o seguinte comando:

    $ sudo chef-client --local-mode apache_server.rb

>Esta receita realiza a instalação de pacotes em nosso sistema operacional, portanto precisaremos utilizar permissões `root` para esta tarefa.

A saída do comando deverá ser semelhante a:

    [2018-04-07T15:12:42-03:00] WARN: No config file found or specified on command line, using command line options.
    [2018-04-07T15:12:42-03:00] WARN: No cookbooks directory found at or above current directory.  Assuming /home/chef-admin.
    Starting Chef Client, version 14.0.190
    resolving cookbooks for run list: []
    Synchronizing Cookbooks:
    Installing Cookbook Gems:
    Compiling Cookbooks...
    [2018-04-07T15:12:43-03:00] WARN: Node chef-client has an empty run list.
    Converging 2 resources
    Recipe: @recipe_files::/home/chef-admin/apache_server.rb
      * apt_package[apache2] action install
        - install version 2.4.18-2ubuntu3.5 of package apache2
      * service[apache2] action enable (up to date)
      * service[apache2] action start (up to date)

    Running handlers:
    Running handlers complete
    Chef Client finished, 1/3 resources updated in 07 seconds

Vamos agora validar o funcionamento de nossa receita.

O primeiro passo é validar se o serviço `apache2` foi devidamente instalado e habilitado. Para isto, vamos utilizar o comando:

    $ service apache2 status

Para sairmos da tela de status deste serviço, basta pressionarmos a tecla `q` em nosso teclado.

Agora, vamos verificar se o servidor está realmente disponível e funcionando devidamente. Para isto, vamos abrir uma nova aba em nosso browser e navegar até o endereço IP de nosso servidor. A página do Apache Server deverá ser exibida:

![apache server](/02-ChefClient/images/apache_server.png)
