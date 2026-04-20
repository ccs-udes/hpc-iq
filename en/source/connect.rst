Connecting
==========

Access to the IQ Cluster is through SSH (Secure Shell). The cluster’s address is
``hpc.iq.ccs.usherbrooke.ca``.

Récent operating systems (Linux, MacOS, Windows 11) typically include the
OpenSSH suite of tools. To connect to the cluster in command line mode, first
open a terminal window (called PowerShell in Windows). In that window, use the
``ssh`` command to connect:

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
     |_   _/ __ \   Grappe IQ / IQ Cluster
       | || |  | |  hpc.iq.ccs.usherbrooke.ca
       | || |  | |
      _| || |__| |  Documentation fr: https://ccs-udes.github.io/hpc-iq/fr
     |_____\___\_\                en: https://ccs-udes.github.io/hpc-iq/en
                       Support fr/en: olivier.fisette@usherbrooke.ca
                                      michel.l.barrette@usherbrooke.ca (backup)

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

It is preferable to use an SSH key pair to connect rather than your password.
This provides added security in addition to being more practical. If you do not
already have a key pair, you can generate one from the terminal on your computer
with:

.. code-block:: console

    $ ssh-keygen
    Generating public/private ed25519 key pair.
    Enter file in which to save the key (/home/alice/.ssh/id_ed25519):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /home/alice/.ssh/id_ed25519.
    Your public key has been saved in /home/alice/.ssh/id_ed25519.pub.
    The key fingerprint is:
    SHA256:GLyOJ2mS7f7d4MPEGu00sa34oSmr7/cTJremUD/G2hI alice@laptop
    The key's randomart image is:
    +--[ED25519 256]--+
    |                 |
    |     .           |
    |      o          |
    |       +.        |
    |      +oS+       |
    |   o =Eo@ .      |
    |  o B o&B=       |
    |   +.+=OO=       |
    |  .=*=***o.      |
    +----[SHA256]-----+

When prompted, enter a secure passphrase: at least 12 characters, including
lowercase and uppercase letters, digits, and special characters. This phrase
will be used to encrypt your private key.

Once your SSH key pair has been generated, configure your public key on the IQ
Cluster:

.. code-block:: console

    $ ssh-copy-id hpc.iq.ccs.usherbrooke.ca
    /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ssh-add -L
    /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
    /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
    (alice@hpc.iq.ccs.usherbrooke.ca) Password:
    (alice@hpc.iq.ccs.usherbrooke.ca) Duo two-factor login for alice

    Enter a passcode or select one of the following options:

     1. Duo Push to Samsung S23 Alice (Android)

    Passcode or option (1-1):

    Number of key(s) added: 1

In order to copy your public key to the server, ``ssh-copy-id`` will prompt you
to authenticate with your CCDB password and a Duo second factor.

You will no longer need to use your password for subsequent connections: your
private key will be used instead. You will, however, need to input your
passphrase to decrypt that key. If you use a password manager (e.g. Windows
Credential Manager, MacOS Passwords, SSH Agent), you will only need to decrypt
your key once per session.

.. note::
    - The term “passphrase” is used in cryptography as an analogy with
      passwords. For your SSH key pair, it is however not necessary for this
      “phrase” to be any longer than a typical secure password.
    - The IQ Cluster does not use the SSH public keys configured in your CCDB
      account. You must configure your key with ``ssh-copy-id`` as explained
      above.
    - SSH keys improve security by avoiding to expose your password. Thus, even
      if the IQ Cluster was compromised, your CCDB account would remain secure.

.. seealso::
    - `SSH Keys <https://docs.alliancecan.ca/wiki/SSH_Keys/en>`_ (Alliance
      technical documentation)

OpenSSH configuration
---------------------

The OpenSSH tools can be configured on your computer using the
``$HOME/.ssh/config`` configuration file, which you can edit with a text editor
(e.g. Nano, Notepad). If you are configuring OpenSSH for the first time, you
will need to create this file.

Simplifying the connection command
----------------------------------

We recommend to create a shortcut to avoid having to type the full
``hpc.iq.ccs.usherbrooke.ca`` address each time you connect:

.. code-block::

    Host iq
    Hostname hpc.iq.ccs.usherbrooke.ca

If your CCDB username is not also your username on your computer, add the
``User`` option:

.. code-block::
    :emphasize-lines: 3

    Host iq
    Hostname hpc.iq.ccs.usherbrooke.ca
    User alice

With this configuration, you should be able to connect to the IQ Cluster with
just:

.. code-block:: console

    $ ssh iq

Avoiding repetitive MFA prompts
'''''''''''''''''''''''''''''''

Duo multifactor authentification prompts for a second factor at each connection.
To avoid this torture, we recommend to activate the OpenSSH multiplexer. Thus,
an authenticated connection will be reused when you establish a new session. You
will therefore not have to use a Duo second factor or even decrypt your
passphrase more than once.

Add the following ``Control..`` options:

.. code-block::
    :emphasize-lines: 3-5

    Host iq
    Hostname hpc.iq.ccs.usherbrooke.ca
    ControlPath ~/.ssh/cm-%r@%h:%p
    ControlMaster auto
    ControlPersist 60m

Avoiding unexpected disconnections
''''''''''''''''''''''''''''''''''

Some servers and networks break inactive SSH connections after a few minutes. To
avoid this annoyance, you can configure OpenSSH to periodically send a signal to
the server indicating that the connection is still active.

Add ``ServerAliveInterval`` to the start of your configuration file, before the
first ``Host`` entry:

.. code-block::
    :emphasize-lines: 1

    ServerAliveInterval 60

    Host iq
    Hostname hpc.iq.ccs.usherbrooke.ca
