# IPA client enroll flavour
A configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to:

* Provision a small virtual machine via Terraform (if its `.tfstate` file
cannot be found a user-specified location), with the following specifications:
  * Operative System: Ubuntu 22.04
  * vCPUs: 2
  * RAM: 2 GB
  * Disk: 30 GB
* Configure the existing or newly provisioned RockyLinux or Ubuntu
virtual machine to connect to an IPA server running on the same subnet,
such that it:
  * Is remotely accessible via public key or password to centrally
    managed LDAP users
  * Is able to leverage DNS resolution and discover other private
    hosts or public addresses

## Authentication

Before proceeding, if you lack OpenStack Application Credentials or do not know
how to make them available to Ansible in your development environment, make sure
to check out this
[EWC documentation page](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+-+How+to+request+Openstack+Application+Credentials).

Additionally, in order to configure the virtual machine after provisioning, you
required a private and public SSH keypair. Checkout this
[EWC documentation page](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+-+OpenStack+Command-Line+client#EWCOpenStackCommandLineclient-ImportSSHkey)
for details on how import your public key into OpenStack.

## Usage

### 1. Configure and apply the template

#### 1.1. Interactive Mode

By running the following command, you can trigger an interactive session that
prompts you for the necessary user inputs, and then applies changes to your
target EWC environment:

```bash
ansible-playbook ipa-client-enroll-flavour.yml
```

#### 1.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for each and every available input (see [inputs section](#inputs) below):

```bash
ansible-playbook \
  -e '{
        "password_allowed_ip_ranges_override": ["10.0.0.0/24","192.168.1.0/24"],
        #  ...
        # all remaining input overrides
        # ...
    }' \
  ipa-client-enroll-flavour.yml
```

Note manual interaction (i.e. typing in a `yes` value) will still be required
before Ansible is able to complete the configuration in the following cases:
1. You haven't previously connected from your development environment to the
target virtual machine via SSH
2. The target virtual machine was newly provisioned by your non-interactive
session

An example of the additional prompt requiring manual interaction is shown below:
```
The authenticity of host '192.168.1.122 (192.168.1.122)' can't be established.
ED25519 key fingerprint is SHA256:aBcdE1f2GHh3iJKlMNOpqRstuV3Wxyzab4cdE+fGH45i.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```
## Requirements

| Name | Version |
|------|---------|
| ansible | >= 2.14.0 |
| terraform | >= 0.14.0 |
| openstack | ~> 1.53.0 |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ewc_provider | your target EWC provider. Must match that the provider of your OpenStack application credentials. Valid input values are `ecmwf` or `eumetsat`. | `string` | n/a | yes |
| tf_project_path | path to terraform module directory. Example: `~/ewc-tf-module-openstack-compute` | `string` | n/a | yes |
| app_name_override | application name, used as prefix in the full instance name. Example: `ipa-client` | `string` | n/a | yes |
| instance_name_override| name of the instance, used in the full instance name.  Example: `ubuntu` | `string` | n/a | yes |
| instance_index_override | index or identifier for the instance, used as suffix in the full instance name. Example: `1` | `number` | n/a | yes |
| public_keypair_name_override | name of public keypair (stored in OpenStack) to be copied into the instance for remote SSH access | `string` | n/a | yes |
| private_keypair_name_override | path to the local private keypair to use for SSH access to the instance. Example: `~/.ssh/id_rsa` | `string` | n/a | yes |
| security_groups_override | list of security group names to apply to the instance. Example: `['ipa']` | `list(string)` | n/a | yes |
| instance_has_fip_override | whether to assign a floating IP to the instance. Only `yes` will be accepted to approve | `string` | n/a | yes |
| ipa_domain_override | domain name managed by the IPA server. Example: `<memberstate>-<organization>-<projectname>.ewc` | `string` | n/a | yes |
| ipa_server_hostname_override | hostname of the IPA server. Example: `ipa-server-1` | `string`| n/a | yes |
| ipa_admin_username_override | username of the administrator account from the IPA server | `string` | n/a | yes |
| ipa_admin_password_override | password of the administrator account from the IPA server | `string` | n/a | yes |
| password_allowed_ip_ranges_override | IP ranges (in CIDR format) to be allowed for password access in SSHD configuration. Example: `["10.0.0.0/24","192.168.1.0/24"]` | `list(string)` | n/a | yes |
