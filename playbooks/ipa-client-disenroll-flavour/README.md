# IPA client disenroll flavour

A configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to run on a virtual machine, running an IPA client previously enrolled in 
your IPA server, such that it:

* Requests configuration changes to said IPA server for:
  * Stopping user authentication/authorization management (LDAP) to target virtual machine
  * Deletion of IPA server-internal DNS records referencing  the target virtual
    machine, if and when found

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
ansible-playbook -i inventory.yml ipa-client-disenroll-flavour.yml
```

#### 2.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for each and every available input (see [inputs section](#inputs) below):

```bash
ansible-playbook \
  -i inventory.yml \
  -e password_allowed_ip_ranges_override": \
  #  ...
  # all remaining input overrides
  # ...
  ipa-client-disenroll-flavour.yml
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ipa_domain_override | domain name managed by the IPA server. Example: `<memberstate>-<organization>-<projectname>.ewc` | `string` | n/a | yes |
| ipa_client_hostname_override | hostname of the target vm to be disenrolled from the IPA server. Example: `<openstack instance name>` | `string` | n/a | yes |
| ipa_server_hostname_override | hostname of the IPA server | `string` | n/a | yes |
| ipa_admin_username_override | username of the administrator account from the IPA server | `string` | n/a | yes |
| ipa_admin_password_override | password of the administrator account from the IPA server | `string` | n/a | yes |
