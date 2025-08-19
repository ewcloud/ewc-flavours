# Remote desktop flavour

The remote desktop is a regular RockyLinux instance equipped with [X2Go](https://wiki.x2go.org/doku.php).
It enables you to access a graphical desktop computer running in your remote 
instance of choice, over a low bandwidth (or high bandwidth) connection.
This means that you can connect to it via the
[X2Go client](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)
to enjoy a regular desktop user experience.

This subdirectory contains a configuration template
(i.e. an [Ansible Playbook](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks.html))
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to:
* Configure a pre-existing RockyLinux virtual machine, with a minimum
  recommended 4GB of RAM, such that it:
  * Enables users to operate the remote hosts through a graphical desktop 
  (i.e. a [MATE desktop environment](https://mate-desktop.org/)), over a low or high bandwidth connection.

## Usage

### 1. Specify the target host and SSH credentials
Create an inventory file to specify address/credentials that Ansible should use
to reach the virtual machine you wish to configure:

```yaml
# inventory.yml
---
ewcloud:
  hosts:
    remote_desktop:
      ansible_python_interpreter: /usr/bin/python3
      ansible_host: <add the IPV4 address of the target host>
      ansible_ssh_private_key_file: <add the path to local SSH private key file>
      ansible_user: cloud-user
      ansible_ssh_common_args: -o StrictHostKeyChecking=accept-new

```

### 2. Configure and apply the template

#### 2.1. Interactive Mode

By running the following command, you can trigger an interactive session that
prompts you for the necessary user inputs, and then applies changes to your
target EWC environment:

```bash
ansible-playbook -i inventory.yml remote-desktop-flavour.yml
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
  -e '{"whitelisted_ip_ranges": ["10.0.0.0/24","192.168.1.0/24"]}' \
  remote-desktop-flavour.yml
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| whitelisted_ip_ranges_override | IPv4 ranges (in CIDR format) to be whitelisted in Fail2ban configuration. When in doubt, do not set. Example: `['10.0.0.0/24','192.168.1.0/24']` | `list(string)` | n/a | no |


## Requirements

> ⚠️ Only RockyLinux 8.10 instances are currently supported due
to constrains imposed by the required ewc-ansible-role-remote-desktop Ansible
Role.

> 💡 A VM plan with at least 4GB of RAM is recommended for successful setup and
stable operation. 

| Name | Version | Package Info |
|------|---------|-------|
| ewc-ansible-role-remote-desktop | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-remote-desktop |
