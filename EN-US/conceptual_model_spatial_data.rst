.. _conceptual_model_spatial_data:

=================================
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

DIAGRAMS
========

OMT-G proposes the use of three different diagrams in the process of designing a geographic application.

The first, and more usual one, is the **class diagram**, in which all classes are specified, along with their representations and relationships. From this diagram, it is possible to derive a set of spatial integrity constraints that must be observed in the implementation.

When the class diagram indicates the need for multiple representations of any class, or when the application involves the derivation of some class from others, a **transformation diagram** must be built. In it, all transformation processes can be specified, allowing for the identification of any required methods for the implementation.

Finally, a **presentation diagram** must be built in order to provide guidelines for the visual aspect of objects in the implementation. There can be several visual aspects for any given class, which allows for the definition of a view or set of views for
each application or group of users.

Class Diagram
=============

In OMT-G, the class diagram is used to describe the structure and contents of a geographic database. It contains specific elements of the structure of the database, in special object classes and their relationships, and no transformations or other dynamic processes are considered.

**Class Structure**

The classes defined by the OMT-G model represent the three main groups of data (continuous, discrete, and non-spatial) that can be found in geographic applications, thereby allowing for an integrated view of the modeled space. The classes can be georeferenced or conventional.

A **conventional class** describes a set of objects with similar properties, behavior, relationships, and semantics, and which can have some sort of relationship with spatial objects, but which do not have geometric or geographic properties.

.. image:: ./omtg/conventionalclasses.png
  :class: inline

A **georeferenced class** describes a set of objects that have spatial representation and are associated to features on Earth, assuming the fields and objects view.

.. image:: ./omtg/georreferencedclasses.png
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

.. image:: ./omtg/geoobjectclass.png
  :class: inline

A **geo-object with geometry and topology** represents objects which have, in addition to geometric properties, topological connectivity properties, and are specifically suited to the representation of spatial network structures, such as water supply systems, electrical distribution systems, or road networks.

.. image:: ./omtg/geoobjecttopology.png
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
  
Generalization and Specialization
---------------------------------

**Generalization** is the process of defining classes that are more general (superclasses) than classes with similar characteristics (subclasses).

**Specialization** is the inverse process, in which more specific classes are detailed from generic ones, adding new properties in the
form of attributes. Each subclass inherits attributes, operations, and associations from the superclass.

In the OMT-G model, the **generalization and specialization** abstractions apply both to georeferenced classes and conventional classes, following the definitions and notation proposed for UML, where a triangle connects a superclass to its subclasses.

Each **generalization** can have an associated discriminator, indicating which property is being abstracted by the generalization relationship.

.. image:: ./omtg/generalization.png
  :class: inline

Generalizations(spatial or not) can be specified as **total** or **partial**.

A generalization is **total** when the union of all instances of the subclasses is equivalent to the complete set of instances of the superclass. In OMT-G, the totallity is presented by a dot placed in the upper vertex of the triangle that denotes the generalization.

OMT-G also adopts the UML predefined constraint elements **disjoint** and **overlapping**, that is, in a **disjoint** relation the triangle is left blank and in a **overlapping** relation the triangle is filled.

.. image:: ./omtg/generalization_complete.png
  :class: inline
  
Aggregation
-----------

**Aggregation** is a special form of association between objects, where one of them is considered to be assembled from others.

The graphic notation used in OMT-G follows the one used by UML.

.. image:: ./omtg/umlaggregation.png
  :class: inline

An **aggregation** can occur between **conventional classes**, between **georeferenced and conventional classes** and also between **georeferenced classes**.

.. image:: ./omtg/aggregation_con_geo.png
  :class: inline

When the **aggregation** is between **georeferenced classes**, **spatial aggregation** must be used.

.. image:: ./omtg/aggregation_geo_geo.png
  :class: inline

**Spatial aggregation** is a special case of aggregation in which topological “whole-part” relationships are made explicit.

The usage of this kind of aggregation imposes spatial integrity constraints regarding the existence of the aggregated object and the corresponding sub-objects.

In spatial aggregation, also called topological “whole-part”, the geometry of each part is entirely contained within the geometry of the whole. Also, no overlapping among the parts is allowed and the geometry of the whole is fully covered by the geometry of the parts.

Cartographic Generalization
---------------------------

Generalization, in the cartographic sense, can be seen as a series of transformations that are performed over the representation of spatial information, geared towards improving readability and understanding of data.

For instance, a real world object can have several different spatial representations, according to the current viewing scale.

A city can be represented in a smallscale map as a point, and as a polygon in a large-scale map. In this sense, this paper uses the term representation in the sense of a coding of the geometry of geographic objects (involving aspects such as resolution, spatial dimension, precision, level of detail, and geometric/topologic behavior).

Cartographic generalization can occur in two representation variations: according to **geometric shape** and according to **scale**.

The variation according to **geometric shape** is used to record the simultaneous existence of multiple scale-independent representations for a class. For instance, a river can be represented by its axis, as a single line, as the space between its margins, as a polygon covered by water, or as a set of flows (directed arcs) within river sections, forming a hydrographic network.

.. image:: ./omtg/generalization_geo_shape.png
  :class: inline


Variation according to **scale** is used in the representation of different geometric aspects of a given class, each corresponding to a range of scales. A city can be represented by its political borders (a polygon) in a larger scale, and by a symbol (a point) in a smaller scale.

.. image:: ./omtg/generalization_geo_scale.png
  :class: inline

The notation used for cartographic generalization uses a square to connect the superclass to its subclasses. The subclass is connected to the square by a dashed line. As a discriminator, the word Scale is used to mean variation according to scale, and the word Shape is used to determine variation according to geometric shape. The square is blank when subclasses are disjoint and filled if subclass overlapping is allowed.

The variation according to geometric shape can also be used in the representation of classes which simultaneously have georeferenced and conventional instances. For instance, a traffic sign can exist in the database as a non-georeferenced object, such as a warehouse item, but it becomes georeferenced when installed at a particular location.

.. image:: ./omtg/generalization_geo_conv.png
  :class: inline

Transformation Diagrams
=======================

The transformation diagram proposed for OMT-G follows the UML notation for the state and activity diagrams and is used to specify transformations between classes. Even though it is used to specify transformation operations, the transformation diagram still operates at the conceptual representation level. This is because both the source and the results of the transformation are representations.

