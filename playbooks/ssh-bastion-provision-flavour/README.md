# Remote ssh bastion provision flavour

A configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to:

* Provision an instance via [Terraform](https://developer.hashicorp.com/terraform),
with your specified Linux distribution and desired flavor (a.k.a VM plan):
  * If a `terraform.tfstate` [state file](https://developer.hashicorp.com/terraform/language/state)
  is not found under the user-defined directory, attempts to create the
  instance from scratch

  OR
  * if  `terraform.tfstate` file is found, leverages Terraform's out-of-the-box
  functionality to update the instance referenced on it
* Configure the existing or newly provisioned RockyLinux virtual machines (with
public IP address), as entrypoint for users who wish to reach private EWC networks
 from the public internet via SSH.

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
ansible-playbook ssh-bastion-provision-flavour.yml
```

#### 1.2. Non-Interactive Mode

>💡 To learn more about defining variables at runtime, checkout the
[official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html).

You can also run in non-interactive mode by passing the
`--extra-vars` or `-e` flag, followed by a map of  key-value pairs; one for
each and every available input (see [inputs section](#inputs) below). For example:

```bash
ansible-playbook \
  -e '{
        "ewc_provider": "eumetsat",
        "tf_project_path": "~/ssh-bastion-server-1/ewc-tf-module-openstack-compute",
        "app_name_override": "ssh-bastion",
        "instance_name_override": "server",
        "instance_index_override": 1,
        "flavor_name_override": "eo2.medium",
        "image_name_override": "Rocky-8.10-20250204105303",
        "public_keypair_name_override": "john-claudy-publickey",
        "private_keypair_path": "~/.ssh/id_rsa",
        "networks_override": ["private"]
        "security_groups_override": ["ssh"],
        "whitelisted_ip_ranges_override": ["10.0.0.0/24"]
    }' \
  ssh-bastion-provision-flavour.yml
```
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| ewc_provider | your target EWC provider. Must match that the provider of your OpenStack application credentials. Valid input values are `ecmwf` or `eumetsat`. | `string` | n/a | yes |
| tf_project_path | path to terraform module directory. Example: `~/ssh-bastion-server-1/ewc-tf-module-openstack-compute` | `string` | n/a | yes |
| app_name_override | application name, used as prefix in the full instance name. Example: `ssh-bastion` | `string` | n/a | yes |
| instance_name_override| name of the instance, used in the full instance name.  Example: `server` | `string` | n/a | yes |
| instance_index_override | index or identifier for the instance, used as suffix in the full instance name. Example: `1` | `number` | n/a | yes |
| flavor_name_override | name the flavor to use for the instance. To learn about available options, checkout the [official EWC VM plans documentation](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+VM+plans). 💡 A VM plan with at least 4GB of RAM is recommended. This is due to constrains imposed by requirement [ewc-ansible-role-ssh-bastion](https://github.com/ewcloud/ewc-ansible-role-ssh-bastion).| `string` | n/a | yes |
| image_name_override | name of the image to use for the instance. For complete information on  available options, see the [official EWC Images documentation](https://confluence.ecmwf.int/display/EWCLOUDKB/EWC+Virtual+Images+Available). ⚠️ Only RockyLinux 8 and RockyLinux 9 VM images are currently supported. This is due to constrains imposed by requirement [ewc-ansible-role-ssh-bastion](https://github.com/ewcloud/ewc-ansible-role-ssh-bastion). Example: `Rocky-9.5-20250204105310`  | `string` | n/a | yes |
| public_keypair_name_override | name of public keypair (stored in OpenStack) to be copied into the instance for remote SSH access | `string` | n/a | yes |
| private_keypair_name_override | path to the local private keypair to use for SSH access to the instance. Example: `~/.ssh/id_rsa` | `string` | n/a | yes |
| networks_override | list of network names to attach the instance to. Example: `['private']` | `list(string)` | n/a | yes |
| security_groups_override | list of security group names to apply to the instance. Example: `['ssh']` | `list(string)` | n/a | yes |
| whitelisted_ip_ranges_override | IPv4 ranges (in CIDR format) to be whitelisted in Fail2ban configuration. Example: `['10.0.0.0/24','192.168.1.0/24']` | `list(string)` | n/a | no |

## Requirements
> ⚠️ Only RockyLinux 8 and RockyLinux 9 VM images are currently supported.
This is due to constrains imposed by requirement [ewc-ansible-role-ssh-bastion](https://github.com/ewcloud/ewc-ansible-role-ssh-bastion).

> 💡 A VM plan with at least 4GB of RAM is recommended. This is due to 
constrains imposed by requirement [ewc-ansible-role-ssh-bastion](https://github.com/ewcloud/ewc-ansible-role-ssh-bastion).

| Name | Version | Package Info |
|------|---------|-------|
| ewc-tf-module-openstack-compute | 1.0 | https://github.com/ewcloud/ewc-tf-module-openstack-compute  |
| ewc-ansible-role-ssh-bastion | 1.3 |  https://github.com/ewcloud/ewc-ansible-role-ssh-bastion |


## Troubleshooting

### 1. Terraform plan could not be created (`data.openstack_networking_network_v2.external`)

To simplify configuration, this template inferrers the default external
(public-internet connected) network where the instance shall be provisioned,
based on your choice of for the `ewc_provider` input variable.

If the default external network is not available during instance provision planning,
you might encounter an error like:
```
fatal: [localhost]: FAILED! => {
  "changed": false, 
  "msg": "Terraform plan could not be created\nSTDOUT: data.openstack_networking_network_v2.external: Reading
  ...
  \n\nSTDERR: \nError: Your query returned no results. Please change your search criteria and try again.\n\n  with data.openstack_networking_network_v2.external,\n  on datasources.tf line 1, in data \"openstack_networking_network_v2\" \"external\":\n   1: data \"openstack_networking_network_v2\" \"external\"
  ... 
```

#### Resolution

Ensure the application credentials you exposed in your work environment
belong to the correct EWC tenancy:
* source the correct `openrc` file

OR

* place the correct `cloud.yaml` file under the `~/.config/openstack` 
directory

Also, make sure that the EWC provider of the tenancy matches your 
choice of the `ewc_provider` input variable, before re-running the 
playbook.

Finally, double-check that the default external network,
(`external-internet` or `external` for ECMWF or EUMETSAT tenancies
respectively), exists in your tenancy or have not been 
renamed/removed.

### 2. Remote host identification has changed

In cases where the following sequence of events took place:
1. You successfully connected to the target VM via SSH at least once (by
running this playbook until the end, by using the `ssh` utility in your shell, etc.)
2. The VM was destroy and the recreated after the initial connection
3. You run this playbook

Execution may fail at the start of the IPA client installation, with the following
error:
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

#### Resolution
Remove the line in your `~/.ssh/know_hosts` file that
shows the same IP address displayed in the error message before re-running
the playbook.

###  3. Quota exceeded for resource "Floating IP" although none was requested

Your EWC tenancy has a maximum number of available public IP addresses.
If you hit that limit during execution of this template, the following error
will be raised:

```
fatal: [localhost]: FAILED! => {
  "changed": false,
  "msg": "\nError: Error creating openstack_networking_floatingip_v2: Expected HTTP response code [201 202] when accessing [POST https://<redacted>/v2.0/floatingips], but got 409 instead\n{\"NeutronError\": {\"type\": \"OverQuota\", \"message\": \"Quota exceeded for resources: ['floatingip'].\", \"detail\": \"\"}}\n\n  with openstack_networking_floatingip_v2.fip[0],\n  on main.tf line 64, in resource \"openstack_networking_floatingip_v2\" \"fip\":\n  64: resource \"openstack_networking_floatingip_v2\" \"fip\" {", "rc": 1, "stderr": "\nError: Error creating openstack_networking_floatingip_v2: Expected HTTP response code [201 202] when accessing [POST https://<redacted>/v2.0/floatingips], but got 409 instead\n{\"NeutronError\": ... 
```

#### Resolution

If no public IP addresses in your tenancy can be released, at least temporarily,
get in touch with the EWC support team to request an clarify your available options.

### 4. Unable to find flavour with name ...

If you input an invalid value for `flavour_name_override`, you might get the following
error:

```
fatal: [localhost]: FAILED! => {
  "changed": false,
  "msg": "\nError: Unable to find flavor with name eo2.medium\n\n  with openstack_compute_instance_v2.instance,\n  on main.tf line 1, in resource \"openstack_compute_instance_v2\"  ...
 ```

 #### Resolution

Ensure the application credentials you exposed in your work environment
belong to the correct EWC tenancy.

Also, make sure that the EWC provider of the tenancy matches your 
choice of the `ewc_provider` input variable, before re-running the 
playbook.

Finally, double-check that the chosen flavour name exists in your
tenancy and has not been renamed/removed.

 ### 5. The role was not found

This playbook requires one or more Ansible Roles to be be available
in your working environment. Otherwise, an error such as the one
below might be thrown during execution:

 ```
 ERROR! the role '<role-name>-<MAJOR.MINOR>' was not found in ~/$(pwd)/roles:~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:~/$(pwd) ...
 ```

 #### Resolution

Make sure to download and place the required Ansible Roles within
one of the paths Ansible expects to find them. Check the "Getting Started"
section of the main [README.md](../../README.md) for details on
how to do so.