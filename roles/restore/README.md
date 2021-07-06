# redhat_cop.tower_utilities.restore

Ansible role to restore a backup of Ansible Tower.

## Requirements

None

## Role Variables

Available variables are listed below, along with default values defined (see defaults/main.yml)

```yaml
# Role Vars
tower_working_location: "/root/"
restore_file: "tower-backup-latest.tar.gz"
restore_location: "{{ tower_working_location }}/{{ restore_file }}"

# Use the default tower installation template
pre_tasks_process_template: true

# Tower variables
tower_admin_password: "password"

# Postgresql variables
tower_pg_database: "awx"
tower_pg_username: "awx"
tower_pg_password: "password"

# RabbitMQ variables (Tower<=3.6)
tower_rabbitmq_username: tower
tower_rabbitmq_password: "password"
tower_rabbitmq_cookie: "cookiemonster"
tower_rabbitmq_port: 5672
tower_rabbitmq_vhost: tower
tower_rabbitmq_use_long_name: false

tower_releases_url: https://releases.ansible.com/ansible-tower/setup
tower_setup_file: ansible-tower-setup-{{ tower_release_version }}.tar.gz

tower_nodes:
  - "localhost ansible_connection=local"

tower_database_node: ""
tower_database_port: ""

tower_ssh_connection_vars: ''

# as long as the host name is empty, Automation Hub will NOT be installed
tower_ah_nodes: []

# the default configures an AH using the default database with similar defaults
# as Tower
tower_ah_admin_password: "{{ tower_admin_password }}"
# if tower_database_node isn't defined or is empty,
# the host where the installation takes place is deemed to host the DB
tower_ah_pg_node: "{{ tower_database_node | default(ansible_host | default(inventory_hostname), true) }}"
tower_ah_pg_port: "{{ tower_database_port }}"
tower_ah_pg_database: 'automationhub'  # it is NOT supported to use the same name as for Tower!
tower_ah_pg_username: "{{ tower_pg_username }}"
tower_ah_pg_password: "{{ tower_pg_password }}"
tower_ah_pg_sslmode: prefer

# The below variables are unset, but can be used to affect certificates within the automation platform
tower_ah_ssl_cert: <unset>
tower_ah_ssl_key: <unset>
tower_ssl_cert: <unset>
tower_ssl_key: <unset>
tower_pg_ssl_cert: <unset>
tower_pg_ssl_key: <unset>
tower_custom_ca_cert: <unset>

# Set isolated groups for isolated nodes if required (commented out, an example setting)
isolated_groups: <unset>
  # - name: dmz1
  #   hostnames:
  #   - isolatednode0.dmz1.example.com
  # - name: dmz2
  #   hostnames:
  #   - isolatednode0.dmz2.example.com
  #   - isolatednode1.dmz2.example.com
```

## Example Playbook

The following playbook and accompanying vars file containing the defined seed objects can be invoked in the following manner.

```sh
$ ansible-playbook playbook.yml -e @tower_vars.yml tower
```

```yaml
---
# Playbook to install Ansible Tower as a cluster

- name: Restore Ansible Tower
  hosts: localhost
  become: true
  vars:
    tower_nodes:
      - "clusternode[1:3].example.com"
    tower_database_node: "dbnode.example.com"
    tower_working_location: "{{playbook_dir}}"
    restore_location: "{{playbook_dir}}/tower-backup-latest.tar.gz"
  roles:
    - redhat_cop.tower_utilities.restore
```

## License

MIT

## Author Information

Sean Sullivan
