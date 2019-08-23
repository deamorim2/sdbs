.. _non_spatial_join:

Non Spatial JOIN Using SQL
==========================

An `SQL <https://en.wikipedia.org/wiki/SQL>`__ **JOIN** clause - corresponding to a `join operation in relational algebra <https://en.wikipedia.org/wiki/Join_(relational_algebra)>`__ - combines `columns <https://en.wikipedia.org/wiki/Column_(database)>`__ from one or more `tables <https://en.wikipedia.org/wiki/Table_(database)>`__ in a relational `database <https://en.wikipedia.org/wiki/Database>`__.

It creates a set that can be saved as a table or used as it is.

A **JOIN** is a means for combining `columns <https://en.wikipedia.org/wiki/Column_(database)>`__ from one (self-join) or more tables by using values common to each.

As a special case, a table (base table, `view <https://en.wikipedia.org/wiki/View_(database)>`__, or joined table) can **JOIN** to itself in a *self-join*.

A programmer declares a **JOIN** statement to identify rows for joining. If the evaluated predicate is true, the combined row is then produced in the expected format, a row set or a temporary table.

To know more about this subject, `Click Here! <https://en.wikipedia.org/wiki/Join_(SQL)>`_

Types of JOIN:
~~~~~~~~~~~~~~

`ANSI-standard SQL <https://en.wikipedia.org/wiki/American_National_Standards_Institute>`__ specifies five types of **JOIN**: ``INNER``, ``LEFT OUTER``, ``RIGHT OUTER``, ``FULL OUTER`` and ``CROSS``.

1. **Inner Join** - all rows in one table relate to all rows in other tables if they have at least 1 field in common.

   * ``Equi-join``  - An equi-join is a specific comparator-based join type, which uses only equality comparisons in the join predicate. Using other comparison operators (such as <) disqualifies an association as an equi-join.

   * ``Natural join`` - The natural join is a special case of equi-join. The natural join (⋈) is a binary operator that is written as (R ⋈ S) where R and S are relations. The result of the natural join is the set of all tuple combinations in R and S that are equal in their common attribute names.

2. **Outer Join** - is a selection that does not require records in one table to have equivalent records in other

   * ``Left Outer Join`` - all records in the left table even when there are no matching records in the right table.

   * ``Right Outer Join`` - all records in the right table even when there are no matching records in the left table.
   
   * ``Full Outer Join`` - This operation displays all data from the left and right tables, even if they are not matched in another table.

3. **Self-Join** - A self-join is joining a table to itself.

.. figure:: ./screenshots/SQL_Joins.png
   
   *Simplified diagram with SQL join types between tables* 

Sample Tables
-------------

Relational databases are usually `normalized <https://en.wikipedia.org/wiki/Database_normalization>`__ to eliminate duplication of information such as when entity types have one-to-many relationships. For example, a department may be associated with a number of employees. Joining separate tables for department and employee effectively creates another table which combines the information from both tables.

All subsequent explanations on join types in this tutorial make use of the following two tables:

.. table:: employee table

   +------------+--------------+
   | lastname   | departmentid |
   +============+==============+
   | Rafferty   | 31           |
   +------------+--------------+
   | Jones      | 33           |
   +------------+--------------+
   | Heisenberg | 33           |
   +------------+--------------+
   | Robinson   | 34           |
   +------------+--------------+
   | Smith      | 34           |
   +------------+--------------+
   | Williams   | ``NULL``     |
   +------------+--------------+

.. table:: department table

   +--------------+----------------+
   | departmentid | departmentname |
   +==============+================+
   | 31           | Sales          |
   +--------------+----------------+
   | 33           | Engineering    |
   +--------------+----------------+
   | 34           | Clerical       |
   +--------------+----------------+
   | 35           | Marketing      |
   +--------------+----------------+

-----

.. Note:: - In the ``employee`` table above, the employee *Williams* has not been assigned to any department yet. Also, note that no employees are assigned to the *Marketing* department.

-----

The rows in these tables serve to illustrate the effect of different types of joins and join-predicates. In the following tables the ``departmentid`` `column <https://en.wikipedia.org/wiki/Column_(database)>`__ of the ``department`` table (which can be designated as ``department.departmentid``) is the `primary key <https://en.wikipedia.org/wiki/Primary_key>`__, while ``employee.departmentid`` is a `foreign key <https://en.wikipedia.org/wiki/Foreign_key>`__.

**Below is the SQL statement to PostgreSQL to create the aforementioned tables.**

  Create the department and employee tables and enter their data from the instructions below into the ``nyc`` database:  
  
.. code-block:: sql

  CREATE TABLE department
  (
  departmentid integer,
  departmentname varchar(20)
  );

  CREATE TABLE employee
  (
  lastname VARCHAR(20),
  departmentid integer
  );

  INSERT INTO department VALUES(31, 'Sales');
  INSERT INTO department VALUES(33, 'Engineering');
  INSERT INTO department VALUES(34, 'Clerical');
  INSERT INTO department VALUES(35, 'Marketing');

  INSERT INTO employee VALUES('Rafferty', 31);
  INSERT INTO employee VALUES('Jones', 33);
  INSERT INTO employee VALUES('Heisenberg', 33);
  INSERT INTO employee VALUES('Robinson', 34);
  INSERT INTO employee VALUES('Smith', 34);
  INSERT INTO employee VALUES('Williams', NULL);

Cross-Join
~~~~~~~~~~

CROSS JOIN returns the `Cartesian product <https://en.wikipedia.org/wiki/Cartesian_product>`__ of rows from tables in the join. In other words, it will produce rows which combine each row from the first table with each row from the second table.

**Example of an explicit cross join:**

