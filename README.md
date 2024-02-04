# 1. Load balancer

| User  | Public IP   | Public Key                                                                                     |
|-------|-------------|------------------------------------------------------------------------------------------------|
| izzet | 49.13.55.66 | `ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBTxjdxPjwE7cUjdnaQJhTLYTYSpAlMsEXtelpy04Qxg izzet@lb-web` |

## 1.1. Cloud configuration
```yaml
#cloud-config
users:
  - name: izzet
    groups: users, admin
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAEAQDJltfUS10DI//tOxePSNRa0C4vM3pdOy6N//ewRPqReEYhHBRc6RLzhjRenFZtQ+f0mumhKpUiV/69NRqyui7ClJTM2+2+M1A/eGP7GklAZu7WVSsqUNmOEm2PdB49xzwjnR36DPkBkQnrI2lo6tHKDmx4nRUYhI672YWiRktBd2k3jIF0sUMZUIlq2z1D1Pix63su9wk8s1aAJv+q8pfKi+7S5PKckGI9wxcjIwJWmW99KAooeDtoWwVKC49byVCvsquZjmYxiBk3OQak9lRfRKw4/bz77QYhLXCCNcQoPUlRiF/HipGrVKjSUhV2rHG5x4fZlapQoJGZNOza0k813uprJdT0lQkQYGNfruwFBiUZPbMl4BNr5PwpJcCjxSbPDMe5dNs709+biXXoSRtD73F0DJ1pi1kO3TPTwJ1uhKK0WjagCUfGjlN7XhOVg09q3YmbLfGNJON5OPRjk6sxuW6DYD/2DaJ5/40OZj0ScBockasQEJDbApNEFCDAtFPUXhVaIrGlgiRQSY5nj71fMJiiEfVFkxUGB0dGpPlBlQT1+Hl3gmxqa/XA/jJsNQ3aOyQ/+2N+jjYQJYNDo7bo5Go+5ti2WGMRCg97tLFaTiuNPjJg7tVmmmeZQ9OYOJptbSEnwl2io3olfcLqEf5Xc4raHO7vpdmAnSl8M9RJiFahMuZ5ouOLTAPmIU+XRHRuIAvSJvvq8XKyK4UGsZmwF6pefN8J91NpWw+d872Oa9TMsCHNach3z8hagqeSZwvYfUg3pWF/wSz/oOEpzH8S4vYy7pdfAlHTyi+7Yq6un9fNeDliQcl6oWRcqyk31E7cfaAD7wlDgNtmUbTDRqbZxcab8MXaiKfc53srjMGmjTTS19aqW47OMnUBVSsCQEoyPlcKgwzoVqX+U+MMGT8mrJYIAZcNsQ+lIH31egQ2FBU19jIGcYK0pH94lEIl6Oud0PS/SwfQHs16TxQY1KCIAlqUfQ/qMUb1OELhwv63vsL5Z5t1WwUbPzDw9GAFpnll1MpfsVpcBg45VrCwvGW2WrdRn0hTRdIGhDmBIw7ekeaf6m6i8VmpBCn7pV2WduoHFOlCFAOMQfOhfljKVezhJT8BG1m5eyq/jgaGatt5hnVEbjSx5SkU35WuXkbVVBZLLgnC273uqHE8v4kLCnTCXtWm9XpiQfQFMndAZEVlcDsFnscbMmQzUZ6VUC/A8eU4aBhrXK68eNdEv+U1TND2iJIlhw1IpQbzaVkF8vjvQN2AFqOGNXxuwhArtF55+iqcWJS+mR1twbNJqUVNqgWFqNXKZOqXrurJ/t3KrJ7RWRhafkrF3g7Jlpqnr67k1ksmJr9obBuZtjRf8xXuuB+V app@localhost
packages:
  - fail2ban
package_update: true
package_upgrade: true
runcmd:
  - printf "[sshd]\nenabled = true\nbanaction = iptables-multiport" > /etc/fail2ban/jail.local
  - systemctl enable fail2ban
  - sed -i -e '/^\(#\|\)PermitRootLogin/s/^.*$/PermitRootLogin no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)PasswordAuthentication/s/^.*$/PasswordAuthentication no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)KbdInteractiveAuthentication/s/^.*$/KbdInteractiveAuthentication no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)ChallengeResponseAuthentication/s/^.*$/ChallengeResponseAuthentication no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)MaxAuthTries/s/^.*$/MaxAuthTries 2/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)X11Forwarding/s/^.*$/X11Forwarding no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)AllowAgentForwarding/s/^.*$/AllowAgentForwarding no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)AuthorizedKeysFile/s/^.*$/AuthorizedKeysFile .ssh\/authorized_keys/' /etc/ssh/sshd_config
  - sed -i '$a AllowUsers izzet' /etc/ssh/sshd_config
  - reboot
```

# 2. Application server
Application servers are not publicly accessible.

