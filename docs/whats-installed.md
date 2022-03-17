What's Installed
================

The following are installed by default.

| Name          | Description / Reason                                                                                 |
| ------------- |------------------------------------------------------------------------------------------------------|
| Python 3.8    | Running superset                                                                                     |
| Postgres      | Default database that houses all data (including configuration details). The default version is version 10. |
| Superset      | BI Tool                                                                                              |                                  |
| Nginx         | Web Server that sits in front of Superset                                             |
| Supervisord   | Process management for Superset                                           |

The above will all be configured, and after install, analytics should be properly set up.

Note that some of these can be enabled/disabled based on variables. For example, setting `superset_enabled: no`
in your environment will prevent Superset from being installed.