.. code-block:: sql

    SELECT *
    FROM employee CROSS JOIN department;

**Example of an implicit cross join:**

.. code-block:: sql

    SELECT *
    FROM employee, department;

+-----------------+-----------------+-----------------+-----------------+
| employee.LastNa | Employee.Depart | Department.Depa | Department.Depa |
| me              | mentID          | rtmentName      | rtmentID        |
+=================+=================+=================+=================+
| Rafferty        | 31              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Smith           | 34              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Williams        | ``NULL``        | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Smith           | 34              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Williams        | ``NULL``        | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Smith           | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Williams        | ``NULL``        | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Marketing       | 35              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Marketing       | 35              |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Marketing       | 35              |
+-----------------+-----------------+-----------------+-----------------+
| Smith           | 34              | Marketing       | 35              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Marketing       | 35              |
+-----------------+-----------------+-----------------+-----------------+
| Williams        | ``NULL``        | Marketing       | 35              |
+-----------------+-----------------+-----------------+-----------------+

The cross join does not itself apply any predicate to filter rows from the joined table. The results of a cross join can be filtered by using a ```WHERE`` <https://en.wikipedia.org/wiki/Where_(SQL)>`__ clause which may then produce the equivalent of an inner join.

In the `SQL:2011 <https://en.wikipedia.org/wiki/SQL:2011>`__ standard, cross joins are part of the optional F401, "Extended joined table", package.

Normal uses are for checking the server's performance.


|A Venn Diagram showing the inner overlapping portion filled.|


A Venn Diagram representing an Inner Join SQL statement between the tables A and B.

An **inner join** requires each row in the two joined tables to have
matching column values, and is a commonly used join operation in
`applications <https://en.wikipedia.org/wiki/Application_software>`__
but should not be assumed to be the best choice in all situations. Inner
join creates a new result table by combining column values of two tables
(A and B) based upon the join-predicate. The query compares each row of
A with each row of B to find all pairs of rows that satisfy the
join-predicate. When the join-predicate is satisfied by matching
non-\ `NULL <https://en.wikipedia.org/wiki/Null_(SQL)>`__ values, column
values for each matched pair of rows of A and B are combined into a
result row.

The result of the join can be defined as the outcome of first taking the
`Cartesian product <https://en.wikipedia.org/wiki/Cartesian_product>`__
(or `Cross
join <https://en.wikipedia.org/wiki/Join_(SQL)#Cross_join>`__) of all
rows in the tables (combining every row in table A with every row in
table B) and then returning all rows that satisfy the join predicate.
Actual SQL implementations normally use other approaches, such as `hash
joins <https://en.wikipedia.org/wiki/Hash_join>`__ or `sort-merge
joins <https://en.wikipedia.org/wiki/Sort-merge_join>`__, since
computing the Cartesian product is slower and would often require a
prohibitively large amount of memory to store.

SQL specifies two different syntactical ways to express joins: the
"explicit join notation" and the "implicit join notation". The "implicit
join notation" is no longer considered a best practice, although
database systems still support it.

The "explicit join notation" uses the ``JOIN`` keyword, optionally
preceded by the ``INNER`` keyword, to specify the table to join, and the
``ON`` keyword to specify the predicates for the join, as in the
following example:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT employee.LastName, employee.DepartmentID, department.DepartmentName 
    FROM employee 
    INNER JOIN department ON
    employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

+-------------------+-----------------------+---------------------------+
| Employee.LastName | Employee.DepartmentID | Department.DepartmentName |
+===================+=======================+===========================+
| Robinson          | 34                    | Clerical                  |
+-------------------+-----------------------+---------------------------+
| Jones             | 33                    | Engineering               |
+-------------------+-----------------------+---------------------------+
| Smith             | 34                    | Clerical                  |
+-------------------+-----------------------+---------------------------+
| Heisenberg        | 33                    | Engineering               |
+-------------------+-----------------------+---------------------------+
| Rafferty          | 31                    | Sales                     |
+-------------------+-----------------------+---------------------------+

The "implicit join notation" simply lists the tables for joining, in the
``FROM`` clause of the ``SELECT`` statement, using commas to separate
them. Thus it specifies a `cross
join <https://en.wikipedia.org/wiki/Join_(SQL)#Cross_join>`__, and the
``WHERE`` clause may apply additional filter-predicates (which function
comparably to the join-predicates in the explicit notation).

The following example is equivalent to the previous one, but this time
using implicit join notation:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee, department
    WHERE employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

The queries given in the examples above will join the Employee and
Department tables using the DepartmentID column of both tables. Where
the DepartmentID of these tables match (i.e. the join-predicate is
satisfied), the query will combine the *LastName*, *DepartmentID* and
*DepartmentName* columns from the two tables into a result row. Where
the DepartmentID does not match, no result row is generated.

Thus the result of the
`execution <https://en.wikipedia.org/wiki/Query_plan>`__ of the query
above will be:

+-----------------+-----------------+-----------------+-----------------+
| Employee.LastNa | Employee.Depart | Department.Depa | Department.Depa |
| me              | mentID          | rtmentName      | rtmentID        |
+=================+=================+=================+=================+
| Robinson        | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Smith           | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+

The employee "Williams" and the department "Marketing" do not appear in
the query execution results. Neither of these has any matching rows in
the other respective table: "Williams" has no associated department, and
no employee has the department ID 35 ("Marketing"). Depending on the
desired results, this behavior may be a subtle bug, which can be avoided
by replacing the inner join with an `outer
join <https://en.wikipedia.org/wiki/Join_(SQL)#Outer_join>`__.

