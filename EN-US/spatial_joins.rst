.. _spatial_joins:

Spatial Joins
=============

Spatial joins are the bread-and-butter of spatial databases.  They allow you to combine information from different tables by using spatial relationships as the join key.  Much of what we think of as "standard GIS analysis" can be expressed as spatial joins.

To review how joins work, here's a step-by-step look at joining the neighborhoods and subway stations tables and then subsequently adding a simple where clause.  Joining two small tables effectively multiplies the number of rows into a much larger dataset with all possible combinations of the two tables.  Then we use a where clause (or two) to narrow our focus.

Instruction 1
-------------

.. code-block:: sql

    SELECT name, boroname FROM nyc_neighborhoods;
    
::

  196 neighborhoods

Instruction 2
-------------

.. code-block:: sql

    
    SELECT name, borough
    FROM nyc_subway_stations;

::

  491 subway stations

Instruction 3
-------------

.. code-block:: sql

    
    SELECT n.name, n.boroname, s.name, s.borough
    FROM nyc_neighborhoods n, nyc_subway_stations s;

::

  join produces 96236 rows (196 * 491)

Instruction 4
-------------

.. code-block:: sql

    
    SELECT n.name, n.boroname, s.name, s.borough
    FROM nyc_neighborhoods n, nyc_subway_stations s
    WHERE n.boroname = s.borough;

::

  a simple where clause narrows this to 18538 rows


In the previous section, we explored spatial relationships using a two-step process: first we extracted a subway station point for 'Broad St'; then, we used that point to ask further questions such as "what neighborhood is the 'Broad St' station in?"

Using a spatial join, we can answer the question in one step, retrieving information about the subway station and the neighborhood that contains it:

.. code-block:: sql

   SELECT subways.name AS subway_name, neighborhoods.name AS neighborhood_name, neighborhoods.boroname AS borough
   FROM nyc_neighborhoods AS neighborhoods
   JOIN nyc_subway_stations AS subways ON ST_Contains(neighborhoods.geom, subways.geom)
   WHERE subways.name = 'Broad St';

::

     subway_name |         neighborhood_name         |  borough
    -------------+-----------------------------------+-----------
     Broad St    | Battery Park City-Lower Manhattan | Manhattan

We could have joined every subway station to its containing neighborhood, but in this case we wanted information about just one.  Any function that provides a true/false relationship between two tables can be used to drive a spatial join, but the most commonly used ones are: ST_Intersects_, ST_Contains_, and ST_DWithin_.

Join and Summarize
------------------

The combination of a ``JOIN`` with a ``GROUP BY`` provides the kind of analysis that is usually done in a GIS system.

For example: **"What is the population and racial make-up of the neighborhoods of Manhattan?"** Here we have a question that combines information from about population from the census with the boundaries of neighborhoods, with a restriction to just one borough of Manhattan.

.. code-block:: sql

  SELECT
    neighborhoods.name AS neighborhood_name,
    Sum(census.popn_total) AS population,
    100.0 * Sum(census.popn_white) / Sum(census.popn_total) AS white_pct,
    100.0 * Sum(census.popn_black) / Sum(census.popn_total) AS black_pct
  FROM nyc_neighborhoods AS neighborhoods
  JOIN nyc_census_blocks AS census
  ON ST_Intersects(neighborhoods.geom, census.geom)
  WHERE neighborhoods.boroname = 'Manhattan'
  GROUP BY neighborhoods.name
  ORDER BY white_pct DESC;

