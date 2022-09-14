# Prerequisite
1. Install ansible
   - Please use this link to install ansible: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installation-guide

# How to apply ansible playbooks?
- `install.yml` is the main file which includes all the configurations
- `inventory.yml` is the inventory configuration file

Please run the following command to apply ansible playbooks:
```shell
ansible-playbook install.yml -i inventory.yml
```

How to generate personal access token for GitHub?
Please visit this page, https://github.com/settings/tokens

# How to deploy a new version of an existing application?
