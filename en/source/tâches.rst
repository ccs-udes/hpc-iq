Running jobs
============

.. note::

   This page covers the specifics of the IQ HPC Platform. If you are not
   familiar with job submission and management commands (such as ``sbatch``,
   ``salloc``, ``squeue``), read `Running jobs
   <https://docs.alliancecan.ca/wiki/Running_jobs/fr>`_ from the Alliance
   technical documentation first or check our page about learning :doc:`HPC
   <apprentissage/chp>`.

Login nodes
-----------

Use the login node (``ip09``) to prepare your jobs. It is however forbidden to
run jobs directly on this node! Cluster login nodes do not have the necessary
computing power to run jobs. In addition, running a job on a login node can slow
it down considerably, which negatively impacts all connected researchers. All
jobs must be submitted to the scheduler using the appropriate commands:
``sbatch``, ``salloc``, ``srun``.

Input/output
------------

Use the storage at ``/net/nfs-iq/data`` to read and write research data in your
jobs. This location offers better performance than your home directory.

Public nodes
------------

Jobs must be submitted to the ``iq-main`` partition, which is the default. The
partition can nevertheless be explicited using the ``--partition`` option or its
short form ``-p``. For example, in a job script:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --job-name=my-job
    #SBATCH --partition=iq-main

    ...

The Platform offers three compute nodes with 96 CPU cores and 500G of memory.
Maximum job duration is seven days. Refer to the :ref:`public nodes
<public-label>` table for more details.

GPU jobs
''''''''

The Platform also offers one GPU compute node with two Nvidia A40 GPUs, which
can be requested with the ``--gpus-per-node``, ``--gpus-per-task``, and
``--gres`` options. The same node has 48 CPU cores and 500G of memory.

For example, to use one GPU in an interactive job:

.. code-block:: console

    [alice@ip09 ~]$ salloc --gres=gpu

To use both GPUs in a job script:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --job-name=my-job
    #SBATCH --partition=iq-main
    #SBATCH --gpus-per-node=nvidia_a40:2

    ...

Contributed nodes
-----------------

To run a job on one or more contributed nodes to which you have access, request
the corresponding partition with ``-p`` ``--partition``. Refer to the
:ref:`contributed nodes <contrib-label>` table. For instance, use
``--partition=iq-apc`` to submit a job to David Sénéchal’s APC nodes. Maximum
job duration varies depending on the partition and is noted in the node table.

Available resources
-------------------

The ``susage`` command shows allocated or available resources in one or more
partitions. Used with no option or argument, it shows the allocated resources in
the partitions you have access to:

.. code-block:: console

    [alice@ip09 ~]$ susage
    iq-main:  nodes: (2+1)/4 cpus: 104/336 mem: 1.0T/2.0T gpus: 0/2
              ██████▓▓▓░░░░░ ████░░░░░░░░░ ███████░░░░░░░ ░░░░░░░░░
    iq-alice:                cpus: 192/192 mem: 612G/2.2T
                             █████████████ ████░░░░░░░░░░

As an example, to show available resources, with a legend, only for the
``iq-main`` partition:

.. code-block:: console

    [alice@ip09 ~]$ susage --available --legend iq-main
    iq-main: nodes: (1+1)/4 cpus: 232/336 mem: 1.0T/2.0T gpus: 2/2
             ███▓▓▓░░░░░░░░ █████████░░░░ ███████░░░░░░░ █████████

Job management
--------------

The ``squeue`` command lists all jobs in the scheduler, including all users’.
Use ``sq`` to list only your own jobs. (This last command is also available on
Alliance clusters.)

.. _tâches-actives-label:

