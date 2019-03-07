# CI: Copy the dynamic configuration of servers on localhost

This role is useful for getting some deploy configuration from a remote host for using in next steps.

## Role Variables

- `ci_save_server_state__instances` You must set this variable for the next use in the template.
- `ci_save_server_state__file` Specify the path to the file on the local host where the information will be saved.
- `ci_save_server_state__template` The template by which the information with the variable will be saved.
- `ci_save_server_state__delegate_to` (default: 127.0.0.1): The IP address of the local host where the information will be saved.
- `ci_save_server_state__become` (default: no)

## Example Playbook

```yaml
- hosts: all
  become: yes
  become_method: sudo
  vars:
    local_workspace: ".."
    project_name: test
    project_default_port: 8080
    project_instances: []
  tasks:
    - name: Set some dynamic information
      set_fact:
        some_dynamic_host: test
        some_dynamic_port: 1234

    - name: Set instance configuration item
      set_fact:
        project_item: {
          host: "{{ some_dynamic_host }}",
          port: "{{ some_dynamic_port | ternary(some_dynamic_port, project_default_port) }}"
        }

    - name: Set project_instances
      set_fact:
        project_instances: "{{ project_instances + [ project_item ] }}"

    - name: Save state
      include_role:
        name: levonet.ci_save_server_state
      vars:
        ci_save_server_state__instances: "{{ project_instances }}"
        ci_save_server_state__file: "{{ local_workspace }}/.state/{{ project_name }}/{{ inventory_hostname }}.conf"
```

## License

[MIT](https://opensource.org/licenses/MIT)

## Author Information

This role was created by [Pavlo Bashynskyi](https://github.com/levonet)
