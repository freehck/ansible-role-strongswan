freehck.strongswan
=========

Install and configure strongSwan IPSec

Description
-----------

This role installs strongSwan IPSec.

It provides you with a good predefined ipsec template and enables by default PSK auth generating ipsec.secrets for you. Both ipsec.conf and ipsec.secrets templates are parameterized so if you need some more complex configuration, you can use your own template.

Role Variables
--------------

TODO


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

`ansible-galaxy install freehck.k8s`

License
-------

MIT

Author Information
------------------

Dmitrii Kashin, <freehck@freehck.ru>
