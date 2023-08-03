.. _projection:

Projecting Data
===============

The earth is not flat, and there is no simple way of putting it down on a flat paper map (or computer screen), so people have come up with all sorts of ingenious solutions, each with pros and cons. Some projections preserve area, so all objects have a relative size to each other; other projections preserve angles (conformal) like the Mercator projection; some projections try to find a good intermediate mix with only little distortion on several parameters. Common to all projections is that they transform the (spherical) world onto a flat Cartesian coordinate system, and which projection to choose depends on how you will be using the data.

We've already encountered projections when we loaded our nyc data. (Recall that pesky SRID 26918).  Sometimes, however, you need to transform and re-project between spatial reference systems. PostGIS includes built-in support for changing the projection of data, using the ST_Transform_ (geometry, srid) function. For managing the spatial reference identifiers on geometries, PostGIS provides the ST_SRID_ (geometry) and ST_SetSRID_ (geometry, srid) functions.

We can confirm the SRID of our data with the ST_SRID_ command:

.. code-block:: sql

  SELECT ST_SRID(geom) FROM nyc_streets LIMIT 1;
  
::

  26918
  
And what is definition of "26918"? As we saw in loading data section, the definition is contained in the ``spatial_ref_sys`` table. In fact, **two** definitions are there. The "well-known text" (WKT_) definition is in the ``srtext`` column, and there is a second definition in "proj.4" format in the ``proj4text`` column.

.. code-block:: sql

   SELECT * FROM spatial_ref_sys WHERE srid = 26918;
   
In fact, for the internal PostGIS re-projection calculations, it is the contents of the ``proj4text`` column that are used. For our 26918 projection, here is the proj.4 text:

.. code-block:: sql

  SELECT proj4text FROM spatial_ref_sys WHERE srid = 26918;
  
::

  +proj=utm +zone=18 +ellps=GRS80 +datum=NAD83 +units=m +no_defs 
  
In practice, both the ``srtext`` and the ``proj4text`` columns are important: the ``srtext`` column is used by external programs like `GeoServer <http://geoserver.org>`_, `uDig <udig.refractions.net>`_, and `FME <http://www.safe.com/>`_  and others; the ``proj4text`` column is used internally.

Comparing Data
--------------

Taken together, a coordinate and an SRID define a location on the globe. Without an SRID, a coordinate is just an abstract notion. A "Cartesian" coordinate plane is defined as a "flat" coordinate system placed on the surface of Earth. Because PostGIS functions work on such a plane, comparison operations require that both geometries be represented in the same SRID.

If you feed in geometries with differing SRIDs you will just get an error:

.. code-block:: sql

  SELECT ST_Equals(
           ST_GeomFromText('POINT(0 0)', 4326),
           ST_GeomFromText('POINT(0 0)', 26918)
           );

::

  ERROR:  Operation on two geometries with different SRIDs
  CONTEXT:  SQL function "st_equals" statement 1
  
-----

.. note:: - Be careful of getting too happy with using ST_Transform_ for on-the-fly conversion. Spatial indexes are built using SRID of the stored geometries.  If comparison are done in a different SRID, spatial indexes are (often) not used. It is best practice to choose **one SRID** for all the tables in your database. Only use the transformation function when you are reading or writing data to external applications.

-----

Transforming Data
-----------------

If we return to our proj4 definition for SRID 26918, we can see that our working projection is UTM (Universal Transverse Mercator) of zone 18, with meters as the unit of measurement.

::

   +proj=utm +zone=18 +ellps=GRS80 +datum=NAD83 +units=m +no_defs 

Let's convert some data from our working projection to geographic coordinates -- also known as "longitude/latitude". 

To convert data from one SRID to another, you must first verify that your geometry has a valid SRID. Since we have already confirmed a valid SRID, we next need the SRID of the projection to transform into. In other words, what is the SRID of geographic coordinates?

The most common SRID for geographic coordinates is 4326, which corresponds to "longitude/latitude on the WGS84 spheroid". You can see the SRID 4326 definition at the `spatialreference.org site <http://spatialreference.org/ref/epsg/4326/>`_.
  
You can also pull the definitions from the ``spatial_ref_sys`` table:

.. code-block:: sql

  SELECT srtext FROM spatial_ref_sys WHERE srid = 4326;
  
::

  GEOGCS["WGS 84",
    DATUM["WGS_1984",
      SPHEROID["WGS 84",6378137,298.257223563,AUTHORITY["EPSG","7030"]],
      AUTHORITY["EPSG","6326"]],
    PRIMEM["Greenwich",0,AUTHORITY["EPSG","8901"]],
    UNIT["degree",0.01745329251994328,AUTHORITY["EPSG","9122"]],
    AUTHORITY["EPSG","4326"]]

