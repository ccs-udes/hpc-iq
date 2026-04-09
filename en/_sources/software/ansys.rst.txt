Ansys
=====

Ansys is a software suite for engineering simulation and 3D design.

.. seealso::


   - `Ansys <https://docs.alliancecan.ca/wiki/Ansys/en>`_ (Alliance technical
     documentation)

License
-------

The IQ Cluster has a CMC Microsystems license server dedicated to Ansys. Each
user must configure their license:

#. Create the ``~/.licences/ansys.lic`` file with this content:

.. code-block::

    setenv("ANSYSLMD_LICENSE_FILE", "6624@ip39.ccs.usherbrooke.ca")
    setenv("ANSYSLI_SERVERS", "2325@ip39.ccs.usherbrooke.ca")

#. Send an email to CMC Microsystems (`mcsupport@cmc.ca`) with your full name,
   the name of the person providing you withe the license, your username on the
   IQ Cluster, and the address of the license server
   (``ip39.ccs.usherbrooke.ca``).

#. CMC Microsystem will activate your license after a few hours, or a few days
   at most. Once this is done, you can use Ansys as described below.

Modules
-------

The Ansys modules are the same as on Alliance clusters. For example:

.. code-block:: console

    [alice@ip09 ~]$ module load ansys/2023R2
