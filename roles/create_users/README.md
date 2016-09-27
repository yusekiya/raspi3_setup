Ansible Role: create users
=========

Create users.
All the tasks need root privilege.

Requirements
------------

None.

Role Variables
--------------

Variable `create_users_list` contains dictionaries
composed of items `name`, `pass`, and optional `groups`.
`name` is an account name to be created, and `pass` is plain text password
for the user.
`groups` is a string of groups separated by comma.
If `groups` is provided, the groups are append to the groups of the user.

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