Let's convert the coordinates of the 'Broad St' subway station into geographics:

.. code-block:: sql

  SELECT ST_AsText(ST_Transform(geom,4326)) 
  FROM nyc_subway_stations 
  WHERE name = 'Broad St';
  
::

  POINT(-74.0106714688735 40.7071048155841)

If you load data or create a new geometry without specifying an SRID, the SRID value will be 0.  Recall in geometries, that when we created our ``geometries`` table we didn't specify an SRID. If we query our database, we should expect all the ``nyc`` tables to have an SRID of 26918, while  the ``geometries`` table defaulted to an SRID of 0.

To view a table's SRID assignment, query the database's ``geometry_columns`` table.

.. code-block:: sql

  SELECT f_table_name AS name, srid 
  FROM geometry_columns;
  
::

          name         | srid  
  ---------------------+-------
   nyc_census_blocks   | 26918
   nyc_neighborhoods   | 26918
   nyc_streets         | 26918
   nyc_subway_stations | 26918
   geometries          |     0

  
However, if you know what the SRID of the coordinates is supposed to be, you can set it post-facto, using ST_SetSRID_ on the geometry. Then you will be able to transform the geometry into other systems.

.. code-block:: sql

   SELECT ST_AsText(
    ST_Transform(
      ST_SetSRID(geom,26918),
    4326)
   )
   FROM geometries;

Projections in Brazil
---------------------

In Brazil, the official projection is **SIRGAS 2000** or **SRID 4674**.

For **proj4**, this projection is geocentric and the units are in degrees (longlat):

::

  +proj=longlat +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +no_defs

..

In the OGC WKT format, the SRID 4674 has the following parameters:

::

    GEOGCS["SIRGAS 2000",
        DATUM["Sistema_de_Referencia_Geocentrico_para_las_AmericaS_2000",
            SPHEROID["GRS 1980",6378137,298.257222101,
                AUTHORITY["EPSG","7019"]],
            TOWGS84[0,0,0,0,0,0,0],
            AUTHORITY["EPSG","6674"]],
        PRIMEM["Greenwich",0,
            AUTHORITY["EPSG","8901"]],
        UNIT["degree",0.0174532925199433,
            AUTHORITY["EPSG","9122"]],
        AUTHORITY["EPSG","4674"]]

..

Calculating Areas
^^^^^^^^^^^^^^^^^

The Brazilian Institute of Geography and Statistics (IBGE) suggests the `projection parameters <https://geoftp.ibge.gov.br/cartas_e_mapas/bases_cartograficas_continuas/bc250/versao2021/informacoes_tecnicas/bc250_documentacao_tecnica.pdf>`_ below to calculate the area to the products of the Continuing Base in the scale of 1:250.000. 

Albers equal-area conic projection:

* Longitude of center: -54°
* Latitude of center: -12°
* Standard Parallel 1: -2°
* Standard Parallel 2: -22°

Here are these parameters converted in **proj4** format:

::

    +proj=aea +lat_1=-2 +lat_2=-22 +lat_0=-12 +lon_0=-54 +x_0=0 +y_0=0 +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs

..

And now in the **OGC WKT** format:

::

    PROJCS["Brazil_Albers_Equal_Area_Conic",
        GEOGCS["SIRGAS 2000",
            DATUM["Sistema_de_Referencia_Geocentrico_para_las_AmericaS_2000",
                SPHEROID["GRS 1980",6378137,298.257222101,
                    AUTHORITY["EPSG","7019"]],
                TOWGS84[0,0,0,0,0,0,0],
                AUTHORITY["EPSG","6674"]],
            PRIMEM["Greenwich",0,
                AUTHORITY["EPSG","8901"]],
            UNIT["degree",0.0174532925199433,
                AUTHORITY["EPSG","9122"]],
            AUTHORITY["EPSG","4674"]],
        PROJECTION["Albers_Conic_Equal_Area"],
        PARAMETER["False_Easting",0],
        PARAMETER["False_Northing",0],
        PARAMETER["longitude_of_center",-54],
        PARAMETER["Standard_Parallel_1",-2],
        PARAMETER["Standard_Parallel_2",-22],
        PARAMETER["latitude_of_center",-12],
        UNIT["Meter",1],
        AUTHORITY["IBGE","55555"]]

..

