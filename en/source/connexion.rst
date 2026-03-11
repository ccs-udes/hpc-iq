Connecting
==========

Access the IQ HPC Platform is through SSH (Secure Shell). The cluster’s address
is ``hpc.iq.ccs.usherbrooke.ca``.

Récent operating systems (Linux, MacOS, Windows 11) typically include an SSH
client. To connect to the platform in command line mode, first open a terminal
window (called PowerShell in Windows). In that window, use the ``ssh`` command
to connect:

.. code-block:: console

   $ ssh alice@hpc.iq.ccs.usherbrooke.ca

Replace ``alice`` by your username.

When you connect for the first time, your program will show the server’s
encryption key’s fingerprint and ask to confirm that you wish to connect. Once
this confirmation is given, your program will store the login node’s key in your
known host list. This dialogue will look like:

.. code-block:: console

    The authenticity of host 'hpc.iq.ccs.usherbrooke.ca (204.19.23.210)' can't be established.
    ED25519 key fingerprint is SHA256:hVAo6KoqKOEbtOaBh6H6GYHAvsStPsDEcg4LXBQUP50.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
    Warning: Permanently added 'hpc.iq.ccs.usherbrooke.ca' (ED25519) to the list of known hosts.

The server’s encryption key’s fingerprint should match the one in the above
example.

Next, type in your CCDB password. Note that no characters are shown on the
screen as you type.

.. code-block:: console

   (alice@hpc.iq.ccs.usherbrooke.ca) Password:

Use a second authentication factor to complete the login process. This prompt
will change according to the device(s) you registered in CCDB. In the following
example, Alice registered an Android phone:

.. code-block:: console

    (alice@hpc.iq.ccs.usherbrooke.ca) Duo two-factor login for alice

    Enter a passcode or select one of the following options:

     1. Duo Push to Samsung S23 Alice (Android)

    Passcode or option (1-1):

By pressing ``1``, Alice would get a notification on her phone.

Finally, you will get a message indicating that the connection was successful:

.. code-block:: console

    Success. Logging you in...
    Last login: Wed Apr  9 10:45:16 2025 from 24.203.57.88
      _____ ____
     |_   _/ __ \   Plateforme CHP-IQ / IQ HPC Platform
       | || |  | |
       | || |  | |  Documentation fr: https://institut-quantique-udes.github.io/fr
      _| || |__| |                en: https://institut-quantique-udes.github.io/en
     |_____\___\_\     Support fr/en: olivier.fisette@usherbrooke.ca

    [alice@ip09 ~]$

.. seealso::
   - `SSH <https://docs.alliancecan.ca/wiki/SSH/en>`_ (Alliance technical
     documentation)

Alternative SSH clients
-----------------------

Even though an SSH client is now included with Windows (since version 10 1803),
some researchers prefer to use a third-party client, either for its
functionalities or simply by habit. Popular choices include:

* `MobaXterm <https://mobaxterm.mobatek.net/>`_
* `PuTTY <https://www.chiark.greenend.org.uk/~sgtatham/putty/>`_
* `Windows Subsystem for Linux (WSL) <https://docs.microsoft.com/en-us/windows/wsl/install>`_

We recommend MobaXterm to new users rather than PuTTY since the former offers
better functionalities. WSL is not an SSH client per say but rather a virtual
Linux system installed inside Windows. WSL provides access to basic Linux
commands, including those for SSH.

.. seealso::
   - Alliance technical documentation:
       - `Connecting with MobaXterm <https://docs.alliancecan.ca/wiki/Connecting_with_MobaXTerm/en>`_
       - `Connecting with PuTTY <https://docs.alliancecan.ca/wiki/Connecting_with_PuTTY/en>`_

SSH keys
--------

You can use an SSH key pair to connect rather than your password. This provides
added security in addition to being more practical.

The `SSH Keys <https://docs.alliancecan.ca/wiki/SSH_Keys/en>`_ page in the
Alliance technical documentation explains how to generate a key pair and install
the public key on the server. To install the key, follow the instructions in the
`Using the authorized_keys file` section rather than `Using CCDB` since the IQ
HPC Platform does not use the SSH keys in your CCDB account.
