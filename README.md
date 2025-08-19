EWC Automation of EWC Instance Flavours
=======================================


This repository contains Ansible playbooks for customizing EWC instances with specific software stacks:

1. [ECMWF data flavour](./playbooks/ecmwf-data-flavour/): includes the basic ECMWF software stack, with MARS client and an environment with ecCodes, Metview, Earthkit and Aviso.
2. [Jupyterhub flavour](./playbooks/jupyterhub-flavour/): installs and run JupyterHub on your instance, offering a convenient way to access it through the web.
3. [ssh-bastion-flavour](./playbooks/ssh-bastion-flavour/): configures an SSH daemon on your instance, providing a secure entrypoint from the public internet into the EWC private network.
4. [remote-desktop-flavour](./playbooks/remote-desktop-flavour/): enable users to operate the remote virtual machines through a typical graphical interface
5. [ipa-server-flavour](./playbooks/ipa-server-flavour/): installs [FreeIPA](https://www.freeipa.org/About.html) on your instance, to enable centralized authentication, authorization and account information by storing data about user, groups, hosts and other objects necessary to manage the security aspects of a network of computers
6. [ipa-client-enroll-flavour](./playbooks/ipa-client-enroll-flavour/): enroll your instance into the services provided by a host previously configured by [ipa-server-flavour](./playbooks/ipa-server-flavour/)
7. [ipa-client-disenroll-flavour](./playbooks/ipa-client-disenroll-flavour/): disenrolls your instance if it was previously configured by [ipa-client-enroll-flavour](./playbooks/ipa-client-enroll-flavour/)

Getting started
---------------

- Install ansible and other dependencies. You may want to do it in its own virtual environment (`pip install -r requirements.txt`)
- Fetch the external requirements

  ```bash
  ansible-galaxy role install -r requirements.yml -p roles/
  ```

- Define your inventory in `inventory`
- Run the appropriate playbook:

  ```bash
  ansible-playbook -i inventory path/to/playbook/filename
  ```

License
-------

Apache 2.0.

Author Information
------------------

ECMWF for the European Weather Cloud
