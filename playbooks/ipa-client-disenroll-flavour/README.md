# IPA Client Disenrollment flavour

This Ansible Playbook configures an existing virtual machine in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/) to disenroll
from a [IPA server](../ipa-server-flavour/).

IPA provides integrated identity management and DNS services for centralized
user authentication, authorization, and resource discovery. This template safely
removes a VM from a [FreeIPA](https://www.freeipa.org/page/Main_Page)-managed
environment, which is essential when decommissioning infrastructure to prevent
stale entries in the IPA directory such as obsolete host records, and DNS 
pointers, that could lead to management overhead, naming conflicts or security
vulnerabilities from lingering credentials.

Suitable for both tenant admin and tenant users, this template streamlines client
removal, ensuring a clean and efficient identity system. The end benefit for 
users is reduced administrative burden, improved security posture,
and easier scaling or redeployment of resources. 

## Functionality
The template is designed to:
- Configure a pre-existing virtual machine, previously enrolled (likely with help of the [IPA client](../ipa-client-enroll-flavour/) template) to disconnect from an [IPA server](../ipa-server-flavour/).
- Disable user authentication and authorization (LDAP) for the target instance.
- Remove IPA server-internal DNS records referencing the target instance, if present.



## Usage

### 1. Specify the target host and SSH credentials
> 💡 To find out which is the default user for your chosen VM image,
checkout the [official EWC documentation](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+-+VM+images+and+default+users).

Create an inventory file to specify address/credentials that Ansible should use
to reach the virtual machine you wish to configure:

```yaml
# inventory.yml
---
ewcloud:
  hosts:
    ipa_client:
      ansible_python_interpreter: /usr/bin/python3
      ansible_host: <add the IPV4 address of the target host>
      ansible_ssh_private_key_file: <add the path to local SSH private key file>
      ansible_user: <add the default user according to your chosen VM image>
      ansible_ssh_common_args: -o StrictHostKeyChecking=accept-new

```

### 2. Configure and apply the template

#### 2.1. Interactive Mode

By running the following command, you can trigger an interactive session that
prompts you for the necessary user inputs, and then applies changes to your
target EWC environment:

```bash
ansible-playbook -i inventory.yml ipa-client-disenroll-flavour.yml
```

#### 2.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for
each and every available input (see [inputs section](#inputs) below). For
example:

```bash
ansible-playbook \
  -i inventory.yml \
  -e '{
      "ipa_domain": "eumetsat.sandbox.ewc",
      "ipa_client_hostname": "ipa-client-1",
      "ipa_server_hostname": "ipa-server-1",
      "ipa_admin_username": "ipaadmin",
      "ipa_admin_password": "my-secret-password"
    }' \
  ipa-client-disenroll-flavour.yml
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ipa_domain | domain name managed by the IPA server. Example: `eumetsat.sandbox.ewc` | `string` | n/a | yes |
| ipa_client_hostname | hostname of the target vm to be disenrolled from the IPA server. Example: `ipa-client-1` | `string` | n/a | yes |
| ipa_server_hostname | hostname of the IPA server. Example: `ipa-server-1` | `string` | n/a | yes |
| ipa_admin_username | username of the administrator account from the IPA server. Example: `ipaadmin` | `string` | n/a | yes |
| ipa_admin_password | password of the administrator account from the IPA server. Example: `my-secret-password` | `string` | n/a | yes |


## Requirements

| Name | Version | Package Info |
|------|---------|-------|
| ewc-ansible-role-ipa-client-disenroll | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-ipa-client-disenroll |
