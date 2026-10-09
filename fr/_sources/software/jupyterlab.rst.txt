JupyterLab
==========

JupyterLab est un environnement web de développement interactif pour les
*notebooks*, le code et les données. Sur la Grappe IQ, JupyterLab peut être
exécuté comme serveur dans une tâche de calcul soumise à l’ordonnanceur. Un
navigateur web est ensuite utilisé sur votre ordinateur local pour interagir
avec le serveur.

Suivez ce guide étape par étape pour installer JupyterLab dans un environnement
virtuel Python dans votre répertoire personnel sur la grappe, démarrer le
serveur dans une tâche de calcul, établir un tunnel SSH entre votre ordinateur
local et le nœud de calcul exécutant le serveur, puis finalement vous connecter
au serveur.

Ce guide est basé sur la page `Jupyter: Configuration avancée
<https://docs.alliancecan.ca/wiki/Advanced_Jupyter_configuration/fr>`_ de la
documentation technique de l’Alliance, mais ajusté pour la grappe IQ et simplifé
afin de couvrir l’utilisation de JupyterLab la plus typique. Si vous avez besoin
de fonctionnalités avancées telles que RStudio ou JupyterLmod, voyez la page
ci-haut.

Installer JupyterLab
--------------------

Chargez un module pour la version de Python que vous souhaitez utiliser dans
JupyterLab:

.. code-block:: console

    [alice@iv11]$ module load StdEnv/2023
    [alice@iv11]$ module load python/3.11.5

Créez un environnement virtuel et installez-y JupyterLab:

.. code-block:: console

    [alice@iv11]$ virtualenv --no-download $HOME/venv/jupyter-py311
    [alice@iv11]$ source $HOME/venv/jupyter-py311/bin/activate
    [alice@iv11]$ pip install --no-index --upgrade pip
    [alice@iv11]$ pip install --no-index jupyterlab

Démarrer le serveur
-------------------

Créez un script ``jupyter-job.sh`` qui démarre le serveur :

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

Soumettez cette tâche à l’ordonnanceur :

.. code-block:: console

    [alice@iv11]$ sbatch jupyter-job.sh
    Submitted batch job 284419

You pouvez outrepasser les options données dans le script avec des arguments en
ligne de commande. Cela est pratique si vos besoins changent d’une session à
l’autre. Par exemple :

.. code-block:: console

    [alice@iv11]$ sbatch --time=8:00:00 --mem-per-cpu=8G jupyter-job.sh
    Submitted batch job 284421

Établir un tunnel SSH
-----------------------

Vérifiez avec ``sq`` que votre tâche a démarré :

.. code-block:: console

    [alice@iv11]$ sq
          JOBID     USER    PARTITION           NAME  ST  TIME_LEFT NODES CPUS TRES_PER_N MIN_MEM NODELIST (REASON)
         284421    alice      iq-main        jupyter   R    7:58:02     1    2        N/A      8G cp3702 (None)

Consultez le fichier de sortie de la tâche pour vous assurez que le serveur
lui-même a démarré :

.. code-block:: console

    [alice@iv11]$ cat slurm-284421.out
    ...
    To access the server, open this file in a browser:
        file:///home/alice/.local/share/jupyter/runtime/jpserver-2454410-open.html
    Or copy and paste one of these URLs:
        http://cp3702.m:8889/lab?token=c3178f7357e6a5bb78a5df56b4cd6672034850719b937410
        http://127.0.0.1:8889/lab?token=c3178f7357e6a5bb78a5df56b4cd6672034850719b937410
    ...

Notez le nœud de calcul et le port du serveur, ici ``cp3702`` et 8889,
respectivement.

Puisqu’il n’est pas possible de se connecter directement à un nœud de calcul à
partir de l’extérieur de la grappe, vous devez d’abord transférer le port
utilisé par le serveur vers votre ordinateur local. Cela devrait être fait avec
un tunnel SSH. Pour transférer le port distant 8889 sur ``cp3702`` vers le port
local 8888 sur votre ordinateur, en passant à travers le nœud de connexion de la
grappe, utilisez la commande suivante *sur votre ordinateur local* :

.. code-block:: console

    $ ssh -N -S none -L 8888:cp3702:8889 alice@hpc.iq.ccs.usherbrooke.ca

L’option ``-L`` établit le tunnel. ``-N`` est utilisée pour ne pas exécuter de
commande distante ; nous voulons seulement établir le tunnel. ``-S none`` évite
le partage de connexions (``ControlMaster``), qui interfère avec les tunnels.

Une fois établi, le tunnel restera actif jusqu’à ce que vous interrompiez la
commande ``ssh``, e.g. avec ``Control+C``.

Se connecter au serveur
-----------------------

Dans votre navigateur web, entrez l’adresse 127.0.0.1:8888, où 8888 est le port
*local* que vous avez utilisé pour établir le tunnel SSH.

Lors de votre première connexion, vous devrez copier/coller le jeton
d’authentification donné dans le fichier de sortie de la tâche. Dans l’exemple
ci-dessus, il s’agit de ``c3178f7357e6a5bb78a5df56b4cd6672034850719b937410``.
Vous pouvez optionnellement choisir un mot de passe à utiliser plutôt qu’un
jeton aléatoire dans vos sessions futures. Vous pouvez également sauvegarder ce
mot de passer dans le gestionnaire de mots de passe de votre navigateur.

Se reconnecter au serveur
-------------------------

Si vous êtes déconnecté de JupyterLab avant d’avoir terminé votre travail, vous
pouvez rétablir la connection en créant un nouveau tunnel SSH. Le serveur
lui-même n’a pas besoin d’être redémarré car il continue de s’exécuter dans la
tâche de calcul sur la grappe.

Votre système d’exploitation pourrait ne pas libérer immédiatement le port local
que vous avez utilisé pour créer le tunnel SSH initial. Dans ce cas, vous ferez
face à l’erreur suivante lorsque vous essaierez de réutiliser le port :

.. code-block:: console

    bind [127.0.0.1]:8888: Address already in use

Pour éviter ce problème, utilisez simplement un port différent pour créer le
nouveau tunnel.

Si vous êtes fréquemment déconnecté pendant que vous attendez la complétion d’un
calcul, ajoutez l’option suivante à votre fichier de configuration SSH,
``~/.ssh/config``, sur votre ordinateur local :

.. code-block::

    ServerAliveInterval 60

Cela envoie un signal au serveur distant à intervalles de 60 secondes afin de
maintenir la connexion active. Si cela ne règle pas votre problème, considérez
l’utilisation d’`autossh <https://www.harding.motd.ca/autossh/>`_ afin
d’automatiser le redémarrage de votre tunnel SSH.

Arrêter le serveur
------------------

Lorsque vous avez terminé votre travail, vous devriez éteindre le serveur
JupyterLab pour libérer les ressources allouées. La façon la plus simple de le
faire est à partir du menu, avec *File* → *Shut Down*. Alternativement, vous
pouvez annuler la tâche avec :

.. code-block::

    [alice@iv11]$ scancel 284421

Si vous fermez simplement votre navigateur ou que vous choisissez *File* → *Log
Out* dans le menu, le serveur continuera de s’exécuter sur la grappe avec les
ressources allouées.