## 2.1. Cloud configuration
```yaml
#cloud-config
users:
  - name: izzet
    groups: users, admin
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBTxjdxPjwE7cUjdnaQJhTLYTYSpAlMsEXtelpy04Qxg izzet@lb-web
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAEAQDJltfUS10DI//tOxePSNRa0C4vM3pdOy6N//ewRPqReEYhHBRc6RLzhjRenFZtQ+f0mumhKpUiV/69NRqyui7ClJTM2+2+M1A/eGP7GklAZu7WVSsqUNmOEm2PdB49xzwjnR36DPkBkQnrI2lo6tHKDmx4nRUYhI672YWiRktBd2k3jIF0sUMZUIlq2z1D1Pix63su9wk8s1aAJv+q8pfKi+7S5PKckGI9wxcjIwJWmW99KAooeDtoWwVKC49byVCvsquZjmYxiBk3OQak9lRfRKw4/bz77QYhLXCCNcQoPUlRiF/HipGrVKjSUhV2rHG5x4fZlapQoJGZNOza0k813uprJdT0lQkQYGNfruwFBiUZPbMl4BNr5PwpJcCjxSbPDMe5dNs709+biXXoSRtD73F0DJ1pi1kO3TPTwJ1uhKK0WjagCUfGjlN7XhOVg09q3YmbLfGNJON5OPRjk6sxuW6DYD/2DaJ5/40OZj0ScBockasQEJDbApNEFCDAtFPUXhVaIrGlgiRQSY5nj71fMJiiEfVFkxUGB0dGpPlBlQT1+Hl3gmxqa/XA/jJsNQ3aOyQ/+2N+jjYQJYNDo7bo5Go+5ti2WGMRCg97tLFaTiuNPjJg7tVmmmeZQ9OYOJptbSEnwl2io3olfcLqEf5Xc4raHO7vpdmAnSl8M9RJiFahMuZ5ouOLTAPmIU+XRHRuIAvSJvvq8XKyK4UGsZmwF6pefN8J91NpWw+d872Oa9TMsCHNach3z8hagqeSZwvYfUg3pWF/wSz/oOEpzH8S4vYy7pdfAlHTyi+7Yq6un9fNeDliQcl6oWRcqyk31E7cfaAD7wlDgNtmUbTDRqbZxcab8MXaiKfc53srjMGmjTTS19aqW47OMnUBVSsCQEoyPlcKgwzoVqX+U+MMGT8mrJYIAZcNsQ+lIH31egQ2FBU19jIGcYK0pH94lEIl6Oud0PS/SwfQHs16TxQY1KCIAlqUfQ/qMUb1OELhwv63vsL5Z5t1WwUbPzDw9GAFpnll1MpfsVpcBg45VrCwvGW2WrdRn0hTRdIGhDmBIw7ekeaf6m6i8VmpBCn7pV2WduoHFOlCFAOMQfOhfljKVezhJT8BG1m5eyq/jgaGatt5hnVEbjSx5SkU35WuXkbVVBZLLgnC273uqHE8v4kLCnTCXtWm9XpiQfQFMndAZEVlcDsFnscbMmQzUZ6VUC/A8eU4aBhrXK68eNdEv+U1TND2iJIlhw1IpQbzaVkF8vjvQN2AFqOGNXxuwhArtF55+iqcWJS+mR1twbNJqUVNqgWFqNXKZOqXrurJ/t3KrJ7RWRhafkrF3g7Jlpqnr67k1ksmJr9obBuZtjRf8xXuuB+V app@localhost
package_update: true
package_upgrade: true
runcmd:
  - sed -i -e '/^\(#\|\)Port/s/^.*$/Port 63254/' /etc/ssh/sshd_config 
  - sed -i -e '/^\(#\|\)PermitRootLogin/s/^.*$/PermitRootLogin no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)PasswordAuthentication/s/^.*$/PasswordAuthentication no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)KbdInteractiveAuthentication/s/^.*$/KbdInteractiveAuthentication no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)ChallengeResponseAuthentication/s/^.*$/ChallengeResponseAuthentication no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)MaxAuthTries/s/^.*$/MaxAuthTries 2/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)AllowTcpForwarding/s/^.*$/AllowTcpForwarding no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)X11Forwarding/s/^.*$/X11Forwarding no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)AllowAgentForwarding/s/^.*$/AllowAgentForwarding no/' /etc/ssh/sshd_config
  - sed -i -e '/^\(#\|\)AuthorizedKeysFile/s/^.*$/AuthorizedKeysFile .ssh\/authorized_keys/' /etc/ssh/sshd_config
  - sed -i '$a AllowUsers izzet' /etc/ssh/sshd_config
  - reboot
```

# Prerequisite
1. Install ansible
   - Please use this link to install ansible: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installation-guide

# Playbooks

## Change SSH Port
We should not use the default SSH port. Please change it to something else.
```
ansible-playbook playbook/change_ssh_port.yaml -i root@<server-ip-or-hostname>,
```

## Remove Backport Repositories
Backport repository is not needed for our installation. For this reason,
we are going to remove these repositories from apt sources.
```
ansible-playbook playbook/remove_repositories.yaml -i inventory.yml
```

## Install Docker
In order to run our applications, we need to have a docker installed in out system.
```
ansible-playbook playbook/install_docker.yaml -i inventory.yml
```

## Install Nginx and configure SSL certificates with Certbot
In order to serve our applications to visitors, we are using nginx as a web server.
```
ansible-playbook playbook/100-nginx.yaml -i inventory.yml
```

## Create an app user and add to Docker group
Our docker containers do not run as a root, they run under the user `app`. This
user is added to the `docker` group.
```
ansible-playbook playbook/create_user.yaml -i inventory.yml
```

## How can I add a new domain?
- Open `playbook/nginx.yaml` file in your editor.
- Find the variables in the find, it should look like this:
```
  vars:
    nginx_sites:
      - server_name: pazl.dev
        admin_email: dev@pazl.dev
        apps:
          - location: /password
            proxy: http://127.0.0.1:8000
```
- Then modify it according to your needs.
- After your modifications, you can run the following command in order to apply them.
```
ansible-playbook playbook/nginx.yaml -i inventory.yml
```

## How can I install MySQL service in Docker?
- Open `playbook/mysql_docker_playbook.yaml` file in your editor.
- Adjust the mysql version according to your needs.
```
ansible-playbook playbook/mysql_docker_playbook.yaml -i inventory.yml
```
