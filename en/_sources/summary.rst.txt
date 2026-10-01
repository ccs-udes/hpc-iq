Summary
=======

**Account**
    Use your `CCDB <https://ccdb.alliancecan.ca/>`_ username, password, and MFA
    devices.

**Address**
    SSH: ``hpc.iq.ccs.usherbrooke.ca``

**Home directory**
    - Location: ``/home/$USER``
    - Do not use for research data (bad input/output performance)

**Research data**
    - Location: ``/net/nfs-iq/data``
    - Use in jobs (good input/output performance)
    - Total capacity: 190T
    - Redundant storage
    - Individual data: ``/net/nfs-iq/data/$USER``
    - Group data: ``/net/nfs-iq/data/def-$SPONSOR``

**Public compute nodes**
    .. include:: nodes/public.rst

**Available resources**
    ``susage --legend``

**MP2 access**
    MP2 ``/project`` and ``/scratch`` storage are not accessible.

**Access from MP2**
    The IQ Cluster’s research data storage is accessible from MP2 at
    ``/net/nfs-iq/data``.

**Alliance software**
    - Use the ``module`` command for software packages
    - Use the ``avail_wheels`` command for Python packages
    - Default environment: ``StdEnv/2023``
    - Default optimisation target: AVX2
    - Default BLAS/LAPACK libraries: Intel MKL
