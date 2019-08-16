.. installation:

Software Installation
=====================

PostgreSQL
----------

  Go to https://www.postgresql.org/download/, download and install the PostgreSQL 9.5 release for your operating system.

  Run the installer file

  Click the *Next>* button to proceed with the installation.

.. figure:: ./installation/install_postgresql_01.png
  
  Select the directory where the program **PostgreSQL** will be installed and then click the *Next>* button
  
.. figure:: ./installation/install_postgresql_02.png
  
  Select the directory where the database data and the database files system setup will be installed and press the *Next>* button
  
.. figure:: ./installation/install_postgresql_03.png
  
--------------

.. Note:: - If possible, install the program files on the drive ``C:\`` and the database on the drive ``D:\``.
          - Use a **hard drive** of type ``SSD`` where the database will be stored.

--------------

  Set the user password ``postgres`` for **postgres** and click the *Next>* button
  
.. figure:: ./installation/install_postgresql_04.png
  
  Set the default port ``5432`` and click the *Next>* button

.. figure:: ./installation/install_postgresql_05.png

  Select *locale* with ``C`` and then click the *Next>* button

.. figure:: ./installation/install_postgresql_06.png
  
  Click the *Next>* Button to Begin PostgreSQL Installation

.. figure:: ./installation//install_postgresql_07.png
 
  Uncheck the option to run Stack Builder and click the *Finish* button to finish PostgreSQL installation.

.. figure:: ./installation/install_postgresql_08.png

--------------

.. note:: - If you want to install the latest version of PostGIS, check the option to run *stack builder* and proceed with installation by checking the option for spatial extent. In our case, we will install a specific version of PostGIS, so uncheck this option.

--------------

PostGIS
-------

  Go to http://postgis.net/install/, download and install the PostGIS 2.3 version for PostgreSQL 9.5 for your operating system.

  Run the installer file

  Click the *I Agree* button to proceed with the installation.

.. figure:: ./installation/install_postgis_01.png

  Leave *PostGIS* checked and click the *Next>* button

.. figure:: ./installation/install_postgis_02.png
   
  Enter the directory where PostgreSQL is installed and click the *Next>* button.

.. figure:: ./installation/install_postgis_03.png
   
  Click the *Yes* button to proceed with the installation.

.. figure:: ./installation/install_postgis_04.png
   
  Click the *Yes* button to proceed with the installation.

.. figure:: ./installation/install_postgis_05.png
  
  Click the *Yes* button to proceed with the installation.

.. figure:: ./installation/install_postgis_06.png
   
  Click the *Close* button to finish the installation.

.. figure:: ./installation/install_postgis_07.png
   
QGIS
----

  Go to https://www.qgis.org/en/site/index.html, download and install version 3.4 for your operating system.

  Run the installer file

  Click the *Next* button to proceed with the installation.

.. figure:: ./installation/install_qgis_01.png

  Click the *I Agree* button

.. figure:: ./installation/install_qgis_02.png
   
  Enter the directory where QGIS will be installed and click the *Next>* button

.. figure:: ./installation/install_qgis_03.png
   
  Leave only *QGIS* checked and Click *Install* button to begin installation

.. figure:: ./installation/install_qgis_04.png
   
  Click the *Finish* button to complete the installation.

.. figure:: ./installation/install_qgis_05.png
   
--------------

.. note:: - You can install more than one version of QGIS on your computer without having to remove the previous installation.

--------------
