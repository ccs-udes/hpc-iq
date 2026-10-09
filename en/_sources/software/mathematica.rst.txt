Mathematica
===========

Mathematica is a commercial software package for symbolic and numeric algebra
developed by Wolfram Research.

License
-------

Mathematica licenses are distributed by a server in the Physics Department. No
configuration is required but each user is responsible for respecting access
policies.

Usage
-----------

To list available Mathematica versions:

.. code-block:: console

    [alice@iv11 ~]$ ls /net/nfs-iq/data/software/Mathematica/
    12.1  14.0

To use Mathematica in a job script:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --time=02:00:00
    #SBATCH --cpus-per-task=2
    #SBATCH --mem=8G

    PATH="$PATH:/net/nfs-iq/data/software/Mathematica/12.1/Executables"

    wolframscript -file script.wls

To use Mathematica in an interactive job:

.. code-block:: console

    [alice@iv11 ~]$ salloc -t 2:00:00 -c 2 --mem=8G
    salloc: Granted job allocation 5843885
    salloc: Waiting for resource configuration
    salloc: Nodes cp3702 are ready for job
    [alice@cp3702 ~]$ PATH="$PATH:/net/nfs-iq/data/software/Mathematica/12.1/Executables"
    [alice@cp3702 ~]$ wolfram
    Mathematica 12.1.0 Kernel for Linux x86 (64-bit)
    Copyright 1988-2020 Wolfram Research, Inc.

    In[1]:=

Graphics
----------

Mathematica cannot be used in graphics mode on the IQ Cluster. Jobs must be done
in text mode, either with scripts (``wolframscript``) or interactively
(``wolfram``).