Programmers should take special care when joining tables on columns that
can contain `NULL <https://en.wikipedia.org/wiki/Null_(SQL)>`__ values,
since NULL will never match any other value (not even NULL itself),
unless the join condition explicitly uses a combination predicate that
first checks that the joins columns are ``NOT NULL`` before applying the
remaining predicate condition(s). The Inner join can only be safely used
in a database that enforces `referential
integrity <https://en.wikipedia.org/wiki/Referential_integrity>`__ or
where the join columns are guaranteed not to be NULL. Many `transaction
processing <https://en.wikipedia.org/wiki/Transaction_processing>`__
relational databases rely on `Atomicity, Consistency, Isolation,
Durability (ACID) <https://en.wikipedia.org/wiki/ACID>`__ data update
standards to ensure data integrity, making inner joins an appropriate
choice. However transaction databases usually also have desirable join
columns that are allowed to be NULL. Many reporting relational database
and `data warehouses <https://en.wikipedia.org/wiki/Data_warehouse>`__
use high volume `Extract, Transform, Load
(ETL) <https://en.wikipedia.org/wiki/Extract,_transform,_load>`__ batch
updates which make referential integrity difficult or impossible to
enforce, resulting in potentially NULL join columns that an SQL query
author cannot modify and which cause inner joins to omit data with no
indication of an error. The choice to use an inner join depends on the
database design and data characteristics. A left outer join can usually
be substituted for an inner join when the join columns in one table may
contain NULL values.

Any data column that may be NULL (empty) should never be used as a link
in an inner join, unless the intended result is to eliminate the rows
with the NULL value. If NULL join columns are to be deliberately removed
from the result set, an inner join can be faster than an outer join
because the table join and filtering is done in a single step.
Conversely, an inner join can result in disastrously slow performance or
even a server crash when used in a large volume query in combination
with database functions in an SQL Where
clause.\ :sup:``[2] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-2>`__\ `[3] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-3>`__\ `[4] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-4>`__`
A function in an SQL Where clause can result in the database ignoring
relatively compact table indexes. The database may read and inner join
the selected columns from both tables before reducing the number of rows
using the filter that depends on a calculated value, resulting in a
relatively enormous amount of inefficient processing.

When a result set is produced by joining several tables, including
master tables used to look up full text descriptions of numeric
identifier codes (a `Lookup
table <https://en.wikipedia.org/wiki/Lookup_table>`__), a NULL value in
any one of the foreign keys can result in the entire row being
eliminated from the result set, with no indication of error. A complex
SQL query that includes one or more inner joins and several outer joins
has the same risk for NULL values in the inner join link columns.

A commitment to SQL code containing inner joins assumes NULL join
columns will not be introduced by future changes, including vendor
updates, design changes and bulk processing outside of the application's
data validation rules such as data conversions, migrations, bulk imports
and merges.

One can further classify inner joins as equi-joins, as natural joins, or
as cross-joins.

.. rubric:: Equi-join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=4>`__\ ]
   :name: equi-joinedit

An **equi-join** is a specific type of comparator-based join, that uses
only `equality <https://en.wikipedia.org/wiki/Equality_(mathematics)>`__
comparisons in the join-predicate. Using other comparison operators
(such as ``<``) disqualifies a join as an equi-join. The query shown
above has already provided an example of an equi-join:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee JOIN department
      ON employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

We can write equi-join as below,

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee, department
    WHERE employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

If columns in an equi-join have the same name,
`SQL-92 <https://en.wikipedia.org/wiki/SQL-92>`__ provides an optional
shorthand notation for expressing equi-joins, by way of the ``USING``
construct:\ :sup:``[5] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-5>`__`

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee INNER JOIN department USING (DepartmentID);

.. raw:: html

   </div>

The ``USING`` construct is more than mere `syntactic
sugar <https://en.wikipedia.org/wiki/Syntactic_sugar>`__, however, since
the result set differs from the result set of the version with the
explicit predicate. Specifically, any columns mentioned in the ``USING``
list will appear only once, with an unqualified name, rather than once
for each table in the join. In the case above, there will be a single
``DepartmentID`` column and no ``employee.DepartmentID`` or
``department.DepartmentID``.

The ``USING`` clause is not supported by MS SQL Server and Sybase.

.. rubric:: Natural
   join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=5>`__\ ]
   :name: natural-joinedit

The natural join is a special case of equi-join. Natural join (⋈) is a
`binary operator <https://en.wikipedia.org/wiki/Binary_relation>`__ that
is written as (*R* ⋈ *S*) where *R* and *S* are
`relations <https://en.wikipedia.org/wiki/Relation_(database)>`__.\ :sup:``[6] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-6>`__`
The result of the natural join is the set of all combinations of
`tuples <https://en.wikipedia.org/wiki/Tuples>`__ in *R* and *S* that
are equal on their common attribute names. For an example consider the
tables *Employee* and *Dept* and their natural join:

