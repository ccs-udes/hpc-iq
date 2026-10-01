JupyterLab
==========

JupyterLab is a web-based interactive environment environment for notebooks,
code, and data. On the IQ Cluster, JupyterLab can be run as a server in a
compute job submitted to the scheduler. A web browser is then used on your local
computer to interface with the server.

Follow this step-by-step guide to install JupyterLab in a Python virtual
environment in your home directory on the cluster, start the server in a
compute job, establish an SSH tunnel between your local computer and the compute
node running the server, and connect to the server.

This guide is based on the `Advanced Jupyter configuration
<https://docs.alliancecan.ca/wiki/Advanced_Jupyter_configuration/en>`_ page from
the Alliance technical documentation, but is tailored for the IQ Cluster and
simplified to cover the most typical JupyterLab usage. If you need advanced
features, such as RStudio or JupyterLmod, please see the above page.

Install JupyterLab
------------------

Load a module for the version of Python you wish to use in JupyterLab:

.. code-block:: console

    [alice@iv11]$ module load StdEnv/2023
    [alice@iv11]$ module load python/3.11.5

Create a virtual environment and install JupyterLab:

.. code-block:: console

    [alice@iv11]$ virtualenv --no-download $HOME/venv/jupyter-py311
    [alice@iv11]$ source $HOME/venv/jupyter-py311/bin/activate
    [alice@iv11]$ pip install --no-index --upgrade pip
    [alice@iv11]$ pip install --no-index jupyterlab

Start the server
----------------

Create a ``jupyter-job.sh`` script that starts the server:

.. code-block:: bash

    #!/bin/bash

    #SBATCH --job-name=jupyter
    #SBATCH --partition=iq-main
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=2
    #SBATCH --mem-per-cpu=1G
    #SBATCH --time=4:00:00

    module load StdEnv/2023
    module load python/3.11.5

    source $HOME/venv/jupyter-py311/bin/activate

    unset XDG_RUNTIME_DIR

    jupyter lab --ip $(hostname) --no-browser

Submit the job to the scheduler:

.. code-block:: console

    [alice@iv11]$ sbatch jupyter-job.sh
    Submitted batch job 284419

You can override the options given in the script with command-line arguments.
This is useful if your requirements change between sessions. For instance:

.. code-block:: console

    [alice@iv11]$ sbatch --time=8:00:00 --mem-per-cpu=8G jupyter-job.sh
    Submitted batch job 284421

Establish an SSH tunnel
-----------------------

Check with ``sq`` that your job is running:

.. code-block:: console

    [alice@iv11]$ sq
          JOBID     USER    PARTITION           NAME  ST  TIME_LEFT NODES CPUS TRES_PER_N MIN_MEM NODELIST (REASON)
         284421    alice      iq-main        jupyter   R    7:58:02     1    2        N/A      8G cp3702 (None)

Check the job output file to make sure the server itself is running:

.. code-block:: console

    [alice@iv11]$ cat slurm-284421.out
    ...
    To access the server, open this file in a browser:
        file:///home/alice/.local/share/jupyter/runtime/jpserver-2454410-open.html
    Or copy and paste one of these URLs:
        http://cp3702.m:8889/lab?token=c3178f7357e6a5bb78a5df56b4cd6672034850719b937410
        http://127.0.0.1:8889/lab?token=c3178f7357e6a5bb78a5df56b4cd6672034850719b937410
    ...

Note the compute node and the server port, here ``cp3702`` and 8889,
respectively.

Since it is not possible to directly connect to a compute node from outside the
cluster, you must first forward the port used by the server to your local
computer. This should be done with an SSH tunnel. To forward remote port 8889 on
``cp3702`` to local port 8888 on your computer, going through the cluster login
node, use the following command *on your local computer*:

.. code-block:: console

    $ ssh -N -S none -L 8888:cp3702:8889 alice@hpc.iq.ccs.usherbrooke.ca

The ``-L`` option establishes the tunnel. ``-N`` is used to not execute a remote
command; we only want to establish the tunnel. ``-S none`` avoids connection
sharing (``ControlMaster``), which interferes with tunnels.

Once established, the tunnel will remain active until you interrupt the ``ssh``
command, e.g. with ``Control+C``.

Connect to the server
---------------------

In your web browser, enter the address 127.0.0.1:8888, where 8888 is the
*local* port you used to establish the SSH tunnel.

The first time you connect, you must copy/paste the authentication token shown
in the job output file. In the above example, this is
``c3178f7357e6a5bb78a5df56b4cd6672034850719b937410``. You have the option to set
a server password to use instead of a random token in future sessions. You can
also save this optional password in your browser’s password manager.

Reconnect to the server
-----------------------

If you are disconnected from JupyterLab before you are finished with your work,
you can reestablish the connection by creating a new SSH tunnel. The server
itself does not need to be restarted since it keeps running in the compute job
on the cluster.

Your operating system might not immediately free up the local port you used to
create the original SSH tunnel. If that is the case, you will encounter the
following error when you try to reuse the port:

.. code-block:: console

    bind [127.0.0.1]:8888: Address already in use

To avoid this issue, simply use a different local port to create the new tunnel.

If you experience frequent disconnections while waiting for computations to
finish, add the following option to your SSH configuration file,
``~/.ssh/config``, on your local computer:

.. code-block::

    ServerAliveInterval 60

This sends a signal to the remote server every 60 seconds to keep the connection
active. If this does not resolve your issue, consider using `autossh
<https://www.harding.motd.ca/autossh/>`_ to automatically restart your SSH
tunnel.

Stop the server
---------------

Once you are finished with your work, you should shut down the JupyterLab server
to free up the allocated resources. The easiest way to do so is from the menu,
with *File* → *Shut Down*. Alternatively, you can cancel the running job:

.. code-block::

    [alice@iv11]$ scancel 284421

If you merely close your browser, or if you select *File* → *Log Out* from the
menu, the server keeps running on the cluster with the allocated resources.
