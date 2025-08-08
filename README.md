EWC Automation of EWC Instance Flavours
=======================================


This repository contains Ansible playbooks for customizing EWC instances with specific software stacks:

1. [ECMWF data flavour](./playbooks/ecmwf-data-flavour/): includes the basic ECMWF software stack, with MARS client and an environment with ecCodes, Metview, Earthkit and Aviso.
2. [Jupyterhub flavour](./playbooks/jupyterhub-flavour/): installs and run jupyterhub on your instance, offering a convenient way to access it through the web.

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
