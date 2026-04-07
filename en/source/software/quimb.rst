QUIMB
=====

Quim is a Python library for quantum information and the many-body problem.

Installation
------------

.. code-block:: bash

    module load StdEnv/2023
    module load python/3.11.5
    virtualenv --no-download $HOME/venv/quimb
    source $HOME/venv/quimb/bin/activate
    pip install --no-index quimb==1.12.1

To generate figures:

.. code-block:: bash

    pip install --no-index matplotlib==3.10.8 networkx==3.6.1

To use Jax as an optimisation engine:

.. code-block:: bash

    pip install --no-index jax==0.8.3

To use Autograd as an optimisation engine:

.. code-block:: bash

    pip install --no-index autograd==1.8.0