Sometimes, you have to use some customized projection. To do this in PostGIS, you have to insert this projection in the postgis ``spatial_ref_sys`` table.

To insert the customized SRID above in the table ``spatial_ref_sys``, execute the SQL instruction below:

.. code-block:: sql


    INSERT into spatial_ref_sys (srid, auth_name, auth_srid, proj4text, srtext)
    values
    (
    55555,
    'IBGE',
    55555,
    '+proj=aea +lat_1=-2 +lat_2=-22 +lat_0=-12 +lon_0=-54 +x_0=0 +y_0=0 +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs ',
    'PROJCS["Brazil_Albers_Equal_Area_Conic",GEOGCS["SIRGAS 2000",DATUM["Sistema_de_Referencia_Geocentrico_para_las_AmericaS_2000",SPHEROID["GRS 1980",6378137,298.257222101,AUTHORITY["EPSG","7019"]],TOWGS84[0,0,0,0,0,0,0],AUTHORITY["EPSG","6674"]],PRIMEM["Greenwich",0,AUTHORITY["EPSG","8901"]],UNIT["degree",0.0174532925199433,AUTHORITY["EPSG","9122"]],AUTHORITY["EPSG","4674"]],PROJECTION["Albers_Conic_Equal_Area"],PARAMETER["False_Easting",0],PARAMETER["False_Northing",0],PARAMETER["longitude_of_center",-54],PARAMETER["Standard_Parallel_1",-2],PARAMETER["Standard_Parallel_2",-22],PARAMETER["latitude_of_center",-12],UNIT["Meter",1],AUTHORITY["IBGE","55555"]]'
    );

..

------

.. note:: - There is no SRID 55555 in the **proj4** with theses parameters.

-----

Calculating Lengths
^^^^^^^^^^^^^^^^^^^

The projection suggested by IBGE to calculate perimeters and lenghts is the SIRGAS 2000/Brazil Polyconic(SRID 5880).

You can check this projection definition in the `epsg.io website <https://epsg.io/5880>`_ .

You can also query the text definitions in OGC WKT format for the SRID 5880 in the table ``spatial_ref_sys``:

.. code-block:: sql

    SELECT srtext FROM spatial_ref_sys WHERE srid = 5880;

..

::

    PROJCS["SIRGAS 2000 / Brazil Polyconic",
        GEOGCS["SIRGAS 2000",
            DATUM["Sistema_de_Referencia_Geocentrico_para_las_AmericaS_2000",
                SPHEROID["GRS 1980",6378137,298.257222101,
                    AUTHORITY["EPSG","7019"]],
                TOWGS84[0,0,0,0,0,0,0],
                AUTHORITY["EPSG","6674"]],
            PRIMEM["Greenwich",0,
                AUTHORITY["EPSG","8901"]],
            UNIT["degree",0.0174532925199433,
                AUTHORITY["EPSG","9122"]],
            AUTHORITY["EPSG","4674"]],
        PROJECTION["Polyconic"],
        PARAMETER["latitude_of_origin",0],
        PARAMETER["central_meridian",-54],
        PARAMETER["false_easting",5000000],
        PARAMETER["false_northing",10000000],
        UNIT["metre",1,
        AUTHORITY["EPSG","9001"]],
        AXIS["X",EAST],
        AXIS["Y",NORTH],
        AUTHORITY["EPSG","5880"]]

..

To view the proj4 format for the SRID 5880 in the table ``spatial_ref_sys`` use the query below:

.. code-block:: sql

    SELECT proj4text FROM spatial_ref_sys WHERE srid = 5880;

..

::

    +proj=poly +lat_0=0 +lon_0=-54 +x_0=5000000 +y_0=10000000 +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs

..

Function List
-------------
ST_AsText_ (geometry): Returns the Well-Known Text (WKT) representation of the geometry/geography without SRID metadata.

ST_SetSRID_ (geometry, srid): Sets the SRID on a geometry to a particular integer value.

ST_SRID_ (geometry): Returns the spatial reference identifier for the ST_Geometry as defined in spatial_ref_sys table.

ST_Transform_ (geometry, srid): Returns a new geometry with its coordinates transformed to the SRID referenced by the integer parameter.

.. _ST_AsText: http://postgis.net/docs/ST_AsText.html
.. _ST_SetSRID: http://postgis.net/docs/ST_SetSRID.html
.. _ST_SRID: http://postgis.net/docs/ST_SRID.html
.. _ST_Transform: http://postgis.net/docs/ST_Transform.html
.. _WKT: https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry
