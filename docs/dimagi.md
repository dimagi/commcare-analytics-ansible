Dimagi-managed Enviornments
===========================

Dimagi maintains several production commcare-analytics environments.
This page outlines the recommended workflow for Dimagi developers.

### Initializing the submodule

Dimagi inventories can be accessed by running `git submodule update --init` after cloning the repository.
This requires access to the [`commcare-inventories` repository on Github](https://github.com/dimagi/commcare-analytics-inventories/).
The config files will be loaded in the `/inventories/dimagi` folder. 

To work on a Dimagi-managed production environment, [all the production instructions are the same](/production/),
but the root path everywhere must be changed from `inventories/` to `inventories/dimagi/`.
Additionally, changes will need to be pushed to the separate
[`commcare-inventories` private repository](https://github.com/dimagi/commcare-analytics-inventories/),
and then committed to the main repository by updating the submodule reference.

### Using the commcare-inventories submodule in production

When referencing the submodule in production environments (on a server),
you should use [deploy keys](https://docs.github.com/en/developers/overview/managing-deploy-keys#deploy-keys).

Follow the instructions there, making sure to run `ssh-keygen` on the server you want to have access.

After adding a deploy key to the `commcare-inventories` repository you should be able to 
update the submodule as normal.
