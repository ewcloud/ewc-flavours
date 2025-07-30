# Jupyterhub flavour
Installs and run jupyterhub on your instance, offering a convenient way to access it through the web.

## Usage

Example Usage for an instance with Fully Qualified Domain name `jupyterhub.mytenancy.f.ewcloud.host` and registered to the email address `myemail@myorg.com`:

```bash
ansible-playbook -e jupyterhub_local_cert_email=myemail@myorg.com -e dns_domain=jupyterhub.mytenancy.f.ewcloud.host -i inventory jupyterhub-flavour.yml
```

## Inputs

You may use the following ansible variables to customise this playbook:
- `jupyterhub_local_cert_email`: If ran outside Morpheus, **pass this variable explicitly when running the ansible playbook.**
- `dns_domain`: **Pass this variable explicitly when running the ansible playbook**. If not present, will try to guess from instance configured hostname in /etc/hostname.
- `jupyterhub_local_env_wipe`: Boolean to decide whether to wipe the environment if exists prior to a reinstallation. Default: no
- `jupyterhub_local_env_name`: Name of the environment containing the Jupyerhub. Default: jupyterhub-local
- `jupyterhub_local_test_cert`: Use Lets encrypt test certificate. Default: false
- `jupyterhub_local_with_otp`: Use OTP authentication for Jupyterhub. It requires manual run of `google-authenticator` by the user to configure your TOTP device after running this playbook Default: false
- `conda_prefix`: Prefix where conda is installed. Default: `/opt/conda`
- `conda_user`: User owning the conda installation. Default: `root`
  