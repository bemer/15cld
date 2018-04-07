# Instalação do Chef Server

O Chef Server é um componente que age como um "hub" para os dados de configuração, sendo responsável por armazenar os cookbooks e suas receitas, as políticas que deverão ser aplicadas a cada um dos servidores em nossa infra estrutura e também os metadados que descrevem cada um destes servidores.

Neste tutorial, vamos realizar o processo de instalação do Chef Server em nosso servidor.

## 1. Instalando o Chef Server

Para realizarmos a instalação do Chef Server, vamos utilizar a máquina virtual com o nome `chef-server`. Inicie a máquina virtual em seu computador e siga os passos descritos no tutorial anterior, nos capítulos [1. Obtendo o endereço IP do servidor](/02-ChefClient#01-obtendo-o-endereço-ip-do-servidor) e [2. Configurando o Putty](/02-ChefClient#02-configurando-o-putty) para realizar acesso ao novo servidor.

>Ao salvar a conexão no Putty, utilize desta vez o nome `chef-server`.
