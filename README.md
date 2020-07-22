# Mapas Culturais Minas Gerais

Este repositório irá armazenar as configurações necessárias para efetuas a instalaçao passo-a-passo da aplicação mapasculturais tendo como base a seguinte documentação http://docs.mapasculturais.org/mc_deploy/, em Minas Gerais tivemos que fazer algumas pequenas mudanças do tutorial original para que fosse possível a sua instalaçao.

# 1. Configuração da máquina ( EC2 amazon )

 * Ubuntu Server 18.04 LTS
 * vCPUS 1
 * 1GB RAM
 * SSD 30GB

<i>Essa configuração de máquina foi criada apenas para efetuarmos os testes iniciais, o procedimento será o mesmo em sua máquina de produção, para ter acesso aos requisitos mínimos acesse [Este Link](https://github.com/mapasculturais/mapasculturais#hardware-requisitos-para-instala%C3%A7%C3%A3o)</i>

# 2. Softwares Requeridos

  ### Atualize os repositórios de referência de sua máquina:
  
  ```
  ubuntu@server# sudo apt-get update 
  ```
  ```
  ubuntu@server# sudo apt-get upgrade
  ```
  
  ### Instale as dependências diversas:
  
  ```
  ubuntu@server# sudo apt-get install git curl npm ruby2.5 ruby2.5-dev
  ```
  
  ###  Atualizar referências para a versão de ruby 2.5:
  
  ```
  ubuntu@server# sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby2.5 10
  ```
  ```
  ubuntu@server# sudo update-alternatives --install /usr/bin/gem gem /usr/bin/gem2.5 10
  ```
  
  ### Instale a versão stable mais nova do nodejs:
  
  ```
  ubuntu@server# sudo curl -sL https://deb.nodesource.com/setup_12.x -o nodesource_setup.sh
  ```
  ```
  ubuntu@server# sudo bash nodesource_setup.sh
  ```
  ```
  ubuntu@server# sudo apt install nodejs
  ```

  ### Verificando se foi instalada a versão mais recente do NodeJS e do NPM:
  
  ```
  ubuntu@server# nodejs -v
  ~ v12.18.3
  ```

  ```
  ubuntu@server# npm -v
  ~ 6.14.6
  ```
  
  ###  Instale o postgresql e postgis: 
  
  ```
  ubuntu@server# apt-get install postgresql-10 postgresql-contrib postgis postgresql-10-postgis-2.4 postgresql-10-postgis-2.4-scripts
  ```
  
  ###  Instale o php7.2, php7.2-fpm e extensões do php utilizadas no sistema
  
  ``` 
  ubuntu@server# sudo apt-get install php7.2 php7.2-gd php7.2-cli php7.2-json php7.2-curl php7.2-pgsql php-apcu php7.2-fpm imagemagick libmagickcore-dev libmagickwand-dev php7.2-imagick 
  ```
  ### Instale o nginx
  
  ```
  ubuntu@server# sudo apt-get install nginx
  ```
  
  ### Instale o gerenciador de dependências do PHP Composer
  
  ```
  ubuntu@server# curl -sS https://getcomposer.org/installer | php
  ```
  ```
  ubuntu@server# sudo mv composer.phar /usr/local/bin/composer.phar
  ```
  
  ### Também é importante ter o pacote zip instalado no seu servidor. Ele é usado para gerar o pacote com os anexos enviados nas inscrições. Caso ainda não tenha:
  
  ```
  sudo apt-get install zip unzip
  ```
  
  ### No Ubuntu o executável do NodeJS se chama nodejs, porém para o correto funcionamento das bibliotecas utilizadas, o executável deve se chamar node. Para isto criamos um link simbólico com o comando abaixo
  
  ```
  ubuntu@server# sudo update-alternatives --install /usr/bin/node node /usr/bin/nodejs 10
  ```
  ### Instalando os minificadores de código Javascript e CSS: uglify-js, uglifycss e autoprefixer
  
  ```
  ubuntu@server# sudo npm install -g uglify-js2 uglifycss autoprefixer
  ubuntu@server# sudo update-alternatives --install /usr/bin/uglifyjs uglifyjs /usr/bin/uglifyjs2 10
  ```
  
  ### Instalando o SASS, utilizado para compilar os arquivos CSS
  
  ```
  ubuntu@server# sudo gem install sass -v 3.4.22
  ```
_Obs.: Tivemos várias mudanças no método de instalação, caso queira você poderá consultar a documentação que foi utilizada como base (clicando aqui)[http://docs.mapasculturais.org/mc_deploy/]_
