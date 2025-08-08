# IPA client provision flavour
> ⚠️ Only Ubuntu 22.04 and RockyLinux 8.10 VM images are currently supported. This is due to constrains imposed by dependency on version 1.0 of [ewc-ansible-role-ipa-client-enroll](https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll).

A configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to:

* Provision a virtual machine via Terraform (if its `.tfstate` file
cannot be found a user-defined location), either with RockyLinux or Ubuntu, and with your desired flavor (a.k.a VM plan)
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

## Setup
To successfully run this playbook, the following packages should be available in your work environment:

| Name | Version | Package Info |
|------|---------|-------|
| git | >= 2.0.0 |  https://git-scm.com/downloads | 
| python | >= 3.9.0 | https://www.python.org/downloads/  |
| ansible | >= 2.14.0 | https://pypi.org/project/ansible  |
| terraform | >= 0.14.0 | https://developer.hashicorp.com/terraform/install |
| openstack | ~> 1.53.0 | https://pypi.org/project/python-openstackclient |

## Usage

### 1. Configure and apply the template

#### 1.1. Interactive Mode

By running the following command, you can trigger an interactive session that
prompts you for the necessary user inputs, and then applies changes to your
target EWC environment:

```bash
ansible-playbook ipa-client-provision-flavour.yml
```

#### 1.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for each and every available input (see [inputs section](#inputs) below):

```bash
ansible-playbook \
  -e '{
        "ewc_provider": "ecmwf",
        "tf_project_path":"~/ewc-tf-module-openstack-compute",
        "app_name_override":"ipa",
        "instance_name_override":"client",
        "instance_index_override": 1,
        "flavor_name_override":"2cpu-2gbmem-30gbdisk",
        "image_name_override":"ubuntu-22.04-20250204105649",
        "public_keypair_name_override":"john-claudy-publickey",
        "private_keypair_path":"~/.ssh/id_rsa",
        "security_groups_override": ["ipa"],
        "instance_has_fip_override":"no",
        "ipa_domain_override":"ecmwf.sandbox.ewc",
        "ipa_server_hostname_override":"ipa-server-1",
        "ipa_admin_username_override":"<redacted>",
        "ipa_admin_password_override":"<redacted>",
        "password_allowed_ip_ranges_override": ["192.168.1.0/24"]
    }' \
  ipa-client-provision-flavour.yml
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
This will add a line to your `~/.ssh/known_hosts` file, containing the IP address
of the instance in question.



| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ewc_provider | your target EWC provider. Must match that the provider of your OpenStack application credentials. Valid input values are `ecmwf` or `eumetsat`. | `string` | n/a | yes |
| tf_project_path | path to terraform module directory. Example: `~/ewc-tf-module-openstack-compute` | `string` | n/a | yes |
| app_name_override | application name, used as prefix in the full instance name. Example: `ipa-client` | `string` | n/a | yes |
| instance_name_override| name of the instance, used in the full instance name.  Example: `ubuntu` | `string` | n/a | yes |
| instance_index_override | index or identifier for the instance, used as suffix in the full instance name. Example: `1` | `number` | n/a | yes |
| flavor_name_override | name the flavor to use for the instance. To learn about available options, checkout the [official EWC VM plans documentation](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+VM+plans) | `string` | n/a | yes |
| image_name_override | name of the image to use for the instance. For complete information on  available options, see the [official EWC Images documentation](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+Virtual+Images+Available). ⚠️ Only Ubuntu 22.04 and RockyLinux 8.10 VM images are currently supported. This is due to constrains imposed by dependency on version 1.0 of [ewc-ansible-role-ipa-client-enroll](https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll). Example: `ubuntu-22.04-20250204105649`  | `string` | n/a | yes |
| public_keypair_name_override | name of public keypair (stored in OpenStack) to be copied into the instance for remote SSH access | `string` | n/a | yes |
| private_keypair_name_override | path to the local private keypair to use for SSH access to the instance. Example: `~/.ssh/id_rsa` | `string` | n/a | yes |
| security_groups_override | list of security group names to apply to the instance. Example: `['ipa']` | `list(string)` | n/a | yes |
| instance_has_fip_override | whether to assign a floating IP to the instance. Only `yes` will be accepted to approve | `string` | n/a | yes |
| ipa_domain_override | domain name managed by the IPA server. Example: `<memberstate>-<organization>-<projectname>.ewc` | `string` | n/a | yes |
| ipa_server_hostname_override | hostname of the IPA server. Example: `ipa-server-1` | `string`| n/a | yes |
| ipa_admin_username_override | username of the administrator account from the IPA server | `string` | n/a | yes |
| ipa_admin_password_override | password of the administrator account from the IPA server | `string` | n/a | yes |
| password_allowed_ip_ranges_override | IP ranges (in CIDR format) to be allowed for password access in SSHD configuration. Example: `["10.0.0.0/24","192.168.1.0/24"]` | `list(string)` | n/a | yes |


## Requirements

| Name | Version | Package Info |
|------|---------|-------|
| ewc-tf-module-openstack-compute | 1.0 | https://github.com/ewcloud/ewc-tf-module-openstack-compute  |
| ewc-ansible-role-ipa-client-enroll | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll |


## Troubleshooting

### Remote host identification has changed

In cases where the following sequence of events took place:
1. You successfully connected to the target VM via SSH at least once (by running this playbook until the end, by using the `ssh` utility in your shell, etc.)
2. The VM was destroy and the recreated after the initial connection
3. You run this playbook

Execution may fail at the start of the IPA client installation, with the following error:
```
fatal: [ipa_client]: UNREACHABLE! => {
  "changed": false,
  "msg": "Failed to connect to the host via ssh: 
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
    Someone could be eavesdropping on you right now (man-in-the-middle attack)!
    It is also possible that a host key has just been changed.
    The fingerprint for the ED25519 key sent by the remote host is
    SHA256:aBcdE1f2GHh3iJKlMNOpqRstuV3Wxyzab4cdE+fGH45i.
    Please contact your system administrator.
    Add correct host key in /home/ubuntu/.ssh/known_hosts to get rid of this message.
    Offending ECDSA key in /home/ubuntu/.ssh/known_hosts:21
    Host key for 192.168.1.122 has changed and you have requested strict checking.
    Host key verification failed.",
  "unreachable": true
}
```
To resolve the issue, remove the line in your `~/.ssh/know_hosts` file that
shows the same IP address displayed in the error message before re-running
the playbook.
