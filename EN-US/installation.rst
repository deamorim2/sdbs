Instalação dos Softwares
===========================

PostgreSQL
==============

Go to https://www.postgresql.org/download/, download and install the
PostgreSQL 9.5 release for your operating system.

  Run the installer file

  Click the *Next>* button to proceed with the installation.

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_01.png
   
  Select the directory where the program ** PostgreSQL ** will be installed and then click the *Next>* button

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_02.png
   
  Select the directory where the **database data* and the **database files system setup** will be installed and press the *Next button >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_03.png
  


--------------

.. Note:: - Se possível, instale os arquivos de programa no drive ``C:\`` e os dados do banco no drive ``D:\``.
Utilize para o drive onde estarão os dados *hard drive* do tipo SSD.

--------------



    Defina a senha do usuário ``postgres`` como *postgres* e clique no
    botão *Next >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_04.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgresql\_04.png

   
    Defina a porta padrão *5432* e clique no botão *Next >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_05.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgresql\_05.png

  
    Selecione o *locale* com ``C`` e depois clique no botão *Next >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_06.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgresql\_06.png

  
    Clique no botão *Next >* para começar a instalação do PostgreSQL

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_07.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgresql\_07.png


    Desmarque a opção para executar o Stack Builder e clique no botão
    *Finish* para terminar a instalação do PostgreSQL

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgresql_08.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgresql\_08.png

--------------

**Nota:** se você deseja instalar a versão mais atual do PostGIS, deixe
marcado a opção de executar o *stack builder* e prossiga com a
instalação marcando a opção da extensão espacial. No nosso caso, nós
iremos instalar uma versão específica do PostGIS, por isso desmarcamos
essa opção. \*\*\*

3.2 PostGIS
===========

Vá até o site http://postgis.net/install/ , baixe e instale a versão do
PostGIS 2.3 para o PostgreSQL 9.5 para o seu sistema operacional.

    Execute o arquivo de instalação

    Clique no botão *I Agree* para prosseguir com a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_01.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_01.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_01.png
    Deixe marcada a opção *PostGIS* e Clique no botão *Next >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_02.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_02.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_02.png
    Indique o diretório onde está instalado o PostgreSQL e clique no
    botão *Next >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_03.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_03.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_03.png
    Clique no botão *Sim* para prosseguir com a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_04.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_04.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_04.png
    Clique no botão *Sim* para prosseguir com a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_05.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_05.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_05.png
    Clique no botão *Sim* para prosseguir com a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_06.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_06.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_06.png
    Clique no botão *Close* para finalizar a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_postgis_07.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_07.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_postgis\_07.png
3.3 QGIS
========

Vá até o site https://www.qgis.org/en/site/index.html , baixe e instale
a versão 2.18 para o seu sistema operacional

    Execute o arquivo de instalação

    Clique no botão *Próximo* para prosseguir com a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_qgis_01.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_01.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_01.png
    Clique no botão *Eu Concordo*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_qgis_02.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_02.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_02.png
    Indique o diretório onde será instalado o QGIS e clique no botão
    *Próximo >*

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_qgis_03.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_03.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_03.png
    Deixe marcada somente a opção *QGIS* e Clique no botão *Instalar*
    para começar a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_qgis_04.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_04.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_04.png
    Clique no botão *Terminar* para finalizar a instalação

.. figure:: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal_qgis_05.png
   :alt: https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_05.png

   https://github.com/deamorim2/sbde/blob/master/wiki/03/instal\_qgis\_05.png

--------------

**Nota:** você pode instalar mais de uma versão do QGIS no seu
computador, não precisando remover a instalação anterior. \*\*\*
