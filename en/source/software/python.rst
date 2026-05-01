Python
======

The information below is complementary to the `Python
<https://docs.alliancecan.ca/wiki/Python/en>`_ page in the Alliance technical
documentation.

Modules
-------

To load a Python version compatible with the default software environment:

.. code-block:: console

    [alice@iv11 ~]$ module avail python
    ------------------------------------- Core Modules --------------------------------------
       ipython-kernel/3.10              python-build-bundle/2024a (D)
       ipython-kernel/3.11       (D)    python/3.10.13            (t,3.10)
       ipython-kernel/3.12              python/3.11.5             (t,D:3.11)
       python-build-bundle/2023b        python/3.12.4             (t)

    ...

    [alice@iv11 ~]$ module load python/3.11.5

The recommended version (default module) is indicated by ``(D)``. If you need a
version which is not available in the default software environment, use `module
spider python` to list all available versions.

In addition to modules for Python itself, the Alliance software contains modules
for `Scientific Python`. Named ``scipy-stack``, they provide ``scipy`` but also
``numpy``, ``pandas``, ``matplotlib``, etc. Once your module for Python itself
is loaded:

.. code-block:: console

    [alice@iv11 ~]$ module avail scipy-stack

    ------------------------------------- Core Modules --------------------------------------
       scipy-stack/2023b (math)    scipy-stack/2024a (math)    scipy-stack/2024b (math,D)

    ...

    [alice@iv11 ~]$ module load scipy-stack/2024b

Virtual environments
--------------------

As explained in the next section, Python packages (other than `Scientific
Python`) can be installed with the ``pip`` command, either by downloading them
from PyPI or by choosing from a collection of packages precompiled by the
Alliance software team. However, we recommend to never install Python packages
without first activating a virtual environment.

Virtual Python environment are directories containing a set of packages
installed for a given job. For instance, you could have a virtual environment
to simulate quantum systems with QuTiP and another for machine learning with
TensorFlow. You could even have several virtual environments for different QuTiP
or TensorFlow versions, as required for various projects.

By systematically using a virtual environment for your Python jobs, you
facilitate software installation, debugging, and research reproducibility.
Conversely, if you install all your Python packages in the default location, you
will encounter more software compatibility issues, and installing or updating a
package can cause previously successful jobs to stop working. In addition, you
will not be able to switch between different versions of a given package.
Finally, it will be more difficult to rerun a job on another computer or to
provide instructions to reproduce your software environment and, therefore, your
research.

To conclude this long explanation, here is a short demonstration. First, load
the modules for Python and `Scientific Python`:

.. code-block:: console

    [alice@iv11 ~]$ module load python/3.11.5
    [alice@iv11 ~]$ module load scipy-stack/2024b

Then, create a virtual environment:

.. code-block:: console

    [alice@iv11 ~]$ virtualenv --no-download $HOME/venv/qutip

Activate the environment:

.. code-block:: console

    [alice@iv11 ~]$ source $HOME/venv/qutip/bin/activate

Notice that the command prompt changes to show the active virtual environment.
All actions performed by the ``pip`` command (installing, uninstalling, updating
packages) will now target the ``$HOME/venv/qutip`` directory.

The first thing to do is update ``pip``:

.. code-block:: console

    (qutip) [alice@iv11 ~]$ pip install --no-index --upgrade pip

Then, install packages, such as QuTiP :

.. code-block:: console

    (qutip) [alice@iv11 ~]$ pip install --no-index qutip==5.0.1

Finally, deactivate the environment.

.. code-block:: console

    (qutip) [alice@iv11 ~]$ deactivate

Once the environment has been created, it can be reused simply by activating it
again; there is no need to reinstall any packages. For example, the above
environment can be used in a job script with:

.. code-block:: bash

   module purge
   module load python/3.11.5
   module load scipy-stack/2024b
   source $HOME/venv/qutip/bin/activate

