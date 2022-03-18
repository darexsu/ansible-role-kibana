# Ansible role: Kibana
[![CI Molecule](https://github.com/darexsu/ansible-role-kibana/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-kibana/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/58430?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [requirements](#requirements)
      - [merge behaviour](#merge-behaviour)
  - Playbooks (merge version):
      - [install and configure: Kibana, FirewallD](#install-and-configure-kibana-firewalld-merge-version)
          - [install: Kibana](#install-kibana-merge-version)
          - [configure: Kibana](#configure-kibana-merge-version)
  - Playbooks (full version):
      - [install and configure: Kibana, FirewallD](#install-and-configure-kibana-firewalld-full-version)
          - [install: Kibana](#install-kibana-full-version)
          - [configure: Kibana](#configure-kibana-full-version)

### Platforms

|  Testing         | repo: elastic      |
| :--------------: | :----------------: |
| Debian 11        |  elastic.co        |
| Debian 10        |  elastic.co        |
| Ubuntu 20.04     |  elastic.co        |
| Ubuntu 18.04     |  elastic.co        |
| Oracle Linux 8   |  elastic.co        |
| Rocky Linux 8    |  elastic.co        |

### Install

```
ansible-galaxy install darexsu.kibana --force
```

### Requirements
collections: [ansible.posix](https://docs.ansible.com/ansible/latest/collections/ansible/posix/index.html)

roles: [FirewallD](https://github.com/darexsu/ansible-role-firewalld) (will automatically be installed)


### Merge behaviour

Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?:
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```

##### Install and configure: Kibana, FirewallD (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Kibana
      kibana:
        enabled: true
        version: "8.x"
      # Kibana -> install
      kibana_install:
        enabled: true
      # Kibana -> config -> kibana.yml
      kibana_yml:
        enabled: true
        data:
          server.host: "0.0.0.0"
          server.publicBaseUrl: "http://0.0.0.0:5601/"

      # FirewallD
      firewalld:
        enabled: true
      # FirewallD -> rules
      firewalld_rules:
        kibana_port_5601:
          enabled: true

  tasks:
    - name: role darexsu kibana
      include_role:
        name: darexsu.kibana

```
##### Install: Kibana (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Kibana
      kibana:
        enabled: true
        version: "8.x"
      # Kibana -> install
      kibana_install:
        enabled: true

  tasks:
    - name: role darexsu kibana
      include_role:
        name: darexsu.kibana

```
##### Configure: Kibana (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Kibana
      kibana:
        enabled: true
      # Kibana -> config -> kibana.yml
      kibana_yml:
        enabled: true
        file: "kibana.yml"
        src: "kibana_yml.j2"
        backup: false
        data:
          server.host: "0.0.0.0"
          server.publicBaseUrl: "http://0.0.0.0:5601/"
          pid.file: /run/kibana/kibana.pid
          logging:
            appenders:
              file:
                type: file
                fileName: /var/log/kibana/kibana.log
                layout:
                  type: json
            root:
              appenders:
                - default
                - file

  tasks:
    - name: role darexsu kibana
      include_role:
        name: darexsu.kibana

```
##### Install and configure: Kibana, FirewallD (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Kibana
      kibana:
        enabled: true
        version: "8.x"
        repo: "elastic"
        service:
          enabled: true
          state: "started"
      # Kibana -> install
      kibana_install:
        enabled: true
        packages: ["kibana"]
      # Kibana -> config -> kibana.yml
      kibana_yml:
        enabled: true
        file: "kibana.yml"
        src: "kibana_yml.j2"
        backup: false
        data:
          server.host: "0.0.0.0"
          server.publicBaseUrl: "http://0.0.0.0:5601/"
          pid.file: /run/kibana/kibana.pid
          logging:
            appenders:
              file:
                type: file
                fileName: /var/log/kibana/kibana.log
                layout:
                  type: json
            root:
              appenders:
                - default
                - file

      # FirewallD
      firewalld:
        enabled: true
      # FirewallD -> rules
      firewalld_rules:
        kibana_port_5601:
          enabled: true
          zone: "public"
          state: "enabled"
          port: "5601/tcp"
          permanent: true

  tasks:
    - name: role darexsu kibana
      include_role:
        name: darexsu.kibana

```
##### Install: Kibana (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Kibana
      kibana:
        enabled: true
        version: "8.x"
        repo: "elastic"
        service:
          enabled: true
          state: "started"
      # Kibana -> install
      kibana_install:
        enabled: true
        packages: ["kibana"]

  tasks:
    - name: role darexsu kibana
      include_role:
        name: darexsu.kibana

```
##### Configure: Kibana (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Kibana
      kibana:
        enabled: true
        version: "8.x"
        repo: "elastic"
        service:
          enabled: true
          state: "started"
      # Kibana -> config -> kibana.yml
      kibana_yml:
        enabled: true
        file: "kibana.yml"
        src: "kibana_yml.j2"
        backup: false
        data:
          server.host: "0.0.0.0"
          server.publicBaseUrl: "http://0.0.0.0:5601/"
          pid.file: /run/kibana/kibana.pid
          logging:
            appenders:
              file:
                type: file
                fileName: /var/log/kibana/kibana.log
                layout:
                  type: json
            root:
              appenders:
                - default
                - file

  tasks:
    - name: role darexsu kibana
      include_role:
        name: darexsu.kibana

```
