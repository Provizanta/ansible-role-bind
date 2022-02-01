Ansible role: bind
=========

![main Build status](https://github.com/Provizanta/ansible-role-bind/actions/workflows/main.yml/badge.svg)

Install and configure a bind9 DNS server.

Requirements
------------

None

Role Variables
--------------

These variables are defined in [defaults/main.yml](./defaults/main.yml):

    dns_use_rfc1918_zones: true

    dns_zones: {}

    dns_options:
      directory: "/var/cache/bind"
      forwarders: []
      allow-query:
        - any
      dnssec-validation: auto
      zone-statistics: none
      max-cache-size: 100m
      listen-on-v6:
        - any
      version: none


Dependencies
------------

None

Example Playbook
----------------
The example playbook contains a procedure to extract all inventory hosts (except the `localhost` and feed them directly into the `dns_zones` variable to be deployed.

    - name: Converge
      hosts: all
      roles:
        - role: ansible-role-bind
          vars:
            dns_use_rfc1918_zones: true
            dns_zones:
              test.example.com:
                records: |-
                  {%- set hosts=dict() -%}
                  {%- for host in (groups['all'] | difference('localhost') | unique) -%}
                  {%-   set _ = hosts.update({ host : { 'host': hostvars[host]['ansible_host'], 'type': 'A'}}) -%}
                  {%- endfor -%}{{ hosts }}
                config:
                  type: master
                  # allow_transfer: []
                  also-notify: []
                  allow-update:
                    - key rndc-key
            dns_options:
              directory: "/var/cache/bind"
              forwarders: []
              allow-query:
                - any
              dnssec-validation: auto
              zone-statistics: none
              max-cache-size: 100m
              listen-on-v6:
                - any
              version: none

License
-------

MIT

Author Information
------------------

Tibor Csóka