Installing outside a virtual environment
''''''''''''''''''''''''''''''''''''''''

If you try to install Python packages without first activating a virtual
environment, you will get the following error:

.. code-block:: console

    [alice@iv11 ~]$ pip install --no-index numpy
    ERROR: Could not find an activated virtualenv (required).

If you nonetheless wish to install a package outside a virtual environment, you
can do it with:

.. code-block:: console

    [alice@iv11 ~]$ PIP_REQUIRE_VIRTUALENV=false pip install --no-index numpy

.. note::

    This behaviour differs from that of Alliance clusters, where it is possible
    by default to install Python packages outside a virtual environment.

Precompiled Python packages
---------------------------

The ``avail_wheels`` command lists Python software packages precompiled by the
Alliance software team. These packages are optimised for HPC. For instance, to
search for Qiskit:

.. code-block:: console

    [alice@iv11 ~]$ avai l_wheels qiskit
    name    version    python    arch
    ------  ---------  --------  -------
    qiskit  1.2.4      cp38      generic

To install this precompiled version in an active virtual environment:

.. code-block:: console

    (qiskit) [alice@iv11 ~]$ pip install --no-index qiskit==1.2.4

Parallel computing with Python
------------------------------

Python code is typically not parallel. As a consequence, asking for more than
one CPU core will not automatically accelerate your jobs! You first need to
parallelise your code, either explicitly or by using parallelised library
functions, such as some of those in NumPy or SciPy.

Due to an intrinsic limitation, the “global interpreter lock”, Python code
cannot be parallelised using the shared memory model. However, there are
alternatives. One is to create a C/C++ Python extension using a parallel
programming library such as OpenMP. Another is to use the distributed memory
model with multiple Python processes. To do so, you can use the
``multiprocessing`` module, or a library such as `mpi4py
<https://mpi4py.readthedocs.io/en/stable/>`_ (message passing) or `Dask
<https://www.dask.org/>`_ (distributed computing).

.. _python-fils-label:

Thread oversubscription
'''''''''''''''''''''''

A common problem when dealing with parallelism in Python is thread
oversubscription: the number of execution threads started in a job is greater
than the number of allocated CPU cores. The ``multiprocessing`` module, in
particular, starts by default as many threads as there are CPU cores, with no
regards to whether or not these cores are accessible. For example, by default,
``multiprocessing`` would start 64 execution threads when used in a job
allocated to an IQ Cluster CPU node, even if you requested only 2, 4, or 8
cores.

This problem is compounded when using parallelised functions that also start as
many threads as there are cores (for instance ``scipy.sparse.linalg.eigsh``). To
build on the above example, in a job that uses both ``multiprocessing`` and
``eigsh``, 4096 execution threads (64 × 64) would be started by default, even
if the job only has access to 2, 4, or 8 cores. Performance is thus drastically
reduced.

To paliate this problem, you must instruct SciPy, ``multiprocessing``, Dask,
etc. to use the right number of execution threads. By adding the following
instructions to your job script (before your actual calculation), you disable
implicit parallelism in most functions, including those in SciPy, which use
OpenMP or Intel MKL in the background:

.. code-block:: bash

    export OMP_NUM_THREADS=1
    export MKL_NUM_THREADS=1

To control the number of processes started by ``multiprocessing``:

.. code-block:: python

    from multiprocessing import Pool
    from os import environ

    nprocesses = int(environ.get('SLURM_CPUS_PER_TASK', default=1))

    pool = Pool(nprocesses)

With Dask:

.. code-block:: python

    from os import environ
    from dask.distributed import LocalCluster

    nprocesses = int(environ.get('SLURM_CPUS_PER_TASK', default=1))

    cluster = LocalCluster(n_workers=nprocesses)

Conversely, if you do not use ``multiprocessing``, Dask, etc. but would rather
take advantage of SciPy’s parallel functions, set the number of execution
threads with:

.. code-block:: bash

    export OMP_NUM_THREADS=${SLURM_CPUS_PER_TASK:-1}
    export MKL_NUM_THREADS=${SLURM_CPUS_PER_TASK:-1}

.. seealso::

   - :ref:`This FAQ entry <calcul-lent-label>` discusses threads and performance
     issues in general.
