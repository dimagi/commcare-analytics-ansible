Production Environments
=======================

This page outlines best practices around using this repository to create
and manage a production environment.


Choose a location for the control
---------------------------------

While it is possible to run the Ansible playbooks from anywhere, it is
recommended to run them *on the server you are setting up*. This will
ensure consistency and also streamline the installation process.

These instructions assume a setup where the Ansible control and
playbooks are run on the server being set up.


Set up user accounts
--------------------

Log into the server and create an account for the Ansible user to
use. On an AWS EC2 instance, it is fine to use the default user, "ubuntu", in which case no action is required. To create a user named "ansible"::

    $ sudo adduser ansible


Clone this repository
---------------------

::

    $ git clone https://github.com/dimagi/commcare-analytics-ansible.git


Initialize environment directory
--------------------------------

Environments live in the ``environments/`` folder.

To add a new environment you can follow the steps below, replacing
"your_environment" with your environment name.

First create a new environment folder for your environment.
This is where your project-specific configuration will live. ::

    $ export ENV=your_environment
    $ cp -r environments/example environments/$ENV


Update inventory files
----------------------

Edit the ``inventory.ini``, ``vars.yml``, and ``vault.yml`` files with
your project-specific details.


Ansible Vault
-------------

Production environments should use
`Ansible Vault <https://docs.ansible.com/ansible/latest/user_guide/vault.html>`_
to manage secrets. That page has lots of details about editing and using
files with Vault.

The example environment includes a vault file which you can edit. When
you're done adding the necessary secrets, you must encrypt the vault
file. It will ask you for a password when you run the encrypt
command::

    $ ansible-vault encrypt vault.yml

You can generate a good random key from a command line::

    $ openssl rand -base64 48

Your ``vault.yml`` file should now be replaced by a digested value and
no secret data should be revealed. You can edit the secret values again
using the following::

    $ ansible-vault edit --ask-vault-password \
        environments/$ENV/vault.yml

Store your vault password in a password manager like
`1Password <https://1password.com/>`_ or
`KeePassXC <https://keepassxc.org/>`_.


SSH access
----------

If you are running on AWS, copy the AWS private key to
``~/.ssh/myproject.pem`` on your local machine.

You may also need to change permissions on it. ::

    $ chmod 400 ~/.ssh/myproject.pem

Test it's working::

    $ ssh -i ~/.ssh/myproject.pem ubuntu@my.server.ip


Set hostname
------------

*On the remote server* ::

    $ sudo hostnamectl set-hostname myproject-server


Run Installation
----------------

::

    $ ansible-galaxy install -r requirements.yml
    $ ansible-playbook -i environments/$ENV/inventory.ini \
        -e @environments/$ENV/vault.yml \
        -e @environments/$ENV/vars.yml \
        -u ubuntu \
        --ask-vault-password \
        commcare_analytics.yml

This should install everything required to run CommCare Analytics.

..
    TODO: Make that true


Setting up HTTPS
----------------

HTTPS set up is currently not supported by this tool. To set up SSL,
login to your machine and install certbot::

    $ sudo apt install certbot python3-certbot-nginx

Then run::

    $ sudo certbot --nginx

and follow the prompts.

If you have multiple subdomains, you'll need to repeat this process for
each one. You may also need to open up port 443 on AWS or your
firewall.

.. important::

    After setting up HTTPS, set ``ssl_enabled=yes`` and
    ``superset_ssl_enabled=yes`` in your ``vars.yml`` file, otherwise
    running a full `ansible-playbook` will undo the changes!


Other tasks
-----------

Some other things you might want to do on production.

Setting up passwordless SSH
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Create a key pair. Accept the default filename. When prompted for a
passphrase, hit Enter for no passphrase. Append the public key to the
``authorized_keys`` file. ::

    $ ssh-keygen
    $ cd ~/.ssh/
    $ cat id_rsa.pub >> authorized_keys
