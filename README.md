Ansible Fluentd Role
====================

This role installs and configures Fluentd v2.x on a Debian-based server from official Treasure Data repositories.

Requirements
------------

This role requires Ansible 1.7 or higher and platform requirements are listed
in the metadata file.

Features
--------
- install fluentd 
- install fluentd plugins
- full fluentd configuration over role variables (fluentd_plugins, fluentd_sources, fluentd_matches, fluentd_system, fluentd_includes)

Examples
========

```
- name: deploy fluentd server
  hosts: fluentd_server
  sudo: yes

  roles:
    - role: clickfreak.fluentd
      fluentd_plugins:
        - fluent-plugin-elasticsearch
        - fluent-plugin-forest
        - fluent-plugin-exclude-filter

      fluentd_sources:
        - comment: "live debugging agent"
          type: "debug_agent"
          params:
            - bind: "127.0.0.1"
            - port: "24230"

        - comment: "Syslog input"
          type: "syslog"
          params:
            - port: "5140"
            - bind: "{{ ansible_default_ipv4.address }}"
            - tag: "syslog"

      fluentd_matches:
        - comment: "match tag=debug.** and dump to console"
          match: "debug.**"
          type: stdout

        - comment: "send all data from net equipment to elasticsearch and files"
          match: "**"
          type: "copy"
          params:
            - store:
              - type: elasticsearch
              - logstash_format: "true"
              - host: "127.0.0.1"
              - port: "9200"
            - store:
              - type: file
              - path: /var/log/td-agent/all_logs
              - time_slice_format: "%Y-%m-%d"
              - time_slice_wait: "1m"
              - append: "true"

      fluentd_system:
        - log_level: "error"

      fluentd_includes:
        - "/etc/td-agent/conf.d/*"

```

Dependencies
------------

python, python-apt on target hosts. You can use ansible to install it :)

Author Information
------------------

Konstantin Novakovsky / Selectel LLC
