.. _spatial_joins_advanced:

More Spatial Joins
==================

In the last section we saw the ST_Centroid_ (geometry) and ST_Union_ ([geometry])` functions, and some simple examples. In this section we will do some more elaborate things with them.

.. _creatingtractstable:

Creating a Census Tracts Table
------------------------------

In the workshop `data <https://drive.google.com/drive/folders/1dmcVfJer0JJgXhj4ADcsEVUtP9nEHH_Z?usp=sharing>`_ directory, is a file that includes attribute data, but no geometry, ``nyc_census_sociodata.sql``. The table includes interesting socioeconomic data about New York: commute times, incomes, and education attainment. There is just one problem. The data are summarized by "census tract" and we have no census tract spatial data! 

In this section we will
~~~~~~~~~~~~~~~~~~~~~~~~

#. Load the ``nyc_census_sociodata.sql`` table

#. Create a spatial table for census tracts 

#. Join the attribute data to the spatial data

#. Carry out some analysis using our new data
 
Loading nyc_census_sociodata.sql
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  Open the SQL query window in PgAdmin

..

  Select **File->Open** from the menu and browse to the ``nyc_census_sociodata.sql`` file
  
..

  Press the "Run Query" button

..

  If you press the "Refresh" button in PgAdmin, the list of tables should now include at ``nyc_census_sociodata`` table
 
Creating a Census Tracts Table
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 
As we saw in the previous section, we can build up higher level geometries from the census block by summarizing on substrings of the ``blkid`` key. In order to get census tracts, we need to summarize grouping on the first 11 characters of the ``blkid``.
 
::

  360610001001001 = 36 061 000100 1 001
  
  36     = State of New York
  061    = New York County (Manhattan)
  000100 = Census Tract
  1      = Census Block Group
  001    = Census Block


Create the new table using the ST_Union_ aggregate:
 
.. code-block:: sql
   
   -- Make the tracts table
   CREATE TABLE nyc_census_tract_geoms AS
   SELECT ST_Union(geom)::Geometry(MultiPolygon,26918) AS geom, SubStr(blkid,1,11) AS tractid
   FROM nyc_census_blocks
   GROUP BY tractid;
     
   -- Index the tractid
   CREATE INDEX nyc_census_tract_geoms_tractid_idx ON nyc_census_tract_geoms (tractid);

Geometry Quality Data Testing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Spatial Union operations (ST_Union_) can result in unwanted interior rings resulted from inaccurated geometry topology consistence like overlap or gap between polygons.

Using the instruction below, we can identify these 14 inconsistencies:

.. code-block:: sql

 SELECT tractid, geom
 FROM
 (
 SELECT tractid, (ST_Dump(geom)).geom as geom
 FROM nyc_census_tract_geoms
 ) as a
 WHERE ST_NumInteriorRings(geom) >= 1;

To fix this, we must UPDATE the nyc_census_tract_geoms's geometry attribute with the Exterior Ring geometry: 

.. code-block:: sql

 UPDATE nyc_census_tract_geoms cnt
 SET geom = a.geom
 FROM
 (
 SELECT tractid, ST_Union(geom)::Geometry(MultiPolygon,26918) AS geom
 FROM
 ( 
 SELECT tractid, ST_Multi(ST_MakePolygon(ST_ExteriorRing((ST_Dump(geom)).geom))) as geom
 FROM
 (
 SELECT tractid, (ST_Dump(geom)).geom as geom
 FROM nyc_census_tract_geoms
 ) as a
 ) as a
 GROUP BY tractid
 ) as a
 WHERE cnt.tractid = a.tractid;

Join the Attributes to the Spatial Data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Join the table of tract geometries to the table of tract attributes with a standard attribute join
  
.. code-block:: sql
  
  -- Make the tracts table
  CREATE TABLE nyc_census_tracts AS
  SELECT g.geom, a.*
  FROM nyc_census_tract_geoms g
  JOIN nyc_census_sociodata a
  ON g.tractid = a.tractid;
    
  -- Index the geometries
  CREATE INDEX nyc_census_tract_gidx ON nyc_census_tracts USING GIST (geom);
    

.. _interestingquestion:

Answer an Interesting Question
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     
Answer an interesting question!

"List top 10 New York neighborhoods ordered by the proportion of people who have graduate degrees."
  
.. code-block:: sql
  
  SELECT 100.0 * Sum(t.edu_graduate_dipl) / Sum(t.edu_total) AS graduate_pct, n.name, n.boroname 
  FROM nyc_neighborhoods n 
  JOIN nyc_census_tracts t 
  ON ST_Intersects(n.geom, t.geom) 
  WHERE t.edu_total > 0
  GROUP BY n.name, n.boroname
  ORDER BY graduate_pct DESC
  LIMIT 10;

We sum up the statistics we are interested, then divide them together at the end. In order to avoid divide-by-zero errors, we don't bother bringing in tracts that have a population count of zero.

::
  
     graduate_pct     |                    name                    | boroname
 ---------------------+--------------------------------------------+-----------
  42.6702869226953502 | Lincoln Square                             | Manhattan
  41.2095891329118166 | Upper West Side                            | Manhattan
  39.5831736444328617 | Upper East Side-Carnegie Hill              | Manhattan
  38.9459465254400823 | Brooklyn Heights-Cobble Hill               | Brooklyn
  38.5675925148946883 | Lenox Hill-Roosevelt Island                | Manhattan
  37.7980858289595554 | Turtle Bay-East Midtown                    | Manhattan
  36.8001551619040582 | Yorkville                                  | Manhattan
  35.6936748987360635 | Murray Hill-Kips Bay                       | Manhattan
  35.6064790175029875 | West Village                               | Manhattan
  34.8544702100006840 | Hudson Yards-Chelsea-Flatiron-Union Square | Manhattan    

