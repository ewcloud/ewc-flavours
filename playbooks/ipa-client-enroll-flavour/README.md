# IPA client enroll flavour
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
  -i inventory.yml \
  -e '{
        "password_allowed_ip_ranges_override": ["10.0.0.0/24","192.168.1.0/24"],
        #  ...
        # all remaining input overrides
        # ...
    }' \
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
| password_allowed_ip_ranges_override | IP ranges (in CIDR format) to be allowed for password access in SSHD configuration. Example: `["10.0.0.0/24","192.168.1.0/24"]["10.0.0.0/24","192.168.1.0/24"]` | `list(string)` | n/a | yes |
