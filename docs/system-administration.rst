System Administration
=====================

Philosophy
----------

CommCare Ansible deployments use an
"`infrastructure as code <https://en.wikipedia.org/wiki/Infrastructure_as_code>`_"
philosophy which means all configuration is documented in code files.
See the sections below to access everything needed to configure the
system.


Services
--------

The complete list of services is available in the
`roles/commcare_analytics/tasks/main.yml <https://github.com/dimagi/commcare-analytics-ansible/blob/master/roles/commcare_analytics/tasks/main.yml>`_
file.

The most important ones are summarized on the
`what's installed </whats-installed/>`_ page.


Common Tasks
------------

Some of the common tasks needed to manage an environment.


Deploying Changes
^^^^^^^^^^^^^^^^^

You may wish to deploy updates to the server, for example to pull the
latest changes from the CommCare analytics code.

The command to deploy updates is::

    $ ansible-playbook -i inventories/yourapp commcare_analytics.yml
          --vault-password-file /path/to/password/file \
          -vv --tags=deploy


Accessing the Virtual Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To access the virtual environments for superset, run the following
commands::

    $ source <venv>/bin/activate
    $ source <venv>/bin/postactivate

On most servers the superset <venv> is at
``~/www/.virtualenvs/superset``.

The second command is required to set the project-specific environment
variables to the correct values.


Other Useful commands
^^^^^^^^^^^^^^^^^^^^^

+----------------------------------+------------------------------------------+
| Task                             | Command                                  |
+==================================+==========================================+
| See running supervisor processes | ``sudo supervisorctl status``            |
+----------------------------------+------------------------------------------+
| Restart a supervisor process     | ``sudo supervisorctl restart [process]`` |
+----------------------------------+------------------------------------------+
| Connect to database              | ``sudo -u postgres psql``                |
+----------------------------------+------------------------------------------+
| Restart nginx                    | ``sudo service nginx restart``           |
+----------------------------------+------------------------------------------+


Database Management
-------------------

Because CommCare Analytics does not provide direct access to the
underlying databases, it is common to have to perform database
management tasks, for example, creating new databases, or dropping
existing databases or tables.

Database management can be done by logging into the server and getting a
postgres shell::

    $ psql -U [postgres user] -h localhost -p 5432

You will need the postgres username and password from the vault.yml file.
