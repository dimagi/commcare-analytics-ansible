Production Environments
=======================

This page describes creating and managing production environments.

### Directory overview

Environments live in the `environments/` folder.

To add a new environment you can follow the steps below, replacing `your_environment` with your environment name.


### Initialize environment directory

First create a new environment folder for your environment.
This is where your project-specific configuration will live. 

```bash
export ENV=your_environment
cp -r environments/example environments/$ENV
```
### Update Inventory Files

Edit the `inventory.ini` and `vault.yml` files with your project-specific details.


### Ansible Vault

Production environments should use [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) to manage secrets.
That page has lots of details about editing and using files with Vault.

The example environment includes a vault file which you can edit. When you're done adding the necessary secrets, you must encrypt the vault file. It will ask you for a password when you run the encrypt command:

```bash
ansible-vault encrypt vault.yml
```

You can generate a good random key from a command line:

```bash
openssl rand -base64 48
```

Your `vault.yml` file should now be replaced by a digested value and no secret data should be revealed. You can edit the secret values again using the following:

```bash
# You can store your password in a file somewhere on the control machine
ansible-vault edit --vault-password-file ~/path/to/vault/password/file \
    environments/$ENV/vault.yml
```

#### SSH access

Assuming you are running on AWS, *Copy the AWS private key to `~/myproject.pem` on your local machine.*

You may also need to change permissions on it.

```bash
chmod 400 ~/myproject.pem
```

Test it's working:

```bash
ssh -i ~/myproject.pem ubuntu@my.server.ip
```

#### Set hostname

*On the remote server*

```bash
sudo hostnamectl set-hostname myproject-server
```

#### Run Installation

```bash
ansible-galaxy install -r requirements.yml
ansible-playbook -i environments/$ENV/inventory.ini commcare_analytics.yml \
    -e @environments/$ENV/vault.yml \
    -e @environments/$ENV/vars.yml \
    --ask-vault-password -u ubuntu --tags=mynew
```

This should install everything required to run CommCare Analytics!

#### Settting up HTTPS

HTTPS set up is currently not supported by this tool. To set up SSL, login to your machine and install certbot:

```bash
sudo apt install certbot python3-certbot-nginx
```

Then run:

```bash
sudo certbot --nginx
```

and follow the prompts.

You'll need to repeat this process for each site (e.g. superset).
You may also need to open up port 443 on AWS or your firewall.

**After setting up HTTPS you should set `ssl_enabled=yes` and `superset_ssl_enabled=yes` in your `vars.yml` file,
otherwise running a full `ansible-playbook` will undo the changes!**

You can set `ssl_enabled=yes` and `superset_ssl_enabled=yes` to prevent this from happening
after enabling SSL support.

### Steady-State Deploy

For existing environments you should get the relevant `myproject-ansible-vault` and `myproject.pem`
files from a project team member and jump straight to deployment.

To deploy, run the following *from your local/control machine*.

```bash
ansible-playbook -i environments/$ENV/inventory.ini commcare_analytics.yml \
    --limit myserver \
    --vault-password-file ~/path/to/vault/password/file \
    -e @./environments/$ENV/vault.yml \
    -vv --tags="deploy"
```

## Database backups
Database backups are pushed to AWS S3. If you want to enable database backups, you can run the following command that will add a cronjob for the `postgres` user to create backups every Sunday at 8AM UCT.

Please make sure that the S3 details in the `vault.yml` file is updated.

```bash
ansible-playbook -i environments/$ENV/inventory.ini commcare_analytics.yml \
    --vault-password-file ~/path/to/vault/password/file \
    --tags="postgres_backup,aws_setup" \
    -e @./environments/$ENV/vault.yml
```
