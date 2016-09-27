raspi_common
=========

Common setup of raspberry pi3.
This role performs the followig tasks:

- Modify privilege of default user pi
- Change timezone
- Change hostname
- Install minimum packages
- Setup virtual keyboard

Requirements
------------

Ansible (2.1.1)

- useradd
- userdel
- usermod


Role Variables
--------------

Available variables are listed below, along with default values (see `raspi_common/vars/main.yml`):

``` yaml
# hostname
raspi_common_hostname: raspberrypi
# timezone
raspi_common_timezone: 'Asia/Tokyo'
# Packages to be installed
raspi_common_packages:
  - {name: florence}
  - {name: at-spi2-core}
  - {name: vim}
  - {name: git}
  - {name: fonts-takao}
  - {name: rsync}
```

Dependencies
------------

None

Example Playbook
----------------

``` yaml
- hosts: all
  vars_files:
      - vars/main.yml
  roles:
      - {role: raspi_common, become: yes}
```


License
-------

MIT

