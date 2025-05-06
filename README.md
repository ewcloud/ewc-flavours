EWC Automation of EWC Instance Flavours
=======================================

This repository contains Ansible playbooks for customising EWC instances with specific software stacks:

- data-flavour: includes the basic ECMWF software stack, with MARS client and an environment with ecCodes, Metview, Earthkit and Aviso.
- jupyterhub-flavour: install and run jupyterhub on your instance, offering a convenient way to access it through the web.

Getting started
---------------

* Install ansible and other dependencies. You may want to do it in its own virtual environment (`pip install -r requirements.txt`)
* Fetch the external requirements
  ```
  $ ansible-galaxy role install -r requirements.yml roles/
  ```

* Define your inventory in `inventory`
* Run the apropriate playbook 

  ```
  $ ansible-playbook -i inventory playbookname.yml
  ```

Available Playbooks
-------------------
- data-flavour.yml

License
-------
Apache 2.0.

Author Information
------------------
ECMWF for the European Weather Cloud
