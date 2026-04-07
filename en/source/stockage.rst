Storage
=======

Home directory
--------------

Your home directory is accessible at ``$HOME``. For instance:

.. code-block:: console

    [alice@ip09 ~]$ echo $HOME
    /home/alice

This is the right location for configuration files, your code, and software that
you install. Due to limited capacity and performance, it is not the right
location for research data and you should not use it to read and write such data
when :doc:`running jobs <tâches>`.

Research data
-------------

Research data is stored on a 190T server accessible at ``/net/nfs-iq/data``. Use
this locaton to read and write data when :doc:`running jobs <tâches>`; it offers
better IO (input/output) performance than your home directory.

To ensure safety in case of a drive failure, research data is redundant,
that is to say that two copies are stored. (The total physical capacity of the
server is 384T, roughly 2 × 190T.) Data is also backed up daily, and archived on
tape weekly.

Individual data
'''''''''''''''

Each user has their own individual space for research data, accessible at
``/net/nfs-iq/data/$USER``. For instance:

.. code-block:: console

    [alice@ip09 ~]$ echo $USER
    alice
    [alice@ip09 ~]$ cd /net/nfs-iq/data/alice

Group data
''''''''''

Research groups have a shared space accessible at
``/net/nfs-iq/data/def-$PROF``, where ``$PROF`` is the group leader’s username.
(This form is identical to that of project storage on Alliance clusters.) For
instance:

.. code-block:: console

    [alice@ip09 ~]$ echo $USER
    alice
    [alice@ip09 ~]$ cd /net/nfs-iq/data/def-alice

Local storage on compute nodes
--------------------------------------

When the scheduler starts a job, a temporary directory is created on each
allocated compute node. The ``SLURM_TMPDIR`` environment variable gives the
directory’s path. When the job ends, files in the temporary directory are
deleted.

Since ``$SLURM_TMPDIR`` is on a local disk, input/output operations are faster
than on network storage (such as ``/net/nfs-iq/data``). If a job performs many
read/write operations, it will probably run faster using ``$SLURM_TMPDIR``
rather than network storage. You must however make sure to copy your data to
network storage before your job ends.

.. seealso::
   - `Using node-local storage <https://docs.alliancecan.ca/wiki/Using_node-local_storage/en>`_
     (Alliance technical documentation)

MP2 cluster interconnection
---------------------------

Research data storage in ``/net/nfs-iq/data`` is accessible from the MP2 login
and compute nodes. MP2’s ``/project`` and ``/scratch`` storage are not
accessible from the IQ Cluster.