+-----------------------+-----------------------+-----------------------+
| .. table:: *Employee* | .. table:: *Dept*     | .. table:: *Employee* |
|                       |                       |  :math:`\bowtie`\ |\\ |
|    +-----+-----+----- |    +------------+---- | bowtie                |
| +                     | -----+                | | *Dept*              |
|    | Nam | Emp | Dep  |    | DeptName   | Man |                       |
| |                     | ager |                |    +---+---+---+---+  |
|    | e   | Id  | tNa  |    +============+==== |    | N | E | D | M |  |
| |                     | =====+                |    | a | m | e | a |  |
|    |     |     | me   |    | Finance    | Geo |    | m | p | p | n |  |
| |                     | rge  |                |    | e | I | t | a |  |
|    +=====+=====+===== |    +------------+---- |    |   | d | N | g |  |
| +                     | -----+                |    |   |   | a | e |  |
|    | Har | 341 | Fin  |    | Sales      | Har |    |   |   | m | r |  |
| |                     | riet |                |    |   |   | e |   |  |
|    | ry  | 5   | anc  |    +------------+---- |    +===+===+===+===+  |
| |                     | -----+                |    | H | 3 | F | G |  |
|    |     |     | e    |    | Production | Cha |    | a | 4 | i | e |  |
| |                     | rles |                |    | r | 1 | n | o |  |
|    +-----+-----+----- |    +------------+---- |    | r | 5 | a | r |  |
| +                     | -----+                |    | y |   | n | g |  |
|    | Sal | 224 | Sal  |                       |    |   |   | c | e |  |
| |                     |                       |    |   |   | e |   |  |
|    | ly  | 1   | es   |                       |    +---+---+---+---+  |
| |                     |                       |    | S | 2 | S | H |  |
|    +-----+-----+----- |                       |    | a | 2 | a | a |  |
| +                     |                       |    | l | 4 | l | r |  |
|    | Geo | 340 | Fin  |                       |    | l | 1 | e | r |  |
| |                     |                       |    | y |   | s | i |  |
|    | rge | 1   | anc  |                       |    |   |   |   | e |  |
| |                     |                       |    |   |   |   | t |  |
|    |     |     | e    |                       |    +---+---+---+---+  |
| |                     |                       |    | G | 3 | F | G |  |
|    +-----+-----+----- |                       |    | e | 4 | i | e |  |
| +                     |                       |    | o | 0 | n | o |  |
|    | Har | 220 | Sal  |                       |    | r | 1 | a | r |  |
| |                     |                       |    | g |   | n | g |  |
|    | rie | 2   | es   |                       |    | e |   | c | e |  |
| |                     |                       |    |   |   | e |   |  |
|    | t   |     |      |                       |    +---+---+---+---+  |
| |                     |                       |    | H | 2 | S | H |  |
|    +-----+-----+----- |                       |    | a | 2 | a | a |  |
| +                     |                       |    | r | 0 | l | r |  |
|                       |                       |    | r | 2 | e | r |  |
|                       |                       |    | i |   | s | i |  |
|                       |                       |    | e |   |   | e |  |
|                       |                       |    | t |   |   | t |  |
|                       |                       |    +---+---+---+---+  |
+-----------------------+-----------------------+-----------------------+

This can also be used to define `composition of
relations <https://en.wikipedia.org/wiki/Composition_of_relations>`__.
For example, the composition of *Employee* and *Dept* is their join as
shown above, projected on all but the common attribute *DeptName*. In
`category theory <https://en.wikipedia.org/wiki/Category_theory>`__, the
join is precisely the `fiber
product <https://en.wikipedia.org/wiki/Fiber_product>`__.

The natural join is arguably one of the most important operators since
it is the relational counterpart of logical AND. Note that if the same
variable appears in each of two predicates that are connected by AND,
then that variable stands for the same thing and both appearances must
always be substituted by the same value. In particular, the natural join
allows the combination of relations that are associated by a `foreign
key <https://en.wikipedia.org/wiki/Foreign_key>`__. For example, in the
above example a foreign key probably holds from *Employee*.\ *DeptName*
to *Dept*.\ *DeptName* and then the natural join of *Employee* and
*Dept* combines all employees with their departments. This works because
the foreign key holds between attributes with the same name. If this is
not the case such as in the foreign key from *Dept*.\ *manager* to
*Employee*.\ *Name* then these columns have to be renamed before the
natural join is taken. Such a join is sometimes also referred to as an
**equi-join**.

More formally the semantics of the natural join are defined as follows:

:math:`R \bowtie S = \left\{ {t \cup s \mid t \in R\  \land \ s \in S\  \land \ {\mathit{F}\mathit{u}\mathit{n}}(t \cup s)} \right\}`\ |{\displaystyle
R\bowtie S=\left\{t\cup s\mid t\in R\\ \\land \\ s\in S\\ \\land \\
{\mathit {Fun}}(t\cup s)\right\}}|,
where *Fun* is a
`predicate <https://en.wikipedia.org/wiki/Predicate_(mathematics)>`__
that is true for a
`relation <https://en.wikipedia.org/wiki/Relation_(mathematics)>`__ *r*
`if and only if <https://en.wikipedia.org/wiki/If_and_only_if>`__ *r* is
a function. It is usually required that *R* and *S* must have at least
one common attribute, but if this constraint is omitted, and *R* and *S*
have no common attributes, then the natural join becomes exactly the
Cartesian product.

The natural join can be simulated with Codd's primitives as follows. Let
*c*\ :sub:`1`, …, *c*\ :sub:`*m*` be the attribute names common to *R*
and *S*, *r*\ :sub:`1`, …, *r*\ :sub:`*n*` be the attribute names unique
to *R* and let *s*\ :sub:`1`, …, *s*\ :sub:`*k*` be the attributes
unique to *S*. Furthermore, assume that the attribute names
*x*\ :sub:`1`, …, *x*\ :sub:`*m*` are neither in *R* nor in *S*. In a
first step the common attribute names in *S* can now be renamed:

:math:`T = \rho_{x_{1}/c_{1},\ldots,x_{m}/c_{m}}(S) = \rho_{x_{1}/c_{1}}(\rho_{x_{2}/c_{2}}(\ldots\rho_{x_{m}/c_{m}}(S)\ldots))`\ |T=\rho
\_{x_{1}/c_{1},\ldots ,x_{m}/c_{m}}(S)=\rho \_{x_{1}/c_{1}}(\rho
\_{x_{2}/c_{2}}(\ldots \\rho \_{x_{m}/c_{m}}(S)\ldots ))|
Then we take the Cartesian product and select the tuples that are to be
joined:

