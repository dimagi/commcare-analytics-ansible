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


Steady-State Maintenance Tasks
------------------------------

Working with Superset
^^^^^^^^^^^^^^^^^^^^^

In order to run any Superset management commands (for example
``superset db upgrade``) you must enter the Superset environment and
*manually run the postactivate script*. This is required to set the
project-specific environment variables. ::

    $ source ~/www/.virtualenvs/superset/bin/activate
    $ source ~/www/.virtualenvs/superset/bin/postactivate


..
    TODO: Make this true:

    Deploying Changes
    ^^^^^^^^^^^^^^^^^

    You may wish to deploy updates to the server, for example to pull the
    latest changes from the CommCare analytics code.

    The command to deploy updates is::

        $ ansible-playbook -i environments/$ENV/inventory.ini commcare_analytics.yml \
              -e @environments/$ENV/vault.yml \
              -e @environments/$ENV/vars.yml \
              --ask-vault-password -u ubuntu
              -vv --tags=deploy


Database Backups
^^^^^^^^^^^^^^^^

Database backups are pushed to AWS S3. If you want to enable database
backups, you can run the following command that will add a cronjob for
the ``postgres`` user to create backups every Sunday at 08:00 UTC.

Please make sure that the S3 details in the ``vault.yml`` file are
up-to-date. ::

    $ ansible-playbook -i environments/$ENV/inventory.ini commcare_analytics.yml \
        --vault-password-file ~/path/to/vault/password/file \
        --tags="postgres_backup,aws_setup" \
        -e @./environments/$ENV/vault.yml


Other Useful Commands
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
