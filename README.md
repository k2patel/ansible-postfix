Role Name
=========
Install postfix service 

Requirements
------------

None

Role Variables
--------------

## OpenDKIM and Postfix configuration
#### Location for the OpenDKIM configuration, Default `/etc/opendkim`
```yml
opendkim_config_path:
  [environment]: '/etc/opendkim'
```
#### Location for Postfix main.cf, Default `/etc/postfix/main.cf`
```yml
postfix_main_cf_path: # 
  [environment]: '/etc/postfix/main.cf'
```
#### Location for Postfix master.cf, Default `/etc/postfix/master.cf`
```yml
postfix_master_cf_path:
  [environment]: '/etc/postfix/master.cf'
```
#### Location for the Postfix Header_Checks (Uses PCRE), Default `/etc/postfix/header_checks`
```yml
postfix_header_checks_path:
  [environment]: '/etc/postfix/header_checks'
```

## Enable disable services
#### Enable OpenDKIM, Default `true`
```yml
opendkim_install:
  [environment]: true
```

#### Enable Firewall, Default `true`
```yml
firewall_install:
  [environment]: true
```

## DKIM Configuration Options
```yml
dkim_config_options:
  [environment]:
    opendkim_inet: "inet"
    opendkim_port: "8891"
    opendkim_user: "opendkim"
    opendkim_group: "opendkim"
    opendkim_host: "127.0.0.1"
    opendkim_bits: 1024
```

## Postfix Configuration Options, Add any configuration Options as hash.
```yml
postfix_config_options:
  [environment]:
    header_checks: "pcre:{{ postfix_header_checks_path[environment] }}"
    delay_warning_time: "0h"
    inet_interfaces: "all"
    inet_protocols: "all"
    maximal_queue_lifetime: "1d"
    message_size_limit: 30720000
    mynetworks_style: "subnet"
    myorigin: "domain.com"
    mynetworks: "10.10.1.0/24 192.168.1.0/24"
    myhostname: "{{ ansible_fqdn }}"
    smtpd_tls_security_level: "may"
    smtpd_tls_cert_file: "/etc/pki/tls/certs/host.crt"
    smtpd_tls_key_file: "/etc/pki/tls/private/host.key"
    smtpd_tls_CAfile: "/etc/pki/tls/certs/ca-bundle.crt"
    smtp_tls_cert_file: "/etc/pki/tls/certs/host.crt"
    smtp_tls_key_file: "/etc/pki/tls/private/host.key"
    smtp_tls_CAfile: "/etc/pki/tls/certs/ca-bundle.crt"
    smtpd_tls_auth_only: "yes"
    smtp_tls_loglevel: 1
    opendkim_domain: "domain.com"
    smtpd_milters: "inet:127.0.0.1:8891"
    non_smtpd_milters: "inet:127.0.0.1:8891"
    milter_default_action: "accept"
    smtpd_tls_protocols: "!SSLv2, !SSLv3"
    smtp_tls_protocols: "!SSLv2, !SSLv3"
    smtp_tls_session_cache_database: "btree:${data_directory}/smtp_scache"
    smtpd_tls_session_cache_database: "btree:${data_directory}/smtpd_scache"
    smtp_tls_security_level: "encrypt"
    always_add_missing_headers: "yes"
```

## Postfix Selinux Options
```yml
postfix_selinux_options:
  [environment]: {}
```

Dependencies
------------

None

Example Playbook
----------------

```yml
---
- name: Update New VM
  hosts: "hostname"
  remote_user: builder
  gather_facts: no
  become: true
  vars:
    - service: "postfix"
    - environment: "dev"

  pre_tasks:
    - set_fact:
        ansible_sudo_pass: "{{ ansible_password }}"
      tags: always
    - set_fact:
        ansible_become_method: sudo
        ansible_become_password: "{{ ansible_password }}"
      tags: always
    - setup:
        gather_subset:
        - '!hardware'
      tags: always

  tasks:
    - include_role:
        name: postfix
      when: service is defined and service in ['postfix']
```

License
-------

MIT

Author Information
------------------
Ketan Patel <k2patel@live.com>
