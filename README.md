EWC Automation of EWC Instance Flavours
=======================================

This repository contains Ansible playbooks for customising EWC instances with specific software stacks:

- **ECMWF data flavour**: includes the basic ECMWF software stack, with MARS client and an environment with ecCodes, Metview, Earthkit and Aviso.
- **Jupyterhub flavour**: installs and run jupyterhub on your instance, offering a convenient way to access it through the web.
3. [ssh-bastion-flavour](./playbooks/ssh-bastion-flavour/): configures an SSH daemon on your instance, providing a secure entrypoint from the public internet into the EWC private network.

Getting started
---------------

- Install ansible and other dependencies. You may want to do it in its own virtual environment (`pip install -r requirements.txt`)
- Fetch the external requirements
  
  ```bash
  ansible-galaxy role install -r requirements.yml roles/
  ```

- Define your inventory in `inventory`
- Run the apropriate playbook:

  ```bash
  ansible-playbook -i inventory playbookname.yml
  ```

Available Playbooks
-------------------

- **ECMWF data flavour**. You may use the following ansible variables to customise this playbook:
  - `reboot_if_required`: Boolean to reboot the instance if required after an update. Default: true
  - `ecmwf_toolbox_env_wipe`: Boolean to decide whether to wipe the environment if exists prior to a reinstallation. Default: no
  - `ecmwf_toolbox_env_name`: Name of the environment containing the ECMWF toolbox. Default: ecmwf-toolbox
  - `ecmwf_toolbox_create_ipykernel`: Boolean to create a system-wide kernel available. Default: yes
  - `conda_prefix`: Prefix where conda is installed. Default: `/opt/conda`
  - `conda_user`: User owning the conda installation. Default: `root`

  Example usage:

  ```bash
  ansible-playbook -i inventory ecmwf-data-flavour.yml
  ```
  
- **Jupyterhub flavour**. You may use the following ansible variables to customise this playbook:
  - `jupyterhub_local_cert_email`: If ran outside Morpheus, **pass this variable explicitly when running the ansible playbook.**
  - `dns_domain`: **Pass this variable explicitly when running the ansible playbook**. If not present, will try to guess from instance configured hostname in /etc/hostname.
  - `jupyterhub_local_env_wipe`: Boolean to decide whether to wipe the environment if exists prior to a reinstallation. Default: no
  - `jupyterhub_local_env_name`: Name of the environment containing the Jupyerhub. Default: jupyterhub-local
  - `jupyterhub_local_test_cert`: Use Lets encrypt test certificate. Default: false
  - `jupyterhub_local_with_otp`: Use OTP authentication for Jupyterhub. It requires manual run of `google-authenticator` by the user to configure your TOTP device after running this playbook Default: false
  - `conda_prefix`: Prefix where conda is installed. Default: `/opt/conda`
  - `conda_user`: User owning the conda installation. Default: `root`

  Example Usage for an instance with Fully Qualified Domain name `jupyterhub.mytenancy.f.ewcloud.host` and registered to the email address `myemail@myorg.com`:

  ```bash
  ansible-playbook -e jupyterhub_local_cert_email=myemail@myorg.com -e dns_domain=jupyterhub.mytenancy.f.ewcloud.host -i inventory jupyterhub-flavour.yml
  ```

License
-------

Apache 2.0.

Author Information
------------------

ECMWF for the European Weather Cloud
