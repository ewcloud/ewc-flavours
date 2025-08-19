# IPA client enrollment/disenrollment automation via Morpheus

> ⚠️ These are public assets meant for Morpheus Integrations/Tasks/Workflows
setup.

> 💡 To learn more about the automation and integration capabilities of the
Morpheus hybrid cloud management software, checkout the
[official Morpheus documentation](https://docs.morpheusdata.com/en/7.0.9/integration_guides/Automation/ansible.html).

This subdirectory contains two configuration templates 
partially needed to enable automated LDAP/DNS management of instances
provisioned and teared down via Morpheus GUI.

To successfully deploy these assets, apply the 
[ewc-ipa-enroll-automation-via-morpheus](https://gitlab.com/ewcloud/ewc-ansible-playbook-ipa-enroll-automation-via-morpheus)
template onto your EWC environment.

If you require standalone templates which similar functionality but which
can be applied  to specific instances on a case-by-case basis, checkout instead
the [ipa-client-enroll-flavour](../ipa-client-enroll-flavour/) and 
[ipa-client-disenroll-flavour](../ipa-client-disenroll-flavour/) templates.

## Requirements

| Name | Version | Package Info |
|------|---------|-------|
| ewc-ansible-role-ipa-client-enroll | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-ipa-client-enroll |
| ewc-ansible-role-ipa-client-disenroll | 1.0 |  https://github.com/ewcloud/ewc-ansible-role-ipa-client-disenroll |
