Connexion
=========

L’accès à la plateforme CHP-IQ se fait par SSH (Secure Shell). L’adresse de la
grappe est ``hpc.iq.ccs.usherbrooke.ca``.

Les systèmes d’exploitation récents (Linux, MacOS, Windows 11) incluent
habituellement un client SSH. Pour vous connecter à la plateforme en mode ligne
de commande, ouvrez d’abord une fenêtre de terminal (nommé PowerShell dans
Windows). Dans cette fenêtre, utilisez la commande ``ssh`` pour établir une
connexion :

.. code-block:: console

   $ ssh alice@hpc.iq.ccs.usherbrooke.ca

Remplacez ``alice`` par votre nom d’utilisateur.

Lorsque vous vous connectez pour la première fois, votre programme affichera
l’empreinte de la clé de chiffrement du serveur et vous demandera de confirmer
que vous souhaitez vous connecter. Une fois cette confirmation donnée, votre
programme enregistrera la clé du nœud de connexion dans la liste des hôtes
connus. Ce dialogue prendra la forme suivante :

.. code-block:: console

    The authenticity of host 'hpc.iq.ccs.usherbrooke.ca (204.19.23.210)' can't be established.
    ED25519 key fingerprint is SHA256:hVAo6KoqKOEbtOaBh6H6GYHAvsStPsDEcg4LXBQUP50.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added 'hpc.iq.ccs.usherbrooke.ca' (ED25519) to the list of known hosts.

L’empreinte de la clé du serveur devrait correspondre à celle donnée dans
l’exemple ci-haut.

Entrez ensuite votre mot de passe CCDB. Notez qu’aucun caractère ne s’affichera
à l’écran pendant la saisie de votre mot de passe.

.. code-block:: console

   (alice@hpc.iq.ccs.usherbrooke.ca) Password:

Utilisez un second facteur d’authentification pour compléter le processus de
connexion. Cette invite différera selon le ou les appareils que vous avez
enregistrés dans CCDB. Dans l’exemple suivant, Alice a enregistré un téléphone
Android :

.. code-block:: console

    (alice@hpc.iq.ccs.usherbrooke.ca) Duo two-factor login for alice

    Enter a passcode or select one of the following options:

     1. Duo Push to Samsung S23 Alice (Android)

    Passcode or option (1-1):

En tapant ``1``, Alice obtiendrait une notification sur son téléphone.

Finalement, vous obtiendrez un message indiquant que la connexion a été
établie :

.. code-block:: console

    Success. Logging you in...
    Last login: Wed Apr  9 10:45:16 2025 from 24.203.57.88
      _____ ____
     |_   _/ __ \   Plateforme CHP-IQ / IQ HPC Platform
       | || |  | |
       | || |  | |  Documentation fr: https://institut-quantique-udes.github.io/fr
      _| || |__| |                en: https://institut-quantique-udes.github.io/en
     |_____\___\_\     Support fr/en: olivier.fisette@usherbrooke.ca
                                      michel.l.barrette@usherbrooke.ca (backup)

    [alice@ip09 ~]$

.. seealso::
   - `SSH <https://docs.alliancecan.ca/wiki/SSH/fr>`_ (documentation technique
     de l’Alliance)

Clients SSH alternatifs
-----------------------

Bien qu’un client SSH soit maintenant inclus avec Windows (depuis la version 10
1803), certains chercheurs préfèrent utiliser un client tierce partie pour
bénéficier de fonctionnalités supplémentaires ou simplement par habitude. Des
choix populaires sont :

* `MobaXterm <https://mobaxterm.mobatek.net/>`_
* `PuTTY <https://www.chiark.greenend.org.uk/~sgtatham/putty/>`_
* `Windows Subsystem for Linux (WSL) <https://docs.microsoft.com/en-us/windows/wsl/install>`_

Nous recommandons MobaXterm aux nouveaux utilisateurs plutôt que PuTTY puisque
le premier offre davantage de fonctionnalités. Pour sa part, WSL n’est pas un
client SSH proprement dit mais plutôt un système Linux virtuel installé à
l’intérieur de Windows. WSL donne accès aux commandes Linux de base, incluant
les commandes pour SSH.

.. seealso::
   - Documentation technique de l’Alliance :
       - `Connexion à un serveur avec MobaXterm <https://docs.alliancecan.ca/wiki/Connecting_with_MobaXTerm/fr>`_
       - `Connexion à un serveur avec PuTTY <https://docs.alliancecan.ca/wiki/Connecting_with_PuTTY/fr>`_

Clés SSH
--------

Vous pouvez utiliser une paire de clés SSH pour vous connecter plutôt que votre
mot de passe. Cela améliore la sécurité tout en étant plus pratique.

La page `Clés SSH <https://docs.alliancecan.ca/wiki/SSH_Keys/fr>`_ de la
documentation technique de l’Alliance explique comment générer une paire de clés
et installer la clé publique sur le serveur. Pour l’installation, suivez les
instructions de la section `Par le fichier authorized_keys` plutôt que `Via
CCDB` puisque la plateforme CHP-IQ n’utilise pas les clés SSH de votre compte
CCDB.
