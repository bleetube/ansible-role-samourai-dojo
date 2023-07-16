# MariaDB

```yaml
  roles:
    - fauust.mariadb
```

## Example Variables

```yaml
mariadb_databases:
  - name: dojo
    collation: utf8_general_ci
    encoding: utf8
    replicate: false

mariadb_users:
  - name: samourai
    host: localhost
    password: "{{ lookup('ansible.builtin.env', 'DOJO_MARIADB') }}"
    priv: "dojo.*:ALL"
    state: present
  - name: samourai
    host: '%'
    password: "{{ lookup('ansible.builtin.env', 'DOJO_MARIADB') }}"
    priv: "dojo.*:ALL"
    state: present

mariadb_innodb_raw: |
    innodb_buffer_pool_size = 512M
    key_buffer_size = 10M
    transaction_isolation=READ-COMMITTED
```
