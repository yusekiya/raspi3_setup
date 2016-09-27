Ansible Role: kiosk_env
=========

Establish environment for kiosk mode.
This role includes following tasks.

- Change autologin user to a specified user (tags: autologin, need_root)
- Show/Hide cursor (tags: cursor, need_root)
- Autohide settings of LXDE panel (tags: lxde_panel)
- Install and configure xscreensaver (tags: screensaver, apt, need_root)
- Add cron tasks for xscreensaver (tags: screensaver, cron)
- Rotate screen upside down (tags: screen_rotation, need_root)
- Configure backlight brightness (tags: backlight)

The need_root tag is added to tasks which requires root privilege.

Requirements
------------

- cron

Role Variables
--------------

Available variables are listed below.

User name for kiosk mode.

``` yaml
kiosk_env_user: 'info'
```

Whether to enable autologin of the account or not.

``` yaml
kiosk_env_autologin_enabled: yes
```

Whether to show cursor.

``` yaml
kiosk_env_cursor_enabled: no

```

Whether to enable autohide of LXDE panel (task bar), and the height of the hidden panel .

``` yaml
kiosk_env_autohide_enabled: yes
kiosk_env_autohide_panel_height: 0
```

Variables to configure xscreensaver.

``` yaml
kiosk_env_xscreensaver:
  # Screen goes blank after keyborad or mouse have been idle for this time
  timeout: '12:00:00'
  mode: 'blank'
  # Settings for power management (may not work)
  dpmsEnabled: 'False'
  dpmsQuickoffEnabled: 'False'
  dpmsStandby: '12:00:00'
  dpmsSuspend: '12:00:00'
  dpmsOff: '12:00:00'
```

Whether to let crontab send you email.

``` yaml
kiosk_env_cron_mail_enabled: no
```

Whether cron tasks are created/removed.

``` yaml
kiosk_env_xscreensaver_cron_enabled: yes

```

When to screensaver is activated/deactivated.

``` yaml
kiosk_env_xscreensaver_activate_time:
  minute: 0
  hour: 23
kiosk_env_xscreensaver_deactivate_time:
  minute: 0
  hour: 12
```

Backlight blightness

``` yaml
# integer between 0 and 255
kiosk_env_backlight_brightness: 128
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
    - {role: kiosk_env, when: kiosk_env_user is defined, become: yes, become_user: '{{kiosk_env_user}}'}
```

If you want to chage autologin user to default user 'pi', perform the following.

``` yaml
- hosts: all
  roles:
    - {role: kiosk_env, become: yes, become_user: 'pi', kiosk_env_autologin_enabled: yes, tags: ['autologin']}
```


License
-------

MIT

