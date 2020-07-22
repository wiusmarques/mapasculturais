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
  
  ``` root@server# apt-get update ```
  ``` root@server# apt-get upgrade ```
  
  Instale as dependências diversas:
  
  ``` root@server# apt-get install git curl npm ruby2.5 ruby2.5-dev ```
