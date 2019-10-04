.. _conceptual_model_spatial_data:


Conceptual Model for Spatial Data
=================================

According to Borges(2001), semantic and object-oriented data models, such as ER, OMT, IFO, and others, have been extensively used for modeling geographic applications.

Despite their semantic expressiveness, such models present limitations to adequately model those applications, since they do not provide appropriate primitives for representing spatial data.

Borges(2001) proposes the OMT-G model, an object oriented data model for geographic applications.

OMT-G provides primitives for modeling the geometry and the topology of spatial data, supporting different topological structures, multiple views of objects, and spatial relationships.

OMT-G also includes tools to specify transformation processes and presentation alternatives, that allow, among many other possibilities, modeling for multiple representations and multiple presentations.

In this way, it overcomes the main limitations of the existing models, like EXT. IFO, OMT EXT., GISER, Geo-OOA, GMOD and Modulo-R, for example, thus providing more adequate tools for modeling geographic applications.

The OMT-G Data Model
====================

The OMT-G model is based on three main concepts:

- Classes
- Relationships
- Spatial Integrity Constraints

Diagrams
========

OMT-G proposes the use of three different diagrams in the process of designing a geographic application.

The first, and more usual one, is the **class diagram**, in which all classes are specified, along with their representations and relationships. From this diagram, it is possible to derive a set of spatial integrity constraints that must be observed in the implementation.

When the class diagram indicates the need for multiple representations of any class, or when the application involves the derivation of some class from others, a **transformation diagram** must be built. In it, all transformation processes can be specified, allowing for the identification of any required methods for the implementation.

Finally, a **presentation diagram** must be built in order to provide guidelines for the visual aspect of objects in the implementation. There can be several visual aspects for any given class, which allows for the definition of a view or set of views for
each application or group of users.

Class Diagram
-------------

In OMT-G, the class diagram is used to describe the structure and contents of a geographic database. It contains specific elements of the structure of the database, in special object classes and their relationships, and no transformations or other dynamic processes are considered.

**Class Structure**

The classes defined by the OMT-G model represent the three main groups of data (continuous, discrete, and non-spatial) that can be found in geographic applications, thereby allowing for an integrated view of the modeled space. The classes can be georeferenced or conventional.

A **georeferenced class** describes a set of objects that have spatial representation and are associated to features on Earth, assuming the fields and objects view.

A **conventional class** describes a set of objects with similar properties, behavior, relationships, and semantics, and which can have some sort of relationship with spatial objects, but which do not have geometric or geographic properties.

.. image:: ./omtg/classes.png
  :class: inline


Georeferenced Classes
---------------------

Georeferenced classes are specialized into geo-field and geo-object classes.

Geo-Field
~~~~~~~~~

Geo-field classes represent objects and phenomena that are continuously distributed over the space, corresponding to variables such as soil type, relief, and mineral contents.

.. image:: ./omtg/geofield.png
  :class: inline

Geo-Object
~~~~~~~~~~

Geo-object classes represent individual, particular geographic objects, which can be traced back to real world elements, such as buildings, rivers, and trees.

A **geo-object with geometry class** represents objects which have only geometric properties (points, lines, and polygons), and is specialized precisely in classes named Point, Line, and Polygon, such as bus stop, curb line, and municipal limits, for example.

A **geo-object with geometry and topology** represents objects which have, in addition to geometric properties, topological connectivity properties, and are specifically suited to the representation of spatial network structures, such as water supply systems, electrical distribution systems, or road networks.

.. image:: ./omtg/geoobject.png
  :class: inline

Relationships
-------------

OMT-G represents the three types of relationship that can occur between its classes:

- Simple Associations
- Topological Network Relations
- Spatial relations.

Simple associations represent structural relationships between objects of different classes, conventional as well as georeferenced. 

Spatial relations represent the topologic, metric, ordinal, and fuzzy relationships. Some relations can be derived automatically, from the geometry of each object, during the execution of data entry or spatial analysis operations.

Topologic relations are an example of this. Others need to be specified by the user, in order to allow the system to store and maintain that information. The latter are called explicit relations.

.. image:: ./omtg/relationship.png
  :class: inline

The DE-9IM model, a minimum set of spatial relation operators is identified, comprising only five spatial relations, from which all others can be specified: touch, in, cross, overlap, and disjoint.

However, sometimes a larger set is required due to cultural or semantic concepts that are familiar to the users. These include relations such as adjacent to, coincide, contain, and near, which are in fact special cases of one of the five basic relations, but deserve special treatment because of their common use in practice.

Additional constraints can be formulated in case some additional relation is required by the application. These include any kind of directional or relative spatial relations, such as north of, left of, in front of, or above.

Cardinality
-----------

Relationships are characterized by their cardinality. The notation for cardinality adopted by OMT-G is the same used by UML.

.. image:: ./omtg/cardinality.png
  :class: inline
  
  
