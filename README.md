[![Build Status](https://travis-ci.org/open-io/ansible-role-openio-conscienceagent.svg?branch=master)](https://travis-ci.org/open-io/ansible-role-openio-conscienceagent)
# Ansible role `conscienceagent`

An Ansible role for Conscience agent. Specifically, the responsibilities of this role are to:

- install the OpenIO Conscienceagent
- configure the OpenIO Conscienceagent

## Requirements

- Ansible 2.4+

## Role Variables


| Variable   | Default | Comments (type)  |
| :---       | :---    | :---             |
| `openio_conscienceagent_namespace` | `"OPENIO"` | Namespace |
| `openio_conscienceagent_serviceid` | `"0"` | ID in gridinit |
| `openio_conscienceagent_version` | `latest` | Install a specific version |
| `openio_conscienceagent_gridinit_dir` | `/etc/gridinit.d/{{ openio_conscienceagent_namespace }}` | Path to copy the gridinit conf |
| `openio_conscienceagent_gridinit_file_prefix` | `""` | Maybe set it to {{ openio_conscienceagent_namespace }}- for old gridinit's style |

## Dependencies

No dependencies.

## Example Playbook

```yaml
- hosts: all
  become: true
  vars:
    NS: OPENIO
  pre_tasks:
    - name: Ensures namespace directory exists
      file:
        dest: "/etc/oio/sds.conf.d"
        state: directory
      tags: install

    - name: Copy using the 'content' for inline data
      copy:
        content: |
          [{{ NS }}]
          # endpoints
          conscience=172.17.0.2:6000
          #zookeeper=172.17.0.2:6005
          proxy=172.17.0.2:6006
          event-agent=beanstalk://172.17.0.2:6014
          ns.meta1_digits=3
          udp_allowed=yes
          ns.storage_policy=THREECOPIES
          ns.chunk_size=10485760
          ns.service_update_policy=meta2=KEEP|3|1|;rdir=KEEP|3|1|;
        dest: "/etc/oio/sds.conf.d/{{ NS }}"
  roles:
    - role: repo
    - role: gridinit
      openio_gridinit_namespace: "{{ NS }}"
    - role: conscience
      openio_conscience_namespace: "{{ NS }}"
      openio_conscience_bind_address: "{{ ansible_default_ipv4.address }}"
      openio_conscience_pools: []
    - role: meta
      openio_meta_namespace: "{{ NS }}"
    - role: oioproxy
      openio_oioproxy_namespace: "{{ NS }}"
    - role: conscienceagent
      openio_conscienceagent_namespace: "{{ NS }}"
```


```ini
[all]
node1 ansible_host=192.168.1.173
```

## Contributing

Issues, feature requests, ideas are appreciated and can be posted in the Issues section.

Pull requests are also very welcome.
The best way to submit a PR is by first creating a fork of this Github project, then creating a topic branch for the suggested change and pushing that branch to your own fork.
Github can then easily create a PR based on that branch.

## License

Apache License, Version 2.0

## Contributors

- [Guillaume DELAPORTE](https://github.com/GuillaumeDelaporte)(maintainer)
