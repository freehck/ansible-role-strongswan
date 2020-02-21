freehck.strongswan
=========

Install and configure strongSwan IPSec

Description
-----------

This role installs strongSwan IPSec.

It provides you with a good predefined ipsec template and enables by default PSK auth generating ipsec.secrets for you. Both ipsec.conf and ipsec.secrets templates are parameterized so if you need some more complex configuration, you can use your own template.

Also, this role is easy to use because of [examples](tests) you can use. All you need is to have Vagrant and Virtualbox installed and run some tests to get a bunch of virtual machines that just show that it works.

Role Variables
--------------

`strongswan_ipsec_conf_template`: template for `ipsec.conf` (if not set, using the default [template](templates/ipsec.conf) from the role)

`strongswan_ipsec_conf_dest`: correct location path for ipsec.conf on server, default is `/etc/ipsec.conf`

`strongswan_ipsec_secrets_enabled`: if `true` (default), creates `ipsec.secrets`

`strongswan_ipsec_secrets_template`: template for `ipsec.secrets` (if not set, using the default [template](templates/ipsec.secrets) from the role)

`strongswan_ipsec_secrets_dest`: correct location path for ipsec.secrets on server, default is `/etc/ipsec.secrets`

`strongswan_default_ipv4_address`: default ipv4 address is `ansible_default_ipv4.address` but here you can specify another one (in case ansible has mistaken when gathered host facts)

`strongswan_psk`: if you use PSK (Pre Shared Key) for your IPSec, specify this variable

All the other variables are for the default `ipsec.conf` template. If you decided to use your own, you don't need them. And probably you don't need this the role. So please, give me some feedback if the default template doesn't fit your needs. I'll be glad to modify it for you and release a new version.


These vars are for `config setup`, I list them with the default values:

`strongswan_config_cachecrls`: "yes"

`strongswan_config_uniqueids`: "yes"

`strongswan_config_charondebug`: ""


These variables are for `config %default`:

`strongswan_conn_fill_default`: yes

`strongswan_conn_keyingtries`: "%forever"

`strongswan_conn_dpddelay`: "30s"

`strongswan_conn_dpdtimeout`: "120s"

`strongswan_conn_dpdaction`: "clear"

`strongswan_conn_left`: "{{ ansible_default_ipv4.address }}"

`strongswan_conn_leftsubnet`: "{{ ansible_default_ipv4.address }}/32"

`strongswan_conn_leftprotoport`: "udp/l2tp"

`strongswan_conn_right`: "%any"

`strongswan_conn_rightsubnet`: "0.0.0.0/0"

`strongswan_conn_leftauth`: "psk"

`strongswan_conn_forceencaps`: "yes"

`strongswan_conn_rightauth`: "psk"

`strongswan_conn_leftid`: "{{ ansible_default_ipv4.address }}"

`strongswan_conn_ikelifetime`: "1h"

`strongswan_conn_keylife`: "8h"

`strongswan_conn_ike`:
    - "aes128-sha256-curve25519"
    - "aes128-sha1-modp1536"
    - "aes128-sha1-modp1024"
    - "aes128-md5-modp1536"
    - "aes128-md5-modp1024"
    - "3des-sha1-modp1536"
    - "3des-sha1-modp1024"
    - "3des-md5-modp1536"
    - "3des-md5-modp1024"

`strongswan_conn_esp`:
    - "aes128-sha1-modp1536"
    - "aes128-sha1-modp1024"
    - "aes128-md5-modp1536"
    - "aes128-md5-modp1024"
    - "3des-sha1-modp1536"
    - "3des-sha1-modp1024"
    - "3des-md5-modp1536"
    - "3des-md5-modp1024"

`strongswan_conn_auto`: "start"

`strongswan_conn_keyexchange`: "ike"

`strongswan_conn_type`: "tunnel"


This variable describes your own connections:

`strongswan_conns`: `[]`

This variable must contain a dictionary. Keys are the names of your `conn`'s, and values are dicts of options that you use for `conn`'s, when write a `ipsec.conf`.

Example
-------

You can found some examples under test directory. F.e. one of them for [net2net-psk](tests/net2net-psk):

    - hosts: strongswan
      become: true
      max_fail_percentage: 0
      serial: 4
      vars:
        strongswan_psk: Vie6ek4Duph4auvo2chu1Foophoh7zaI
        strongswan_conns:
          net2net:
            type: tunnel
            leftid: "@moon"
            left: "192.168.0.11"
            leftsubnet: "10.1.0.0/24"
            rightid: "@sun"
            right: "192.168.0.22"
            rightsubnet: "10.2.0.0/24"
      roles:
        - role: freehck.strongswan
        - role: freehck.sysctl
          sysctl_opt_name: "net.ipv4.ip_forward"
          sysctl_opt_value: "1"
          when: forward | default(false)
      tasks:
        - name: remove default route and add new one
          shell: |
            ip route del default;
            ip route add default via {{ def_gw }};

Tests
-----

This role provides you with a Vagrant-based test suite under `tests/` directory.

Just type following command in terminal:

    cd tests/test-on-your-choice && make

This playbook provides you with following vagrant tests:

1. [net2net-psk](tests/net2net-psk) -- this is a test for 4 hosts: alice and bob are endpoint-users each in his own network, moon and sun are their gateways. For more info about this test look [here](https://www.strongswan.org/testing/testresults/ikev1/net2net-psk/index.html)

Install
-------

This role can be installed from [Ansible Galaxy](https://galaxy.ansible.com/):

`ansible-galaxy install freehck.strongswan`

License
-------

MIT

Author Information
------------------

Dmitrii Kashin, <freehck@freehck.ru>
