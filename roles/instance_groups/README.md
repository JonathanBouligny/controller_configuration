# controller_configuration.instance_groups

## Description

An Ansible Role to create instance groups on Ansible Controller.

## Requirements

ansible-galaxy collection install -r tests/collections/requirements.yml to be installed
Currently:
  awx.awx
  or
  ansible.controller

## Variables

### Authentication

|Variable Name|Default Value|Required|Description|Example|
|:---:|:---:|:---:|:---:|:---:|
|`controller_state`|"present"|no|The state all objects will take unless overridden by object default|'absent'|
|`controller_hostname`|""|yes|URL to the Ansible Controller Server.|127.0.0.1|
|`controller_validate_certs`|`True`|no|Whether or not to validate the Ansible Controller Server's SSL certificate.||
|`controller_username`|""|yes|Admin User on the Ansible Controller Server.||
|`controller_password`|""|yes|Controller Admin User's password on the Ansible Controller Server. This should be stored in an Ansible Vault at vars/controller-secrets.yml or elsewhere and called from a parent playbook.||
|`controller_oauthtoken`|""|yes|Controller Admin User's token on the Ansible Controller Server. This should be stored in an Ansible Vault at or elsewhere and called from a parent playbook.||
|`controller_instance_groups`|`see below`|yes|Data structure describing your instance groups Described below.||

### Secure Logging Variables

The following Variables compliment each other.
If Both variables are not set, secure logging defaults to false.
The role defaults to False as normally the add instance groups task does not include sensitive information.
controller_configuration_instance_groups_secure_logging defaults to the value of controller_configuration_secure_logging if it is not explicitly called. This allows for secure logging to be toggled for the entire suite of controller configuration roles with a single variable, or for the user to selectively use it.

|Variable Name|Default Value|Required|Description|
|:---:|:---:|:---:|:---:|
|`controller_configuration_instance_groups_secure_logging`|`False`|no|Whether or not to include the sensitive instance groups role tasks in the log. Set this value to `True` if you will be providing your sensitive values from elsewhere.|
|`controller_configuration_secure_logging`|`False`|no|This variable enables secure logging as well, but is shared across multiple roles, see above.|

### Asynchronous Retry Variables

The following Variables set asynchronous retries for the role.
If neither of the retries or delay or retries are set, they will default to their respective defaults.
This allows for all items to be created, then checked that the task finishes successfully.
This also speeds up the overall role.

|Variable Name|Default Value|Required|Description|
|:---:|:---:|:---:|:---:|
|`controller_configuration_async_retries`|30|no|This variable sets the number of retries to attempt for the role globally.|
|`controller_configuration_instance_groups_async_retries`|`{{ controller_configuration_async_retries }}`|no|This variable sets the number of retries to attempt for the role.|
|`controller_configuration_async_delay`|1|no|This sets the delay between retries for the role globally.|
|`controller_configuration_instance_groups_async_delay`|`controller_configuration_async_delay`|no|This sets the delay between retries for the role.|

## Data Structure

### Instance Group Variables

|Variable Name|Default Value|Required|Type|Description|
|:---:|:---:|:---:|:---:|:---:|
|`name`|""|yes|str|Name of this instance group.|
|`new_name`|""|str|no|Setting this option will change the existing name (looked up via the name field).|
|`credential`|""|no|str|Credential to authenticate with Kubernetes or OpenShift. Must be of type "Kubernetes/OpenShift API Bearer Token". Will make instance part of a Container Group. |.
|`is_container_group`|False|no|bool|Signifies that this InstanceGroup should act as a ContainerGroup. If no credential is specified, the underlying Pod's ServiceAccount will be used.|
|`policy_instance_percentage`|""|no|int|Minimum percentage of all instances that will be automatically assigned to this group when new instances come online.|
|`policy_instance_minimum`|""|no|int|Static minimum number of Instances that will be automatically assign to this group when new instances come online.|
|`policy_instance_list`|""|no|list|List of exact-match Instances that will be assigned to this group.|
|`pod_spec_override`|""|no|str|A custom Kubernetes or OpenShift Pod specification.|
|`instances`|""|no|list|The instances associated with this instance_group.|
|`state`|`present`|no|str|Desired state of the resource.|

### Standard Project Data Structure

#### Yaml Example

```yaml
---
controller_instance_groups:
  - name: test_instance_group
```

## Playbook Examples

### Standard Role Usage

```yaml
---
- name: Playbook to configure ansible controller post installation
  hosts: localhost
  connection: local
  # Define following vars here, or in controller_configs/controller_auth.yml
  # controller_hostname: ansible-controller-web-svc-test-project.example.com
  # controller_username: admin
  # controller_password: changeme
  pre_tasks:
    - name: Include vars from controller_configs directory
      include_vars:
        dir: ./yaml
        ignore_files: [controller_config.yml.template]
        extensions: ["yml"]
  roles:
    - {role: redhat_cop.controller_configuration.groups, when: controller_groups is defined}
```

## License

[MIT](LICENSE)

## Author

[Sean Sullivan](https://github.com/sean-m-sullivan)