:math:`U = \pi_{r_{1},\ldots,r_{n},c_{1},\ldots,c_{m},s_{1},\ldots,s_{k}}(P)`\ |U=\pi
\_{r_{1},\ldots ,r_{n},c_{1},\ldots ,c_{m},s_{1},\ldots ,s_{k}}(P)|
A `natural join <https://en.wikipedia.org/wiki/Natural_join>`__ is a
type of equi-join where the **join** predicate arises implicitly by
comparing all columns in both tables that have the same column-names in
the joined tables. The resulting joined table contains only one column
for each pair of equally named columns. In the case that no columns with
the same names are found, the result is a `cross
join <https://en.wikipedia.org/wiki/Cross_join>`__.

Most experts agree that NATURAL JOINs are dangerous and therefore
strongly discourage their
use.\ :sup:``[7] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-7>`__`
The danger comes from inadvertently adding a new column, named the same
as another column in the other table. An existing natural join might
then "naturally" use the new column for comparisons, making
comparisons/matches using different criteria (from different columns)
than before. Thus an existing query could produce different results,
even though the data in the tables have not been changed, but only
augmented. The use of column names to automatically determine table
links is not an option in large databases with hundreds or thousands of
tables where it would place an unrealistic constraint on naming
conventions. Real world databases are commonly designed with `foreign
key <https://en.wikipedia.org/wiki/Foreign_key>`__ data that is not
consistently populated (NULL values are allowed), due to business rules
and context. It is common practice to modify column names of similar
data in different tables and this lack of rigid consistency relegates
natural joins to a theoretical concept for discussion.

The above sample query for inner joins can be expressed as a natural
join in the following way:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee NATURAL JOIN department;

.. raw:: html

   </div>

As with the explicit ``USING`` clause, only one DepartmentID column
occurs in the joined table, with no qualifier:

+--------------+-------------------+---------------------------+
| DepartmentID | Employee.LastName | Department.DepartmentName |
+==============+===================+===========================+
| 34           | Smith             | Clerical                  |
+--------------+-------------------+---------------------------+
| 33           | Jones             | Engineering               |
+--------------+-------------------+---------------------------+
| 34           | Robinson          | Clerical                  |
+--------------+-------------------+---------------------------+
| 33           | Heisenberg        | Engineering               |
+--------------+-------------------+---------------------------+
| 31           | Rafferty          | Sales                     |
+--------------+-------------------+---------------------------+

PostgreSQL, MySQL and Oracle support natural joins; Microsoft T-SQL and
IBM DB2 do not. The columns used in the join are implicit so the join
code does not show which columns are expected, and a change in column
names may change the results. In the
`SQL:2011 <https://en.wikipedia.org/wiki/SQL:2011>`__ standard, natural
joins are part of the optional F401, "Extended joined table", package.

In many database environments the column names are controlled by an
outside vendor, not the query developer. A natural join assumes
stability and consistency in column names which can change during vendor
mandated version upgrades.

.. rubric:: Outer
   join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=6>`__\ ]
   :name: outer-joinedit

The joined table retains each row—even if no other matching row exists.
Outer joins subdivide further into left outer joins, right outer joins,
and full outer joins, depending on which table's rows are retained:
left, right, or both (in this case *left* and *right* refer to the two
sides of the ``JOIN`` keyword). Like `inner
joins <https://en.wikipedia.org/wiki/Join_(SQL)#Inner_join>`__, one can
further sub-categorize all types of outer joins as
`equi-joins <https://en.wikipedia.org/wiki/Join_(SQL)#Equi-join>`__,
`natural
joins <https://en.wikipedia.org/wiki/Join_(SQL)#Natural_join>`__,
``ON <predicate>``
(`*θ*-join <https://en.wikipedia.org/wiki/Relational_algebra#%CE%B8-join_and_equijoin>`__),
etc.\ :sup:``[8] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-8>`__`

No implicit join-notation for outer joins exists in standard SQL.

.. raw:: html

   <div class="thumb tright">

.. raw:: html

   <div class="thumbinner" style="width:222px;">

|A Venn Diagram showing the left circle and overlapping portion filled.|

.. raw:: html

   <div class="thumbcaption">

.. raw:: html

   <div class="magnify">

` <https://en.wikipedia.org/wiki/File:SQL_Join_-_01_A_Left_Join_B.svg>`__

.. raw:: html

   </div>

A Venn Diagram representing the Left Join SQL statement between tables A
and B.

.. raw:: html

   </div>

.. raw:: html

   </div>

.. raw:: html

   </div>

.. rubric:: Left outer
   join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=7>`__\ ]
   :name: left-outer-joinedit

The result of a *left outer join* (or simply **left join**) for tables A
and B always contains all rows of the "left" table (A), even if the
join-condition does not find any matching row in the "right" table (B).
This means that if the ``ON`` clause matches 0 (zero) rows in B (for a
given row in A), the join will still return a row in the result (for
that row)—but with NULL in each column from B. A **left outer join**
returns all the values from an inner join plus all values in the left
table that do not match to the right table, including rows with NULL
(empty) values in the link column.

For example, this allows us to find an employee's department, but still
shows employees that have not been assigned to a department (contrary to
the inner-join example above, where unassigned employees were excluded
from the result).

Example of a left outer join (the **``OUTER``** keyword is optional),
with the additional result row (compared with the inner join)
italicized:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee 
    LEFT OUTER JOIN department ON employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

+-----------------+-----------------+-----------------+-----------------+
| Employee.LastNa | Employee.Depart | Department.Depa | Department.Depa |
| me              | mentID          | rtmentName      | rtmentID        |
+=================+=================+=================+=================+
| Jones           | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Smith           | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| *Williams*      | ``NULL``        | ``NULL``        | ``NULL``        |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+

.. rubric:: Alternative
   syntaxes[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=8>`__\ ]
   :name: alternative-syntaxesedit

