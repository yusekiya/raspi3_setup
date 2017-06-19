# Setup raspi3 with ansible

## Write os image to SD card using mac

1. Identify the disk of SD card, e.g., `disk4` (not `disk4s1`) with `diskutil list`
2. Format the SD card

  ``` bash
  $ diskutil eraseDisk FAT32 <RASPBIAN> </dev/diskX>
  ```

  Here, `<RASPBIAN>` is any name of the volume (must be capital characters),
  and `diskX` is a disk identifier such as `disk4`.

3. Unmount disk

  ``` bash
  $ diskutil unmountDisk </dev/diskX>
  ```

4. Write os image

  ``` bash
  $ sudo dd bs=1m if=<path/to/raspbian.img> of=</dev/rdiskX>
  ```

  Note that 'r' is prefixed to the disk identifier.
  If this command results in `dd: invalid number '1m'`,
  replace `bs=1m` with `bs=1M`.
  If this command still fails, see [here][1].

5. Eject SD card

  ``` bash
  $ diskutil eject </dev/diskX>
  ```

See [here][1] for detailed information.

## Initial setup

1. Insert the SD card to raspi
2. Connect raspi and rooter with LAN cable
3. Turn on raspi
4. Login to raspi via SSH

  ``` bash
  $ ssh pi@raspberrypi.local
  ```

  The default password is 'raspberry'.
5. Create users
6. Configure the following settings with `sudo raspi-config`
  - Expand FileSystem: To make entire space of SD card available
  - Change User Password: Change default password of the user 'pi'
7. Update system

  ``` bash
  $ sudo apt-get update
  $ sudo apt-get upgrade
  $ sudo rpi-update
  $ sudo reboot
  ```


## Setup with ansible

Raspi setup procedure such as wifi setup and development environment setup is described in the ansible playbook, site.yml.
Check roles described in site.yml and README.md of each role
to see what type of tasks will be performed.
Take the following procedure to configure your raspi.

1. Clone raspi3_setup repository on control (host) computer.

  ``` bash
  $ git clone https://github.com/yusekiya/raspi3_setup.git
  ```

2. Copy template inventory file, template_hosts, to inventory file, hosts, and write the ip address of raspi into the inventory file, e.g.,

  ``` ini:hosts
  [raspi]
  192.168.xxx.xxx
  # or with port number
  192.168.xxx.xxx:yyyy
  ```

3. Copy vars/template.yml to vars/main.yml, and modify variables in the file.
See README.md of each role for detailed information about the variables.
4. Modify the value of `remote_user` in ansible.cfg to a user name you want to login as. Default is 'pi'.
5. To setup wifi, the vault password is required. If you don't need wifi setup, comment out `vars/vault_wifi_key.yml` in site.yml, and `ask_vault_pass = True` in ansible.cfg. Otherwise, modify variable `raspi_network_wifi` according to your environment.
6. Fetch required roles

  ```bash
  $ ansible-galaxy install -r requirements.yml -p ./roles
  ```

7. Run playbook

  ``` bash
  $ ansible-playbook site.yml
  ```

8. Rerun playbook after reboot just in case, and confirm that each task is ok (not changed).


## Launch application automatically after login for kiosk user
Edit /home/`<user>`/.config/autostart/`<name>`.desktop.

``` ini:/home/<user>/.config/autostart/<name>.desktop
[Desktop Entry]
Type=Application
Name=<name>
Exec=<absolute/path/to/executable>
```


<!-- Reference -->
[1]: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md
