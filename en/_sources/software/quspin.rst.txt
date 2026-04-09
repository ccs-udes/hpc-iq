QuSpin
======

QuSpin is an `Open Source` Python package for exact diagonalisation and
quantum dynamics of arbitrary bosons, fermions, and spin many-body systems. It
supports a variety of user-defined symmetries in one and higher dimensional
lattice systems, and time evolution following a user-specified protocol.

Installation
------------

.. code-block:: bash

    module load StdEnv/2023
    module load python/3.11.5 scipy-stack/2024a
    virtualenv --no-download $HOME/venv/quspin
    source $HOME/venv/quspin/bin/activate
    pip install --no-index --upgrade pip
    pip install --no-index quspin==1.0.0