Oracle supports the
deprecated\ :sup:``[9] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-deprecated_plus_sign-9>`__`
syntax:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee, department
    WHERE employee.DepartmentID = department.DepartmentID(+)

.. raw:: html

   </div>

`Sybase <https://en.wikipedia.org/wiki/Sybase>`__ supports the syntax
(`Microsoft SQL
Server <https://en.wikipedia.org/wiki/Microsoft_SQL_Server>`__
deprecated this syntax since version 2000):

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee, department
    WHERE employee.DepartmentID *= department.DepartmentID

.. raw:: html

   </div>

`IBM Informix <https://en.wikipedia.org/wiki/IBM_Informix>`__ supports
the syntax:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee, OUTER department
    WHERE employee.DepartmentID = department.DepartmentID

.. raw:: html

   </div>

.. raw:: html

   <div class="thumb tright">

.. raw:: html

   <div class="thumbinner" style="width:222px;">

|A Venn Diagram show the right circle and overlapping portions filled.|

.. raw:: html

   <div class="thumbcaption">

.. raw:: html

   <div class="magnify">

` <https://en.wikipedia.org/wiki/File:SQL_Join_-_03_A_Right_Join_B.svg>`__

.. raw:: html

   </div>

A Venn Diagram representing the Right Join SQL statement between tables
A and B.

.. raw:: html

   </div>

.. raw:: html

   </div>

.. raw:: html

   </div>

.. rubric:: Right outer
   join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=9>`__\ ]
   :name: right-outer-joinedit

A **right outer join** (or **right join**) closely resembles a left
outer join, except with the treatment of the tables reversed. Every row
from the "right" table (B) will appear in the joined table at least
once. If no matching row from the "left" table (A) exists, NULL will
appear in columns from A for those rows that have no match in B.

A right outer join returns all the values from the right table and
matched values from the left table (NULL in the case of no matching join
predicate). For example, this allows us to find each employee and his or
her department, but still show departments that have no employees.

Below is an example of a right outer join (the **``OUTER``** keyword is
optional), with the additional result row italicized:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee RIGHT OUTER JOIN department
      ON employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

+-----------------+-----------------+-----------------+-----------------+
| Employee.LastNa | Employee.Depart | Department.Depa | Department.Depa |
| me              | mentID          | rtmentName      | rtmentID        |
+=================+=================+=================+=================+
| Smith           | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| ``NULL``        | ``NULL``        | *Marketing*     | *35*            |
+-----------------+-----------------+-----------------+-----------------+

Right and left outer joins are functionally equivalent. Neither provides
any functionality that the other does not, so right and left outer joins
may replace each other as long as the table order is switched.

.. raw:: html

   <div class="thumb tright">

.. raw:: html

   <div class="thumbinner" style="width:222px;">

|A Venn Diagram showing the right circle, left circle, and overlapping
portion filled.|

.. raw:: html

   <div class="thumbcaption">

.. raw:: html

   <div class="magnify">

` <https://en.wikipedia.org/wiki/File:SQL_Join_-_05b_A_Full_Join_B.svg>`__

.. raw:: html

   </div>

A Venn Diagram representing the Full Join SQL statement between tables A
and B.

.. raw:: html

   </div>

.. raw:: html

   </div>

.. raw:: html

   </div>

.. rubric:: Full outer
   join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=10>`__\ ]
   :name: full-outer-joinedit

Conceptually, a **full outer join** combines the effect of applying both
left and right outer joins. Where rows in the FULL OUTER JOINed tables
do not match, the result set will have NULL values for every column of
the table that lacks a matching row. For those rows that do match, a
single row will be produced in the result set (containing columns
populated from both tables).

For example, this allows us to see each employee who is in a department
and each department that has an employee, but also see each employee who
is not part of a department and each department which doesn't have an
employee.

Example of a full outer join (the **``OUTER``** keyword is optional):

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT *
    FROM employee FULL OUTER JOIN department
      ON employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

+-----------------+-----------------+-----------------+-----------------+
| Employee.LastNa | Employee.Depart | Department.Depa | Department.Depa |
| me              | mentID          | rtmentName      | rtmentID        |
+=================+=================+=================+=================+
| Smith           | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| Jones           | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Robinson        | 34              | Clerical        | 34              |
+-----------------+-----------------+-----------------+-----------------+
| *Williams*      | ``NULL``        | ``NULL``        | ``NULL``        |
+-----------------+-----------------+-----------------+-----------------+
| Heisenberg      | 33              | Engineering     | 33              |
+-----------------+-----------------+-----------------+-----------------+
| Rafferty        | 31              | Sales           | 31              |
+-----------------+-----------------+-----------------+-----------------+
| ``NULL``        | ``NULL``        | *Marketing*     | *35*            |
+-----------------+-----------------+-----------------+-----------------+

Some database systems do not support the full outer join functionality
directly, but they can emulate it through the use of an inner join and
UNION ALL selects of the "single table rows" from left and right tables
respectively. The same example can appear as follows:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT employee.LastName, employee.DepartmentID,
           department.DepartmentName, department.DepartmentID
    FROM employee
    INNER JOIN department ON employee.DepartmentID = department.DepartmentID

    UNION ALL

    SELECT employee.LastName, employee.DepartmentID,
           cast(NULL as varchar(20)), cast(NULL as integer)
    FROM employee
    WHERE NOT EXISTS (
        SELECT * FROM department
                 WHERE employee.DepartmentID = department.DepartmentID)

    UNION ALL

    SELECT cast(NULL as varchar(20)), cast(NULL as integer),
           department.DepartmentName, department.DepartmentID
    FROM department
    WHERE NOT EXISTS (
        SELECT * FROM employee
                 WHERE employee.DepartmentID = department.DepartmentID)

.. raw:: html

   </div>

.. rubric:: Self-join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=11>`__\ ]
   :name: self-joinedit