Monitoring active jobs
''''''''''''''''''''''

When one of your jobs starts, it is important to verify that it uses allocated
resources properly. For instance, if a job has access to 4 CPU cores and 80G of
memory, is it really using these 4 cores at 100% and is memory usage in that
order of magnitude?

To verify, use ``ssh`` to  connect to a compute node allocated to your job.
There, run ``htop``, which gives an overview of CPU and memory usage. In the
following example, ``alice`` uses the output of ``sq`` to identify node
``cp1433`` before connecting to it. ``htop`` shows 4 processes running at 100%
and belonging to Alice, which matches the 4 CPUs allocated to her job.

.. code-block:: console

   [alice@ip09 ~]$ sq
             JOBID     USER      ACCOUNT           NAME  ST  TIME_LEFT NODES CPUS       GRES MIN_MEM NODELIST (REASON)
           5623630 alice    def-alice         md-job.sh   R      14:56     1    4     (null)      1G cp1433 (None)
   [alice@ip09 ~]$ ssh cp1433
   Last login: Wed Aug 21 11:16:34 2024 from ip09.m
   [alice@cp1433-mp2 ~]$ htop

       0[||||||||100.0%]    8[          0.0%]    16[          0.0%]   24[          0.0%]
       1[||||||||100.0%]    9[          0.0%]    17[|         0.7%]   25[          0.0%]
       2[||||||||100.0%]   10[          0.0%]    18[          0.0%]   26[          0.0%]
       3[||||||||100.0%]   11[          0.0%]    19[          0.0%]   27[          0.0%]
       4[          0.0%]   12[          0.0%]    20[          0.0%]   28[          0.0%]
       5[          0.0%]   13[          0.0%]    21[          0.0%]   29[          0.0%]
       6[          0.0%]   14[          0.0%]    22[          0.0%]   30[          0.0%]
       7[          0.0%]   15[          0.0%]    23[|         0.7%]   31[          0.0%]
     Mem[|||                      6.82G/252G]   Tasks: 63, 174 thr; 5 running
     Swp[                              0K/0K]   Load average: 2.40 0.71 1.22
                                             Uptime: 1 day, 20:53:58

      PID USER      PRI  NI  VIRT   RES   SHR S CPU%▽MEM%   TIME+  Command
    35160 alice      20   0  457M 97680 19588 R  99.  0.0  0:51.67 /cvmfs/soft.computecanada.
    35161 alice      20   0  454M 96376 19248 R  99.  0.0  0:51.93 /cvmfs/soft.computecanada.
    35162 alice      20   0  454M 95832 19248 R  99.  0.0  0:51.83 /cvmfs/soft.computecanada.
    35163 alice      20   0  446M 93644 19252 R 99.3  0.0  0:51.82 /cvmfs/soft.computecanada.
    35449 alice      20   0 58960  4812  3044 R  0.7  0.0  0:00.08 htop
        1 root       20   0  122M  4116  2636 S  0.0  0.0  0:47.60 /usr/lib/systemd/systemd -
     1041 root       20   0 39060  8500  8172 S  0.0  0.0  0:01.65 /usr/lib/systemd/systemd-j
     1074 root       20   0 45472  1840  1352 S  0.0  0.0  0:11.67 /usr/lib/systemd/systemd-u
     1318 root       20   0 48920  1328  1012 S  0.0  0.0  0:00.00 /usr/sbin/rdma-ndd --syste
     1393 root       16  -4 55532   860   456 S  0.0  0.0  0:00.37 /sbin/auditd
     1394 root       16  -4 55532   860   456 S  0.0  0.0  0:00.00 /sbin/auditd
     1395 root       12  -8 84556   888   740 S  0.0  0.0  0:00.39 /sbin/audispd
   F1Help  F2Setup F3SearchF4FilterF5Tree  F6SortByF7Nice -F8Nice +F9Kill  F10Quit

GPU jobs
""""""""

For GPU jobs, you must also check that they use the allocated GPU(s). To do so,
connect to the compute node and use the ``nvidia-smi`` command, which lists GPUs
and the programs using them. For example:

.. code-block:: console

   [alice@ip09 ~]$ ssh cp3705
   Last login: Wed Aug 21 13:47:44 2024 from ip09.m
   [alice@cp3705-mp2 ~]$ nvidia-smi
   Wed Aug 21 13:52:41 2024
   +-----------------------------------------------------------------------------------------+
   | NVIDIA-SMI 550.54.15              Driver Version: 550.54.15      CUDA Version: 12.4     |
   |-----------------------------------------+------------------------+----------------------+
   | GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
   |                                         |                        |               MIG M. |
   |=========================================+========================+======================|
   |   0  NVIDIA A40                     Off |   00000000:65:00.0 Off |                    0 |
   |  0%   30C    P0             81W /  300W |     370MiB /  46068MiB |      0%      Default |
   |                                         |                        |                  N/A |
   +-----------------------------------------+------------------------+----------------------+
   |   1  NVIDIA A40                     Off |   00000000:CA:00.0 Off |                    0 |
   |  0%   29C    P0             70W /  300W |     276MiB /  46068MiB |      0%      Default |
   |                                         |                        |                  N/A |
   +-----------------------------------------+------------------------+----------------------+

   +-----------------------------------------------------------------------------------------+
   | Processes:                                                                              |
   |  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
   |        ID   ID                                                               Usage      |
   |=========================================================================================|
   |    0   N/A  N/A     14734      C   gmx_mpi                                       362MiB |
   |    1   N/A  N/A     14734      C   gmx_mpi                                       268MiB |
   +-----------------------------------------------------------------------------------------+

We notice that process ``gmx_mpi`` (id 14734) is using both GPUs.

Statistics for finished jobs
''''''''''''''''''''''''''''

The ``seff`` command shows statistics about finished jobs, including their CPU
and memory efficiency. For example:

.. code-block:: console

   [alice@ip15-mp2 ~]$ seff 5623631
   Job ID: 5623631
   Cluster: mp2
   User/Group: alice/alice
   State: COMPLETED (exit code 0)
   Nodes: 1
   Cores per node: 4
   CPU Utilized: 01:00:09
   CPU Efficiency: 99.59% of 01:00:24 core-walltime
   Job Wall-clock time: 00:15:06
   Memory Utilized: 353.91 MB (estimated maximum)
   Memory Efficiency: 8.64% of 4.00 GB (1.00 GB/core)

Typically, CPU efficiency should be close to 100%. A lower efficiency indicates
that CPU time is wasted, possibly because the job is not using all allocated
resources. If the efficiency of one of your jobs is under 70%, you should not
submit other similar jobs before fixing this problem.

Memory efficiency should be at least 50%. If one of your jobs is under this
treshold, reduce the amount of requested memory for similar jobs. (If you ask
for the default amount of memory, 1G per CPU core, ignore memory efficiency
since your absolute usage is very low anyway.)

By monitoring job efficiency, you not only ensure that they run faster: you also
allow a greater number of jobs to run simultaneously, which reduces the wait
time for all researchers.
