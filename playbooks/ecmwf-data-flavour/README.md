# ECMWF data flavour
Includes the basic ECMWF software stack, with MARS client and an environment with ecCodes, Metview, Earthkit and Aviso.

## Usage
Example usage:

  ```bash
  ansible-playbook -i inventory ecmwf-data-flavour.yml
  ```

## Inputs
You may use the following ansible variables to customize this playbook:

- `reboot_if_required`: Boolean to reboot the instance if required after an update. Default: true
- `ecmwf_toolbox_env_wipe`: Boolean to decide whether to wipe the environment if exists prior to a reinstallation. Default: no
- `ecmwf_toolbox_env_name`: Name of the environment containing the ECMWF toolbox. Default: ecmwf-toolbox
- `ecmwf_toolbox_create_ipykernel`: Boolean to create a system-wide kernel available. Default: yes
- `conda_prefix`: Prefix where conda is installed. Default: `/opt/conda`
- `conda_user`: User owning the conda installation. Default: `root`
  