# IPA client enroll flavour
This subdirectory contains a configuration template
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
ansible-playbook -i inventory.yml ipa-client-enroll-flavour.yml
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
        "ipa_client_hostname": "ipa-client-1",
        "ipa_domain": "eumetsat.sandbox.ewc",
        "ipa_server_hostname": "ipa-server-1",
        "ipa_admin_username": "ipaadmin",
        "ipa_admin_password": "my-secret-password"
    }' \
  ipa-client-enroll-flavour.yml
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ipa_client_hostname | hostname of the target vm where the IPA client will be installed. Example: `ipa-client-1` | `string`| n/a | yes |
| ipa_domain | domain name managed by the IPA server. Example: `eumetsat.sandbox.ewc` | `string` | n/a | yes |
| ipa_server_hostname | hostname of the IPA server. Example: `ipa-server-1` | `string`| n/a | yes |
| ipa_admin_username | username of the administrator account from the IPA server. Example: `ipaadmin` | `string` | n/a | yes |
| ipa_admin_password | password of the administrator account from the IPA server. Example: `my-secret-password` | `string` | n/a | yes |
| password_allowed_ip_ranges | IP addresses or IP ranges (in CIDR format) to be allowed for password access in SSHD configuration. When in doubt, add only IP addresses you know and trust. Example: `['10.0.0.0/24','192.168.1.0/24']` | `list(string)` | `['10.0.0.0/8','172.16.0.0/12','192.168.0.0/16']` | no |

## Requirements
> ⚠️ Only Ubuntu 22.04 and RockyLinux 8.10 VM images are currently supported.
This is due to constrains imposed by the required
ewc-ansible-role-ipa-client-enroll Ansible Role.

| Name | Version | Package Info |
|------|---------|-------|
| ewc-ansible-role-ipa-client-enroll | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll |
