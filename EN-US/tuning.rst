.. _tuning:

Tuning PostgreSQL for Spatial
=============================

PostgreSQL is a very versatile database system, capable of running efficiently in very low-resource environments and environments shared with a variety of other applications. In order to ensure it will run properly for many different environments, the default configuration is very conservative and not terribly appropriate for a high-performance production database.  Add the fact that geospatial databases have different usage patterns, and the data tend to consist of fewer, much larger records than non-geospatial databases, and you can see that the default configuration will not be totally appropriate for our purposes.

Click in this `link <https://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server>`_ for more detailed information.

All of these configuration parameters can edited in the database configuration file. On Windows, this is ``C:\Program Files\PostgreSQL\9.5\data\postgresql.conf`` file.  This is a regular text file and can be edited using Notepad or any other text editor.  The changes will not take effect until the server is restarted.

.. image:: ./tuning/conf01.png

An easier way of editing this configuration is by using the built-in ``Backend Configuration Editor``.  In pgAdmin, go to *File > Open postgresql.conf...*.  It will ask for the location of the file, and navigate to ``C:\Program Files\PostgreSQL\9.5\data\`` and then select ``postgresql.conf``.

.. image:: ./tuning/conf02.png

.. image:: ./tuning/conf03.png

This section describes some of the configuration parameters that should be adjusted for a production-ready geospatial database.  For each section, find the appropriate item in the list, double-click on the line to edit the configuration.  Change the *Value* to the recommended value as described, make sure the item is *Enabled*, the click *OK*.

------

.. note:: - These values are recommendations only; each environment will differ and testing is required to determine the optimal configuration.  But this section should get you off to a good start.

------

shared_buffers
--------------

Sets the amount of memory the database server uses for shared memory buffers.  These are shared amongst the back-end processes, as the name suggests.  The default values are typically woefully inadequate for production databases.

The shared_buffers configuration parameter determines how much memory is dedicated to PostgreSQL to use for caching data. One reason the defaults are low is because on some platforms (like older Solaris versions and SGI), having large values requires invasive action like recompiling the kernel. Even on a modern Linux system, the stock kernel will likely not allow setting shared_buffers to over 32MB without adjusting kernel settings first. (PostgreSQL 9.4 and later use a different shared memory mechanism, so kernel settings will usually not have to be adjusted there.)

If you have a system with 1GB or more of RAM, a reasonable starting value for shared_buffers is 1/4 of the memory in your system. If you have less RAM you'll have to account more carefully for how much RAM the OS is taking up; closer to 15% is more typical there. There are some workloads where even larger settings for shared_buffers are effective, but given the way PostgreSQL also relies on the operating system cache, it's unlikely you'll find using more than 40% of RAM to work better than a smaller amount.

Be aware that if your system or PostgreSQL build is 32-bit, it might not be practical to set shared_buffers above 2 to 2.5GB.

Note that on Windows, large values for shared_buffers aren't as effective, and you may find better results keeping it relatively low and using the OS cache more instead. On Windows the useful range is 64MB to 512MB.


  *Default value*: typically 32MB

  *Recommended value*: 2GB (25% of memory) for Linux System
  
  *Recommended value*: 512MB for Windows Operational System

.. image:: ./tuning/conf04.png

work_mem
--------

Defines the amount of memory that internal sorting operations and hash tables can consume before the database switches to on-disk files.  This value defines the available memory for each operation; complex queries may have several sort or hash operations running in parallel, and each connected session may be executing a query.

As such you must consider how many connections and the complexity of expected queries before increasing this value.  The benefit to increasing is that the processing of more of these operations, including ORDER BY, and DISTINCT clauses, merge and hash joins, hash-based aggregation and hash-based processing of subqueries, can be accomplished without incurring disk writes.

If you do a lot of complex sorts, and have a lot of memory, then increasing the work_mem parameter allows PostgreSQL to do larger in-memory sorts which, unsurprisingly, will be faster than disk-based equivalents.

This size is applied to each and every sort done by each user, and complex queries can use multiple working memory sort buffers. Set it to 50MB, and have 30 users submitting queries, and you are soon using 1.5GB of real memory. Furthermore, if a query involves doing merge sorts of 8 tables, that requires 8 times work_mem. You need to consider what you set max_connections to in order to size this parameter correctly. This is a setting where data warehouse systems, where users are submitting very large queries, can readily make use of many gigabytes of memory.

log_temp_files can be used to log sorts, hashes, and temp files which can be useful in figuring out if sorts are spilling to disk instead of fitting in memory. You can see sorts spilling to disk using EXPLAIN ANALYZE plans as well. For example, if you see a line like Sort Method: external merge Disk: 7526kB in the output of EXPLAIN ANALYZE, a work_mem of at least 8MB would keep the intermediate data in memory and likely improve the query response time (although it may take substantially more than 8MB to do the sort entirely in memory, as data on disk is stored in a more compact format).

  *Default value*: 1MB

  *Recommended value*: 16MB

.. image:: ./tuning/conf05.png

maintenance_work_mem
--------------------

Defines the amount of memory used for maintenance operations, including vacuuming, index and foreign key creation.  As these operations are not terribly common, the default value may be acceptable.  This parameter can alternately be increased for a single session before the execution of a number of ``CREATE INDEX`` or ``VACUUM`` calls as shown below.



.. code-block:: sql

    SET maintenance_work_mem TO '128MB';
    VACUUM ANALYZE;
    SET maintenance_work_mem TO '16MB';

..

It defaults to 64 megabytes (64MB) since version 9.4. Since only one of these operations can be executed at a time by a database session, and an installation normally doesn't have many of them running concurrently, it's safe to set this value significantly larger than work_mem. Larger settings might improve performance for vacuuming and for restoring database dumps.

  *Default value*: 64MB

  *Recommended value*: 128MB

.. image:: ./tuning/conf06.png

wal_buffers
-----------

Sets the amount of memory used for write-ahead log (WAL) data.  Write-ahead logs provide a high-performance mechanism for insuring data-integrity.  During each change command, the effects of the changes are written first to the WAL files and flushed to disk.  Only once the WAL files have been flushed will the changes be written to the data files themselves.  This allows the data files to be written to disk in an optimal and asynchronous manner while ensuring that, in the event of a crash, all data changes can be recovered from the WAL.  

The size of this buffer only needs to be large enough to hold WAL data for a single typical transaction.  While the default value is often sufficient for most data, geospatial data tends to be much larger.  Therefore, it is recommended to increase the size of this parameter.

Starting with PostgreSQL 9.1 wal_buffers defaults to being 1/32 of the size of shared_buffers, with an upper limit of 16MB. But you can try higher values that not exceed 1/32 shared_buffer.

  *Default value*: 64kB

  *Recommended value*: 16MB (1/32 shared_buffer, with an upper limit of 16MB)

.. image:: ./tuning/conf07.png

random_page_cost
----------------

This is a unit-less value that represents the cost of a random page access from disk.  This value is relative to a number of other cost parameters including sequential page access, and CPU operation costs.  While there is no magic bullet for this value, the default is generally conservative.

This setting suggests to the optimizer how long it will take your disks to seek to a random disk page, as a multiple of how long a sequential read (with a cost of 1.0) takes. If you have particularly fast disks, as commonly found with RAID arrays of SCSI disks, it may be appropriate to lower random_page_cost, which will encourage the query optimizer to use random access index scans. Some feel that 4.0 is always too large on current hardware; it's not unusual for administrators to standardize on always setting this between 2.0 and 3.0 instead. In some cases that behavior is a holdover from earlier PostgreSQL versions where having random_page_cost too high was more likely to screw up plan optimization than it is now (and setting at or below 2.0 was regularly necessary). Since these cost estimates are just that--estimates--it shouldn't hurt to try lower values.

But this not where you should start to search for plan problems. Note that random_page_cost is pretty far down this list (at the end in fact). If you are getting bad plans, this shouldn't be the first thing you look at, even though lowering this value may be effective. Instead, you should start by making sure autovacuum is working properly, that you are collecting enough statistics, and that you have correctly sized the memory parameters for your server--all the things gone over above. After you've done all those much more important things, if you're still getting bad plans then you should see if lowering random_page_cost is still useful.

This value can be set on a per-session basis using the command:

  .. code-block:: sql

    SET random_page_cost TO 2.0

  *Default value*: 4.0

  *Recommended value*: 2.0

.. image:: ./tuning/conf09.png

seq_page_cost
-------------

This is the parameter that controls the cost of a sequential page access.  This value does not generally require adjustment but the difference between this value and ``random_page_cost`` greatly affects the choices made by the query planner.  This value can also be set on a per-session basis.

  *Default value*: 1.0

  *Recommended value*: 1.0

.. image:: ./tuning/conf10.png

effective_cache_size
--------------------

Effective_cache_size should be set to an estimate of how much memory is available for disk caching by the operating system and within the database itself, after taking into account what's used by the OS itself and other applications. This is a guideline for how much memory you expect to be available in the OS and PostgreSQL buffer caches, not an allocation! This value is used only by the PostgreSQL query planner to figure out whether plans it's considering would be expected to fit in RAM or not. If it's set too low, indexes may not be used for executing queries the way you'd expect. The setting for shared_buffers is not taken into account here--only the effective_cache_size value is, so it should include memory dedicated to the database too.

Setting effective_cache_size to 1/2 of total memory would be a normal conservative setting, and 3/4 of memory is a more aggressive but still reasonable amount. You might find a better estimate by looking at your operating system's statistics. On UNIX-like systems, add the free+cached numbers from free or top to get an estimate. On Windows see the "System Cache" size in the Windows Task Manager's Performance tab. Changing this setting does not require restarting the database (HUP is enough).


  *Default value*: -

  *Recommended value*: 4GB (50-75% Memory)

Reload configuration
--------------------

After these changes are made, save changes and reload the configuration. The easiest way to do this is to restart the PostgreSQL service.

In pgAdmin, right-click the server **PostGIS (localhost:5432)** and select *Disconnect*.
  
.. image:: ./tuning/conf11.png
  
In Windows Services (``services.msc``) right-click **postgresql-x64-9.5** and select *Restart*.

.. image:: ./tuning/conf12.png
