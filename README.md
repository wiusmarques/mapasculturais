# Mapas Culturais Minas Gerais

Este repositório irá armazenar as configurações necessárias para efetuas a instalaçao passo-a-passo da aplicação mapasculturais tendo como base esta [documentação](http://docs.mapasculturais.org/mc_deploy/), em Minas Gerais tivemos que fazer algumas mudanças do tutorial original para que fosse possível a sua instalaçao.

# 1. Configuração da máquina ( EC2 amazon )

 * Ubuntu Server 18.04 LTS
 * vCPUS 1
 * 1GB RAM
 * SSD 30GB

_Essa máquina foi criada apenas para efetuarmos testes iniciais e fazer a primeira instalação, o procedimento será o mesmo em sua máquina de produção, para ter acesso aos requisitos mínimos acesse para rodar em produção acesse [este Link](https://github.com/mapasculturais/mapasculturais#hardware-requisitos-para-instala%C3%A7%C3%A3o)_

# 2. Softwares Requeridos

  #### Atualize os repositórios de referência de sua máquina:
  
  ```
  ubuntu@server# sudo apt-get update 
  ```
  ```
  ubuntu@server# sudo apt-get upgrade
  ```
  
  #### Instale as dependências diversas:
  
  ```
  ubuntu@server# sudo apt-get install git curl npm ruby2.5 ruby2.5-dev
  ```
  
  ####  Atualizar referências para a versão de ruby 2.5:
  
  ```
  ubuntu@server# sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby2.5 10
  ```
  ```
  ubuntu@server# sudo update-alternatives --install /usr/bin/gem gem /usr/bin/gem2.5 10
  ```
  
  #### Instale a versão stable mais nova do nodejs:
  
  ```
  ubuntu@server# sudo curl -sL https://deb.nodesource.com/setup_12.x -o nodesource_setup.sh
  ```
  ```
  ubuntu@server# sudo bash nodesource_setup.sh
  ```
  ```
  ubuntu@server# sudo apt install nodejs
  ```

  #### Verificando se foi instalada a versão mais recente do NodeJS e do NPM:
  
  ```
  ubuntu@server# nodejs -v
  ~ v12.18.3
  ```

  ```
  ubuntu@server# npm -v
  ~ 6.14.6
  ```
  
  ####  Instale o postgresql e postgis: 
  
  ```
  ubuntu@server# apt-get install postgresql-10 postgresql-contrib postgis postgresql-10-postgis-2.4 postgresql-10-postgis-2.4-scripts
  ```
  
  ####  Instale o php7.2, php7.2-fpm e extensões do php utilizadas no sistema
  
  ``` 
  ubuntu@server# sudo apt-get install php7.2 php7.2-gd php7.2-cli php7.2-json php7.2-curl php7.2-pgsql php-apcu php7.2-fpm imagemagick libmagickcore-dev libmagickwand-dev php7.2-imagick
  ```
  
  #### Instale o nginx
  
  ```
  ubuntu@server# sudo apt-get install nginx
  ```
  
  #### Instale o gerenciador de dependências do PHP Composer
  
  ```
  ubuntu@server# curl -sS https://getcomposer.org/installer | php
  ```
  ```
  ubuntu@server# sudo mv composer.phar /usr/local/bin/composer.phar
  ```
  
  #### Também é importante ter o pacote zip instalado no seu servidor. Ele é usado para gerar o pacote com os anexos enviados nas inscrições. Caso ainda não tenha:
  
  ```
  sudo apt-get install zip unzip
  ```
  
  #### No Ubuntu o executável do NodeJS se chama nodejs, porém para o correto funcionamento das bibliotecas utilizadas, o executável deve se chamar node. Para isto criamos um link simbólico com o comando abaixo:
  
  ```
  ubuntu@server# sudo update-alternatives --install /usr/bin/node node /usr/bin/nodejs 10
  ```
  #### Instalando os minificadores de código Javascript e CSS: uglify-js, uglifycss e autoprefixer:
  
  ```
  ubuntu@server# sudo npm install -g uglify-js2 uglifycss autoprefixer
  ubuntu@server# sudo update-alternatives --install /usr/bin/uglifyjs uglifyjs /usr/bin/uglifyjs2 10
  ```
  
  #### Instalando o SASS, utilizado para compilar os arquivos CSS:
  
  ```
  ubuntu@server# sudo gem install sass -v 3.4.22
  ```
# 3. Clonando o Repositório

  #### Primeiro vamos criar o usuário que rodará a aplicação e que será proprietário do banco de dados, definindo sua home para /srv e colocando-o no grupo www-data:
  
  ```
  ubuntu@server# sudo useradd -G www-data -d /srv/mapas -m mapas
  ```
  
  #### Vamos clonar o repositório usando o usuário criando, então precisamos primeiro "logar" com este usuário:
  
  ```
  ubuntu@server# sudo su - mapas
  mapas@server$ git clone https://github.com/hacklabr/mapasculturais.git
  ```
  #### Agora vamos colocar o repositório na branch master. Caso queira você poderá ver todas as versões dosponíveis [aqui](https://github.com/hacklabr/mapasculturais/releases)
  
  ```
  mapas@server$ cd mapasculturais
  mapas@server$ git checkout master
  mapas@server$ git pull origin master
  ```
  
  ####  Instale alguns pacotes que serão necessários para instalação das dependências do próximo passo
  
  ```
  ubuntu@server# sudo apt-get install php7.2-common php7.2-mbstring php7.2-xml php7.2-zip
  ```
  
  #### Agora vamos instalar as dependências de PHP utilizando o Composer.
  
  ```
  mapas@server$ cd ~/mapasculturais/src/protected/
  mapas@server$ composer.phar install
  ```
  
# 3. Banco de Dados

  #### Vamos voltar ao usuário root para criar o banco de dados.
  
  ```
  mapas@server$ exit
  ubntu@server$
  ```
  
  #### Primeiro vamos criar o usuário no banco de dados com o mesmo nome do usuário do sistema
  
  ```
  ubuntu@server# sudo -u postgres psql -c "CREATE USER mapas"
  ```
  
  #### Agora vamos criar a base de dados para a aplicação com o mesmo nome do usuário
  
  ```
  ubuntu@server# sudo -u postgres createdb --owner mapas mapas
  ```
  #### Criar as extensões necessárias no banco
  
  ```
  ubuntu@server# sudo -u postgres psql -d mapas -c "CREATE EXTENSION postgis;"
  ubuntu@server# sudo -u postgres psql -d mapas -c "CREATE EXTENSION unaccent;"
  ```
  
  #### Volte a "logar" com o usuário criado e importar o esquema da base de dados
  
  ```
  ubuntu@server# sudo su - mapas
  mapas@server$ psql -f mapasculturais/db/schema.sql
  ```
# 4. Configurações de instalação
  
  #### Primeiro crie um arquivo de configuração copiando o arquivo de template de configuração. Este arquivo está preparado para funcionar com este guia, utilizando o método de autenticação Fake.
  
  ```
  mapas@server$ cp mapasculturais/src/protected/application/conf/config.template.php mapasculturais/src/protected/application/conf/config.php
  ```
  
  #### Criando diretórios de log, files e estilo
  
  ```
  $ exit
  ubuntu@server# sudo mkdir /var/log/mapasculturais
  ubuntu@server# sudo chown mapas:www-data /var/log/mapasculturais
  ```
  
  #### Com o usuário criado, crie a pasta para os assets, para os uploads e para os uploads privados (arquivos protegidos, como anexos de inscrições em oportunidades):
  
  ```
  ubuntu@server# sudo su - mapas
  mapas@server$ mkdir mapasculturais/src/assets
  mapas@server$ mkdir mapasculturais/src/files
  mapas@server$ mkdir mapasculturais/private-files
  ```