::

                 neighborhood_name              | population |    white_pct     | black_pct
    --------------------------------------------+------------+------------------+------------------
     Upper East Side-Carnegie Hill              |     106900 | 87.3947614593078 | 2.26192703461179
     Lenox Hill-Roosevelt Island                |      99806 | 82.2034747409975 | 4.21617938801274
     West Village                               |      86604 | 82.0770403214632 | 2.68232414207196
     Yorkville                                  |      98242 | 81.9781763400582 | 3.84865943282914
     Turtle Bay-East Midtown                    |      62563 | 79.6141489378706 | 2.52865111967137
     Lincoln Square                             |      70077 | 78.8889364556131 | 4.78616379125819
     Gramercy                                   |      48677 | 76.7467181625819 | 3.65470345337634
     Upper West Side                            |     143791 |  75.645902733829 | 9.83997607638865
     Stuyvesant Town-Cooper Village             |      32928 | 73.3357628765792 | 5.77623906705539
     Hudson Yards-Chelsea-Flatiron-Union Square |      91653 | 73.2425561629188 | 6.42641266516099
     Murray Hill-Kips Bay                       |      67694 | 72.7642036221822 | 5.11419032706001
     Battery Park City-Lower Manhattan          |      44470 | 70.7668090847763 | 4.01169327636609
     Midtown-Midtown South                      |      64174 | 70.5550534484371 | 4.98021005391592
     East Village                               |      72035 | 69.2482820850975 | 5.63337266606511
     Clinton                                    |      58661 | 65.5580368558327 | 7.51436218271083
     SoHo-TriBeCa-Civic Center-Little Italy     |      59362 | 64.4115764293656 | 3.33209797513561
     park-cemetery-etc-Manhattan                |     114309 | 59.0810872284772 | 16.6942235519513
     Morningside Heights                        |      67952 | 52.1559336001884 |  20.661643513068
     Lower East Side                            |      89940 |  44.607516121859 | 12.4305092283745
     Washington Heights North                   |      80988 | 43.2064009482886 |  10.094088013039
     East Harlem South                          |      73560 | 37.8847199564981 | 29.2292006525285
     Marble Hill-Inwood                         |      51435 | 34.9198016914552 |  16.607368523379
     Washington Heights South                   |      93422 | 32.7974138853803 | 17.2432617584723
     Chinatown                                  |      61722 | 26.0231359968893 | 5.60740092673601
     Manhattanville                             |      40972 | 23.0083959777409 | 39.0534999511862
     Hamilton Heights                           |      59489 | 22.8193447528114 | 41.3471398073593
     Central Harlem South                       |      54996 | 22.1470652411084 |  58.366062986399
     East Harlem North                          |      86212 | 21.9888182619589 | 49.3446387973832
     Central Harlem North-Polo Grounds          |      87915 | 11.1255189671842 | 71.3404993459592



What's going on here? Notionally (the actual evaluation order is optimized under the covers by the database) this is what happens:

#. The ``JOIN`` clause creates a virtual table that includes columns from both the neighborhoods and census tables.
#. The ``WHERE`` clause filters our virtual table to just rows in Manhattan.
#. The remaining rows are grouped by the neighborhood name and fed through the aggregation function to sum_() the population values.
#. After a little arithmetic and formatting (e.g., ``GROUP BY``, ``ORDER BY``) on the final numbers, our query spits out the percentages.

--------

.. note:: - The ``JOIN`` clause combines two ``FROM`` items.  By default, we are using an ``INNER JOIN``, but there are four other types of joins. For further information see the join_type_ definition in the PostgreSQL documentation.

--------

We can also use distance tests as a join key, to create summarized "all items within a radius" queries. Let's explore the racial geography of New York using distance queries.

First, let's get the baseline racial make-up of the city.

.. code-block:: sql

  SELECT
    100.0 * Sum(popn_white) / Sum(popn_total) AS white_pct,
    100.0 * Sum(popn_black) / Sum(popn_total) AS black_pct,
    Sum(popn_total) AS popn_total
  FROM nyc_census_blocks;

::

      white_pct     |    black_pct     | popn_total
  ------------------+------------------+------------
   44.0039500762811 | 25.5465789002416 |    8175032


So, of the 8M people in New York, about 44% are recorded as "white" and 26% are recorded as "black".

Duke Ellington once sang that "You / must take the A-train / To / go to Sugar Hill way up in Harlem." As we saw earlier, Harlem has far and away the highest African-American population in Manhattan (80.5%). Is the same true of Duke's A-train?

First, note that the contents of the ``nyc_subway_stations`` table ``routes`` field is what we are interested in to find the A-train. The values in there are a little complex.

