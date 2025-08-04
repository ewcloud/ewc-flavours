# IPA server flavour
A configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to:

* Validate that network/subnet configuration in the EWC tenancy, that is OpenStack
security group rules, is suitable for IPA server operation (e.g. open ports for
DNS, LDAP, Kerberos, HTTP/HTTPS, SSH, etc.)
* Check that target host has the recommended RAM availability
* Configure a pre-existing RockyLinux 8 or RockyLinux 9 virtual machine such that it:
    * Provides DNS resolutions for discovery of resources (i.e. other virtual machines)
    * Enables centralized user and credentials creation/edition/deletion/authentication
    * Allows centralized authorization between users and resources
* Automatically update the underlying subnet DNS nameserver to point to the newly configured IPA server

## Usage

### 1. Specify the target host and SSH credentials
Create an inventory file to specify address/credentials that Ansible should use
to reach the virtual machine you wish to configure:

```yaml
# inventory.yml
---
ewcloud:
  hosts:
    ipa_server:
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
ansible-playbook -i inventory.yml ipa-server-flavour.yml
```

#### 2.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for each and every available input (see [inputs section](#inputs) below):

```bash
ansible-playbook \
  -i inventory.yml \
  -e ipa_server_hostname_override=ipa-server-1 \
  # 
  # all remaining input overrides
  # ...
  ipa-server-flavour.yml
  
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ipa_domain_override | domain name to be managed by the IPA server. Example: `<memberstate>-<organization>-<projectname>.ewc` | `string` | n/a | yes |
| ipa_server_hostname_override | hostname of the target vm where the IPA server will be installed. Example: `ipa-server-1` | `string`| n/a | yes |
| ipa_admin_username_override | username of administrator account to replace the default IPA admin | `string` | n/a | yes |
| ipa_admin_password_override | password of administrator account to replace the default IPA admin | `string` | n/a | yes |
| ipa_admin_givenname_override | given name of the administrator to replace the default IPA admin (needs not be a physical person). Example: `EWC` | `string` | n/a | yes |
| ipa_admin_surname_override | surname of the administrator to replace the default IPA admin (needs not to belong to a physical person). Example: `IPAADMIN` | `string` | n/a | yes |
| os_network_name_override | OpenStack network to which the target virtual machine has access to. Example: `private` | `string` | n/a | yes |
| os_subnet_name_override | OpenStack subnet in which the target virtual machine is running. Example: `private subnet` | `string` | n/a | yes |
| os_subnet_cidr_override | IP range (in CIDR format) that spans the OpenStack subnet in which the target virtual machines is running. Example: `10.0.0.0/24` | `string` |n/a  | yes |
| os_security_group_name_override | OpenStack security group containing all firewall rules required by the IPA server/client communication. Example: `ipa`  | `string` | n/a | yes |
| os_subnet_dns_nameserver_ip_default_override | default DNS nameserver IPV4 address registered on the OpenStack subnet where the IPA server will run. Example: `1.1.1.1` | `string` | n/a | yes |
| os_subnet_dns_nameserver_ip_fallback_override | fallback DNS nameserver IPV4 address registered on the OpenStack subnet where the IPA server will run. Example: `8.8.8.8` | `string` | n/a  | yes |
