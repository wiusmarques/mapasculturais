# Mapas Culturais Minas Gerais

Este repositório irá armazenar as configurações necessárias para efetuas a instalaçao passo-a-passo da aplicação mapasculturais tendo como base a seguinte documentação http://docs.mapasculturais.org/mc_deploy/, em Minas Gerais tivemos que fazer algumas pequenas mudanças do tutorial original para que fosse possível a sua instalaçao.

# 1. Configuração da máquina ( EC2 amazon )

 * Ubuntu Server 18.04 LTS
 * vCPUS 1
 * 1GB RAM
 * SSD 30GB

<i>Essa configuração de máquina foi criada apenas para efetuarmos os testes iniciais, o procedimento será o mesmo em sua máquina de produção, para ter acesso aos requisitos mínimos acesse: https://github.com/mapasculturais/mapasculturais#hardware-requisitos-para-instala%C3%A7%C3%A3o</i>

# 2. Softwares Requeridos

  Atualize os repositórios de referência de sua máquina:
  
  ``` ubuntu@server# sudo apt-get update ```</br>
  ``` ubuntu@server# sudo apt-get upgrade ```</br>
  
  Instale as dependências diversas:
  
  ``` ubuntu@server# sudo apt-get install git curl npm ruby2.5 ruby2.5-dev ``` </br>
  
  Atualizar referências para a versão de ruby 2.5:
  
  ``` ubuntu@server# sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby2.5 10 ``` </br>
  ``` ubuntu@server# sudo update-alternatives --install /usr/bin/gem gem /usr/bin/gem2.5 10 ``` </br>
  
  Instale a versão stable mais nova do nodejs:
  
  ```ubuntu@server# sudo curl -sL https://deb.nodesource.com/setup_12.x -o nodesource_setup.sh ``` </br>
  ```ubuntu@server# sudo bash nodesource_setup.sh  ``` </br>
  ```ubuntu@server# sudo apt install nodejs ``` </br>
  
  ```ubuntu@server# nodejs -v ``` //v12.18.3 </br>
  ```ubuntu@server# npm -v ``` //6.14.6 </br>
  
  Instale o postgresql e postgis: 
  
  ``` root@server# apt-get install postgresql-10 postgresql-contrib postgis postgresql-10-postgis-2.4 postgresql-10-postgis-2.4-scripts ```
  
  
