Ansible Role: Network setup for raspberry pi
=========

Configure network settings of raspberry pi.
This role

- Set WiFi SSID and psk (tags: wifi)

The all tasks need root privilege.

Requirements
------------

None.

Role Variables
--------------
Available variables are listed below.

A list of dictionary composed of ssid and pass phrase of wifi.

``` yaml
raspi_network_wifi:
  - {SSID: "wifi1", PASS: "passphrase_of_wifi1"}
  - {SSID: "wifi2", PASS: "passphrase_of_wifi2"}
```

Dependencies
------------

None.

Example Playbook
----------------

``` yaml
- hosts: all
  vars_files:
    - var/wifi_key.yml
  roles:
    - { role: raspi_network}
```

License
-------

MIT