.. _polypolyjoins:

Polygon/Polygon Joins
---------------------

In our interesting query above we used the ST_Intersects_ (geometry_a, geometry_b) function to determine which census tract polygons to include in each neighborhood summary. Which leads to the question: what if a tract falls on the border between two neighborhoods? It will intersect both, and so will be included in the summary statistics for **both**.

.. image:: ./screenshots/centroid_neighborhood.png

To avoid this kind of double counting there are two methods:

* The simple method is to ensure that each tract only falls in **one** summary area (using ST_Centroid_ (geometry))
* The complex method is to divide crossing tracts at the borders (using ST_Intersection_ (geometry A, geometry B))
 
Here is an example of using the simple method to avoid double counting in our graduate education query:

.. code-block:: sql

  SELECT 100.0 * Sum(t.edu_graduate_dipl) / Sum(t.edu_total) AS graduate_pct, n.name, n.boroname 
  FROM nyc_neighborhoods n 
  JOIN nyc_census_tracts t 
  ON ST_Contains(n.geom, ST_Centroid(t.geom)) 
  WHERE t.edu_total > 0
  GROUP BY n.name, n.boroname
  ORDER BY graduate_pct DESC
  LIMIT 10;
  
Note that the query takes longer to run now, because the ST_Centroid_ function has to be run on every census tract.

::

     graduate_pct     |               name                | boroname
 ---------------------+-----------------------------------+-----------
  45.5608109515971079 | Lincoln Square                    | Manhattan
  45.1985480145198548 | Upper East Side-Carnegie Hill     | Manhattan
  45.1713395638629283 | Brooklyn Heights-Cobble Hill      | Brooklyn
  41.2391913998597803 | Morningside Heights               | Manhattan
  41.0893364728838523 | Upper West Side                   | Manhattan
  39.6799251286850725 | West Village                      | Manhattan
  38.7729734528988724 | Midtown-Midtown South             | Manhattan
  38.2312242446360415 | Lenox Hill-Roosevelt Island       | Manhattan
  38.1342532700876815 | Battery Park City-Lower Manhattan | Manhattan
  37.2739813765581120 | Turtle Bay-East Midtown           | Manhattan  

Avoiding double counting changes the results! 

.. _largeradiusjoins:

Large Radius Distance Joins
---------------------------

A query that is fun to ask is "How do the commute times of people near (within 500 meters) subway stations differ from those of people far away from subway stations?"

However, the question runs into some problems of double counting: many people will be within 500 meters of multiple subway stations. Compare the population of New York:

.. code-block:: sql

  SELECT Sum(popn_total)
  FROM nyc_census_blocks;
  
::

    sum
 ---------
  8175032
  
With the population of the people in New York within 500 meters of a subway station:

.. code-block:: sql

  SELECT Sum(popn_total)
  FROM nyc_census_blocks census
  JOIN nyc_subway_stations subway
  ON ST_DWithin(census.geom, subway.geom, 500);
  
::

    sum
 ----------
  10855873

There's more people close to the subway than there are people! Clearly, our simple SQL is making a big double-counting error. You can see the problem looking at the picture of the buffered subways.

.. image:: ./screenshots/subways_buffered.png

The solution is to ensure that we have only distinct census blocks before passing them into the summarization portion of the query. We can do that by breaking our query up into a subquery that finds the distinct blocks, wrapped in a summarization query that returns our answer:

.. code-block:: sql

  WITH distinct_blocks AS (
    SELECT DISTINCT ON (blkid) popn_total
    FROM nyc_census_blocks census
    JOIN nyc_subway_stations subway
    ON ST_DWithin(census.geom, subway.geom, 500)
  )
  SELECT Sum(popn_total)
  FROM distinct_blocks;

::

    sum
 ---------
  5005743

That's better! So a bit over half the population of New York is within 500m (about a 5-7 minute walk) of the subway.

Function List
=============

ST_Intersects_ (geometry A, geometry B): Returns TRUE if the Geometries/Geography "spatially intersect" - (share any portion of space) and FALSE if they don't (they are Disjoint). 

ST_Centroid_ (geometry): Computes the geometric center of a geometry, or equivalently, the center of mass of the geometry as a POINT.

ST_Union_ (geometry A, geometry B): This function returns a MULTI geometry or NON-MULTI geometry from a set of geometries. The ST_Union_ () function is an "aggregate" function in the terminology of PostgreSQL. That means that it operates on rows of data, in the same way the SUM() and AVG() functions do and like most aggregates, it also ignores NULL geometries.

ST_Intersection_ (geometry A, geometry B): Returns a geometry that represents the point set intersection of the Geometries.


.. _ST_Centroid: http://postgis.net/docs/ST_Centroid.html

.. _ST_Union: http://postgis.net/docs/ST_Union.html

.. _ST_Intersects: http://postgis.net/docs/ST_Intersects.html

.. _ST_Intersection: http://postgis.net/docs/ST_Intersection.html

