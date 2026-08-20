Pyqcm
=====

Pyqcm is a Python library developed by David Sénéchal, implementing quantum
cluster methods for highly correlated systems.

Pyqcm is available as a precompiled Python package:

.. code-block:: bash

    module load StdEnv/2023
    module load python/3.11.5 scipy-stack/2025a nlopt/2.10.0
    virtualenv --no-download $HOME/venv/qcm
    source $HOME/venv/qcm/bin/activate
    pip install --no-index --upgrade pip
    pip install --no-index pyqcm==2.12.1

For a custom installation, for instance a development version or to experiment
with options:

.. code-block:: bash

    git clone https://bitbucket.org/dsenechQCM/qcm_wed
    cd qcm_wed
    module load StdEnv/2023 gcc/12.3
    module load python/3.11.5 scipy-stack/2025a
    module load flexiblas/3.3.1 eigen/3.4.0 cuba/4.2.2 primme/3.2 nlopt/2.10.0
    virtualenv --no-download $HOME/venv/qcm-dev
    source $HOME/venv/qcm-dev/bin/activate
    pip install --no-index --upgrade pip
    export CMAKE_ARGS="-DEIGEN_HAMILTONIAN=1 -DWITH_PRIMME=1 -DBLA_VENDOR=FlexiBLAS -DPRIMME_DIR=$EBROOTPRIMME -DCUBA_DIR=$EBROOTCUBA -DWITH_GF_OPT_KERNEL=0"
    pip install . --no-index

.. warning::

    Pyqcm offers several methods for parallel computing, including OpenMP
    multi-threading. If Pyqcm is compiled with GCC but using MKL as the BLAS
    library with Intel OpenMP multi-threading, two OpenMP libraries will be used
    at the same time: GCC’s and Intel’s. This results in a incorrect number of
    execution threads: if :math:`n` threads are requested, :math:`n \times n`
    are created. This significantly slows down computations.

    To avoid this problem, compile Pyqcm with the FlexiBLAS interface.

.. warning::

    If you use local CPU-specific optimisations (e.g. ``-march-native`` or
    ``-xHost``) or if you enable ``-DWITH_GF_OPT_KERNEL=1``, make sure to
    compile in an interactive job on the same compute node where you will use
    Pyqcm. A compilation done on the login node might be incompatible with some
    compute nodes, resulting in “illegal instruction” errors.

.. note::

    The Pyqcm authors `report low performance with BLIS
    <https://qcm-wed.readthedocs.io/en/stable/parallel.html#numerical-integration>`_.
    On the IQ Cluster, Intel MKL is used by default.

.. seealso::

    - `Pyqcm documentation <https://dsenech.github.io/qcm_wed_doc/>`_