.. code-block:: sql

  SELECT DISTINCT routes FROM nyc_subway_stations;

::

          routes
    -----------------

     2
     G,R,V
     L
     C
     3,4
     D,M
     4
     2,3
     F,Q
     A,C,G
     4,5
     D,F,N,Q
     5
     E,F
     E,J,Z
     R,W
     A,B,C,D
     B,D,4
     J
     1,2,3
     E,V
     N,Q,R,W
     N,W,7
     N,R
     4,5,6
     D
     B,Q
     F
     7
     F,G
     E,G,R,V
     B,D,F,V
     F,J,M,Z
     N,W
     A,B,C
     N,Q,R,S,W,7
     S
     2,3,4,5
     F,V
     D,M,N,R
     B,Q,S
     N
     L,N,Q,R,W
     R
     F,L,V
     2,5
     B,M,Q,R
     C,E
     A,S
     3
     M
     A,C,F
     J,Z
     J,M,Z
     N,R,W
     B,D,F,N,Q,R,V,W
     6
     B,D,E
     M,R
     A,C
     B,C
     J,M
     A
     M,D
     A,C,E,L
     Q
     1
     G
     B,D
     E,F,G,R,V
     E
     A,C,E

------

.. note:: - The ``DISTINCT`` keyword eliminates duplicate rows from the result.  Without the ``DISTINCT`` keyword, the query above identifies 491 results instead of 73.

------

So to find the A-train, we will want any row in ``routes`` that has an 'A' in it. We can do this a number of ways, but today we will use the fact that strpos_(routes,'A') will return a non-zero number only if 'A' is in the ``routes`` field.

.. code-block:: sql

   SELECT DISTINCT routes
   FROM nyc_subway_stations AS subways
   WHERE strpos(subways.routes,'A') > 0;

::

     routes
    ---------
     A,B,C
     A,C
     A
     A,C,G
     A,C,E,L
     A,S
     A,C,F
     A,B,C,D
     A,C,E

Let's summarize the racial make-up of within 200 meters of the A-train line.

.. code-block:: sql

  SELECT
    100.0 * Sum(popn_white) / Sum(popn_total) AS white_pct,
    100.0 * Sum(popn_black) / Sum(popn_total) AS black_pct,
    Sum(popn_total) AS popn_total
  FROM nyc_census_blocks AS census
  JOIN nyc_subway_stations AS subways
  ON ST_DWithin(census.geom, subways.geom, 200)
  WHERE strpos(subways.routes,'A') > 0;

::

      white_pct     |    black_pct     | popn_total
  ------------------+------------------+------------
   45.5901255900202 | 22.0936235670937 |     189824

So the racial make-up along the A-train isn't radically different from the make-up of New York City as a whole.

Advanced Join
-------------

In the last section we saw that the A-train didn't serve a population that differed much from the racial make-up of the rest of the city. Are there any trains that have a non-average racial make-up?

To answer that question, we'll add another join to our query, so that we can simultaneously calculate the make-up of many subway lines at once. To do that, we'll need to create a new table that enumerates all the lines we want to summarize.

.. code-block:: sql

    CREATE TABLE subway_lines ( route char(1) );
    INSERT INTO subway_lines (route) VALUES
      ('A'),('B'),('C'),('D'),('E'),('F'),('G'),
      ('J'),('L'),('M'),('N'),('Q'),('R'),('S'),
      ('Z'),('1'),('2'),('3'),('4'),('5'),('6'),
      ('7');

Now we can join the table of subway lines onto our original query.

.. code-block:: sql

    SELECT
      lines.route,
      100.0 * Sum(popn_white) / Sum(popn_total) AS white_pct,
      100.0 * Sum(popn_black) / Sum(popn_total) AS black_pct,
      Sum(popn_total) AS popn_total
    FROM nyc_census_blocks AS census
    JOIN nyc_subway_stations AS subways
    ON ST_DWithin(census.geom, subways.geom, 200)
    JOIN subway_lines AS lines
    ON strpos(subways.routes, lines.route) > 0
    GROUP BY lines.route
    ORDER BY black_pct DESC;

