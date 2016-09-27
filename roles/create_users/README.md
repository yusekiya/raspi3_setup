Ansible Role: create users
=========

Create users.

Requirements
------------

None.

Role Variables
--------------

``` yaml
create_users_list:
  - {name: 'user1', pass: 'plain_passowrd1', groups: 'sudo,video'}
  - {name: 'user2', pass: 'plain_password2'}
```

Dependencies
------------

None.

Example Playbook
----------------

``` yaml
- hosts: all
  vars_files:
    - vars/main.yml
  roles:
    - {role: create_users, become: yes}
```


License
-------

MIT

