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
  
# 4. Banco de Dados

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
# 5. Configurações de instalação
  
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
  
# 6. Configuração do nginx
  
  #### Precisamos criar o virtual host do nginx para a aplicação. Para isto crie, como root, o arquivo /etc/nginx/sites-available/mapas.conf
  
  ```
  mapas@server$ exit
  ubuntu@server# sudo vi /etc/nginx/sites-available/mapas.conf
  ```
  
  _Eu costumo utilizar o vi, mas você pode usar o vim, nano e afins para fazer a edição deste arquivo_
  
  #### Coloque o conteúdo abaixo dentro do arquivo /etc/nginx/sites-available/mapas.conf criado anteriormente: Muita atenção aqui, pois você precisará subistituir todos os "meu.dominio.gov.br" pelo seu domínio ou IP fixo dependendo de qual for o seu caso.
  
  ```
  server {
    set $site_name meu.dominio.gov.br;

    listen *:80;
    server_name meu.dominio.gov.br;
    access_log   /var/log/mapasculturais/nginx.access.log;
    error_log    /var/log/mapasculturais/nginx.error.log;

    index index.php;
    root  /srv/mapas/mapasculturais/src/;

    location / {
      try_files $uri $uri/ /index.php?$args;
    }
    
    location ~ /files/.*\.php$ {
      return 80;
    }
    

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|woff)$ {
            expires 1w;
            log_not_found off;
    }

    location ~ \.php$ {
      try_files $uri =404;
      include fastcgi_params;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_pass unix:/var/run/php/php7.2-fpm-$site_name.sock;
      client_max_body_size 0;
    }

    charset utf-8;
  }

  server {
    listen *:80;
    server_name www.meu.dominio.gov.br;
    return 301 $scheme://meu.dominio.gov.br$request_uri;
  }
  ```
  
  #### Feita a alteração no arquivo, salve!
  
  #### Crie o link para habilitar o virtual host
  
  ```
  ubuntu@server# sudo ln -s /etc/nginx/sites-available/mapas.conf /etc/nginx/sites-enabled/mapas.conf
  ```
  
  #### Remover o arquivo default da pasta /etc/nginx/sites-available/ e /etc/nginx/sites-enabled/
  
  ```
  ubuntu@server# sudo rm /etc/nginx/sites-available/default
  sudo rm /etc/nginx/sites-enabled/default
  ```
  
  #### Configurações pool do php-fpm: Crie o arquivo /etc/php/7.2/fpm/pool.d/mapas.conf com o conteúdo abaixo: Muita atenção aqui, pois você precisará subistituir "meu.dominio.gov.br" pelo seu domínio ou IP fixo dependendo de qual for o seu caso.
  
  ```
  ubuntu@server# sudo vi /etc/php/7.2/fpm/pool.d/mapas.conf
  ```
  
  _Eu costumo utilizar o vi, mas você pode usar o vim, nano e afins para fazer a edição deste arquivo_
  
  ```
  [mapas]
listen = /var/run/php/php7.2-fpm-meu.dominio.gov.br.sock
listen.owner = mapas
listen.group = www-data 
user = mapas
group = www-data
catch_workers_output = yes
pm = dynamic
pm.max_children = 10
pm.start_servers = 1
pm.min_spare_servers = 1
pm.max_spare_servers = 3
pm.max_requests = 500
chdir = /srv/mapas
; php_admin_value[open_basedir] = /srv/mapas:/tmp
php_admin_value[session.save_path] = /tmp/
; php_admin_value[error_log] = /var/log/mapasculturais/php.error.log
; php_admin_flag[log_errors] = on
php_admin_value[display_errors] = 'stderr'
  ```
  
# 7. Concluindo
  
  #### Para finalizar, precisamos popular o banco de dados com os dados iniciais e executar um script que entre outras coisas compila e minifica os assets, otimiza o autoload de classes do composer e roda atualizações do banco.
  
  
  ```
  ubuntu@server# sudo su - mapas
  mapas@server$ psql -f mapasculturais/db/initial-data.sql
  mapas@server$ ./mapasculturais/scripts/deploy.sh
  ```
  
  #### Reinicie os serviços do nginx e php7.2-fpm
  
  
  ```
  ubuntu@server# sudo service nginx restart
  ubuntu@server# sudo service php7.2-fpm restart
  ```
 
_A partir deste momento sua aplicação estará disponível para acesso utilizando a Autenticação Fake_
