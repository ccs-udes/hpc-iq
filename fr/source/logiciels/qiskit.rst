Qiskit
======

Qiskit est une plateforme logicielle d’IBM pour le calcul quantique.

Installation
------------

.. code-block:: bash

    module load StdEnv/2023
    module load python/3.11.5 symengine/0.13.0
    virtualenv --no-download $HOME/venv/qiskit
    source $HOME/venv/qiskit/bin/activate
    pip install --no-index --upgrade pip
    pip install --no-index qiskit==2.3.1
    # Optional Aer simulator
    pip install --no-index qiskit-aer==0.17.2
    # Optional GPU support in Aer
    pip install --no-index qiskit-aer-gpu==0.17.2
    # Optional package for natural science problems
    pip install --no-index qiskit-nature==0.7.2
