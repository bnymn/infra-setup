# Prerequisite
1. Install ansible
   - Please use this link to install ansible: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installation-guide

# Playbooks

## 01. Remove Repositories
universe, multiverse and backports repositories are not needed for our installation. For this reason,
we are going to remove these repositories from apt sources.

## How to apply ansible playbooks?
- `install.yml` is the main file which includes all the configurations
- `inventory.yml` is the inventory configuration file

Please run the following command to apply ansible playbooks:
```shell
ansible-playbook install.yml -i inventory.yml
```

# How to generate personal access token for GitHub?
Please visit the following page to generate an access token: https://github.com/settings/tokens.
After generating the token, please put it into `projects/web-ansible/playbook/05_create_applications.yml`
file.

# How to deploy a new version of an existing application?
