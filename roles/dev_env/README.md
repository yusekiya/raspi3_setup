Ansible Role: dev_env
=========

Establish development environment.

Requirements
------------

- git

Role Variables
--------------

Available variables are listed below.

User account name for development environement.

``` yaml
dev_env_user: 'username'
```

List of directories preferred to be made in advance.

``` yaml
dev_env_basic_dirs:
  - {path: '~/Downloads'}
  - {path: '~/.nodebrew/src'}
  - {path: '~/bin'}
  - {path: '~/repos'}
  - {path: '~/scratch'}
  - {path: '~/share'}
  - {path: '~/usr/bin'}
  - {path: '~/usr/include'}
  - {path: '~/usr/lib'}
  - {path: '~/usr/lib/python3/site-packages'}
  - {path: '~/usr/share'}
  - {path: '~/usr/src'}
```

Information about your dotfiles.
`symlink_script` will be executed after the task changed.

``` yaml
dev_env_dotfiles:
  repository: 'https://github.com/USER/dotfiles.git'
  dest: '~/dotfiles'
  symlink_script: 'bash setup.sh -f'
```

Information for anaconda.

``` yaml
dev_env_anaconda:
  prefix: '~/miniconda3'
  src: 'http://repo.continuum.io/miniconda/Miniconda3-latest-Linux-armv7l.sh'
  dest: '~/Downloads/Miniconda3-latest-Linux-armv7l.sh'
```

List of packages to be installed through apt command.

``` yaml
dev_env_packages:
  - {name: git}
  - {name: build-essential}
  - {name: htop}
  - {name: pkg-config}
```

List of git repositories to be cloned.

``` yaml
dev_env_git_repos:
  - {name: tpm,
     repo: "https://github.com/tmux-plugins/tpm.git",
     dest: "~/.tmux/plugins/tpm" }
  - {name: dircolors-solarized,
     repo: "https://github.com/seebi/dircolors-solarized.git",
     dest: "~/repos/dircolors-solarized" }
```

Information for nodebrew.

``` yaml
dev_env_nodebrew:
  executable_path: '~/.nodebrew/nodebrew'
  src: 'https://raw.githubusercontent.com/hokaccha/nodebrew/master/nodebrew'
  dest: '~/Downloads/nodebrew'
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
    - {role: dev_env, when: dev_env_user is defined, become: yes, become_user: '{{dev_env_user}}'}
```


License
-------

MIT
