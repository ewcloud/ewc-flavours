# IPA client enroll flavour
> ⚠️ Only Ubuntu 22.04 and RockyLinux 8.10 VM images are currently supported. This is due to constrains imposed by dependency on version 1.0 of [ewc-ansible-role-ipa-client-enroll](https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll).

A configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to:

* Configure a pre-existing RockyLinux or Ubuntu virtual machine to
  connect to an IPA server running on the same subnet, such that it:
  * Is able to leverage DNS resolution and discover other private
    hosts or public addresses
  * Is remotely accessible via public key or password to centrally
    managed LDAP users

## Authentication

Before proceeding, if you lack OpenStack Application Credentials or do not know
how to make them available to Ansible in your development environment, make sure
to check out this
[EWC documentation page](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+-+How+to+request+Openstack+Application+Credentials).

## Setup
To successfully run this playbook, the following packages should be available in your work environment:

| Name | Version | Package Info |
|------|---------|-------|
| git | >= 2.0.0 |  https://git-scm.com/downloads | 
| python | >= 3.9.0 | https://www.python.org/downloads/  |
| ansible | >= 2.14.0 | https://pypi.org/project/ansible  |

## Usage

### 1. Specify the target host and SSH credentials
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
      ansible_ssh_private_key_file: <add the path to local SSH RSA private key file>
      ansible_user: <add the username which owns the SSH RSA private key >

```

### 2. Configure and apply the template

#### 2.1. Interactive Mode

By running the following command, you can trigger an interactive session that
prompts you for the necessary user inputs, and then applies changes to your
target EWC environment:

```bash
ansible-playbook -i inventory.yml ipa-client-enroll-flavour.yml
```

#### 2.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for each and every available input (see [inputs section](#inputs) below):

```bash
ansible-playbook \
  -e '{
        "ipa_client_hostname_override": "ipa-client-1"
        "ipa_domain_override":"ecmwf.sandbox.ewc",
        "ipa_server_hostname_override":"ipa-server-1",
        "ipa_admin_username_override":"<redacted>",
        "ipa_admin_password_override":"<redacted>",
        "password_allowed_ip_ranges_override": ["192.168.1.0/24"]
    }' \
  - i inventory.yml \
  ipa-client-enroll-flavour.yml
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ipa_client_hostname_override | hostname of the target vm where the IPA client will be installed. Example: `<openstack instance name>` | `string`| n/a | yes |
| ipa_domain_override | domain name managed by the IPA server. Example: `<memberstate>-<organization>-<projectname>.ewc` | `string` | n/a | yes |
| ipa_server_hostname_override | hostname of the IPA server. Example: `ipa-server-1` | `string`| n/a | yes |
| ipa_admin_username_override | username of the administrator account from the IPA server | `string` | n/a | yes |
| ipa_admin_password_override | password of the administrator account from the IPA server | `string` | n/a | yes |
| password_allowed_ip_ranges_override | IP ranges (in CIDR format) to be allowed for password access in SSHD configuration. Example: `["10.0.0.0/24","192.168.1.0/24"]` | `list(string)` | n/a | yes |

## Requirements

| Name | Version | Package Info |
|------|---------|-------|
| ewc-ansible-role-ipa-client-enroll | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll |
