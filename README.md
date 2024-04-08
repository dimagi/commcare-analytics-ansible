CommCare Analytics Ansible
==========================

Tools for setting up and deploying Commcare Analytics server


Documentation
-------------

## Building the docs

To build the docs locally,

1. Clone this repository, and change into the directory.

2. Create and activate a virtual environment
   ```shell
   $ python3 -m venv venv
   $ source venv/bin/activate
   ```

3. Install requirements:
   ```shell
   $ pip install -r requirements/docs-requirements.txt
   ```

4. Change into the docs/ directory, and build the docs in HTML
   ```shell
   $ cd docs/
   $ make html
   ```

5. To serve the docs locally, you can use Python's webserver:
   ```shell
   $ cd _build/html/
   $ python -m http.server 9000
   $ firefox 'http://localhost:9000/'
   ```

   Alternatively, open `_build/html/index.html` in a browser:
   ```shell
   $ firefox _build/html/index.html
   ```


Hosting the docs
----------------

Docs are hosted on readthedocs and managed by Dimagi.
Credentials for the Dimagi account can be found in Keepass.