::

     route |    white_pct     |    black_pct     | popn_total
    -------+------------------+------------------+------------
     S     | 39.8396444551215 | 46.5031080147743 |      33301
     3     | 42.7273175608728 | 42.0619869354889 |     223047
     5     | 33.7937776072429 | 41.3856266472988 |     218919
     2     | 39.2630485392288 | 38.3911458851201 |     291661
     C     | 46.8787180664049 | 30.5987674400987 |     224411
     4     | 37.5530006057212 | 27.4283134664396 |     174998
     B     | 39.9558817224836 | 26.8525194576414 |     256583
     A     | 45.5901255900202 | 22.0936235670937 |     189824
     J     | 37.6295526904058 | 21.6376513800137 |     132861
     Q     | 56.8844798288124 | 20.6314116684499 |     127112
     Z     | 38.3571863056777 | 20.1570049695286 |      87131
     D     | 39.4971289442432 | 19.3856919691314 |     234931
     L     | 57.5900397755135 | 16.7756406763654 |     110118
     G     | 49.5711492311794 | 16.1311587118182 |     135012
     6     | 52.3213187826622 | 15.7166461727636 |     260240
     1     | 59.0577344374539 | 11.2664840026606 |     327742
     F     | 60.8671585911724 | 7.50177607119975 |     229439
     M     | 56.5374635468093 | 6.44561298766906 |     174196
     E     | 66.7626816772576 |    4.71756195167 |      90958
     R     | 58.4576571454677 | 4.01727927552932 |     196999
     7     | 35.7320729289753 | 3.48043476137928 |     102401
     N     | 59.6892930605175 | 3.47447764425679 |     147792

As before, the joins create a virtual table of all the possible combinations available within the constraints of the ``JOIN ON`` restrictions, and those rows are then fed into a ``GROUP`` summary. The spatial magic is in the ST_DWithin_ function, that ensures only census blocks close to the appropriate subway stations are included in the calculation.

Function List
-------------

ST_Contains_ (geometry A, geometry B): Returns true if and only if no points of B lie in the exterior of A, and at least one point of the interior of B lies in the interior of A.

ST_DWithin_ (geometry A, geometry B, radius): Returns true if the geometries are within the specified distance of one another.

ST_Intersects_ (geometry A, geometry B): Returns TRUE if the Geometries/Geography "spatially intersect" - (share any portion of space) and FALSE if they don't (they are Disjoint).

round_ (v numeric, s integer): PostgreSQL math function that rounds to s decimal places

strpos_ (string, substring): PostgreSQL string function that returns an integer location of a specified substring.

sum_ (expression): PostgreSQL aggregate function that returns the sum of records in a set of records.

.. _ST_Relate: http://postgis.net/docs/ST_Relate.html

.. _ST_Crosses: http://postgis.net/docs/ST_Crosses.html

.. _ST_Disjoint: http://postgis.net/docs/ST_Disjoint.html

.. _ST_Within: http://postgis.net/docs/ST_Within.html

.. _ST_Overlaps: http://postgis.net/docs/ST_Overlaps.html

.. _ST_Touches: http://postgis.net/docs/ST_Touches.html

.. _ST_Contains: http://postgis.net/docs/ST_Contains.html

.. _ST_Distance: http://postgis.net/docs/ST_Distance.html

.. _ST_DWithin: http://postgis.net/docs/ST_DWithin.html

.. _ST_Intersects: http://postgis.net/docs/ST_Intersects.html

.. _ST_Equals: http://postgis.net/docs/ST_Equals.html

.. _join_type: https://www.postgresql.org/docs/current/sql-select.html#SQL-FROM

.. _round: http://www.postgresql.org/docs/current/interactive/functions-math.html

.. _strpos: http://www.postgresql.org/docs/current/static/functions-string.html

.. _sum: http://www.postgresql.org/docs/current/static/functions-aggregate.html#FUNCTIONS-AGGREGATE-TABLE
