Software
========

When you connect to the IQ Cluster, your environment contains only minimal
software. You can add more from a vast selection of `available software
<https://docs.alliancecan.ca/wiki/Available_software/en>`_. Pre-installed and
optimised for HPC, these packages are the same ones that are available on the
national clusters, which guarantees that your jobs can run reproducibly on all
clusters.

Adding software to your environment is mostly done with the ``module`` command.
If you are not familiar with its usage, read `using modules
<https://docs.alliancecan.ca/wiki/Utiliser_des_modules/en>`_ first. Briefly, you
can list loaded modules with ``module list``, search for modules with ``module
spider``, and add them to your environment with ``module load``. For instance:

.. code-block:: console

    [alice@ip09 ~]$ module list

    Currently Loaded Modules:
      1) CCconfig            6) ucx/1.14.1            11) flexiblas/3.3.1
      2) gentoo/2023   (S)   7) libfabric/1.18.0      12) imkl/2023.2.0            (math)
      3) gcccore/.12.3 (H)   8) pmix/4.2.4            13) StdEnv/2023              (S)
      4) gcc/12.3      (t)   9) ucc/1.2.0             14) mii/1.1.2
      5) hwloc/2.9.1        10) openmpi/4.1.5    (m)  15) slurm-completion/23.02.7

      Where:
       S:     Module is Sticky, requires --force to unload or purge
       m:     MPI implementations / Implémentations MPI
       math:  Mathematical libraries / Bibliothèques mathématiques
       t:     Tools for development / Outils de développement
       H:                Hidden Module

    [alice@ip09 ~]$ module spider gromacs

    --------------------------------------------------------------------------------------
      gromacs:
    --------------------------------------------------------------------------------------
        Description:
          GROMACS is a versatile package to perform molecular dynamics, i.e. simulate the
          Newtonian equations of motion for systems with hundreds to millions of
          particles.

         Versions:
            gromacs/2016.6
            gromacs/2020.4
            gromacs/2020.6
            gromacs/2021.2
            gromacs/2021.4
            gromacs/2021.6
            gromacs/2022.2
            gromacs/2022.3
            gromacs/2023
            gromacs/2023.2
            gromacs/2023.3
            gromacs/2023.5
            gromacs/2024.1
            gromacs/2024.4
         Other possible modules matches:
            gromacs-colvars  gromacs-cp2k  gromacs-ls  gromacs-plumed  gromacs-ramd
            gromacs-swaxs

    --------------------------------------------------------------------------------------
      To find other possible module matches execute:

          $ module -r spider '.*gromacs.*'

    --------------------------------------------------------------------------------------
      For detailed information about a specific "gromacs" package (including how to load
      the modules) use the module's full name. Note that names that have a trailing (E)
      are extensions provided by other modules. For example:

         $ module spider gromacs/2024.4
    --------------------------------------------------------------------------------------

    [alice@ip09 ~]$ module spider gromacs/2024.4

    --------------------------------------------------------------------------------------
      gromacs: gromacs/2024.4
    --------------------------------------------------------------------------------------
        Description:
          GROMACS is a versatile package to perform molecular dynamics, i.e. simulate the
          Newtonian equations of motion for systems with hundreds to millions of
          particles.

        Properties:
          Chemistry libraries/apps / Logiciels de chimie

        You will need to load all module(s) on any one of the lines below before the
        "gromacs/2024.4" module is available to load.

          StdEnv/2023  gcc/12.3  openmpi/4.1.5
          StdEnv/2023  gcc/12.3  openmpi/4.1.5  cuda/12.2

        Help:
          Description
          ===========
          GROMACS is a versatile package to perform molecular dynamics, i.e. simulate the
          Newtonian equations of motion for systems with hundreds to millions of
          particles.

          More information
          ================
           - Homepage: http://www.gromacs.org

    [alice@ip09 ~]$ module load StdEnv/2023 gcc/12.3 openmpi/4.1.5
    [alice@ip09 ~]$ module load gromacs/2024.4
    [alice@ip09 ~]$ module list

    Currently Loaded Modules:
      1) CCconfig                         10) hwloc/2.9.1
      2) gentoo/2023              (S)     11) ucx/1.14.1
      3) imkl/2023.2.0            (math)  12) libfabric/1.18.0
      4) StdEnv/2023              (S)     13) pmix/4.2.4
      5) mii/1.1.2                        14) ucc/1.2.0
      6) slurm-completion/23.02.7         15) openmpi/4.1.5    (m)
      7) gcc/12.3                 (t)     16) fftw/3.3.10      (math)
      8) flexiblas/3.3.1                  17) gromacs/2024.4   (chem)
      9) gcccore/.12.3            (H)

      Where:
       S:     Module is Sticky, requires --force to unload or purge
       m:     MPI implementations / Implémentations MPI
       math:  Mathematical libraries / Bibliothèques mathématiques
       t:     Tools for development / Outils de développement
       chem:  Chemistry libraries/apps / Logiciels de chimie
       H:                Hidden Module

Software environment version
----------------------------

The Alliance team in charge of software periodically releases new versions of
the environment, such as ``StdEnv/2023`` or ``StdEnv/2020``. On the IQ Cluster,
``StdEnv/2023`` is the environment initially loaded when you connect. The other
environments are available and can be loaded. For instance:

.. code-block:: console

    [alice@ip09 ~]$ module load StdEnv/2020

    Inactive Modules:
      1) slurm-completion

    Due to MODULEPATH changes, the following have been reloaded:
      1) mii/1.1.2

    The following have been reloaded with a version change:
      1) StdEnv/2023 => StdEnv/2020           5) libfabric/1.18.0 => libfabric/1.10.1
      2) gcccore/.12.3 => gcccore/.9.3.0      6) openmpi/4.1.5 => openmpi/4.0.3
      3) gentoo/2023 => gentoo/2020           7) ucx/1.14.1 => ucx/1.8.0
      4) imkl/2023.2.0 => imkl/2020.1.217

Optimisation target
-------------------

Alliance software available on the IQ Cluster is optimised for x86 processors
that support the AVX2 instruction set. It was not compiled to use more recent
instruction sets such as AVX512. This is the best choice to support both the
Intel and AMD CPUs on the compute nodes while ensuring good performance. If you
compile your code with GCC, the corresponding optimisation option is
``-march=core-avx2``. With Intel compilers, use ``-xCORE-AVX2``.

We do not recommend to use a different architecture (e.g. ``module load
arch/avx512``) or to compile your code with a different option (e.g.
``-march=native`` or ``-xHost``) since that can lead to compatibility issues.

BLAS/LAPACK libraries
---------------------

The Alliance software offers several implementations of the `BLAS and LAPACK
<https://docs.alliancecan.ca/wiki/BLAS_and_LAPACK/en>`_ libraries for linear
algebra. Intel MKL is used by default on the IQ Cluster and most Alliance
clusters.

Software guides
---------------

The following pages detail the use of specific software packages. Some are also
available on the national clusters, in which case the focus is on the
specificities of using that software on the IQ Cluster.

.. toctree::
   :maxdepth: 1

   ansys
   mathematica
   pyqcm
   python
   qiskit
   quimb
   quspin