A self-join is joining a table to
itself.\ :sup:``[10] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-10>`__`

.. rubric:: Example[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=12>`__\ ]
   :name: exampleedit

If there were two separate tables for employees and a query which
requested employees in the first table having the same country as
employees in the second table, a normal join operation could be used to
find the answer table. However, all the employee information is
contained within a single large
table.\ :sup:``[11] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-11>`__`

Consider a modified ``Employee`` table such as the following:

.. table:: Employee Table

   +------------+------------+---------------+--------------+
   | EmployeeID | LastName   | Country       | DepartmentID |
   +============+============+===============+==============+
   | 123        | Rafferty   | Australia     | 31           |
   +------------+------------+---------------+--------------+
   | 124        | Jones      | Australia     | 33           |
   +------------+------------+---------------+--------------+
   | 145        | Heisenberg | Australia     | 33           |
   +------------+------------+---------------+--------------+
   | 201        | Robinson   | United States | 34           |
   +------------+------------+---------------+--------------+
   | 305        | Smith      | Germany       | 34           |
   +------------+------------+---------------+--------------+
   | 306        | Williams   | Germany       | ``NULL``     |
   +------------+------------+---------------+--------------+

.. raw:: html

   <div style="clear:both;">

.. raw:: html

   </div>

An example solution query could be as follows:

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT F.EmployeeID, F.LastName, S.EmployeeID, S.LastName, F.Country
    FROM Employee F INNER JOIN Employee S ON F.Country = S.Country
    WHERE F.EmployeeID < S.EmployeeID
    ORDER BY F.EmployeeID, S.EmployeeID;

.. raw:: html

   </div>

Which results in the following table being generated.

.. table:: Employee Table after Self-join by Country

   +------------+----------+------------+------------+-----------+
   | EmployeeID | LastName | EmployeeID | LastName   | Country   |
   +============+==========+============+============+===========+
   | 123        | Rafferty | 124        | Jones      | Australia |
   +------------+----------+------------+------------+-----------+
   | 123        | Rafferty | 145        | Heisenberg | Australia |
   +------------+----------+------------+------------+-----------+
   | 124        | Jones    | 145        | Heisenberg | Australia |
   +------------+----------+------------+------------+-----------+
   | 305        | Smith    | 306        | Williams   | Germany   |
   +------------+----------+------------+------------+-----------+

.. raw:: html

   <div style="clear:both;">

.. raw:: html

   </div>

For this example:

-  ``F`` and ``S`` are
   `aliases <https://en.wikipedia.org/wiki/Alias_(SQL)>`__ for the first
   and second copies of the employee table.
-  The condition ``F.Country = S.Country`` excludes pairings between
   employees in different countries. The example question only wanted
   pairs of employees in the same country.
-  The condition ``F.EmployeeID < S.EmployeeID`` excludes pairings where
   the ``EmployeeID`` of the first employee is greater than or equal to
   the ``EmployeeID`` of the second employee. In other words, the effect
   of this condition is to exclude duplicate pairings and self-pairings.
   Without it, the following less useful table would be generated (the
   table below displays only the "Germany" portion of the result):

+------------+----------+------------+----------+---------+
| EmployeeID | LastName | EmployeeID | LastName | Country |
+============+==========+============+==========+=========+
| 305        | Smith    | 305        | Smith    | Germany |
+------------+----------+------------+----------+---------+
| 305        | Smith    | 306        | Williams | Germany |
+------------+----------+------------+----------+---------+
| 306        | Williams | 305        | Smith    | Germany |
+------------+----------+------------+----------+---------+
| 306        | Williams | 306        | Williams | Germany |
+------------+----------+------------+----------+---------+

.. raw:: html

   <div style="clear:both;">

.. raw:: html

   </div>

Only one of the two middle pairings is needed to satisfy the original
question, and the topmost and bottommost are of no interest at all in
this example.

.. rubric:: Alternatives[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=13>`__\ ]
   :name: alternativesedit

The effect of an outer join can also be obtained using a UNION ALL
between an INNER JOIN and a SELECT of the rows in the "main" table that
do not fulfill the join condition. For example,

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT employee.LastName, employee.DepartmentID, department.DepartmentName
    FROM employee
    LEFT OUTER JOIN department ON employee.DepartmentID = department.DepartmentID;

.. raw:: html

   </div>

can also be written as

.. raw:: html

   <div class="mw-highlight mw-content-ltr" dir="ltr">

::

    SELECT employee.LastName, employee.DepartmentID, department.DepartmentName
    FROM employee
    INNER JOIN department ON employee.DepartmentID = department.DepartmentID

    UNION ALL

    SELECT employee.LastName, employee.DepartmentID, cast(NULL as varchar(20))
    FROM employee
    WHERE NOT EXISTS (
        SELECT * FROM department
                 WHERE employee.DepartmentID = department.DepartmentID)

.. raw:: html

   </div>

.. rubric:: Implementation[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=14>`__\ ]
   :name: implementationedit

Much work in database-systems has aimed at efficient implementation of
joins, because relational systems commonly call for joins, yet face
difficulties in optimising their efficient execution. The problem arises
because inner joins operate both
`commutatively <https://en.wikipedia.org/wiki/Commutative>`__ and
`associatively <https://en.wikipedia.org/wiki/Associative>`__. In
practice, this means that the user merely supplies the list of tables
for joining and the join conditions to use, and the database system has
the task of determining the most efficient way to perform the operation.
A `query optimizer <https://en.wikipedia.org/wiki/Query_optimizer>`__
determines how to execute a query containing joins. A query optimizer
has two basic freedoms:

#. **Join order**: Because it joins functions commutatively and
   associatively, the order in which the system joins tables does not
   change the final result set of the query. However, join-order
   **could** have an enormous impact on the cost of the join operation,
   so choosing the best join order becomes very important.
#. **Join method**: Given two tables and a join condition, multiple
   `algorithms <https://en.wikipedia.org/wiki/Algorithm>`__ can produce
   the result set of the join. Which algorithm runs most efficiently
   depends on the sizes of the input tables, the number of rows from
   each table that match the join condition, and the operations required
   by the rest of the query.

Many join-algorithms treat their inputs differently. One can refer to
the inputs to a join as the "outer" and "inner" join operands, or "left"
and "right", respectively. In the case of nested loops, for example, the
database system will scan the entire inner relation for each row of the
outer relation.

One can classify query-plans involving joins as
follows:\ :sup:``[12] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-Yu1998-12>`__`

left-deep 
    using a base table (rather than another join) as the inner operand
    of each join in the plan
right-deep 
    using a base table as the outer operand of each join in the plan
bushy 
    neither left-deep nor right-deep; both inputs to a join may
    themselves result from joins

These names derive from the appearance of the `query
plan <https://en.wikipedia.org/wiki/Query_plan>`__ if drawn as a
`tree <https://en.wikipedia.org/wiki/Tree_data_structure>`__, with the
outer join relation on the left and the inner relation on the right (as
convention dictates).

.. rubric:: Join
   algorithms[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=15>`__\ ]
   :name: join-algorithmsedit

Three fundamental algorithms for performing a join operation exist:
`nested loop join <https://en.wikipedia.org/wiki/Nested_loop_join>`__,
`sort-merge join <https://en.wikipedia.org/wiki/Sort-merge_join>`__ and
`hash join <https://en.wikipedia.org/wiki/Hash_join>`__.

.. rubric:: Join
   indexes[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=16>`__\ ]
   :name: join-indexesedit

Join indexes are `database
indexes <https://en.wikipedia.org/wiki/Database_index>`__ that
facilitate the processing of join queries in `data
warehouses <https://en.wikipedia.org/wiki/Data_warehouse>`__: they are
currently (2012) available in implementations by
`Oracle <https://en.wikipedia.org/wiki/Oracle_database>`__\ :sup:``[13] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-13>`__`
and
`Teradata <https://en.wikipedia.org/wiki/Teradata>`__.\ :sup:``[14] <https://en.wikipedia.org/wiki/Join_(SQL)#cite_note-14>`__`

In the Teradata implementation, specified columns, aggregate functions
on columns, or components of date columns from one or more tables are
specified using a syntax similar to the definition of a `database
view <https://en.wikipedia.org/wiki/Database_view>`__: up to 64
columns/column expressions can be specified in a single join index.
Optionally, a column that defines the `primary
key <https://en.wikipedia.org/wiki/Primary_key>`__ of the composite data
may also be specified: on parallel hardware, the column values are used
to partition the index's contents across multiple disks. When the source
tables are updated interactively by users, the contents of the join
index are automatically updated. Any query whose `WHERE
clause <https://en.wikipedia.org/wiki/Where_(SQL)>`__ specifies any
combination of columns or column expressions that are an exact subset of
those defined in a join index (a so-called "covering query") will cause
the join index, rather than the original tables and their indexes, to be
consulted during query execution.

The Oracle implementation limits itself to using `bitmap
indexes <https://en.wikipedia.org/wiki/Bitmap_index>`__. A *bitmap join
index* is used for low-cardinality columns (i.e., columns containing
fewer than 300 distinct values, according to the Oracle documentation):
it combines low-cardinality columns from multiple related tables. The
example Oracle uses is that of an inventory system, where different
suppliers provide different parts. The schema has three linked tables:
two "master tables", Part and Supplier, and a "detail table", Inventory.
The last is a many-to-many table linking Supplier to Part, and contains
the most rows. Every part has a Part Type, and every supplier is based
in the US, and has a State column. There are not more than 60
states+territories in the US, and not more than 300 Part Types. The
bitmap join index is defined using a standard three-table join on the
three tables above, and specifying the Part_Type and Supplier_State
columns for the index. However, it is defined on the Inventory table,
even though the columns Part_Type and Supplier_State are "borrowed" from
Supplier and Part respectively.

As for Teradata, an Oracle bitmap join index is only utilized to answer
a query when the query's `WHERE
clause <https://en.wikipedia.org/wiki/Where_(SQL)>`__ specifies columns
limited to those that are included in the join index.

.. rubric:: Straight
   join[\ `edit <https://en.wikipedia.org/w/index.php?title=Join_(SQL)&action=edit&section=17>`__\ ]
   :name: straight-joinedit

Some database systems allow the user to force the system to read the
tables in a join in a particular order. This is used when the join
optimizer chooses to read the tables in an inefficient order. For
example, in `MySQL <https://en.wikipedia.org/wiki/MySQL>`__ the command
``STRAIGHT_JOIN`` reads the tables in exactly the order listed in the
query.
