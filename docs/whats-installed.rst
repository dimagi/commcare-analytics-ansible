What's Installed
================

The following are installed by default.

+-------------+--------------------------------------------------------+
| Name        | Description/Reason                                     |
+=============+========================================================+
| Python 3.10 | Required for running Superset                          |
+-------------+--------------------------------------------------------+
| PostgreSQL  | Default database that houses all data, including       |
|             | configuration details. Uses version 12.                |
+-------------+--------------------------------------------------------+
| Superset    | Business Intelligence tool                             |
+-------------+--------------------------------------------------------+
| nginx       | Web server that sits in front of Superset              |
+-------------+--------------------------------------------------------+
| Supervisord | Process management for Superset                        |
+-------------+--------------------------------------------------------+

The above will all be configured, and after install, CommCare Analytics
should be properly set up.

Note that some of these can be enabled/disabled based on variables. For
example, setting ``superset_enabled: no`` in your environment will
prevent Superset from being installed.
