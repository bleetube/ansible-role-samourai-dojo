# Ansible Role: samourai-dojo

This Ansible role builds and installs a single Podman [container](https://code.samourai.io/dojo/samourai-dojo/-/blob/master/docker/my-dojo/node/Dockerfile) that runs the Javascript code for [Samourai Dojo](https://docs.samourai.io/dojo/start). It is intended to be composed with separate roles for Podman, mysql, and web proxy. It can be backended by a [nixbitcoin](https://github.com/bleetube/satstack/blob/main/nix/chespin.satstack.net/configuration.nix) node, or any external node with the necessary configuration.

For node experts or developers only: Usage of this role implies complete personal accountability and you should never reach out to the Samourai team for support regarding it. This project has no relation to Samourai.

## Requirements

* [podman](docs/PODMAN.md)
* [containers.podman](https://github.com/containers/ansible-podman-collections)
* [mariadb](docs/DATABASE.md)

## Dependencies

* [nginx_conf](docs/NGINX.md) (optional)

## Secrets

Use your favorite secret-store. As an example, you could use GNU pass:

```bash
pass generate -n SAMOURAI_DOJO_API
pass generate -n SAMOURAI_DOJO_ADMIN
pass generate -n SAMOURAI_DOJO_JWT
pass generate -n SAMOURAI_DOJO_MARIADB_PASSWORD
pass generate -n BITCOIND_DOJO_RPC_PASSWORD
```

Then source them into your local environment.

## Role Variables

See the [defaults](defaults/main.yml) for the role.

```yaml
samourai_dojo_version: "1.20.0"
samourai_dojo_onion_service: "{{ lookup('ansible.builtin.env', 'SAMOURAI_DOJO_ONION') }}"
samourai_dojo_onion_port: 80
#onion_services_path: /opt/dojo # a globally readable/writable path where the hsv3 hostname will be copied (optional)
```

## Example Playbook

```yaml
- hosts: dojo
  roles:
    - role: nginxinc.nginx_core.nginx
      become: true
    - role: fauust.mariadb
      become: true
    - role: alvistack.podman
      become: true
    - role: bleetube.dojo
      tags: dojo
  tasks:
    - import_tasks: nginx_conf.yml
      become: true
```

Have tor, bitcoind, and electrs running separately (e.g. nixbitcoin). And optionally whirlpool-cli.

For a working example, see [bleetube/satstack](https://github.com/bleetube/satstack).

## Systemd

```
systemctl --user status container-dojo.service
```

## Upgrades

Configure `samourai_dojo_version` and rerun the playbook.

```bash
ansible-playbook playbooks/samourai.yml --tags dojo
```

Mind any changes specifically to Node.js [environment variables](https://code.samourai.io/dojo/samourai-dojo/-/tree/master/docker/my-dojo/node), and the Dojo [install scripts](https://code.samourai.io/dojo/samourai-dojo/-/tree/master/docker/my-dojo/install) (or wait for this role to document any extra upgrade steps).

## Monitoring

See [dojo-exporter](https://github.com/bleetube/dojo_exporter)

## Troubleshooting

```bash
podman logs --follow dojo
podman inspect dojo | jq .[].Config.Env
ngrep -d lo port 80 -W byline
```

## Resources

* [Dojo repo](https://code.samourai.io/dojo/samourai-dojo)
* [Dojo docs](https://docs.samourai.io/en/dojo)